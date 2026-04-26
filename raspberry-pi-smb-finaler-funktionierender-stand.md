# Raspberry Pi SMB-Server: Finaler funktionierender Stand mit neuer Dockingstation

## Ausgangslage

Der Raspberry Pi 3 B+ wurde als SMB-Server mit externer 2-TB-Festplatte eingesetzt.  
Dabei trat ein schwerer Stabilitätsfehler auf:

- Der Server war nach einiger Zeit nicht mehr erreichbar.
- SMB-Freigaben fielen aus.
- Teilweise half nur noch ein Neustart.
- Zeitweise wurden Partitionen nicht korrekt erkannt.

Im Verlauf der Diagnose stellte sich heraus, dass nicht Samba selbst der Hauptverdächtige war, sondern sehr wahrscheinlich die USB-Anbindung der Festplatte beziehungsweise die verwendete Dockingstation.

---

## Beobachtungen während der Fehlersuche

Im Verlauf der Tests zeigten sich unter anderem folgende Hinweise:

- Samba selbst lief beim frischen Start zunächst sauber.
- Temperatur und Unterspannung waren unauffällig.
- Die ursprüngliche Dockingstation zeigte instabiles Verhalten.
- Teilweise wurde nur noch ein `PMBR` erkannt.
- Die Partition `/dev/sda1` war zeitweise nicht sauber verfügbar.
- Die Festplatte wurde über verschiedene USB-Bridge-IDs erkannt.

Dadurch rückte die Kombination aus

- USB-SATA-Bridge
- UAS
- USB-Autosuspend
- Dockingstation

in den Mittelpunkt der Fehlersuche.

---

## Wichtiger technischer Unterschied

Die neue Dockingstation meldete sich mit folgender Kennung:

```text
152d:0539
```

Die zuvor verwendete Konfiguration bezog sich jedoch noch auf eine andere Gerätekennung.  
Deshalb wurde der Quirk-Eintrag an die tatsächlich neue USB-Bridge angepasst.

---

## Final funktionierende Boot-Parameter

Die funktionierende Konfiguration in `/boot/firmware/cmdline.txt` lautet am Ende der Zeile:

```bash
usb-storage.quirks=152d:0539:u usbcore.autosuspend=-1
```

Wichtig:

- Die komplette `cmdline.txt` bleibt **in einer einzigen Zeile**.
- Es darf **kein Zeilenumbruch** eingefügt werden.

---

## Vorgehensweise

### Datei bearbeiten

```bash
sudo nano /boot/firmware/cmdline.txt
```

### Parameter korrekt eintragen

```bash
usb-storage.quirks=152d:0539:u usbcore.autosuspend=-1
```

### System neu starten

```bash
sudo reboot
```

---

## Ergebnis nach dem Neustart

Die Logs zeigten anschließend einen sauberen Start der Festplatte und des Dateisystems.

Wesentliche Punkte aus der erfolgreichen Erkennung:

- Die neue Dockingstation wurde korrekt erkannt.
- Der Quirk griff auf die richtige VID:PID.
- Die Festplatte wurde sauber als `sda` erkannt.
- Die Partition `sda1` wurde korrekt angelegt.
- Das Dateisystem auf `sda1` wurde sauber read/write gemountet.
- Es traten keine USB-Resets, Disconnects oder I/O-Fehler auf.

---

## Technische Bestätigung aus dem Log

Auffällige positive Punkte aus `dmesg`:

```text
usb 1-1.1.2: New USB device found, idVendor=152d, idProduct=0539
usb-storage 1-1.1.2:1.0: Quirks match for vid 152d pid 0539
sda: sda1
EXT4-fs (sda1): mounted filesystem ... r/w
```

Es gab dabei **keine** Hinweise auf:

- `reset`
- `disconnect`
- `I/O error`
- `read-only remount`

Das spricht für eine aktuell stabile und saubere USB-Storage-Anbindung.

---

## Bewertung

Die wahrscheinlich entscheidenden Faktoren für die Stabilisierung waren:

1. **Wechsel der Dockingstation**
2. **Anpassung des Quirk-Eintrags auf die richtige USB-Bridge**
3. **Deaktivierung des USB-Autosuspend**

Die Kombination aus neuer Dockingstation und korrekt gesetzten Bootparametern war damit der bisher beste und technisch sauberste Stand.

---

## Finaler dokumentierter Stand

### Aktuelle relevante Parameter

```bash
usb-storage.quirks=152d:0539:u usbcore.autosuspend=-1
```

### Aktuell verwendete USB-Bridge

```text
VID:PID = 152d:0539
```

### Beobachteter Status

- Platte wird erkannt
- `sda1` wird angelegt
- EXT4-Dateisystem wird korrekt gemountet
- aktuell keine USB- oder EXT4-Fehler im Log sichtbar

---

## Empfehlung für den weiteren Betrieb

Die aktuelle Konfiguration sollte nun erst einmal unverändert weiter getestet werden.

Sinnvoll ist dabei:

- keine weiteren gleichzeitigen Änderungen vornehmen
- System einige Zeit im Realbetrieb laufen lassen
- bei erneuten Problemen wieder `dmesg` und `journalctl` prüfen
- Dockingstation und USB-Anbindung als besonders wichtigen Faktor dokumentieren

---

## Fazit

Der SMB-Server zeigte mit der neuen Dockingstation und der angepassten Boot-Konfiguration erstmals wieder einen sauberen und technisch plausiblen Start.

Der aktuell funktionierende Stand lautet:

```bash
usb-storage.quirks=152d:0539:u usbcore.autosuspend=-1
```

Damit ist dies der derzeitige Referenzstand für die weitere Nutzung und Dokumentation des Raspberry-Pi-SMB-Servers.
