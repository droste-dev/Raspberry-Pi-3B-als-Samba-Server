# Befehlsübersicht – Raspberry Pi als Samba-Server im Heimnetz

Diese Datei bündelt die wichtigsten Terminalbefehle aus dem Kurs in logischer Reihenfolge.
Sie eignet sich als Nachschlagewerk, Cheatsheet oder Arbeitsgrundlage beim Nachbauen.

> **Hinweis:**
> Gerätenamen wie `/dev/sda` oder UUID-Werte können bei deinem System abweichen.
> Bitte immer zuerst mit `lsblk -f` oder `blkid` prüfen, bevor du Befehle ausführst.

---

## 1. System aktualisieren

```bash
sudo apt update
sudo apt upgrade -y
sudo apt full-upgrade -y
sudo reboot
```

---

## 2. Laufwerke und Dateisysteme prüfen

```bash
lsblk
lsblk -f
sudo fdisk -l
sudo blkid
```

---

## 3. Alte Signaturen auf der Festplatte entfernen

> **Achtung:** Dieser Befehl löscht vorhandene Dateisystem-Signaturen auf dem angegebenen Gerät.

```bash
sudo wipefs -a /dev/sda
```

---

## 4. Neue Partitionstabelle und Partition anlegen

```bash
sudo parted /dev/sda --script mklabel gpt
sudo parted /dev/sda --script mkpart primary ext4 0% 100%
```

Danach erneut prüfen:

```bash
lsblk -f
```

---

## 5. Dateisystem erstellen

```bash
sudo mkfs.ext4 -L samba_data /dev/sda1
```

Anschließend die UUID ermitteln:

```bash
sudo blkid /dev/sda1
```

---

## 6. Mountpunkt anlegen

```bash
sudo mkdir -p /srv/samba/data
```

---

## 7. Testweise mounten

```bash
sudo mount /dev/sda1 /srv/samba/data
```

Prüfen, ob der Datenträger eingebunden wurde:

```bash
df -h
mount | grep samba
ls -la /srv/samba/data
```

---

## 8. Besitzrechte vorbereiten

```bash
sudo chown -R pi:pi /srv/samba/data
sudo chmod -R 775 /srv/samba/data
```

Optional mit gemeinsamer Gruppenlogik:

```bash
sudo chown -R pi:sambashare /srv/samba/data
sudo chmod -R 2775 /srv/samba/data
```

---

## 9. UUID in `/etc/fstab` eintragen

Zuerst UUID anzeigen:

```bash
sudo blkid /dev/sda1
```

Dann die Datei bearbeiten:

```bash
sudo nano /etc/fstab
```

Beispiel für den Eintrag:

```fstab
UUID=DEINE-UUID /srv/samba/data ext4 defaults,nofail 0 2
```

Danach testen:

```bash
sudo umount /srv/samba/data
sudo mount -a
df -h
```

Neustart-Test:

```bash
sudo reboot
```

Nach dem Neustart erneut prüfen:

```bash
df -h
mount | grep samba
ls -la /srv/samba/data
```

---

## 10. Samba installieren

```bash
sudo apt install samba samba-common-bin -y
```

Optional zum Testen mit SMB-Client:

```bash
sudo apt install smbclient -y
```

---

## 11. Samba-Konfiguration sichern

```bash
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
```

Datei öffnen:

```bash
sudo nano /etc/samba/smb.conf
```

---

## 12. Freigabeordner anlegen

```bash
sudo mkdir -p /srv/samba/data/freigabe
sudo chown -R pi:sambashare /srv/samba/data/freigabe
sudo chmod -R 2775 /srv/samba/data/freigabe
```

---

## 13. Samba-Benutzer vorbereiten

Gruppe sicherstellen:

```bash
getent group sambashare
```

Falls nötig Benutzer zur Gruppe hinzufügen:

```bash
sudo usermod -aG sambashare pi
```

Samba-Passwort setzen:

```bash
sudo smbpasswd -a pi
sudo smbpasswd -e pi
```

---

## 14. Beispiel einer einfachen Samba-Freigabe

Diese Zeilen werden in `/etc/samba/smb.conf` ergänzt:

```ini
[Freigabe]
   path = /srv/samba/data/freigabe
   browseable = yes
   read only = no
   writable = yes
   guest ok = no
   valid users = pi
   force group = sambashare
   create mask = 0664
   directory mask = 2775
```

---

## 15. Samba-Konfiguration prüfen

```bash
sudo testparm
```

---

## 16. Samba-Dienste neu starten und aktivieren

```bash
sudo systemctl restart smbd
sudo systemctl enable smbd
```

Falls `nmbd` im Kurs mit verwendet wird:

```bash
sudo systemctl restart nmbd
sudo systemctl enable nmbd
```

Status prüfen:

```bash
sudo systemctl status smbd
sudo systemctl status nmbd
```

---

## 17. Freigaben lokal testen

Verfügbare Freigaben anzeigen:

```bash
smbclient -L localhost -U pi
```

Direkt mit einer Freigabe verbinden:

```bash
smbclient //localhost/Freigabe -U pi
```

---

## 18. Netzwerkadresse des Raspberry Pi anzeigen

```bash
hostname -I
ip -br a
```

Damit kannst du später z. B. unter Windows zugreifen über:

```text
\\IP-DES-RASPBERRY\Freigabe
```

---

## 19. Rechte und Ordnerstruktur prüfen

```bash
ls -ld /srv/samba/data
ls -ld /srv/samba/data/freigabe
ls -la /srv/samba/data/freigabe
id pi
```

---

## 20. Typische Fehleranalyse

Samba-Status prüfen:

```bash
sudo systemctl status smbd --no-pager
sudo journalctl -u smbd -n 50 --no-pager
```

Konfiguration prüfen:

```bash
sudo testparm
```

Mounts prüfen:

```bash
lsblk -f
sudo blkid
df -h
mount | grep samba
```

Berechtigungen prüfen:

```bash
ls -ld /srv/samba/data
ls -ld /srv/samba/data/freigabe
id pi
getent group sambashare
```

---

## 21. Optional: Testdatei anlegen

```bash
touch /srv/samba/data/freigabe/testdatei.txt
ls -la /srv/samba/data/freigabe
```

---

## 22. Optional: Freigabe sichern oder zurücksetzen

Samba-Konfiguration wiederherstellen:

```bash
sudo cp /etc/samba/smb.conf.bak /etc/samba/smb.conf
sudo systemctl restart smbd
```

Freigabeordner neu anlegen:

```bash
sudo rm -rf /srv/samba/data/freigabe
sudo mkdir -p /srv/samba/data/freigabe
sudo chown -R pi:sambashare /srv/samba/data/freigabe
sudo chmod -R 2775 /srv/samba/data/freigabe
```

---

## 23. Kompakte Reihenfolge für den Praxisdurchlauf

```bash
sudo apt update
sudo apt upgrade -y
lsblk -f
sudo wipefs -a /dev/sda
sudo parted /dev/sda --script mklabel gpt
sudo parted /dev/sda --script mkpart primary ext4 0% 100%
sudo mkfs.ext4 -L samba_data /dev/sda1
sudo mkdir -p /srv/samba/data
sudo mount /dev/sda1 /srv/samba/data
sudo blkid /dev/sda1
sudo nano /etc/fstab
sudo apt install samba samba-common-bin smbclient -y
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
sudo mkdir -p /srv/samba/data/freigabe
sudo chown -R pi:sambashare /srv/samba/data/freigabe
sudo chmod -R 2775 /srv/samba/data/freigabe
sudo usermod -aG sambashare pi
sudo smbpasswd -a pi
sudo nano /etc/samba/smb.conf
sudo testparm
sudo systemctl restart smbd
sudo systemctl enable smbd
hostname -I
smbclient -L localhost -U pi
```

---

## 24. Empfehlung für die Praxis

Arbeite die Befehle nicht blind ab, sondern prüfe an den kritischen Stellen immer:

- **Ist wirklich das richtige Laufwerk gemeint?**
- **Stimmt die UUID?**
- **Ist der Mount erfolgreich?**
- **Passen Besitzer, Gruppe und Rechte?**
- **Ist die Samba-Konfiguration mit `testparm` fehlerfrei?**

Genau diese saubere Arbeitsweise macht aus einer Bastellösung einen stabilen Samba-Server für den Alltag.
