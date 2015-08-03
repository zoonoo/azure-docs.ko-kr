<properties
	pageTitle="Azure에서 Linux를 실행하는 가상 컴퓨터 만들기"
	description="Azure의 이미지를 사용하여 Linux를 실행하는 Azure VM(가상 컴퓨터)을 만드는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="rasquill"/>

# Linux를 실행하는 가상 컴퓨터 만들기

> [AZURE.SELECTOR]
- [Azure Portal](virtual-machines-linux-tutorial-portal-rm.md)
- [Azure CLI](virtual-machines-linux-tutorial.md)

Linux를 실행하는 Azure 가상 컴퓨터(VM) 만들기는 명령줄 또는 포털에서 수행하는 것이 쉽습니다. 이 자습서는 Mac, Linux 및 Windows용 Azure 명령줄 인터페이스(Azure CLI)를 사용하여 Azure에서 실행하는 Ubuntu Server VM을 신속하게 만들고, **ssh**를 사용하여 새 디스크를 작성 및 탑재하여 연결합니다. (이 항목에서는 Ubuntu Server VM을 사용 하지만 [템플릿으로 사용자 고유의 이미지](virtual-machines-linux-create-upload-vhd.md)를 사용하여 Linux VM을 만들 수도 있습니다.)

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Azure CLI 설치

첫 번째 단계는 [Azure CLI 설치](../xplat-cli-install.md)입니다.

좋습니다. 이제 `azure config mode arm`를 입력하여 리소스 관리 모드에 있는 지 확인합니다.

훨씬 낫습니다. 이제 `azure login`를 입력하고 나타나는 메시지에 따라 회사 또는 학교 ID로 로그인합니다.

> [AZURE.NOTE]로그인 오류가 나타나면, [개인 Microsoft 계정에서 회사 또는 학교 ID를 만들어야](resource-group-create-work-id-from-personal.md) 할 수 있습니다.

## Azure VM 만들기

_&lt;my-group-name&gt;_을 고유 그룹 이름으로 바꾸는 `azure group create <my-group-name> westus`을 입력합니다(원하는 경우 다른 지역을 사용할 수 있음). 다음과 유사한 출력이 표시됩니다.

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

이제 `azure vm quick-create`을 입력하면 나머지 매개 변수를 입력하라는 메시지가 나타납니다. 방금 만든 리소스 그룹의 이름 및 **ImageURN** 값을 사용하고 `canonical:ubuntuserver:14.04.2-LTS:latest`을 사용하여 환경을 다음과 유사하도록 할 수 있습니다.

	azure vm quick-create
	info:    Executing command vm quick-create
	Resource group name: myuniquegroupname
	Virtual machine name: myuniquevmname
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
	User name: ops
	Password: *********
	Confirm password: *********
	+ Looking up the VM "myuniquevmname"
	info:    Using the VM Size "Standard_D1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
	+ Looking up the storage account cli3c0464f24f1bf4f014323
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	info:    Preparing to create new virtual network and subnet
	/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
	info:    Found public ip parameters, trying to setup PublicIP profile
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
	+ Creating public ip "myuni-westu-1432328437727-pip"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	+ Creating NIC "myuni-westu-1432328437727-nic"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Creating VM "myuniquevmname"
	+ Looking up the VM "myuniquevmname"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
	data:    ProvisioningState               :Succeeded
	data:    Name                            :myuniquevmname
	data:    Location                        :westus
	data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_D1
	data:
	data:    Storage Profile:
	data:      Image reference:
	data:        Publisher                   :canonical
	data:        Offer                       :ubuntuserver
	data:        Sku                         :14.04.2-LTS
	data:        Version                     :latest
	data:
	data:      OS Disk:
	data:        OSType                      :Linux
	data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :myuniquevmname
	data:      User Name                     :ops
	data:      Linux Configuration:
	data:        Disable Password Auth       :false
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-31-55-31
	data:          Provisioning State        :Succeeded
	data:          Name                      :myuni-westu-1432328437727-nic
	data:          Location                  :westus
	data:            Private IP alloc-method :Dynamic
	data:            Private IP address      :10.0.1.4
	data:            Public IP address       :191.239.51.1
	data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	info:    vm quick-create command OK

VM은 작동되어 실행되며 연결하기를 대기합니다.

## VM에 연결

Linux VM에서는 일반적으로 **ssh**를 사용하여 연결합니다. 이 항목에서는 사용자 이름 및 암호를 사용하여 VM에 연결합니다. 공용 및 개인 키 쌍을 사용하여 VM과 통신하려면 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-use-ssh-key.md)을 참조하세요.

**ssh**를 사용한 연결에 익숙하지 않은 경우 , 명령 `ssh <username>@<publicdnsaddress> -p <the ssh port>` 형식을 취합니다. 이 경우, 이전 단계의 사용자 이름과 암호 및 포트 22를 사용하며, 기본값은 **ssh** 포트입니다.

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
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

VM에 연결했으므로 디스크를 연결할 준비가 되었습니다.

## 디스크 연결 및 탑재

새 디스크 연결이 빠릅니다. `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`를 입력하여 VM에 대한 새 GB 디스크를 만들어 연결합니다. 다음과 같이 표시됩니다.

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


이제 `dmesg | grep SCSI`를 사용하여 디스크를 찾아 봅니다(새 디스크를 찾는 데 사용하는 방법은 다를 수 있습니다). 이 경우, 다음과 유사하게 표시됩니다.

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

이 항목의 경우에서 `sdc` 디스크는 우리가 원하는 디스크입니다. 이제 `sudo fdisk /dev/sdc`로 디스크를 파티션하고 사용자의 경우에서 디스크는 `sdc`이며, 파티션 1에 기본 디스크를 작성하고 다른 기본값을 적용하는 것으로 가정합니다.

	sudo fdisk /dev/sdc
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

프롬프트에서 `p`를 입력하여 파티션을 만듭니다.

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

**mkfs** 명령을 사용하고, 파일 시스템 형식 및 장치 이름을 지정하여 파티션에 파일 시스템을 작성합니다. 이 항목에서 위의 `ext4` 및 `/dev/sdc1`을 사용합니다.

	sudo mkfs -t ext4 /dev/sdc1
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

이제 `mkdir`을 사용하여 파일 시스템을 탑재할 디렉터리를 만듭니다.

	sudo mkdir /datadrive

`mount`을 사용하여 디렉터리를 탑재합니다.

	sudo mount /dev/sdc1 /datadrive

이제 데이터 디스크는 `/datadrive`로 사용할 준비가 되었습니다.

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE]SSH 키를 식별에 사용하여 Linux 가상 컴퓨터에 연결할 수도 있습니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-use-ssh-key.md)을 참조하세요.

## 다음 단계

해당 정보를 [fstab](http://en.wikipedia.org/wiki/Fstab) 파일에 쓰지 않았는데 다시 부팅하면 일반적으로 새 디스크는 VM에서 사용할 수 없게 됩니다.

Azure의 Linux에 대한 자세한 내용은 다음 을 참조하세요.

- [Azure에서 Linux 및 오픈 소스 컴퓨팅](virtual-machines-linux-opensource.md)

- [Azure 명령줄 인터페이스를 사용하는 방법](../virtual-machines-command-line-tools.md)

- [Linux용 Azure CustomScript 확장을 사용하여 LAMP 앱 배포](virtual-machines-linux-script-lamp.md)

- [Azure VM 구성 설정 정보](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Azure의 Linux용 Docker 가상 컴퓨터 확장](virtual-machines-docker-vm-extension.md)
 

<!---HONumber=July15_HO4-->