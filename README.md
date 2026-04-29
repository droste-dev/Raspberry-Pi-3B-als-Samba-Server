# Raspberry Pi 3 B+ als Samba-Server

Ein praxisnahes Projekt für einen **schlanken, stabilen und alltagstauglichen Samba-Dateiserver im Heimnetz** auf Basis eines **Raspberry Pi 3 B+** mit **einer externen 2-TB-Festplatte**.

Der Fokus liegt bewusst auf einem **klaren und nachvollziehbaren Aufbau**:

- kein Docker
- kein Nextcloud
- kein OpenMediaVault
- kein Medienserver
- keine unnötigen Zusatzdienste

Stattdessen geht es um genau das, was viele im Alltag wirklich brauchen:

**eine zentrale Dateiablage im Heimnetz, die zuverlässig funktioniert und verständlich aufgebaut ist.**

---

## Projektziel

Dieses Projekt zeigt Schritt für Schritt, wie aus einem Raspberry Pi 3 B+ ein sauber aufgebauter Samba-Dateiserver wird, auf den Geräte im Heimnetz zugreifen können.

Am Ende steht ein System, das:

- per SSH erreichbar ist
- eine externe 2-TB-Festplatte sauber eingebunden hat
- den Datenträger automatisch mountet
- eine funktionierende Samba-Freigabe bereitstellt
- unter Windows, macOS und Linux nutzbar ist
- im Alltag kontrollierbar und wartbar bleibt

---

## Für wen ist dieses Projekt gedacht?

Dieses Repository richtet sich an alle, die:

- einen einfachen Heimnetz-Dateiserver selbst aufbauen möchten
- Samba auf dem Raspberry Pi verstehen und sauber einrichten wollen
- kein überladenes NAS-Projekt suchen
- eine praxisnahe und nachvollziehbare Lösung bevorzugen
- lokale Daten im eigenen Netzwerk statt in der Cloud verwalten möchten

---

## Warum nur Samba?

Dieses Projekt konzentriert sich bewusst auf **einen einzigen Dienst: Samba**.

Das ist kein Nachteil, sondern Absicht.

Denn gerade auf einem Raspberry Pi 3 B+ ist ein **schlankes und stabiles Setup** oft deutlich sinnvoller als ein überladenes Bastelprojekt mit vielen gleichzeitig laufenden Diensten.

Die Vorteile:

- weniger Fehlerquellen
- einfachere Konfiguration
- klarere Struktur
- bessere Nachvollziehbarkeit
- stabilerer Betrieb im Alltag

---

## Verwendete Basis

- **Raspberry Pi 3 B+**
- **Raspberry Pi OS Lite**
- **Samba**
- **1 externe 2-TB-Festplatte**
- Verwaltung per **SSH**
- Nutzung im **lokalen Heimnetz**

---

## Was in diesem Projekt behandelt wird

Im Projekt beziehungsweise Kurs geht es unter anderem um folgende Themen:

1. Raspberry Pi OS Lite vorbereiten
2. SSH-Zugriff einrichten und erste Grundbasis schaffen
3. Hostname, Updates und Netzwerkgrundlage sinnvoll vorbereiten
4. Externe Festplatte sicher erkennen
5. Alte Signaturen entfernen und Datenträger sauber vorbereiten
6. Partition anlegen und ext4-Dateisystem erstellen
7. UUID ermitteln und Mountpunkt anlegen
8. Festplatte testweise mounten
9. `/etc/fstab` korrekt einrichten
10. Neustart-Test und Mount-Verhalten prüfen
11. Samba installieren
12. `smb.conf` verstehen und sauber aufbauen
13. Netzwerkfreigabe einrichten
14. Samba-Benutzer und Passwort setzen
15. Rechte und Freigabe-Einstellungen sinnvoll konfigurieren
16. Zugriff unter Windows testen
17. Zugriff unter macOS und Linux testen
18. Typische Fehler systematisch prüfen
19. Wichtige Prüfwerkzeuge und kleine Wartungsroutine für den Alltag

---

## Bewusst nicht Teil dieses Projekts

Damit dieses Projekt klar und praxisnah bleibt, werden folgende Themen bewusst **nicht** behandelt:

- RAID
- Mehrplatten-Setups
- komplexe Backup-Automation
- Docker
- OpenMediaVault
- Fernzugriff aus dem Internet
- VPN
- zusätzliche Weboberflächen
- unnötige System-Spielereien

Dieses Projekt soll kein Universalserver werden, sondern eine **verständliche, reale und alltagstaugliche Samba-Lösung für das Heimnetz**.

---

## Typische Projektstruktur

Die zentrale Freigabe wird in diesem Projekt auf einen klaren Pfad aufgebaut:

```bash
/srv/storage/daten
```

Dieser Datenbereich wird über Samba als Freigabe bereitgestellt.

Ein verständlicher Freigabename ist dabei zum Beispiel:

```ini
[daten]
```

---

## Beispielhafte Werkzeuge im Alltag

Im laufenden Betrieb sind vor allem diese Befehle nützlich:

```bash
sudo systemctl status smbd --no-pager
testparm
mount | grep /srv/storage/daten
df -h
hostname -I
ls -ld /srv/storage/daten
```

Diese kleine Prüfkette hilft dabei, den Zustand des Servers schnell und nachvollziehbar einzuschätzen.

---

## GitHub-Repository

Dieses Repository begleitet das Projekt dauerhaft weiter.

Hier findest du:

- Unterlagen
- Projektmaterialien
- begleitende Dokumentation
- künftig auch aktualisierte Informationen rund um dieses Setup

Repository:  
**https://github.com/droste-dev/Raspberry-Pi-3B-als-Samba-Server**

---

## X / Updates

Ergänzende Hinweise und Neuerungen zu diesem Projekt werden außerdem über X veröffentlicht:

**@Strange99361830**

---

## Hinweis zum Video

Zum begleitenden Video gilt:

In einzelnen Videos können **KI-Elemente** zum Einsatz kommen. Das betrifft zum Beispiel den Avatar oder bestimmte Präsentationselemente.

Wichtig ist dabei:

**Der Avatar ist nicht echt – das Projekt und der gezeigte Systemaufbau jedoch sehr wohl.**

Dieses Repository dokumentiert ein **reales, praxisnah aufgebautes Samba-Projekt**.

---

## Warum dieses Projekt stark ist

Der große Vorteil dieses Projekts liegt nicht in unnötiger Komplexität, sondern in seiner Klarheit.

Du baust hier kein unübersichtliches Testsystem, sondern:

- einen verständlichen Raspberry-Pi-Server
- mit sauber eingebundener Festplatte
- mit nachvollziehbarer Freigabestruktur
- mit klarem Benutzerzugriff
- und mit echtem Alltagsnutzen

Genau das macht dieses Projekt stabil, lehrreich und praktisch nutzbar.

---

## Lizenz / Hinweis

Dieses Repository dient der Dokumentation und Begleitung des Projekts  
**„Raspberry Pi 3 B+ als Samba-Server“**.

Bitte beachte je nach enthaltenem Material, ob einzelne Dateien, PDFs oder Kursunterlagen gesonderten Nutzungsbedingungen unterliegen.

---

## Abschluss

Aus einem Raspberry Pi 3 B+ wird hier Schritt für Schritt ein **klarer und alltagstauglicher Samba-Dateiserver für das Heimnetz**.

Nicht mehr.  
Aber vor allem auch nicht weniger.

Genau das ist die Stärke dieses Projekts.
