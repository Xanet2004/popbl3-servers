This is a summary of the installation process.

| Category        | Setting                   | zaldua1zerb1                 | zaldua1bez1                  |
| --------------- | ------------------------- | ---------------------------- | ---------------------------- |
| Access          | user / passwd             | `user` / `user`              | `user` / `user`              |
| Access          | root / passwd             | `root` / `root`              | `root` / `root`              |
| Network         | Domain                    | `__`                         | `__`                         |
| Network         | Hostname                  | `zaldua1zerb1`               | `zaldua1bez1`                |
| Network         | Primary NIC               | `enp0s3` -> NAT              | `enp0s3` -> NAT              |
| System          | Locale / Keyboard         | `es_ES`                      | `es_ES`                      |
| System          | Timezone                  | `Europe/Madrid`              | `Europe/Madrid`              |
| System          | Disk layout               | default / no partitions (OK) | default / no partitions (OK) |
| Install options | Desktop/GUI               | `No`                         | `GNOME`                      |
| Install options | SSH server                | `Yes`                        | `No`                         |
| Install options | Standard system utilities | `Yes`                        | `Yes`                        |
# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "01_0_installed" --description="This is the virtual machine after installing the OS."
VBoxManage snapshot "zaldua1bez1" take "01_0_installed" --description="This is the virtual machine after installing the OS."
```

> Note
> Poweroff the machines first to avoid future conflicts.