<properties
	pageTitle="Linux VM에 디스크 추가 | Microsoft Azure"
	description="Linux VM에 영구 디스크를 추가하는 방법 알아보기"
	keywords="Linux 가상 컴퓨터, 리소스 디스크 추가"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.topic="article"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.date="04/29/2016"
	ms.author="rclaus"/>

# Linux VM에 디스크 추가

이 문서에는 VM이 유지 관리 또는 크기 조정으로 인해 다시 프로비전되더라도 데이터를 유지할 수 있도록 VM에 영구 디스크를 연결하는 방법을 보여줍니다. 디스크를 추가하려면 리소스 관리자 모드에 구성된 [Azure CLI](../xplat-cli-install.md)가 필요합니다(`azure config mode arm`).

## 빠른 명령

다음 명령 예제에서 &lt;와 &gt; 사이의 값을 사용자 환경의 값으로 바꿉니다.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## 디스크 연결

새 디스크 연결이 빠릅니다. `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`를 입력하여 VM에 대한 새 GB 디스크를 만들어 연결합니다. 저장소 계정을 명시적으로 식별하지 않는 경우 만드는 모든 디스크가 OS 디스크가 있는 동일한 저장소 계정에 배치됩니다. 다음과 같이 표시됩니다.

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

출력

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## Linux VM에 연결하여 새 디스크 탑재

> [AZURE.NOTE] 이 항목에서는 사용자 이름 및 암호를 사용하여 VM에 연결합니다. 공용 및 개인 키 쌍을 사용하여 VM과 통신하려면 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-ssh-from-linux.md)을 참조하세요. `azure vm reset-access` 명령을 사용하여 `azure vm quick-create` 명령으로 만든 VM의 **SSH** 연결 기능을 수정하여 **SSH** 액세스를 완전히 초기화하거나 사용자를 추가/제거하고 공용 키 파일을 추가하여 액세스를 보호할 수 있습니다.

Linux VM에서 사용할 수 있도록 새 디스크를 파티션, 포맷 및 탑재하기 위해 Azure VM에 SSH해야 합니다. **ssh**를 사용하여 연결하는 데 익숙하지 않을 경우 명령은 `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`의 형식으로 다음과 유사하게 표시됩니다.

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

출력

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

VM에 연결했으므로 디스크를 연결할 준비가 되었습니다. 먼저 `dmesg | grep SCSI`를 사용하여 디스크를 찾습니다(새 디스크를 찾는 데 사용하는 방법은 다를 수 있습니다). 이 경우, 다음과 유사하게 표시됩니다.

```bash
dmesg | grep SCSI
```

출력

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

이 항목의 경우에서 `sdc` 디스크는 우리가 원하는 디스크입니다. 이제 `sudo fdisk /dev/sdc`로 디스크를 파티션하고 사용자의 경우에서 디스크는 `sdc`이며, 파티션 1에 기본 디스크를 작성하고 다른 기본값을 적용하는 것으로 가정합니다.

```bash
sudo fdisk /dev/sdc
```

출력

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

프롬프트에서 `p`를 입력하여 파티션을 만듭니다.

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

**mkfs** 명령을 사용하고, 파일 시스템 형식 및 장치 이름을 지정하여 파티션에 파일 시스템을 작성합니다. 이 항목에서 위의 `ext4` 및 `/dev/sdc1`을 사용합니다.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

출력

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

이제 `mkdir`을 사용하여 파일 시스템을 탑재할 디렉터리를 만듭니다.

```bash
sudo mkdir /datadrive
```

`mount`을 사용하여 디렉터리를 탑재합니다.

```bash
sudo mount /dev/sdc1 /datadrive
```

이제 데이터 디스크는 `/datadrive`로 사용할 준비가 되었습니다.

```bash
ls
```

출력

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

> [AZURE.NOTE] SSH 키를 식별에 사용하여 Linux 가상 컴퓨터에 연결할 수도 있습니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-ssh-from-linux.md)을 참조하세요.

## 다음 단계

- 해당 정보를 [fstab](http://en.wikipedia.org/wiki/Fstab) 파일에 쓰지 않았는데 다시 부팅하면 일반적으로 새 디스크는 VM에서 사용할 수 없게 됩니다.
- [Linux 컴퓨터 성능 최적화](virtual-machines-linux-optimization.md) 권장 사항을 검토하여 Linux VM을 올바르게 구성했는지 확인합니다.
- 디스크를 추가하여 저장소 용량을 확장하고 추가 성능이 필요할 경우 [RAID를 구성](virtual-machines-linux-configure-raid.md)합니다.

<!---HONumber=AcomDC_0504_2016-->