---
title: "Linux용 사용자 이름 선택 | Microsoft Docs"
description: "Azure에서 Linux 가상 컴퓨터의 사용자 이름을 선택하는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 33b50c97-92f1-46c9-a623-e37f67459c5c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eaea0ce42537789278d3a0476b261e6a750dde2b


---
# <a name="selecting-user-names-for-linux-on-azure"></a>Azure에서 Linux용 사용자 이름 선택
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure에서 Linux 가상 컴퓨터를 프로비전할 때는 나중에 VM 로그인에 사용할 수 있는 루트가 아닌 사용자의 이름을 지정해야 합니다. 새 사용자의 이름을 선택할 수 있습니다. 또는 Azure 클래식 포털을 통해 프로비전하는 경우 기본 이름인 "azureuser"를 사용할 수 있습니다. 

대부분의 경우 이러한 사용자는 기본 이미지에 존재하지 않으며 프로비전 프로세스 중에 생성됩니다. 사용자가 이미 기본 VM 이미지에 존재하는 경우 Azure Linux 에이전트가 VM을 만들 때 사용자가 지정한 정보를 토대로 해당 사용자에 대한 암호 및/또는 SSH 키를 구성합니다.

**그러나**Linux에서는 사용해서는 안 되는 일련의 사용자 이름을 정의합니다. UID가 0-99인 사용자로 정의된 기존 시스템 사용자를 사용하여 Linux VM을 프로비전하려고 하면 프로비전 프로세스가 **실패** 합니다. 전형적인 예로는 UID가 0인 `root` 사용자가 있습니다.

* 참고 항목: [Linux 표준 기반 - 사용자 ID 범위](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

다음은 Azure에서 Linux 가상 컴퓨터를 프로비전할 때 사용하지 말아야 할 Ubuntu 및 CentOS에 대한 일반적인 기본 제공 시스템 사용자 목록입니다. 이 목록은 예로 든 것일 뿐입니다. 선택한 사용자 이름이 기존 시스템 사용자와 충돌하지 않는지 확인하기 위해 배포에 대한 설명서를 참조하세요.

## <a name="centos"></a>CentOS
* abrt
* adm
* audio
* bin
* cdrom
* cgred
* daemon
* dbus
* dialout
* dip
* disk
* floppy
* ftp
* ftp
* games
* gopher
* haldaemon
* halt
* kmem
* lock
* lp
* mail
* man
* mem
* nfsnobody
* nobody
* ntp
* operator
* oprofile
* postdrop
* postfix
* qpidd
* root
* rpc
* rpcuser
* saslauth
* shutdown
* slocate
* sshd
* stapdev
* stapusr
* sync
* sys
* tape
* test
* tcpdump
* tty
* users
* utempter
* utmp
* uucp
* vcsa
* video
* wheel

## <a name="ubuntu"></a>Ubuntu
* adm
* admin
* audio
* backup
* bin
* cdrom
* crontab
* daemon
* dialout
* dip
* disk
* fax
* floppy
* fuse
* games
* gnats
* irc
* kmem
* landscape
* libuuid
* list
* lp
* mail
* man
* messagebus
* mlocate
* netdev
* news
* nobody
* nogroup
* operator
* plugdev
* proxy
* root
* sasl
* shadow
* src
* ssh
* sshd
* staff
* sudo
* sync
* sys
* syslog
* tape
* tty
* users
* utmp
* uucp
* video
* voice
* whoopsie
* www-data




<!--HONumber=Nov16_HO3-->


