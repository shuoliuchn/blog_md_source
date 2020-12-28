# Linux 磁盘挂载

[toc]

## 查看磁盘分区状态

使用 lsblk 命令查看：

```bash
$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sdb           8:16   0  12.7T  0 disk              # 一整块硬盘
└─sdb1        8:17   0  12.7T  0 part
nvme0n1     259:0    0 931.5G  0 disk
├─nvme0n1p1 259:1    0   512M  0 part /boot/efi    # 硬盘下的分区
└─nvme0n1p2 259:2    0   931G  0 part /
```

各字段的含义都一目了然了，比如：

- NAME 是设备文件名，会省略 /dev 等前导目录
- SIZE 是磁盘容量
- MOUNTPOINT 是当前磁盘挂载到的位置，为空表示尚未挂载

## 磁盘格式化

这一步按需操作吧，格式化会丢失全部数据，需慎重。

使用 mkfs 命令格式化（创建文件系统），比如将 sdb1 格式化成 ext4 文件系统：

```bash
mkfs.ext4 /dev/sdb1
```

## 挂载磁盘

一般情况下，我们会把磁盘挂载到 `/mnt` 目录下。

首先创建一个要挂载到的目录，并赋予权限：

```bash
sudo mkdir /mnt/ele
sudo chmod 766 /mnt/ele
```

然后，使用 mount 命令挂载磁盘，注意挂载的是分区，而非整块磁盘：

```bash
sudo mount /dev/sdb1 /mnt/ele/
```

再次使用 lsblk 命令，可以看到磁盘挂载成功：

```bash
sure@sure-NUC8i3CYS:/mnt$ lsblk
sdb           8:16   0  12.7T  0 disk
└─sdb1        8:17   0  12.7T  0 part /mnt/ele
nvme0n1     259:0    0 931.5G  0 disk
├─nvme0n1p1 259:1    0   512M  0 part /boot/efi
└─nvme0n1p2 259:2    0   931G  0 part /
```

## 设置自动挂载

手动挂载有个问题：每次重启系统之后还需要重新挂载硬盘。我们先让不希望这样做，于是可以通过修改 `/etc/fstab` 文件的方式设置磁盘的自动挂载。

首先要确保磁盘未被挂载，可以通过 df 命令查看已经挂在了的磁盘：

```bash
$ df
文件系统             1K-块     已用        可用 已用% 挂载点
/dev/nvme0n1p2   959863856 12687148   898348500    2% /
/dev/nvme0n1p1      523248     7984      515264    2% /boot/efi
/dev/sdb1      13672347644   519432 13671828212    1% /mnt/ele
```

如果磁盘已经挂载，可以先卸载掉：

```bash
sudo umount /dev/sdb1
```

编辑 `/etc/fstab` 文件：

```bash
sudo vim /etc/fstab
```

在文件末尾加上这样一行：

```
/dev/sdb1   /mnt/ele        ntfs    defaults    0     0
```

使用命令读取挂载配置：

```bash
sudo mount -a
```

查看是否成功挂载：

```bash
$ df /mnt/ele/
文件系统             1K-块   已用        可用 已用% 挂载点
/dev/sdb1      13672347644 519432 13671828212    1% /mnt/ele


$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sdb           8:16   0  12.7T  0 disk
└─sdb1        8:17   0  12.7T  0 part /mnt/ele    # 挂载成功
nvme0n1     259:0    0 931.5G  0 disk
├─nvme0n1p1 259:1    0   512M  0 part /boot/efi
└─nvme0n1p2 259:2    0   931G  0 part /
```

