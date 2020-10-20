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
ms.date: 10/19/2020
ms.author: b-juche
ms.openlocfilehash: f4b8b4b56693023ede2ccf8ae7eeac7ed5e16824
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216864"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFS 클라이언트 구성

이 문서에 설명 된 NFS 클라이언트 구성은 [nfsv 4.1 Kerberos 암호화를 구성](configure-kerberos-encryption.md) 하거나 [이중 프로토콜 볼륨을 만들](create-volumes-dual-protocol.md)때 설치 프로그램의 일부입니다. Azure NetApp Files와 함께 사용할 수 있는 광범위 한 Linux 배포판입니다. 이 문서에서는 일반적으로 사용 되는 두 가지 환경 (RHEL 8 및 Ubuntu 18.04)에 대 한 구성을 설명 합니다. 

사용 하는 Linux 버전에 관계 없이 다음 구성이 필요 합니다.
* 시간 오차 문제를 방지 하도록 NTP 클라이언트를 구성 합니다.
* 이름 확인을 위해 Linux 클라이언트의 DNS 항목을 구성 합니다.  
    이 구성에는 "A" (전달) 레코드와 PTR (역방향) 레코드가 포함 되어야 합니다. 
* 도메인 가입의 경우 대상 Active Directory (영역 조인 명령 중에 만들어짐)에서 Linux 클라이언트에 대 한 컴퓨터 계정을 만듭니다. 
    > [!NOTE] 
    > `$SERVICEACCOUNT`아래 명령에 사용 되는 변수는 대상 조직 구성 단위에 컴퓨터 계정을 만들 수 있는 권한 또는 위임이 있는 사용자 계정 이어야 합니다.

## <a name="rhel-8-configuration"></a>RHEL 8 구성 

이 섹션에서는 NFSv 4.1 Kerberos 암호화 및 이중 프로토콜에 필요한 RHEL 구성에 대해 설명 합니다.  

이 섹션의 예제에서는 다음 도메인 이름 및 IP 주소를 사용 합니다.  

* 도메인 이름: `contoso.com`
* 개인 IP: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>NFSv 4.1 Kerberos 암호화를 사용 하는 경우 RHEL 8 구성

1. `/etc/resolv.conf`적절 한 DNS 서버를 사용 하 여를 구성 합니다.  

    예를 들어:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Dns 전달 및 역방향 조회 영역에 대 한 DNS 서버에 NFS 클라이언트 레코드를 추가 합니다.

3. DNS를 확인 하려면 NFS 클라이언트에서 다음 명령을 사용 합니다.   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. 패키지 설치:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  NTP 클라이언트를 구성 합니다.  

    RHEL 8은 기본적으로 chrony를 사용 합니다. [ `Chrony` 도구 모음을 사용 하 여 NTP 구성](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)의 구성 지침을 따릅니다.

6.  Active Directory 도메인에 가입 합니다.  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    예를 들어: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`


7. 모든 NFS 서비스를 다시 시작 합니다.  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    다시 시작 하면 Kerberos 탑재 중에 오류 조건이 방지 `“mount.nfs: an incorrect mount option was specified”` 됩니다.

8. `kinit`사용자 계정으로 명령을 실행 하 여 티켓을 가져옵니다. 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    예를 들어:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>이중 프로토콜을 사용 하는 경우 RHEL 8 구성

다음 단계는 선택 사항입니다. NFS 클라이언트에서 사용자 매핑을 사용 하는 경우에만이 단계를 수행 해야 합니다. 

1. [Nfsv 4.1 Kerberos 암호화를 사용 하는 경우 RHEL 8 구성](#rhel8_nfsv41_kerberos) 섹션에서 설명 하는 모든 단계를 완료 합니다.   

2. SSSD 구성 파일에서 IP 주소를 사용 하는 대신 AD의 FQDN (정규화 된 도메인 이름)을 사용 하도록/etc/hosts 파일에 고정 DNS 레코드를 추가 합니다.  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. 도메인에 대 한 추가 섹션을 추가 하 여 AD LDAP 서버에서 식별자를 확인 합니다.    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. 에 항목이 있는지 확인 `/etc/nsswitch.conf` 합니다 `sss` .   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. 서비스를 다시 시작 `sssd` 하 고 캐시를 지웁니다.   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. 클라이언트가 LDAP 서버와 통합 되었는지 테스트 합니다.   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu 구성   

이 섹션에서는 NFSv 4.1 Kerberos 암호화 및 이중 프로토콜에 필요한 Ubuntu 구성에 대해 설명 합니다. 

이 섹션의 예제에서는 다음 도메인 이름 및 IP 주소를 사용 합니다.  

* 도메인 이름: `contoso.com`
* 개인 IP: `10.6.1.4`

1. `/etc/resolv.conf`적절 한 DNS 서버를 사용 하 여를 구성 합니다.

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Dns 전달 및 역방향 조회 영역에 대 한 DNS 서버에 NFS 클라이언트 레코드를 추가 합니다.
 
    DNS를 확인 하려면 NFS 클라이언트에서 다음 명령을 사용 합니다.

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. 패키지 설치:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    메시지가 표시 되 면 `$DOMAIN.NAME` 대문자를 사용 하 여 (예: `CONTOSO.COM` )를 기본 Kerberos 영역으로 입력 합니다.

4. 서비스를 다시 시작 합니다 `rpc-gssd.service` . 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18.04는 기본적으로 chrony를 사용 합니다. Ubuntu Bionic의 구성 지침에 따라 [chrony를 사용 하 여 NTP를 구성](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)합니다.

6. Active Directory 도메인에 조인 합니다.   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    예를 들어:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. `kinit`사용자를 사용 하 여 티켓 가져오기: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    예를 들어:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>이중 프로토콜을 사용 하는 경우 Ubuntu 구성  

다음 단계는 선택 사항입니다.  NFS 클라이언트에서 사용자 매핑을 사용 하려는 경우에만이 단계를 수행 해야 합니다.  

1. 다음 명령을 실행 하 여 설치 된 패키지를 업그레이드 합니다.   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    다음 예제에서는 샘플 값을 사용 합니다. 명령에 입력 하 라는 메시지가 표시 되 면 사용자 환경에 따라 입력을 제공 해야 합니다. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. 다음 명령을 실행 하 여 서비스를 다시 시작 하 고 사용 하도록 설정 합니다.

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

다음 예에서는 Ubuntu LDAP 클라이언트에서 LDAP 사용자에 대 한 AD LDAP 서버를 쿼리 합니다 `‘hari1’` . 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대 한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)

