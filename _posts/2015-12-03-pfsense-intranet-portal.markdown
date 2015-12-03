---
layout: post
title:  "pfSense Captive Portal login/logout page"
date:   2015-12-03 09:09:09
categories: portal, pfsense, intranet
---

> Bringing Part of the Portal into Captive Portal.

or:

> Where I do post php code.

### Prerequisistes

* pfsense installed (version 2.2.5, looks like the story will change for 2.3)
* (separate) 'intranet' server
* at least one user without technical background

### Aims

Play around and make a dedicated pfSense Captive Portal login/logout page.

### Overview

In the [community](http://siebenlinden.de "Homepage of ecovillage Sieben Linden") I live in we run a couple of internal services and guard Internet-Access by [pfSenses](https://pfsense.org "Homepage of pfSense") Captive Portal ("CP").

The Captive Portal intercepts any traffic for a not yet registered IP/MAC-Adress pair (clients come from the local network, so IP-Adresses can resolved to MAC-Adresses, although this can be tricked) and responds with a login page.

Users can either be defined with a local user manager, a RADIUS server <del>or an [LDAP server](https://redmine.pfsense.org/issues/5112 "Link to LDAP Authentication Captive Portal redmine issue for pfSense")</del> (this is another story and worth another blog post).  Upon successfull login, the MAC/IP/username-triple will be saved in a SQLite-database, the user is redirected to the requested URL (`redirurl`) and forthcoming traffic will be allowed.

The Captive Portal (with default settings) has a serious drawback: Only standard HTTP traffic can be intercepted, or more precisely: a redirect to the login page only works for HTTP-connections (for good reason).  In principle it is possible to use a certificate and intercept/redirect https-traffic, but you need a proper certificate if the users should not be scared away by a certificate warning.  *Note:* Yes, allowing http logins is as good as no logins if you are talking real security.

<img src="/assets/Diagram_pfsense_net.png"/>

As a result, the login page of the Captive portal will **only be shown if users attempt to access a http (vs https) page**.  Many users browsers are however configured to initially load ("homepage") a https page (which is good).  These users are confronted with a connection timeout and will be scared that no internet connection is available, our intranet or their computer is broken.

### The resulting need for a dedicated login page

One solution (besides many others) is to communicate a dedicated login page (e.g. https://intranet.intern), where after successfull login the Captive Portal redirects to a given page - the `redirurl` is just a parameter to the login-page (`http://192.168.0.1:8002/?redirurl=http://github.com` for example).

But this would force users to generate traffic to an external page, so the better idea is to redirect the user to a dedicated internal portal-kind-of-page.

The "Captive" Part of the Captive Portal will still work - a not yet registered MAC/IP-pair will get the login page presented.

<img src="/assets/Diagram_pfsense_intranet.png"/>

{% comment %}
### Where to redirect

The pfSense captive portal does not really know where to redirect.  What can happen is that people bookmark the login page (default: 192.168.0.1:8002) without any parameters or visit that page by other irreproducible accidents (Murphy).  The redirection parameter is encoded as a hidden field in the HTML login form and can be set to a default by the following snippet:

< code

Btw, the code can be uploaded using the webConfigurator (login page) and is then 'rendered' into `/var/etc/captiveportal_ZONE.html` (where ZONE is the zones name).  Note that the file itself gets overridden when configuration changes, so you really need to upload the file through the web-interface once happy.
{% endcomment %}

### Am I logged in?

Now, it would be nice if users can see whether they are logged in to the Captive Portal and get a logout-page instead of the login-page if they are.  To figure out whether a client is logged in, we resolve its IP and MAC-address and look up the IP/MAC-address in the SQLite3 database.  We present the username if the client indeed is logged in (minimal example):

{% highlight php %}
<html>
  <body>
    <h1>Captive Portal</h1>
    <?php
       require_once("captiveportal.inc");
  
       // Get IP/MAC from request and arp.
       $client_ip = $_SERVER['REMOTE_ADDR'];
       $client_mac = pfSense_ip_to_mac($client_ip)['macaddr'];
  
       // Get username from CP db.
       $db = captiveportal_opendb();
       if ($db && $client_ip && $client_mac) {
         $username_query = "SELECT username FROM captiveportal " .
                           " WHERE mac='{$client_mac}' AND ip='{$client_ip}';";
         $response = $db->query($username_query);
         if ($response != FALSE) {
           $username = $response->fetchArray()[0];
         }
         else {
           // Not logged in
         }
         $db->close();
       }
       else {
         echo "No Database connection or invalid request!";
         if ($db) {
           $db->close();
         }
         return;
       }
    ?>
    <?php
      if ($username) {
    ?>
      You should be able to access the internet and are logged in as
    <?php
        echo $username;
      }
      else {
        // Not logged in
    ?>
      <h2>By Username/Pass</h2>
      <form method="post" action="#PORTAL_ACTION#">
        <input name="auth_user"  type="text"></input>
        <input name="auth_pass"  type="password"></input>
        <input name="redirurl"   type="hidden" value="#PORTAL_REDIRURL#"></input>
        <input name="accept"     type="submit" value="Login"></input>
      </form>
  
      <h2>By Voucher</h2>
      <form method="post" action="#PORTAL_ACTION#">
        <input name="auth_voucher" type="text"></input>
        <input name="redirurl"     type="hidden" value="#PORTAL_REDIRURL#">
        </input>
        <input name="accept"       type="submit" value="Submit"></input>
      </form>
    <?php
      // Not logged in
      }
    ?>
  </body>
</html>
{% endhighlight %}

### Logout?

The default "solution" of pfSenses Captive Portal is to javascript-wise open a popup-window with a button to log out.  What I did not yet mention is that actually, not only username, IP- and MAC addresses of clients are stored, besides other things that happen, a session(id) is created on successfull authentication against the CP.  For security reasons the client can only log out by giving a session-id.  As in our http-case all traffic can possibly be listened to anyway, the sessionid does not provide much additional security (against ip/mac faking), so we can as well use the information gathered above to log the user out.

Therefore, we make the "login" page (`192.168.0.1:8002`) behave differently if requested as `192.168.0.1:8002/logout`.  In that case, the IP/MAC and username info is gathered (see above) and the respective session deleted from the database.  All that is missing now is a 'page' or 'path' to request that /logout-URL.

As the default captive portal logout action requires the session id (and passes it to the function `captiveportal_disconnect_client` in `/etc/inc/captiveportal.inc`), we will also use that function instead of taking care of RADIUS-server notification, firewall-rule-modification etc. ourselves.

To make the code paths and changes read easy, in this example I will **query the database again** to access the sessionId and call the internal `captiveportal_disconnect_client` function. I extracted some code into functions to further improve readability.

{% highlight php %}
<html>
  <body>
    <h1>Captive Portal</h1>
    <?php
       require_once("captiveportal.inc");

       // Get IP/MAC from request and arp.
       $client_ip  = $_SERVER['REMOTE_ADDR'];
       $client_mac = pfSense_ip_to_mac($client_ip)['macaddr'];

       function captiveportal_get_logged_in_username($ip, $mac) {
         $username = NULL;
         // Get username from CP db.
         $db = captiveportal_opendb();
         if ($db) {
           $ip  = SQLite3::escapeString($ip);
           $mac = SQLite3::escapeString($mac);
           $username_query = "SELECT username FROM captiveportal " .
                             " WHERE ip='{$ip}' AND mac='{$mac}';";
           $response = $db->query($username_query);
           if ($response != FALSE) {
             $username = $response->fetchArray()[0];
           }
           else {
             // Not logged in
           }
           $db->close();
         }
         else {
           echo "No Database connection!";
         }
         return $username;
      }

      function captiveportal_get_sessionid($ip, $mac) {
         $sessionid = NULL;
         // Get sessionid from CP db.
         $db = captiveportal_opendb();
         if ($db) {
           $ip  = SQLite3::escapeString($ip);
           $mac = SQLite3::escapeString($mac);
           $sessionid_query = "SELECT sessionid FROM captiveportal " .
                              " WHERE ip='{$ip}' AND mac='{$mac}';";
           $response = $db->query($sessionid_query);
           if ($response != FALSE) {
             $sessionid = $response->fetchArray()[0];
           }
           else {
             // Not logged in
           }
           $db->close();
         }
         else {
           echo "No Database connection!";
         }
         return $sessionid;
      }

      $username  = captiveportal_get_logged_in_username($client_ip, $client_mac);
      $sessionid = captiveportal_get_sessionid($client_ip, $client_mac);
    ?>
    <?php
      if ($sessionid && $_SERVER['REQUEST_URI'] == '/logout') {
        captiveportal_disconnect_client($sessionid);
    ?>
      Logged out.
    <?php
      }
      elseif ($username) {
    ?>
      You should be able to access the internet and are logged in as
    <?php
        echo $username;
    ?>
      <br/>
      <a href="/logout">Log me out.</a>
    <?php
      }
      else {
        // Not logged in
    ?>
      <h2>By Username/Pass</h2>
      <form method="post" action="#PORTAL_ACTION#">
        <input name="auth_user"  type="text"></input>
        <input name="auth_pass"  type="password"></input>
        <input name="redirurl"   type="hidden" value="#PORTAL_REDIRURL#"></input>
        <input name="accept"     type="submit" value="Login"></input>
      </form>
  
      <h2>By Voucher</h2>
      <form method="post" action="#PORTAL_ACTION#">
        <input name="auth_voucher" type="text"></input>
        <input name="redirurl"     type="hidden" value="#PORTAL_REDIRURL#">
        </input>
        <input name="accept"       type="submit" value="Einlösen"></input>
      </form>
    <?php
      // Not logged in
      }
    ?>
  </body>
</html>
{% endhighlight %}


### More things to do

If you use these templates, make sure to also include them for the error-page (and/or success-page if you use one).

Refine all those ideas.

### Use at own risk
The approach outlined here has at least following security issues:

* login via http is easily spoofable
* giving the logout ability makes it possible for attackers to logout any given client
* the resolution of ip to mac is valid only in local networks and everything about it can be faked
* you might run into issues if concurrent logins are enabled on your captive portal
* no SQL sanitization happens in the first code example. I am not sure whether you could fake your MAC or IP adress in a way to cause harm (probably you can).
* The code ignores malconditions and possible return values here and there.

*Think twice before throwing this in production!*

## Other approaches

Use the logout page, it is shown after successfull login, trunk seems to prepare this when the default page is visited (probably in pfSense 2.3).

---

#### Having a better idea?

Awesome!  Get in contact with me!
