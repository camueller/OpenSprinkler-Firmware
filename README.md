# Bewässerungsteuerung mit OpenSprinkler

Dieses Repo ist ein Fork von https://github.com/OpenSprinkler/OpenSprinkler-Firmware zum Zweck, die Software um eine **Dokumentation meiner Installation** zu erweitern. Dadurch will ich in der Lage sein, bei Bedarf die Hardware und Software meiner Steuerung erneut zu bauen. Gleichzeitig will ich es durch diese Dokumentation anderen Interessenten erleichtern, **OpenSprinkler für die Steuerung ihrer Bewässerung** einzusetzen. Beim Aufbau der Steuerung für meine Bewässerung hat sich gezeigt, dass die relevanten Informationen sehr verstreut zu finden sind.

Der **Zweck einer Bewässerungssteuerung** besteht darin, Magnetventile zeigesteuert zu öffnen, um die mit diesem Rohr verbundenen Regner und Sprüher mit Wasser zu versorgen. Dazu kann man einen Bewässerungscomputer kaufen oder eine Software wie **OpenSprinkler** auf einem **Raspberry Pi** verwenden. Letzteres hat den Vorteil, dass man Hardware und Software leicht erweitern oder tauschen kann, falls irgendwelche Anwendungsfälle das erforderlich machen. Da ich mit dem [Smart Appliance Enabler](https://github.com/camueller/SmartApplianceEnabler) selbst eine Software für Raspberry Pi entwickelt habe, war für mich die Entscheidung klar.

## Hardware
In meiner Bewässerungsanlage kommen **Magnetventile** [100-HV von Rainbird](https://www.rainbird.de/produkte/ventile/ventile-der-typenreihe-hv) mit 24V Wechselspannung zum Einsatz, die es so aber auch von anderen Herstellern gibt (z.B. Hunter).

TODO:
- Raspberry Pi
- Netzteil für Raspberry Pi
- Netzteil für Magnetventile
- Floppy-Kabel
- 8-fach Relais-Board
- Verteilerkasten

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

Jetzt kann die SD-Karte in den Raspberry Pi gesteckt werden. Soblad er auch mit dem Netzteil verbunden wird, sollt er das Raspberry Pi OS von der SD-Karten booten.

Wenn der Boot-Vorgang abgeschlossen ist, muss man sich **via SSH mit dem Raspberry verbinden** für die weiteren Installations- und Konfigurationsschritte:
```console
ssh pi@raspberrypi
```

#### WLAN aktivieren

#### Hostname setzen

#### Zeitzone setzen

#### Software aktualisieren



### Installation von OpenSprinkler

#### Git installieren

#### OpenSprinkler Firmware installieren

### Konfiguration von OpenSprinkler



Zum Setzen der Zeitzone in OpenSprinkler muss erst der Standort gelöscht werden!
siehe: https://github.com/OpenSprinkler/OpenSprinkler-Firmware/issues/84
