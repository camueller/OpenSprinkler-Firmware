# Bewässerungsteuerung mit OpenSprinkler

Dieses Repo ist ein Fork von https://github.com/OpenSprinkler/OpenSprinkler-Firmware zum Zweck, die Software um eine **Dokumentation meiner Installation** zu erweitern. Dadurch will ich in der Lage sein, bei Bedarf die Hardware und Software meiner Steuerung erneut zu bauen. Gleichzeitig will ich es durch diese Dokumentation anderen Interessenten erleichtern, **OpenSprinkler für die Steuerung ihrer Bewässerung** einzusetzen. Beim Aufbau der Steuerung für meine Bewässerung hat sich gezeigt, dass die relevanten Informationen sehr verstreut zu finden sind.

Der **Zweck einer Bewässerungssteuerung** besteht darin, Magnetventile zeigesteuert zu öffnen, um die mit diesem Rohr verbundenen Regner und Sprüher mit Wasser zu versorgen. Dazu kann man einen Bewässerungscomputer kaufen oder eine Software wie **OpenSprinkler** auf einem **Raspberry Pi** verwenden. Letzteres hat den Vorteil, dass man Hardware und Software leicht erweitern oder tauschen kann, falls irgendwelche Anwendungsfälle das erforderlich machen. Da ich mit dem [Smart Appliance Enabler](https://github.com/camueller/SmartApplianceEnabler) selbst eine Software für Raspberry Pi entwickelt habe, war für mich die Entscheidung klar.

## Hardware
In meiner Bewässerungsanlage kommen **Magnetventile** [Rainbird 100-HV](https://www.rainbird.de/produkte/ventile/ventile-der-typenreihe-hv) mit 24V Wechselspannung zum Einsatz, die es so aber auch von anderen Herstellern gibt (z.B. Hunter).

### Raspberry Pi
Der Raspberry Pi ist mit seinen digitalen I/O-Ports perfekt zur Steuerung der Magnetventile einer Bewässerungsanlage geeignet. Weil dafür und die Web-Oberfläche von *OpenSprinkler* nicht viel Rechenleistung erforderlich ist, genügt ein Standard-Raspberry Pi 2/3/4, wobei die Grösse des Arbeitsspeichers nicht relevant sein sollte.

Die Netzwerkanbindung erfolgt bei mir über WLAN, weshalb der Ethernet-Anschluss des Raspberry Pi nicht verwendet wird. 

Ich habe den Raspberry Pi auf einen DIN-Schienenhalter geschraubt mit Hilfe dessen er im auf der DIN-Schiene des Verteilerkasten fixiert ist, aber auch leicht entnommen werden kann um beispielsweise die SD-Karte des Raspberry Pi zu wechseln.

![Raspberry Pi](pics/raspberrypi.png)

Für die I/O-Ports zur Steuerung der Magnetventile dürfen nur die Pins verwendet werden, die nicht von OpenSprinkler für einen speziellen Zweck vorgesehen sind.

![OpenSprinkler GPIO pin use](pics/OpenSprinklerGpioPinUse.jpg)

Für die Nummerierung der Pin existieren leider unterschiedliche Nummerierungen in Abhängigkeit von der verwendeten Software.
Mit folgendem Befehl kann man sich die auf dem jeweiligen Raspberry Pi Model vorhanden Pins ansehen mit Namen, Bedeutung und Nummern-Zuordnung:

```console
pi@sprinkler:~ $ gpio readall
 +-----+-----+---------+------+---+---Pi 4B--+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 |     |     |    3.3v |      |   |  1 || 2  |   |      | 5v      |     |     |
 |   2 |   8 |   SDA.1 |   IN | 1 |  3 || 4  |   |      | 5v      |     |     |
 |   3 |   9 |   SCL.1 |   IN | 1 |  5 || 6  |   |      | 0v      |     |     |
 |   4 |   7 | GPIO. 7 |  OUT | 1 |  7 || 8  | 1 | IN   | TxD     | 15  | 14  |
 |     |     |      0v |      |   |  9 || 10 | 0 | OUT  | RxD     | 16  | 15  |
 |  17 |   0 | GPIO. 0 |  OUT | 0 | 11 || 12 | 0 | IN   | GPIO. 1 | 1   | 18  |
 |  27 |   2 | GPIO. 2 |  OUT | 0 | 13 || 14 |   |      | 0v      |     |     |
 |  22 |   3 | GPIO. 3 |  OUT | 1 | 15 || 16 | 0 | IN   | GPIO. 4 | 4   | 23  |
 |     |     |    3.3v |      |   | 17 || 18 | 0 | IN   | GPIO. 5 | 5   | 24  |
 |  10 |  12 |    MOSI |   IN | 0 | 19 || 20 |   |      | 0v      |     |     |
 |   9 |  13 |    MISO |   IN | 0 | 21 || 22 | 0 | IN   | GPIO. 6 | 6   | 25  |
 |  11 |  14 |    SCLK |   IN | 0 | 23 || 24 | 1 | IN   | CE0     | 10  | 8   |
 |     |     |      0v |      |   | 25 || 26 | 1 | IN   | CE1     | 11  | 7   |
 |   0 |  30 |   SDA.0 |   IN | 1 | 27 || 28 | 1 | IN   | SCL.0   | 31  | 1   |
 |   5 |  21 | GPIO.21 |  OUT | 1 | 29 || 30 |   |      | 0v      |     |     |
 |   6 |  22 | GPIO.22 |   IN | 1 | 31 || 32 | 0 | IN   | GPIO.26 | 26  | 12  |
 |  13 |  23 | GPIO.23 |   IN | 1 | 33 || 34 |   |      | 0v      |     |     |
 |  19 |  24 | GPIO.24 |  OUT | 1 | 35 || 36 | 1 | OUT  | GPIO.27 | 27  | 16  |
 |  26 |  25 | GPIO.25 |   IN | 1 | 37 || 38 | 1 | OUT  | GPIO.28 | 28  | 20  |
 |     |     |      0v |      |   | 39 || 40 | 1 | OUT  | GPIO.29 | 29  | 21  |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+---Pi 4B--+---+------+---------+-----+-----+
```
Beispiel: der Regensensor wird an Pin 8 angeschlossen, das:
- aktuell HIGH ist ("V"alue ist 1)
- als "IN"put konfiguriert ist
- den Namen "TxD" hat
- mit der Bibliothek WiringPi ("wPi") als GPIO 15 angesprochen wird
- die Broadcom-Anschlussnummer (BCM) 14 hat

Wenn bei einem `gpio`-Befehl eine Pin-Nummer angegeben werden muss, ist das standardmässig die WiringPi-Nummer:
```console
$ gpio read 15
1
```

### Netzteil für Raspberry Pi
Für den Betrieb des Raspberry Pi wird ein 5V-Netzteil benötigt, das diesen dauerhaft mit Strom versorgt. Dabei muss sichergestellt sein, dass das Netzteil ausreichend Leistung für das jeweilige Raspberry Pi-Modell dauerhaft bereitstellt.

Weil im Verteilerkasten kein Platz für eine Steckdose ist, habe ich die Stecker-Stifte des Netzteils mittels Lüsterklemmen mit dem Stromnetz verbunden.

Mittels Korkens ist das Netzteil in der Ecke des Verteilerkastens fixiert.

![Raspberry Pi Netzteil](pics/raspberrypi_netzteil.png)

### Netzteil für Magnetventile
Zum Betrieb der Magenetventile wird ein Netzteil mit 24V Wechselspannung benötigt.

Weil im Verteilerkasten kein Platz für eine Steckdose ist, habe ich die Stecker-Stifte des Netzteils mittels Lüsterklemmen mit dem Stromnetz verbunden.

Mittels Korkens ist das Netzteil in der Ecke des Verteilerkastens fixiert.

![Magnetventil Netzteil](pics/magnetventil_netzteil.png)

### Relais-Board
Die I/O-Ports des Raspberry Pi mit 5V-Pegel dienen nur der Steuerung von Relais, welche den 24V-AC-Stromkreis für die Magnetventile schalten. Für jedes Ventil benötigt man ein Relais. Kompakt und preisgünstig geht mit einem Relais-Board. Für meine 8 Magnetventile habe ich dementsprechend ein 8-Kanal-Relais-Board verwendet.

![Relais Board](pics/relais_board.png)

### Raspberry Pi I/O-Port-Kabel
Zum Anschluss des Relais-Boards an die I/O-Ports des Raspberry Pi wird ein Flachkabel mit Pfostenstecker benötigt. Wer noch alte IDE-Flachkabel herumliegen hat, kann diese verwenden - so habe ich es auch bisher immer gemacht.

![GPIO Kabel](pics/gpio_kabel.png)

### Verteilerkasten
Der Raspberry Pi mit Netzteilen und Releais-Board müssen in der Nähe der Magnetventile untergebracht werden. Ich habe mich dazu entschieden, diese Elektronik in einem Verteilerkasten unterzubringen, der wiederum neben den Magnetventilen in der Ventilbox Platz finden muss. Also darf er nicht zu gross sein, muss aber Platz für alle Bauteile bieten. Außerdem sollte er vor der Luftfeuchtigkeit und Wasser schützen. Ursprünglich war ich deswegen auf der Suche nach einem Verteilerkasten mit Schutzart IP65, konnte allerdings keinen finden mit genug Platz aber nicht zu gross für die Ventilbox. Letztlich habe ich mich für einen Aufputz-Kleinverteiler für 8 Module auf Hutschiene mit Schutzart IP40 entschieden, den bei dem ich die vorhanden Schlitze mit Heisskleber verschlossen habe und für die Kabel Löcher gebohrt habe zur Aufnahme von Kabeldurchführungen mit Quetschdichtung. Rund um die Fronttüre habe ich Teile eines alten Fahrradschlauchs als Flachdichtung platziert. Insgesamt hoffe ich, dass diese Massnahmen ausreichen, um Luftfeuchtigkeit draussen zu halten.

![Verteiler](pics/verteiler.png)

### Regensensor
Wenn es regnet, soll die Bewässerung nicht laufen. Ich hatte zunächst an einen Sensor zur Messung der Bodenfeuchte gedacht, aber angesichts der Probleme hinsichtlich Haltbarkeit und Aussagekraft wieder verworfen. Stattdessen verwende ich den Regensensor [Rainbird RSD-BEx](https://www.rainbird.de/produkte/rsd-bex), der mittels Korkscheiben das Verhalten des Bodens (Aufsaugen von Wasser und verzögertes Trocknen) simuliert.

![Regensensor](pics/regensensor.jpg)

Eine Ader des Regensensor-Kabels wird mit GND verbunden. Die andere Ader wird mit GPIO 14 und mit einem 1-kOhm-Widerstand verbunden. Die andere Seite des Widerstand muss mit VCC verbunden sein (Pull-Up).

![Regensensor Anschluss](pics/regensensor_anschluss.png)


## Software
### Installation des Betriebssystems
#### Raspberry Pi OS
Wie jeder Computer benötigt der Raspberry Pi ein Betriebssystem, das auf einem Speichermedium installiert ist. Letzteres ist im konkreten Fall eine SD-Karte, auf welcher das **Raspberrys Pi OS** installiert sein muss. Am einfachsten geht das mit dem [Raspberry Pi OS Imager](https://www.raspberrypi.org/software).

Nachdem sich das Image auf der SD-Karte befindet, muss der **Zugriff per SSH noch freigeschaltet** werden, indem auf der SD-Karte eine leere Datei mit dem Namen `ssh` angelegt wird. Unter Linux geht das wie folgt:
```console
$ sudo mount /dev/mmcblk0p1 /mnt
$ sudo touch /mnt/ssh
$ sudo umount /mnt
```

Jetzt kann die SD-Karte in den Raspberry Pi gesteckt werden. Sobald dieser auch mit dem Netzteil verbunden wird, sollt er das Raspberry Pi OS von der SD-Karte booten.

Wenn der Boot-Vorgang abgeschlossen ist, muss man sich **via SSH mit dem Raspberry verbinden** für die weiteren Installations- und Konfigurationsschritte:
```console
ssh pi@raspberrypi
```

### Konfiguration mit `raspi-config`
Einige Parameter müssen mit Hilfe des Programms `raspi-config` eingestellt werden, das wie folgt gestartet wird:
```console
$ sudo raspi-config
```

#### WLAN aktivieren
Auswahl des Menüpunktes `Network Options -> Wireless LAN -> DE` zur Eingabe der SSID und des WLAN-Passworts.

#### Hostname setzen
Auswahl des Menüpunktes `Network Options -> Hostname` zur Eingabe der Hostnamen (z.B. "sprinkler").

#### Zeitzone setzen
Auswahl des Menüpunktes `Localisation Options -> Change Time Zone -> Europe -> Berlin` zum Setzen der Zeitzone.

### Software aktualisieren
Nachdem das Raspi OS auf die SD-Karte geschrieben wurde, muss dieses erstmal aktualisiert werden:
```console
$ sudo apt update
$ sudo apt upgrade
```
Danach sollte ein Reboot durchgeführt werden, damit das aktualisierte Software auch aktiv ist:
```console
$ sudo reboot
```

### Installation von OpenSprinkler Firmware
#### Git installieren
Zunächst muss `git` installiert werden, falls noch nicht geschehen:
```console
$ sudo apt install git
```

#### OpenSprinkler Firmware installieren
Mit dem folgenden Befehl wird eine lokale Kopie des *OpenSprinkler Firmware* Git-Repositories erstellt:
```console
cd ~
git clone https://github.com/OpenSprinkler/OpenSprinkler-Firmware.git
```

Anschliessend muss *OpenSprinkler Firmware* nocht gebaut werden:
```console
cd OpenSprinkler-Firmware
sudo ./build.sh ospi
```

### Konfiguration von OpenSprinkler







Zum Setzen der Zeitzone in OpenSprinkler muss erst der Standort gelöscht werden!
siehe: https://github.com/OpenSprinkler/OpenSprinkler-Firmware/issues/84


#### Regensensor
Mit dem installierte Pull-Up-Widerstandt verhält sich der entsprechende Pin wie folgt:

bei Regen: 
```console
pi@sprinkler:~ $ gpio read 15
1
```

kein Regen:
```console
pi@sprinkler:~ $ gpio read 15
0
```
