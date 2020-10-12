---
title: Azure NetApp Files에 대 한 NFSv 4.1 Kerberos 암호화 구성 | Microsoft Docs
description: Azure NetApp Files 및 성능 영향에 대해 NFSv 4.1 Kerberos 암호화를 구성 하는 방법을 설명 합니다.
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
ms.date: 9/29/2020
ms.author: b-juche
ms.openlocfilehash: b683719fa2d0c1e7b5333c2ddf9c93f2797ade9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461481"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 NFSv 4.1 Kerberos 암호화 구성

Azure NetApp Files는 256 암호화를 사용 하는 Kerberos 모드 (krb5.conf, krb5i 및 krb5p)에서 NFS 클라이언트 암호화를 지원 합니다. 이 문서에서는 Kerberos 암호화와 함께 NFSv 4.1 볼륨을 사용 하는 데 필요한 구성을 설명 합니다.

## <a name="requirements"></a>요구 사항

NFSv 4.1 클라이언트 암호화에는 다음 요구 사항이 적용 됩니다. 

* Kerberos 티켓을 용이 하 게 하는 Active Directory Domain Services (AD DS) 연결 
* 클라이언트 및 Azure NetApp Files NFS 서버 IP 주소에 대 한 DNS A/PTR 레코드 만들기
* Linux 클라이언트  
    이 문서에서는 RHEL 및 Ubuntu 클라이언트에 대 한 지침을 제공 합니다.  다른 클라이언트는 비슷한 구성 단계와 함께 작동 합니다. 
* NTP 서버 액세스  
    일반적으로 사용 되는 AD DC (Active Directory 도메인 Controller) 도메인 컨트롤러 중 하나를 사용할 수 있습니다.

## <a name="create-an-nfs-kerberos-volume"></a>NFS Kerberos 볼륨 만들기

1.  [Azure NetApp Files에 대 한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md) 의 단계에 따라 nfsv 4.1 볼륨을 만듭니다.   

    볼륨 만들기 페이지에서 NFS 버전을 **nfsv 4.1**으로 설정 하 고 Kerberos를 **사용**으로 설정 합니다.

    > [!IMPORTANT] 
    > 볼륨을 만든 후에는 Kerberos를 사용할 수 있는 선택 항목을 수정할 수 없습니다.

    ![NFSv 4.1 Kerberos 볼륨 만들기](../media/azure-netapp-files/create-kerberos-volume.png)  

2. **정책 내보내기** 를 선택 하 여 해당 볼륨에 대 한 원하는 액세스 권한 수준과 보안 옵션 (kerberos 5, kerberos 5I 또는 kerberos 5p)을 일치 시킵니다.   

    Kerberos의 성능에 미치는 영향에 대 한 자세한 내용은 [nfsv 4.1의 Kerberos 성능 영향](#kerberos_performance)을 참조 하세요.  

    Azure NetApp Files 탐색 창에서 정책 내보내기를 클릭 하 여 볼륨에 대 한 Kerberos 보안 방법을 수정할 수도 있습니다.

3.  **검토 + 만들기** 를 클릭 하 여 nfsv 4.1 볼륨을 만듭니다.

## <a name="configure-the-azure-portal"></a>Azure Portal 구성 

1.  [Active Directory 연결 만들기](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)의 지침을 따릅니다.  

    Kerberos를 사용 하려면 Active Directory에서 하나 이상의 컴퓨터 계정을 만들어야 합니다. 제공 하는 계정 정보는 SMB *및* Nfsv 4.1 Kerberos 볼륨 모두에 대 한 계정을 만드는 데 사용 됩니다. 이 컴퓨터는 볼륨을 만드는 동안 자동으로 생성 됩니다.

2.  **Kerberos 영역**에서 **AD 서버 이름과** **KDC IP** 주소를 입력 합니다.

    AD 서버와 KDC IP는 동일한 서버 일 수 있습니다. 이 정보는 Azure NetApp Files에서 사용 하는 SPN 컴퓨터 계정을 만드는 데 사용 됩니다. 컴퓨터 계정을 만든 후에는 DNS 서버 레코드를 사용 하 여 필요에 따라 추가 KDC 서버를 찾을 Azure NetApp Files. 

    ![Kerberos 영역](../media/azure-netapp-files/kerberos-realm.png)
 
3.  **연결** 을 클릭 하 여 구성을 저장 합니다.

## <a name="configure-active-directory-connection"></a>Active Directory 연결 구성 

NFSv 4.1 Kerberos를 구성 하면 Active Directory에 두 개의 컴퓨터 계정이 만들어집니다.
* SMB 공유에 대 한 컴퓨터 계정
* NFSv 4.1의 컴퓨터 계정--접두사를 통해이 계정을 식별할 수 있습니다 `NFS-` . 

첫 번째 NFSv 4.1 Kerberos 볼륨을 만든 후 다음 PowerShell 명령을 사용 하 여 암호화 유형 또는 컴퓨터 계정을 설정 합니다.

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>NFS 클라이언트 구성 

Nfs 클라이언트를 구성 하려면 [Azure NetApp Files에 대 한 nfs 클라이언트 구성](configure-nfs-clients.md) 의 지침을 따르세요.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>NFS Kerberos 볼륨 탑재

1. **볼륨** 페이지에서 탑재할 NFS 볼륨을 선택 합니다.

2. 볼륨에서 **명령 탑재** 를 선택 하 여 지침을 표시 합니다.

    예를 들면 다음과 같습니다. 

    ![Kerberos 볼륨에 대 한 탑재 명령](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 새 볼륨의 디렉터리 (탑재 지점)를 만듭니다.  

4. 컴퓨터 계정에 대 한 기본 암호화 종류를 AES 256로 설정 합니다.  
    `Set-ADComputer $COMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * 이 명령은 각 컴퓨터 계정에 대해 한 번만 실행 해야 합니다.
    * 도메인 컨트롤러 또는 [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) 가 설치 된 PC에서이 명령을 실행할 수 있습니다. 
    * `$COMPUTERACCOUNT`변수는 Kerberos 볼륨을 배포할 때 Active Directory에서 생성 된 컴퓨터 계정입니다. 접두사가 있는 계정입니다 `NFS-` . 
    * `$ANFSERVICEACCOUNT`변수는 컴퓨터 계정이 만들어진 조직 구성 단위에 대해 위임 된 컨트롤이 있는 권한이 없는 Active Directory 사용자 계정입니다. 

5. 호스트에 볼륨을 탑재 합니다. 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`변수는 `host:/export` 탑재 명령에서 찾은 경로입니다.
    * `$ANFMOUNTPOINT`변수는 Linux 호스트의 사용자가 만든 폴더입니다.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>NFSv 4.1에서 Kerberos의 성능 영향 

이 섹션은 NFSv 4.1의 Kerberos에 대 한 성능 영향을 이해 하는 데 도움이 됩니다.

### <a name="available-security-options"></a>사용 가능한 보안 옵션 

NFSv 4.1 볼륨에 현재 사용할 수 있는 보안 옵션은 다음과 같습니다. 

* **sec = sys** 는 AUTH_SYS을 사용 하 여 NFS 작업을 인증 하는 로컬 UNIX Uid 및 gid를 사용 합니다.
* **sec = krb5.conf** 는 로컬 UNIX Uid 및 gid Ds 대신 Kerberos V5를 사용 하 여 사용자를 인증 합니다.
* **sec = krb5i** 는 사용자 인증에 Kerberos V5를 사용 하 고 보안 체크섬을 사용 하 여 NFS 작업의 무결성 검사를 수행 하 여 데이터 변조를 방지 합니다.
* **sec = krb5p** 사용자 인증 및 무결성 검사에 Kerberos V5를 사용 합니다. 트래픽 스니핑을 방지 하기 위해 NFS 트래픽을 암호화 합니다. 이 옵션은 가장 안전한 설정 이지만 대부분의 성능 오버 헤드가 포함 됩니다.

### <a name="performance-vectors-tested"></a>테스트 된 성능 벡터

이 섹션에서는 다양 한 옵션의 단일 클라이언트 쪽 성능 영향에 대해 설명 합니다 `sec=*` .

* 성능 영향은 낮은 동시성 (낮은 부하) 및 높은 동시성 (i/o 및 처리량의 상한)의 두 가지 수준으로 테스트 되었습니다.  
* 테스트 된 세 가지 종류의 작업은 다음과 같습니다.  
    * 작은 작업 무작위 읽기/쓰기 (FIO 사용)
    * FIO를 사용 하는 대량 작업 순차 읽기/쓰기
    * Git와 같은 응용 프로그램에 의해 생성 된 메타 데이터 작업량이 많은 작업

### <a name="expected-performance-impact"></a>예상 성능 영향 

포커스에는 두 가지 영역, 즉 낮은 부하와 상한이 있습니다. 다음 목록에서는 보안 설정 및 시나리오 별로 성능 영향 보안 설정에 대해 설명 합니다. 모든 비교는 보안 매개 변수를 기준으로 수행 됩니다 `sec=sys` . 단일 클라이언트를 사용 하 여 단일 볼륨에서 테스트가 수행 되었습니다. 

Krb5.conf의 성능 영향:

* 낮은 동시성 (r/w):
    * 순차적 대기 시간이 0.3 밀리초로 증가 했습니다.
    * 임의 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
    * 메타 데이터 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
* 높은 동시성 (r/w): 
    * Krb5.conf의 최대 순차 처리량에 영향을 주지 않았습니다.
    * 작업 부하가 순수 쓰기로 전환 될 때 전체 영향을 0으로 줄이면 순수 읽기 작업의 경우 최대 임의 i/o가 30% 감소 합니다. 
    * 최대 메타 데이터 워크 로드가 30% 감소 했습니다.

Krb5i의 성능 영향: 

* 낮은 동시성 (r/w):
    * 순차적 대기 시간이 0.5 밀리초로 증가 했습니다.
    * 임의 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
    * 메타 데이터 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
* 높은 동시성 (r/w): 
    * 워크 로드 혼합에 상관 없이 최대 순차적 처리량이 70% 감소 했습니다.
    * 작업 부하가 순수한 write로 이동 하므로 전체 영향이 25%로 감소 하는 순수한 읽기 작업의 경우 최대 임의 i/o가 50% 감소 합니다. 
    * 최대 메타 데이터 워크 로드가 30% 감소 했습니다.

Krb5p의 성능 영향:

* 낮은 동시성 (r/w):
    * 순차적 대기 시간이 0.8 밀리초로 증가 했습니다.
    * 임의 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
    * 메타 데이터 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
* 높은 동시성 (r/w): 
    * 워크 로드 혼합에 상관 없이 최대 순차적 처리량이 85% 감소 했습니다. 
    * 작업 부하가 순수 쓰기로 전환 될 때 전체 영향이 43%로 감소 하는 순수한 읽기 작업의 경우 최대 무작위 i/o 수는 65% 감소 합니다. 
    * 최대 메타 데이터 워크 로드가 30% 감소 했습니다.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대 한 Faq](azure-netapp-files-faqs.md)
* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Active Directory 연결 만들기](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md) 
