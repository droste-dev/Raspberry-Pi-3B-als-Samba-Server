# Raspberry Pi SMB-Server: UAS testweise abschalten

## Ausgangssituation

Der Raspberry Pi SMB-Server war nach ungefähr 10 Minuten nicht mehr erreichbar.  
Samba selbst lief zunächst sauber, ebenso waren Temperatur und Stromversorgung unauffällig.

Auffällig war jedoch ein Hinweis im Kernel-Log auf den USB-Storage-Bereich bzw. auf **UAS** (USB Attached SCSI), was auf Probleme mit dem USB-SATA-Adapter oder der USB-Bridge hindeuten konnte.

---

## Ziel der Maßnahme

UAS testweise deaktivieren, um zu prüfen, ob die Nichterreichbarkeit des SMB-Servers durch den USB-Massenspeicher bzw. dessen Bridge verursacht wird.

---

## Vorgehensweise

### 1. USB-Gerät identifizieren

Zuerst die Gerätekennung des USB-Speicheradapters ermitteln:

```bash
lsusb
```

Beispiel für eine erkannte Kennung:

```bash
152d:0567
```

Diese Kennung besteht aus:

- **VID** = Hersteller-ID
- **PID** = Produkt-ID

---

### 2. Boot-Parameter bearbeiten

Die Datei `cmdline.txt` öffnen:

```bash
sudo nano /boot/firmware/cmdline.txt
```

Wichtig:

- Der gesamte Inhalt muss **in einer einzigen Zeile** bleiben.
- Es darf **kein Zeilenumbruch** eingefügt werden.

---

### 3. UAS-Quirk ergänzen

Am Ende der bestehenden Zeile folgenden Parameter anhängen:

```bash
usb-storage.quirks=152d:0567:u
```

> Hinweis:  
> Die Kennung `152d:0567` ist nur ein Beispiel.  
> Hier muss die tatsächlich mit `lsusb` ermittelte Gerätekennung eingetragen werden.

Der Buchstabe `u` sorgt dafür, dass das Gerät nicht mehr über UAS, sondern über den klassischen `usb-storage`-Treiber angesprochen wird.

---

### 4. System neu starten

```bash
sudo reboot
```

---

## Ergebnis

Nach dem Eintragen des Quirk-Parameters und dem Neustart lief der SMB-Server wieder stabil.  
Das Problem konnte damit erfolgreich entschärft werden.

---

## Warum das sinnvoll ist

Einige USB-SATA-Adapter oder externe Festplattengehäuse arbeiten am Raspberry Pi nicht sauber mit UAS zusammen.  
Das kann zu Problemen führen wie:

- Verbindungsabbrüchen
- Hängern beim Zugriff auf die Festplatte
- zeitweiser Nichterreichbarkeit des SMB-Servers
- instabilem Verhalten nach einigen Minuten Laufzeit

Durch das Deaktivieren von UAS für genau dieses Gerät wird oft eine deutlich stabilere Anbindung erreicht.

---

## Schnellfassung

```bash
lsusb
sudo nano /boot/firmware/cmdline.txt
```

Am Ende der einzigen Zeile ergänzen:

```bash
usb-storage.quirks=152d:0567:u
```

Danach:

```bash
sudo reboot
```

---

## Dokumentationsnotiz

Diese Maßnahme war in diesem Fall erfolgreich und sollte bei ähnlichen Problemen mit Raspberry Pi, SMB-Server und externer USB-Festplatte als früher Diagnose- und Stabilitätstest berücksichtigt werden.
