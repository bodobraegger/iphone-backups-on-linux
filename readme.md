# Notes on Linux based iPhone backup and restore

For field research where loss of property is expected.

Assumptions:
- You own a primary device in use and a secondary iPhone of equal or better specs
- Your primary device has an equal or older iOs version than the backup phone.
- The primary device is named `iphone_b` and the secondary device `iphone_m` for this example

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
# Extract and save UDID
UDID=$(ideviceinfo | grep UniqueDeviceID | awk '{print $2}')

# Verify it was captured
echo "Device UDID: $UDID"

# Restore backup
idevicebackup2 -i restore \
  --source "$UDID" \ # UDID of iphone_b, example format: 00008020-0012345A1234567B, the backup folder will be named as such
  --system \
  --reboot \
  --copy \
  --settings \
  --remove \ # remove existing settings and data
  ./iphone_b_backup
```

---

## Important Info

* The target device must be on **the same or a newer iOS version** than the source device.

* The device you restore *to* must:

  * Be **activated**
  * Be connected to **Wi-Fi during initial setup**
  * Advance setup only until activation completes (e.g. reach the **Apps & Data** screen)
  * Do **NOT** finish full setup before restoring

* Use encrypted backups if you want **Keychain, Wi-Fi passwords, and most app data** preserved.

* Keep a copy of the original backup folder if restoring to a newer iOS (backup data migrates forward).
