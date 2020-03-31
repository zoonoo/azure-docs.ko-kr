---
title: 도메인 독립적 작업 그룹 가용성 그룹 구성
description: Azure의 SQL Server 가상 컴퓨터에서 Active Directory 도메인 독립적 작업 그룹 Always On 가용성 그룹을 구성하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122676"
---
# <a name="configure-a-workgroup-availability-group"></a>작업 그룹 가용성 그룹 구성 

이 문서에서는 Always On 가용성 그룹이 있는 Active Directory 도메인 독립 클러스터를 만드는 데 필요한 단계를 설명합니다. 이를 작업 그룹 클러스터라고도 합니다. 이 문서에서는 작업 그룹 및 가용성 그룹을 준비하고 구성하는 것과 관련된 단계와 클러스터를 만들거나 가용성 그룹을 배포하는 방법과 같은 다른 문서에서 다루는 단계에 대해 광택을 내는 단계에 대해 중점적으로 설명합니다. 


## <a name="prerequisites"></a>사전 요구 사항

작업 그룹 가용성 그룹을 구성하려면 다음이 필요합니다.
- 정적 IP 주소를 사용하여 동일한 가용성 집합 또는 다른 가용성 영역에 배포된 SQL Server 2016(또는 그 이상) 가상 컴퓨터를 실행하는 두 대 이상의 Windows Server(또는 그 이상) 가상 시스템입니다. 
- 서브넷에 최소 4개의 무료 IP 주소가 있는 로컬 네트워크입니다. 
- SQL Server 내에서 시스템 관리자 권한이 있는 관리자 그룹의 각 컴퓨터에 대한 계정입니다. 
- 오픈 포트: TCP 1433, TCP 5022, TCP 59999. 

참고로 이 문서에서는 다음 매개 변수를 사용하지만 필요에 따라 수정할 수 있습니다. 

| **이름** | **매개 변수** |
| :------ | :---------------------------------- |
| **노드 1**   | AGNode1 (10.0.0.4) |
| **노드 2**   | AGNode2 (10.0.0.5) |
| **클러스터 이름** | AGWGAG (10.0.0.6) |
| **수신기** | AGListener (10.0.0.7) | 
| **DNS 접미사** | ag.wgcluster.example.com | 
| **작업 그룹 이름** | AGWork 그룹 | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>DNS 접미사 설정 

이 단계에서는 두 서버에 대해 DNS 접미사를 구성합니다. `ag.wgcluster.example.com`)을 입력합니다. 이렇게 하면 네트워크 내에서 정규화된 주소로 연결할 개체의 이름을 `AGNode1.ag.wgcluster.example.com`사용할 수 있습니다. 

DNS 접미사를 구성하려면 다음 단계를 따르십시오.

1. RDP를 첫 번째 노드에 넣고 서버 관리자를 엽니다. 
1. **로컬 서버를** 선택한 다음 **컴퓨터 이름**에서 가상 컴퓨터의 이름을 선택합니다. 
1. **이 컴퓨터의 이름을 바꾸려면**아래에서 변경 **을** 선택합니다... . . 
1. 작업 그룹 이름의 이름을 `AGWORKGROUP`다음과 같이 의미 있는 것으로 변경합니다. 

   ![작업 그룹 이름 변경](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. **DNS 접미사 및 NetBIOS 컴퓨터 이름** 대화 상자를 열려면 **더 많은** 것을 선택합니다. 
1. **이 컴퓨터의 기본 DNS 접미사**아래에 DNS 접미사 이름을 `ag.wgcluster.example.com` 입력한 다음 **확인을**선택합니다. 

   ![DNS 접미사 추가](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. **전체 컴퓨터 이름이** DNS 접미사를 표시하고 있는지 확인한 다음 **확인을** 선택하여 변경 내용을 저장합니다. 

   ![DNS 접미사 추가](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. 메시지가 표시되면 서버를 다시 부팅합니다. 
1. 가용성 그룹에 사용할 다른 노드에서 이 단계를 반복합니다. 

## <a name="edit-host-file"></a>호스트 파일 편집

활성 디렉터리가 없기 때문에 windows 연결을 인증할 수 있는 방법은 없습니다. 따라서 텍스트 편집기로 호스트 파일을 편집하여 트러스트를 할당합니다. 

호스트 파일을 편집하려면 다음 단계를 따르십시오.

1. 가상 머신에 RDP를 제공합니다. 
1. **파일 탐색기를** 사용하여 `c:\windows\system32\drivers\etc`로 이동합니다. 
1. **호스트** 파일을 마우스 오른쪽 단추로 클릭하고 **메모장(또는** 기타 텍스트 편집기)을 통해 파일을 엽니다.
1. 파일의 끝에서 각 노드, 가용성 그룹 및 수신기에 대한 항목을 `IP Address, DNS Suffix #comment` 다음과 같은 형식으로 추가합니다. 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![호스트 파일에 IP 주소, 클러스터 및 리스너에 대한 항목을 추가합니다.](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>권한 설정

사용 권한을 관리할 Active Directory가 없으므로 기본 제공되지 않은 로컬 관리자 계정이 클러스터를 만들 도록 수동으로 허용해야 합니다. 

이렇게 하려면 모든 노드에서 관리 PowerShell 세션에서 다음 PowerShell cmdlet을 실행합니다. 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>장애 조치 클러스터 만들기

이 단계에서는 장애 조치 클러스터를 만듭니다. 이러한 단계에 익숙하지 않은 경우 장애 조치 클러스터 [자습서에서](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)수행할 수 있습니다.

자습서와 작업 그룹 클러스터에 대해 수행해야 하는 작업 간의 주목할 만한 차이점:
- 클러스터 유효성 검사를 실행할 때 **저장소**및 **저장소 공간 직접** 선택을 취소합니다. 
- 클러스터에 노드를 추가할 때 다음과 같이 정규화된 이름을 추가합니다.
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- 선택 취소 **클러스터에 모든 적격 저장소 추가.** 

클러스터가 만들어지면 정적 클러스터 IP 주소를 할당합니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 노드 중 하나에서 장애 **조치 클러스터 관리자를**열고 클러스터를 선택하고 **이름: \<클러스터내>** 클러스터 코어 **리소스** 아래에 있는 클러스터남 을 마우스 오른쪽 단추로 클릭한 다음 **속성을**선택합니다. 

   ![클러스터 이름에 대한 속성 시작](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. IP 주소에서 IP 주소를 선택하고 **편집을** **선택합니다.** 
1. **정적 사용을**선택하고 클러스터의 IP 주소를 제공한 다음 **확인을**선택합니다. 

   ![클러스터에 정적 IP 주소 제공](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. 설정이 올바른지 확인한 다음 **확인을** 선택하여 저장합니다.

   ![클러스터 속성 확인](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>클라우드 감시 만들기 

이 단계에서는 클라우드 공유 감시를 구성합니다. 단계에 익숙하지 않은 경우 장애 조치 [클러스터 자습서를](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness)참조하십시오. 

## <a name="enable-availability-group-feature"></a>가용성 그룹 기능 사용 

이 단계에서는 가용성 그룹 기능을 사용하도록 설정합니다. 단계에 익숙하지 않은 경우 가용성 그룹 [자습서를](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups)참조하십시오. 

## <a name="create-keys-and-certificate"></a>키 및 인증서 만들기

이 단계에서는 암호화된 끝점에서 SQL 로그인이 사용하는 인증서를 만듭니다. 각 노드에 폴더를 만들어 `c:\certs`인증서 백업을 보유합니다. 

첫 번째 노드를 구성하려면 다음 단계를 따르십시오. 

1. **SQL Server 관리 Studio를** 열고 와 같은 `AGNode1`첫 번째 노드에 연결합니다. 
1. 새 **쿼리** 창을 열고 복잡하고 안전한 암호로 업데이트한 후 다음 T-SQL(Transact-SQL) 문을 실행합니다.

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. 다음으로 HADR 끝점을 만들고 이 T-SQL(거래-SQL) 문을 실행하여 인증에 인증서를 사용합니다.

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **파일 탐색기를** 사용하여 인증서가 있는 파일 위치로 `c:\certs`이동합니다. 
1. 첫 번째 노드에서 와 같은 `AGNode1Cert.crt`인증서의 복사본을 수동으로 만들고 두 번째 노드의 동일한 위치로 전송합니다. 

두 번째 노드를 구성하려면 다음 단계를 따르십시오. 

1. 와 같은 `AGNode2`SQL **서버 관리 스튜디오를**사용하여 두 번째 노드에 연결합니다. 
1. 새 **쿼리** 창에서 복잡하고 안전한 암호로 업데이트한 후 다음과 같은 T-SQL(Transact-SQL) 문을 실행합니다. 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. 다음으로 HADR 끝점을 만들고 이 T-SQL(거래-SQL) 문을 실행하여 인증에 인증서를 사용합니다.

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **파일 탐색기를** 사용하여 인증서가 있는 파일 위치로 `c:\certs`이동합니다. 
1. 두 번째 노드에서 와 같은 `AGNode2Cert.crt`인증서의 복사본을 수동으로 만들고 첫 번째 노드의 동일한 위치로 전송합니다. 

클러스터에 다른 노드가 있는 경우 해당 인증서 이름을 수정하여 이 단계를 반복합니다. 

## <a name="create-logins"></a>로그인 만들기

인증서 인증은 노드 간에 데이터를 동기화하는 데 사용됩니다. 이를 허용하려면 다른 노드에 대한 로그인을 만들고, 로그인을 위해 사용자를 만들고, 백업된 인증서를 사용하도록 로그인할 수 있는 인증서를 만든 다음 미러링 끝점에 연결을 부여합니다. 

이렇게 하려면 먼저 다음 첫 번째 노드에서 다음과 같은 `AGNode1`T-SQL(Transact-SQL) 쿼리를 실행합니다. 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

그런 다음 다음과 같은 두 번째 노드에서 다음과 같은 `AGNode2`T-SQL(T-SQL) 쿼리를 실행합니다. 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

클러스터에 다른 노드가 있는 경우 해당 인증서 및 사용자 이름을 수정하여 다음 단계를 반복합니다. 

## <a name="configure-availability-group"></a>가용성 그룹 구성

이 단계에서는 가용성 그룹을 구성하고 데이터베이스를 추가합니다. 지금은 수신기를 만들지 마십시오. 단계에 익숙하지 않은 경우 가용성 그룹 [자습서를](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group)참조하십시오. 장애 조치(failover)를 시작하고 장애 조치(failback)를 시작하여 모든 것이 예상대로 작동하는지 확인해야 합니다. 

   > [!NOTE]
   > 동기화 프로세스 중에 오류가 발생하는 경우 일시적 과 `NT AUTHORITY\SYSTEM` 같은 `AGNode1` 첫 번째 노드에서 클러스터 리소스를 만들 수 있도록 sysadmin 권한을 부여해야 할 수 있습니다. 

## <a name="configure-load-balancer"></a>로드 밸러블러 구성

이 마지막 [단계에서Azure 포털](virtual-machines-windows-portal-sql-alwayson-int-listener.md) 또는 [PowerShell을](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) 사용하여 로드 밸러터를 구성합니다.


## <a name="next-steps"></a>다음 단계

Az SQL [VM CLI를](virtual-machines-windows-sql-availability-group-cli.md) 사용하여 가용성 그룹을 구성할 수도 있습니다. 


