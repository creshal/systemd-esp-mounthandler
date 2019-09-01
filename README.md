# systemd-esp-mounthandler
Example systemd handler to un/remount a shared ESP volume for multi-boot suspend to disk setups

## Why?

I like to keep several fully independent Linux (or even Windows) setups on the same disk, e.g. to fully separate work and home environments. Suspend to disk makes switching between them relatively seamless (especially with fast NVMe storage), and on modern laptops there might not be any way to have a second internal storage device.

To make suspend to disk safe on these setups, the shared EFI System Partition gets unmounted before each sleep and re-mounted after each resume, so different systems cannot accidentally corrupt each other's writes.

## How?

Toss the two `.service` files into `/etc/systemd/system` and enable them. Depending on how your ESP is set up, you might need to change the `Exec…` lines. In this example, ESP is mounted to `/media/ESP` and each Linux install gets its own directory under `ESP/` that is bind mounted as `/boot` in `/etc/fstab` like so:

```fstab
/dev/nvme0n1p2    /media/ESP  vfat    rw,fmask=0022,dmask=0022,shortname=mixed,errors=remount-ro	0 2
/media/ESP/foobar /boot       none    bind	0	0
```

Using a proper `UUID=` entry for `/media/ESP` is left as an exercise for the reader.

## Known issues

On a regular boot, `esp-remount` "fails" because the disk is already mounted. Can probably be fixed by messing further with `fstab`, but it still works anyway so I'm not bothering to figure out.
