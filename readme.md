## Check device / pairing

```bash
ideviceinfo
idevicepair unpair
idevicepair pair
idevicepair validate
ideviceinfo
```

---

## Backup (from main device)

Enable encrypted backup (once):

```bash
idevicebackup2 -i encryption on
```

Create backup:

```bash
idevicebackup2 -i backup ./iphone_b_backup
```

---

## Inspect backup contents (before restoring)

List backup metadata:

```bash
idevicebackup2 info ./iphone_b_backup
```

Check backup size:

```bash
du -sh ./iphone_b_backup
```

Quick check if media exists in backup:

```bash
sqlite3 ./iphone_b_backup/*/Manifest.db \
"SELECT fileID, relativePath FROM Files WHERE relativePath LIKE 'Media/DCIM%';" | head
```

---

## Restore (to backup device)

Re-pair first (after activation):

```bash
idevicepair unpair
idevicepair pair
idevicepair validate
```

Restore:

```bash
idevicebackup2 -i restore \
  --source ***REMOVED*** \ # UUID of iphone_b
  --system \
  --reboot \
  --copy \
  --settings \
  --remove \
  ./iphone_b_backup
```

---

## Important Notes

* The target device (your other **iPhone 11 Pro**) must be on **the same or a newer iOS version** than the source device.

* The device you restore *to* must:

  * Be **activated**
  * Be connected to **Wi-Fi during initial setup**
  * Advance setup only until activation completes (e.g. reach the **Apps & Data** screen)
  * Do **NOT** finish full setup before restoring

* Use encrypted backups if you want **Keychain, Wi-Fi passwords, and most app data** preserved.

* Keep a copy of the original backup folder if restoring to a newer iOS (backup data migrates forward).
