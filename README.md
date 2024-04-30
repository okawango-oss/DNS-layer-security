# DNS-layer security auf dem RaspberryPi

Der Vorteil ist, dass Du die Möglichkeit hast, Bedrohungen zu blockieren, bevor sie Dich erreichen.

## Sind DNS-Server sicher?
* DNS-Tunneling wird häufig verwendet, um mittels DNS-Anfragen und -Antworten verschlüsselte Nutzdaten zu übermitteln, Daten aus kompromittierten Netzwerken zu exfiltrieren und Command-and-Control-Angriffe durchzuführen.
* DNS-Beaconing wird häufig verwendet, um die Kommunikation mit einem Command-and-Control-Server ausschließlich über DNS herzustellen, was in einem Netzwerk fast immer erlaubt ist.

Diese Taktiken, Techniken und Verfahren (TTPs) spielen bei modernen Cyberangriffen oft eine wichtige Rolle und werden durch cname-cloaking begünstigt. Bei vielen bekannten Ransomware-Angriffen wurde DNS-Beaconing eingesetzt, der Supply-Chain-Angriff SUNBURST nutzte DNS-Tunneling während der Post-Exploitation, und die APT-Gruppe OilRig verwendet häufig DNS-Tunneling zur Datenexfiltration. Was kann man also tun, um DNS-Aktivitäten im heimischen Netzwerk / KMU abzusichern?

## Wie die Sicherheit auf DNS-Ebene hilft, Cyberangriffe zu stoppen:
Da alle Internet-Aktivitäten durch DNS ermöglicht werden, kann die Überwachung von DNS-Anfragen - sowie der darauffolgenden IP-Verbindungen - einen großen Beitrag zur Sicherung Deines Netzwerks leisten. Die Einrichtung von Sicherheitsprotokollen zur Erkennung anomaler DNS-Aktivitäten kann eine bessere Genauigkeit und Erkennung bösartiger Aktivitäten und kompromittierter Systeme ermöglichen, die Sicherheitstransparenz verbessern und den Netzwerkschutz erhöhen. Hierzu bietet suricata die detection von Netzwerkprotokoll Anomalien. Im IPS mode werden Attacken gleich gestoppt. DNS Filtering mittels Pi-hole Blocklisten und DNSSEC Validierung verhindert die Kontaktierung zu kompromittierten Seiten und Malvertising oder zur Malware und Botnet Infrastruktur. Nichts stoppt Angriffe früher als die Sicherheit der DNS-Schicht. Schließlich ist DNS der erste Schritt bei der Herstellung einer Verbindung im Internet. Wenn eine gefährliche Verbindung auf der DNS-Ebene blockiert wird, endet der Angriff dort.

￼![image](https://github.com/okawango-oss/DNS-layer-security/assets/125760839/ccc6fcb2-f29f-40ca-89b9-a98a9a91e85a)


Abbildung 1: Die blauen Schilder zeigen, wo die DNS-Sicherheitsschicht die Kommunikation von Angreifern stoppt.

In der obigen Abbildung sieht man, wie die DNS-Sicherheitsebene feststellt, wo bösartige Domänen und andere gefährliche Internet-Infrastrukturen untergebracht sind. Sichere DNS-Server blockieren dann Anfragen, die von diesen Staging-Sites über einen beliebigen Port oder ein beliebiges Protokoll kommen, und verhindern so sowohl Infiltrations- als auch Exfiltrationsversuche. Die Sicherheit auf der DNS-Schicht stoppt Malware früher und verhindert Rückrufe an Angreifer, wenn sich infizierte Rechner mit Ihrem Netz verbinden.

## Pi-hole


Das Image pihole-dhcp.img basiert auf dem Betriebssystem dietpi bzw. debian bookworm. Mittels dietpi-launcher wurde Pi-hole und der Resolver Unbound aus den Paketquellen installiert. Die Netzwerkkonfiguration ist auf dhcp voreingestellt. 84 Blocklisten sind eingepflegt worden und filtern Werbung + malicious domains. Die wichtige Option DNSSEC validiert die signierten domains und bietet dadurch einen Grundschutz. Angreifer können durch sogenanntes cname cloaking (DNS alias) versuchen dnssec zu umgehen. Deshalb wurden zusätzliche Filter gewählt, um bekannte malicious domains zu filtern.
Für systemd-resolved ist DNSSEC aktiviert worden. Da DNSSEC zeitkritisch ist, hat sich der ntp server chrony bewährt. Abschließend wurde Suricata als Applikation Firewall (NGFW) im IPS-Modus mit der stateful Firewall ufw ergänzt.

### Links:
  - www.dietpi.com
  - www.pi-hole.net
  - https://www.nlnetlabs.nl/projects/unbound/security-advisories/
  - www.suricata.io
  - https://techblog.nexxwave.eu/public-dns-malware-filters-tested-in-2024/
  - https://avm.de/service/wissensdatenbank/dok/FRITZ-Box-6690-Cable/3757_Lokales-DNSSEC-uber-FRITZ-Box-nicht-nutzbar/
  - https://www.heise.de/hintergrund/Admin-Know-how-Das-Domain-Name-System-erklaert-9592007.html?nid=aY9ad4Us
  - https://www.kuketz-blog.de/tracking-wettruesten-das-cname-cloaking/
  - https://attack.mitre.org/techniques/T1071/004/

### Hardwarevoraussetzungen: 
  - Raspberry Pi 4 Model B Rev 1.5, 2GB RAM
  - SANDISK Extreme PRO® UHS-I, Micro-SDXC, 32 GB

### Download des Images pihole-dhcp.img ca. 3,5 GB
  - pihole-dhcp.img https://1drv.ms/f/s!AkAr4GI6DPy8bU7_Na-vQclJckY?e=4MwRjB onedrive location

  - sha256sum:
    
    30d31640e5b8b12ab5ca49bb9f0257d010022f993d8c0ed2938c175f248abb4c

### Empfehlung:
  - SD-Karte vorher prüfen. https://www.maketecheasier.com/check-sd-card-speed-capacity/#fake-flash-test
  -  [Router Setup](https://docs.pi-hole.net/routers/fritzbox-de/)
  - DNS Anfragen nur vom Pi-hole erlauben. https://docs.pi-hole.net/routers/fritzbox-de/

### Flashen der SD-Karte mit dem heruntergeladenen Image.
Zum Beispiel mit Balena Etcher:
  - macos https://github.com/balena-io/etcher/releases/download/v1.14.3/balenaEtcher-1.14.3.dmg
  - Linux https://github.com/balena-io/etcher#debian-and-ubuntu-based-package-repository-gnulinux-x86x64
  - Windows https://github.com/balena-io/etcher/releases/download/v1.14.3/balenaEtcher-Setup-1.14.3.exe

### Fritz.box

  - Im Menü Heimnetz/Netzwerk die Pi-hole statische ip-adress prüfen.
  - Im Menü Internet/Zugangsdaten/DNS-Server in der Zeile "Bevorzugter DNSv4-Server" die statische ip-adress des Pi-hole eintragen.
  - Prüfen, ob die Fritzbox DNSSEC unterstützt: https://avm.de/service/wissensdatenbank/dok/FRITZ-Box-6690-Cable/3757_Lokales-DNSSEC-uber-FRITZ-Box-nicht-nutzbar/ (betrifft alle Fritzboxen nicht nur die 6690)

### Remote Zugang per ssh für macOS, Linux oder Windows, nachdem der Raspi das erste Mal hochfährt. 
Hinweis: Das Dollarzeichen ($) bedeutet: als root den Befehl eingeben und mit Return abschließen, das
         Doppelkreuz (#) markiert Kommentare 

  Terminal/Putty:
  - $ssh root@ip-adress #ip-adress siehe Fritzbox
  - dietpi #das initiale Passwort
  - $dietpi-launcher #config tool https://dietpi.com/docs/dietpi_tools/
  - im Menüpunkt DietPi Config/Network options Adapters: DHCP auf STATIC ip-adress ändern
  - im Menüpunkt DietPi Config/Security options: das Passwort ändern und den host Namen, wenn man 2 Pi-holes betreiben möchte

### Passwörter für die Benutzer „dietpi“ und „omv“ ändern

- $ssh dietpi@pi-hole				    # login als Benutzer dietpi: Passwort lautet dietpi
- dietpi@pi-hole:~$ sudo su 		# eingeben und Return drücken
- dietpi@pi-hole's password: 		# das aktuelle Passwort eingeben: dietpi + Return drücken

    Kontrolle durch Statusangabe P (=OK)
- $passwd -aS | grep P		# passwd -aS | grep P eingeben
  -    root P 									# wurde schon per dietpi-launcher geändert, falls nicht bitte nachholen
  -    dietpi P 								# sollte jetzt geändert werden
  -    omv P 								    # sollte jetzt geändert werden

- root@pi-hole:~$passwd dietpi	# passwd dietpi eingeben
- New password: 						    # neues Passwort für den Benutzer dietpi eingeben + Return drücken
- Retype new password:			  	# neues Passwort wiederholen + Return drücken
- neues Passwort notieren

- root@pi-hole:~$passwd omv		# passwd omv eingeben
- New password: 						  # neues Passwort für den Benutzer omv eingeben+ Return drücken
- Retype new password:				# neues Passwort wiederholen + Return drücken
- neues Passwort notieren, ausloggen und fertig

### Pihole web login Passwort ändern

  Terminal/Putty:
  - $ssh root@ip-adress
  - $pihole -a -p # anschließend das neue Passwort eingeben

### Aufruf der Pihole Seite
  - http://ip-adress/admin
  - das neue Passwort eingeben 

### Funktionstest im Browser für DNSSEC
  
  - https://dnscheck.tools im Browser aufrufen #die unteren drei Blöcke sollten grün sein (DNSSEC), in der unteren Statusleiste -> links unten in der Ecke: sollte ein Wert zwischen 50 ms und 70 ms (Performance) angezeigt werden. In den pi-hole logs sieht man dann mehrere bogus Meldungen für dnscheck.tools.

### Funktionstest Blocklisten, DNSSEC und normales surfen
   Terminal/Putty:
  - $dig @localhost doubleclick.net #darf keine ip-adress anzeigen, stattdessen 0.0.0.0
  - $delv +dnssec +rtrace netdata.cloud # hier sollte "fully validated" erscheinen
  - $dig +dnssec go-unsigned-ipv4.dnscheck.tools
  - $dig +dnssec nosig.go-ipv4.dnscheck.tools # ANSWER: 0
  - $dig +dnssec badsig.go-ipv4.dnscheck.tools # ANSWER: 0
  - $dig +dnssec expiredsig.go-ipv4.dnscheck.tools # ANSWER: 0
  - $iptables -vnL
  - Optional: Pentesting mit dem dnssec-test.sh script, dauert mehrere Stunden. 

### Updates:
  - Es wird automatisch auf Updates geprüft, aber nicht automatisch das Update eingespielt (Empfehlung der Pihole Entwickler). Man erkennt ein verfügbares Update an der blinkenden Versionsangabe auf der admin Seite, ganz unten nach der Anmeldung auf http://ip-adress/admin 

  Terminal/Putty:
  - $pihole -up #eingeben, dann wird Pihole entsprechend aktualisiert
  - Die Blocklisten werden täglich und automatisch aktualisiert, ebenso security updates gemäß Debian standard
  - apt update && apt dist-upgrade && apt autoclean # Aktualisierung des Betriebsystems
  - suricata-update --no-test # aktualisiert die Firewall Regeln

### Troubleshooting
- Erscheint keine IP-Adresse für den Pi-hole in der Netzwerkübersicht der Fritz!Box, ist es notwendig Monitor und Tastatur am Pi anzuschließen und man meldet sich mit dem Benutzer root und dem Passwort dietpi an.
- In der erscheinenden Bildschirminformation des dietpi wird die aktuelle IP-Adresse unterhalb der farbigen Temperaturanzeige angezeigt. Diese muss mit dem Adressbereich der Fritz!Box übereinstimmen. Falls es nicht der Fall sein sollte, ruft man mit $dietpi-config das Konfigurationstool auf und wählt „Netzwerk Options: Adapters“ anschließend „Ethernet“ aus. Über „Change Mode“ kann man dann die Netzwerkeinstellungen korrigieren.
