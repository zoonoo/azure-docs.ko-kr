<properties linkid="services-linux-user-names" urlDisplayName="User Names in Linux" pageTitle="Selecting User Names for Linux on Azure" metaKeywords="" description="Learn how to select user names for a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Selecting User Names for Linux on Azure" authors="" solutions="" manager="" editor="" />

Azure에서 Linux용 사용자 이름 선택
==================================

Azure에서 Linux 이미지용 가상 컴퓨터 인스턴스를 만드는 경우 프로비전할 사용자 이름을 선택할 수 있습니다.

Linux에서는 사용할 수 없는 일련의 사용자 이름이 정의되어 있습니다. **이 이름을 사용하지 않는** 것이 매우 중요합니다. 이 이름 중 하나를 사용하면 이미지를 프로비전할 수 없습니다.

뿐만 아니라 서비스 관리 API에서 사용자 *xxxxxx*을(를) 만들지 못했다는 오류가 반환됩니다. 이미지에서 이미 만들어진 사용자 이름의 프로비전을 해제하지 않은 이전 캡처 작업의 결과로 이미지에 이미 존재하는 사용자 이름을 사용하는 경우에도 마찬가지입니다.

다음은 사용할 수 없는 사용자 이름입니다.

OpenSUSE
--------

-   abrt
-   adm
-   audio
-   bin
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

SLES
----

-   audio
-   bin
-   cdrom
-   console
-   daemon
-   dialout
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   haldaemon
-   kmem
-   lp
-   lp
-   mail
-   maildrop
-   man
-   messagebus
-   modem
-   news
-   news
-   nobody
-   nogroup
-   polkituser
-   postfix
-   public
-   root
-   shadow
-   sshd
-   sys
-   test
-   trusted
-   tty
-   users
-   utmp
-   uucp
-   uuidd
-   video
-   wheel
-   www
-   wwwrun
-   xok

CentOS
------

-   abrt
-   adm
-   audio
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

UBUNTU
------

-   adm
-   admin
-   audio
-   backup
-   bin
-   cdrom
-   crontab
-   daemon
-   dialout
-   dip
-   disk
-   fax
-   floppy
-   fuse
-   games
-   gnats
-   irc
-   kmem
-   landscape
-   libuuid
-   list
-   lp
-   mail
-   man
-   messagebus
-   mlocate
-   netdev
-   news
-   nobody
-   nogroup
-   operator
-   plugdev
-   proxy
-   root
-   sasl
-   shadow
-   src
-   ssh
-   sshd
-   staff
-   sudo
-   sync
-   sys
-   syslog
-   tape
-   tty
-   users
-   utmp
-   uucp
-   video
-   voice
-   whoopsie
-   www-data

