<properties 
	pageTitle="Linux용 사용자 이름 선택 | Microsoft Azure" 
	description="Azure에서 Linux 가상 컴퓨터의 사용자 이름을 선택하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



#Azure에서 Linux용 사용자 이름 선택#

Azure에서 Linux 가상 컴퓨터를 프로비전할 때는 나중에 VM 로그인에 사용할 수 있는 루트가 아닌 사용자의 이름을 지정해야 합니다. 새 사용자의 이름을 선택할 수 있습니다. 또는 관리 포털을 통해 프로비전하는 경우 기본 이름인 "azureuser"를 사용할 수 있습니다.

대부분의 경우 이러한 사용자는 기본 이미지에 존재하지 않으며 프로비전 프로세스 중에 생성됩니다. 사용자가 이미 기본 VM 이미지에 존재하는 경우 Azure Linux 에이전트가 VM을 만들 때 사용자가 지정한 정보를 토대로 해당 사용자에 대한 암호(및/또는 SSH 키)를 구성합니다.

**그러나** Linux에서는 사용해서는 안 되는 일련의 사용자 이름을 정의합니다. UID가 0-99인 사용자로 정의된 기존 시스템 사용자를 사용하여 Linux VM을 프로비전하려고 하면 프로비전 프로세스가 **실패**합니다. 전형적인 예로는 UID가 0인 `root` 사용자가 있습니다.

 - 참고 항목: [Linux 표준 기반 - 사용자 ID 범위](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

다음은 Linux 가상 컴퓨터를 프로비전할 때 사용하지 말아야 할 사용자 이름입니다. VM 프로비전 프로세스가 실패할 수 있으므로 **이러한 사용자 이름은 사용하지 않는 것**이 좋습니다.


## openSUSE
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## SLES
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
## CentOS
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## Ubuntu
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

 

<!---HONumber=Sept15_HO4-->