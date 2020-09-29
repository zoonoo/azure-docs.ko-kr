---
title: Azure NetApp Files에 대 한 NFS 클라이언트 구성 | Microsoft Docs
description: Azure NetApp Files에서 사용 하도록 NFS 클라이언트를 구성 하는 방법을 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449649"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFS 클라이언트 구성

이 문서에 설명 된 NFS 클라이언트 구성은 [nfsv 4.1 Kerberos 암호화를 구성](configure-kerberos-encryption.md) 하거나 [이중 프로토콜 볼륨을 만들](create-volumes-dual-protocol.md)때 설치 프로그램의 일부입니다. Azure NetApp Files와 함께 사용할 수 있는 광범위 한 Linux 배포판입니다. 이 문서에서는 일반적으로 사용 되는 두 가지 환경 (RHEL 8 및 Ubuntu 18.04)에 대 한 구성을 설명 합니다. 

사용 하는 Linux 버전에 관계 없이 다음 구성이 필요 합니다.
* 시간 오차 문제를 방지 하도록 NTP 클라이언트를 구성 합니다.
* 이름 확인을 위해 Linux 클라이언트의 DNS 항목을 구성 합니다.  
    이 구성에는 "A" (전달) 레코드와 PTR (역방향) 레코드가 포함 됩니다. 
* 도메인 가입의 경우 대상 Active Directory (영역 조인 명령 중에 생성 됨)에 컴퓨터 계정을 만듭니다. 
    > [!NOTE] 
    > `$SERVICEACCOUNT`아래 명령에 사용 되는 변수는 대상 조직 구성 단위에 컴퓨터 계정을 만들 수 있는 권한 또는 위임이 있는 사용자 계정 이어야 합니다.
* 클라이언트에서 NFS 볼륨 및 기타 관련 모니터링 도구를 탑재할 수 있습니다.

## <a name="rhel-8-configuration"></a>RHEL 8 구성 

1. 패키지 설치:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. NTP 클라이언트를 구성 합니다.  
    RHEL 8은 `chrony` 기본적으로를 사용 합니다.  [Chrony suite를 사용 하 여 NTP 구성](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)의 구성 지침을 따릅니다.

3. Active Directory 도메인에 가입 합니다.  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu 구성 
이 섹션에서는 NFS 클라이언트의 Ubuntu 구성에 대해 설명 합니다.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>NFSv 4.1 Kerberos 암호화를 사용 하는 경우 

1. 패키지 설치:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. NTP 클라이언트를 구성 합니다.  
    Ubuntu 18.04는 `chrony` 기본적으로를 사용 합니다.  Ubuntu Bionic의 구성 지침에 따라 [chrony를 사용 하 여 NTP를 구성](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)합니다.

3. Active Directory 도메인에 조인 합니다.  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>이중 프로토콜을 사용 하는 경우  

1. 다음 명령을 실행 하 여 설치 된 패키지를 업그레이드 합니다.  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    예제:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. 다음 명령을 실행 하 여 서비스를 다시 시작 하 고 사용 하도록 설정 합니다.   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

다음 예에서는 Ubuntu LDAP 클라이언트에서 LDAP 사용자에 대 한 AD LDAP 서버를 쿼리 합니다 `ldapu1` .   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대 한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)

