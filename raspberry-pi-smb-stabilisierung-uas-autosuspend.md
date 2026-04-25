# Raspberry Pi SMB-Server: Stabilisierung durch UAS-Quirk und deaktivierten USB-Autosuspend

## Ausgangssituation

Der Raspberry Pi SMB-Server war nach ungefähr 10 Minuten nicht mehr erreichbar.  
Ein Zugriff war dann nicht mehr möglich, und der Server ließ sich nur durch einen Neustart wieder nutzen.

Die ersten Prüfungen zeigten jedoch:

- keine Unterspannung
- keine auffällige Temperatur
- Samba selbst lief beim frischen Boot zunächst sauber
- Netzwerk über `eth0` war grundsätzlich aktiv

Dadurch entstand der Verdacht, dass die Ursache nicht direkt bei Samba selbst lag, sondern eher im Bereich:

- USB-Storage
- USB-SATA-Adapter
- UAS
- USB-Energiesparfunktionen

---

## Ziel der Maßnahme

Den Raspberry Pi SMB-Server so stabilisieren, dass die externe USB-Festplatte dauerhaft sauber eingebunden bleibt und der Server nicht mehr nach kurzer Zeit ausfällt.

---

## Maßnahme 1: UAS testweise abschalten

Zuerst wurde für das betroffene USB-Speichergerät ein UAS-Quirk gesetzt.

### Datei öffnen

```bash
sudo nano /boot/firmware/cmdline.txt
```

Wichtig:

- Die komplette Bootzeile muss **in einer einzigen Zeile** bleiben.
- Es darf **kein Zeilenumbruch** eingefügt werden.

### Beispielhafter Zusatz

```bash
usb-storage.quirks=152d:0567:u
```

> Hinweis:  
> `152d:0567` ist nur ein Beispiel.  
> Hier muss die tatsächlich zum USB-Gerät gehörende VID:PID-Kennung eingetragen werden.

Dieser Parameter sorgt dafür, dass das Gerät nicht mehr über UAS, sondern über den klassischen `usb-storage`-Treiber angesprochen wird.

### Neustart

```bash
sudo reboot
```

### Zwischenstand

Diese Maßnahme brachte eine Verbesserung, löste das Problem aber noch nicht vollständig.  
Der Server fiel später erneut aus.

---

## Maßnahme 2: USB-Autosuspend deaktivieren

Danach wurde zusätzlich der USB-Autosuspend deaktiviert.

### Datei erneut öffnen

```bash
sudo nano /boot/firmware/cmdline.txt
```

### Zusätzlichen Parameter ergänzen

```bash
usbcore.autosuspend=-1
```

Die Bootzeile enthielt danach sinngemäß beide Parameter:

```bash
usb-storage.quirks=152d:0567:u usbcore.autosuspend=-1
```

> Wichtig:  
> Auch hier bleibt alles in **einer einzigen Zeile**.

### Neustart

```bash
sudo reboot
```

---

## Ergebnis

Nach dem Ergänzen von

- `usb-storage.quirks=...:u`
- `usbcore.autosuspend=-1`

lief der Raspberry Pi SMB-Server im anschließenden Test über einen ganzen Tag stabil.

Das ist ein sehr deutlicher Hinweis darauf, dass die eigentliche Ursache im Bereich

- USB-Storage-Verhalten
- UAS
- USB-Energiesparfunktion / Autosuspend

lag und nicht bei Samba selbst.

---

## Bewertung

Die Kombination aus

1. **UAS für das betroffene Gerät abschalten**
2. **USB-Autosuspend deaktivieren**

war in diesem Fall die erfolgreiche Lösung.

Gerade bei Raspberry Pi-Systemen mit externer USB-Festplatte kann diese Maßnahme sinnvoll sein, wenn:

- Freigaben plötzlich nicht mehr erreichbar sind
- der Pi nur noch nach Neustart wieder reagiert
- Samba unauffällig wirkt, das System aber trotzdem „verschwindet“
- der Fehler nach einigen Minuten Leerlauf oder Laufzeit auftritt

---

## Dokumentierter Lösungsweg

### `cmdline.txt` bearbeiten

```bash
sudo nano /boot/firmware/cmdline.txt
```

### Bootparameter ergänzen

```bash
usb-storage.quirks=DEINE_VID:DEINE_PID:u usbcore.autosuspend=-1
```

### Neustart

```bash
sudo reboot
```

---

## Praxishinweis

Wenn ein Raspberry Pi mit externer USB-Festplatte als SMB-Server genutzt wird, sollte bei ähnlichen Symptomen nicht nur Samba geprüft werden, sondern gezielt auch:

- USB-SATA-Adapter
- UAS
- USB-Autosuspend
- Stromversorgung der USB-Geräte
- Verhalten der Festplatte im Leerlauf

---

## Fazit

In diesem Fall war die Kombination aus UAS-Quirk und deaktiviertem USB-Autosuspend die entscheidende Stabilisierung.  
Der SMB-Server blieb danach im Dauertest stabil und erreichbar.

Diese Lösung sollte deshalb in der technischen Projektdokumentation ausdrücklich festgehalten werden.
