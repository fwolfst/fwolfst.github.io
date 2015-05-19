---
layout: post
title:  "Using Microsoft Edge IE VMs with kvm/libvirt"
date:   2015-05-18 15:47:25
categories: docker dokku dokku-alt virtualization
---

> Or how to use virt-convert on a practical example.

Ever wanted to check how Microsoft Internet Explorer renders stuff but no license at hand?

Welcome [http://dev.modern.ie][dev-modern-ie], where you can *download time-bombed VMs*.  Pretty cool service from Microsoft.

Now, these machines come as `.ova` files, suitable to be used with Oracles VirtualBox.  Prefering kvm and virsh?  Thought so.  See how to get it done.

### Aim

* Use Microsoft Edge VM with kvm/virsh/virt-manager,

#### Requirements

* Ubuntu 14.04 system
* several kvm, virsh, virt-manager, libvirtd etc. packages installed and respective permissions set up.

#### Download VM

Head over to [http://dev.modern.ie/tools/vms][dev-modern-ie] , and download your image.

#### Unzip and untar the thing

{% highlight bash %}
# Your filenames may vary.
unzip IE10\ -\ Win7.zip
tar xvf IE10\ -\ Win7.ova
{% endhighlight %}

#### Convert to qcow2

{% highlight bash %}
virt-convert IE10\ -\ Win7.ovf  --destination . --disk-format qcow2
{% endhighlight %}

This starts the viewer directly and you are ready to enjoy your IE experience (remember: you probably get paid to do so).

Note that you can even install an Excel viewer, although I am not sure whether thats fine with the EULA (like powering Windows XP with more than two cpu cores).

---

#### Having a better idea?

Awesome!  Get in contact with me!

[dev-modern-ie]:  http://dev.modern.ie/tools/vms
