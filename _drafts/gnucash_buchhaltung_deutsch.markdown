---
layout: post
title:  "Buchhaltung mit GnuCash (deutsch)"
date:   2015-05-27 17:01:25
categories: accounting business germany
---

> gnucash, make me new cash.

Jedem sein/ihr Mantra: Buchhaltung wird eines Tages Spaß machen.  [GnuCash][gnucash] sieht wie ein wirklich mächtiges Werkzeug aus, und das mitgelieferte Handbuch/Tutorial ist zwar nicht schlecht, für einen Laien mit echtem Anwendungsfall fehlt aber eine ganze Menge.  

### Voraussetzungen

* Ubuntu 14.04
* gnucash aus dem offiziellen Ubuntu repository
* Einnahmen/Ausgaben
* optional ein Konto z.B. bei der [GLS-Bank][glsbank]

### Ziele

Eine GnuCash Datei für ein kleines Unternehmen anlegen und ein paar Anleitungen zur Hand haben wie man damit umgeht, wie z.B. Rechnungen erstellt werden können oder der Überblick hergestellt wird.

### A word of precaution

Ich schreibe das hier auch, weil zur Zeit (2015) kaum ein brauchbares Tutorial vorliegt und ich *absolut keine Ahnung habe*, wie richtige Buchhaltung geht.  Ich freue mich über Feedback (gerne auch Warnungen wie "mach' das auf gar keinen Fall!") und hoffe, dass es Menschen gibt, denen ich hiermit helfe.  Also, bitte auf eigene Gefahr nutzen!

### Die Firma

Meine Firma, econya, ist ein kleines Gewerbe.  Ich habe mich auf freiwilliger Basis entschieden, *Umsatzsteuer auszuweisen* und möchte nach *Ist-Besteuerung* vorgehen (meine Angaben sollen sich also immer auf den tatsächlichen Kontostand beziehen und nicht darauf, welche Zahlungen ich noch erwwarte, weil bereits Rechnungen vorliegen, mit herrlichen Ausnahmen zum Jahreswechsel).  Leider werde ich nicht bilanzieren müssen (das müsste ich bei hohem Umsatz), sondern nur eine Einnahmen-Überschuss-Rechnung am Ende des Jahres vorlegen.

### Schritt 0: Spezialisten-Sprache

Um gnucash mit Voreinstellungen für Szenarien in Deutschland zu starten benutze ich folgendes "Skript":

{% highlight bash %}
#!/bin/bash
LANGUAGE=de_DE.utf8 LANG=de_DE.utf8 /usr/bin/gnucash
{% endhighlight %}

### Schritt 1: GnuCash starten und Geschäftsdaten eingeben, Kontorahmen wählen

Über obiges Skript GnuCash starten.  Wenn schon herumgespielt, "Neue Datei wählen", dann dem Assistenten folgen.
![Neues Buch: Geschäftsdaten](/assets/gnucash_1_geschaeftsdaten.png)
Wenn man später nicht drüber stolpern oder lisp lernen will, bringt man beim Eingeben der Geschäftsdaten irgendwo die Steuernummer unter (z.B. als Firmennummer oder unter der Adresse).  Die Nummernzähler lasse ich unberührt.
Ich benutze den Kontenrahmen SKR03.  Frag mich nicht, ich frag mich auch - das macht man so.
![Neues Buch: Kontenrahmen](/assets/gnucash_1_kontenrahmen.png)
Nun sucht man sich noch einen Speicherort für die xml-Datei aus.  *Achtung*, per default werden an die gleiche Stelle auch die u.U. vielen vielen Sicherungskopien gelegt, also gönne Dir ein eigenes Verzeichnis dafür.

Und voila, wenn du GnuCash glaubst, stehst du nun bei genau 0.00€!  Die eben vorgenommenen Einstellungen lassen sich übrigends über `Datei->Einstellungen` einsehen und ändern.  Buchhaltung wird eines Tages Spaß machen.


### Schritt 2: Steuer-Tabellen anlegen

Steuertabellen findet man unter Geschäft->Steuertabellen, sie erlauben es später bei Rechnungen automatisch einen bestimmten Betrag in ein anderes Konto (buchhalterisches Konto) zu buchen.

Für die bezahlte Vorsteuer (jemand schreibt mir eine Rechnung, auf der MwSt. [== Umsatzsteuer] ausgewiesen ist, die ich mitbezahle, mir später aber zurückholen kann) lege ich die Steuertabelle "USt 19" an (Konto 1776).
![Neue Steuertabelle: Vorsteuern](/assets/gnucash_2_vorsteuer.png)
Für die eingenommene Umsatzsteuer (ich schreibe eine Rechnung, auf der MwSt. [== Umsatzsteuer] ausgewiesen ist, die ich mit-einnehme, später aber an das Finanzamt abgeben muss) lege ich die Steuertabelle "Vorsteuer USt 19" (Konto 1576) an.
![Neue Steuertabelle: Umsatzsteuer](/assets/gnucash_2_umsatzsteuer.png)

Irgendwie muss ich (beziehungsweise besser ein uns noch unbekanntes Programm) diese Posten später monatsweise gegeneinander aufrechnen und die Differenz an das Finanzamt zahlen bzw. zurückbekommen.

Gegebenenfalls braucht man auch noch Steuertabellen für die 7 und 0%, die nach gleichem Schema anlegen.

### Schritt 3: Zahlungsbedingungen anlegen

Auch wenn das mit den Erinnerungen an fällige Zahlungen irgendwie nicht klappt: Ich lege Zahlungsbedingungen (unter Geschäft->Zahlungsbedingungen) an.
Und zwar eine für "Zwei Wochen" und eine für "Drei Wochen".  Achtung, die "Beschreibung" ist später u.U. auf der Rechnung zu sehen, also da nichts rein wie "für Leute bei denen ich schlechte Erfahrungen gemacht habe nur 10 Tage".
![Neue Zahlungsbedingung: 2 Wochen](/assets/gnucash_3_zahlungsbedingung.png)

### Schritt 4: Kunden und Lieferanten anlegen

Unter Geschäft->Kunden->Neuer Kunde bzw. analog zu Lieferanten lassen sich Geschäftspartner (Kunden zahlen mir, Lieferanten bezahle ich) anlegen.

Die Anlegemaske ist eigentlich selbsterklärend, eine mindestens einzeilige Adressangabe zwingend.
Dabei auf den Voreintrag bei "Inkl. Steuern", Steuertabelle, Zahlungsbedingungen etc. achten, diese werden später bei neuen Rechnungen übernommen (können dort aber geändert werden).  Wenn du auf die Preise, die du später angiebst also noch Steuern drauflegen willst, lass den Haken bei "Inkl. Steuern" weg.  Bei den Kunden wird entsprechend Steuertabelle "USt 19" (s.o.) gewählt, dadurch wird bei ausgehenden Rechnungen (Rechnungen, die ich stelle) ein Betrag auf die Umsatzsteuer gebucht.  Nochmal: Die Umsatzsteuer erhalte ich zwar, aber sie gehört erstmal nicht mir, sondern dem Finanzamt/Staat.
![Neuer Kunde](/assets/gnucash_4_kunde.png)

Mein erster Lieferant ist die manitu GmbH, wo ich meinen Server miete.
Bei Lieferanten wähle ich die Steuertabelle "Vorsteuer 19 USt.", ich zahle zwar im Preis die Steuern mit, bekomme sie aber hoffentlich vom Finanzamt/Staat zurück.

### Schritt 5: Arbeiten

Jezt ist es Zeit ein paar verrückte Dinge mit Servern, Ruby, docker, bash-Skripten, python code usw. anzustellen und dabei die Stoppuhr mitlaufen zu lassen (hoffentlich kommt mir irgendwann ein brauchbares time- and project-tracking-cli-Tool mit gnucash-Anbindung über den Weg ...).

### Schritt 6: Rechnung schreiben

Fast forward, ich habe einige Stunden gearbeitet und stelle diese nun dem Kunden in Rechnung.  Berichtsoptionen etc.

Über Geschäft->Kundenübersicht, dann rechte Maustaste auf den entsprechenden Kunden (Freundeskreis Ökodorf) neue Rechnung, 
oder eben Lieferantenrechnung.
![Neue Rechnung](/assets/gnucash_5_rechnung.png)

Die Standard-Rechnungen sind nicht wirklich brauchbar.  Entweder Lisp lernen oder damit leben, am besten noch mit Steuer, da Logo wählen.
Berichtsoptionen speichern.  Beim nächsten Mal kann man eine Rechnung duplizieren.  In den Berichtsoptionen gibt man nun noch die Übersetzung für einige Begriffe an (apropos: Bei Übersetzung für "An:" ändert man "To:" zu "An"...)

### Schritt 7: Geld ausgeben

manitu hat mir eine Rechnung geschrieben, ich trage sie als Lieferantenrechnung ein.

### Schritt 8: Lieferanten-Rechnungen anlegen

s.o.

### Schritt 9: Online-Banking einrichten (Beispiel GLS Bank)

Cool, klappt.

### Schritt 10: Zahlungseingang verarbeiten

### Schritt 11: Zahlungsausgang verarbeiten

### Schritte 12++: Analysieren und verstehen

Hier endet mein kleines Bilderbuch, auch wenn andere wichtige Arbeiten nun anfangen, nämlich monatlich und jährlich die Zahlen auszuwerten.

---

So einfach ist das.
Wenn du es besser weißt oder noch andere Ideen hast, BITTE BITTE kontaktiere mich, Danke.

[glsbank]: https://gls.de
[gnucash]: http://gnucash.org/
