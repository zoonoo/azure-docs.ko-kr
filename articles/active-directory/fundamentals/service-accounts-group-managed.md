---
title: 그룹 관리 서비스 계정 보안 설정 | Azure Active Directory
description: 그룹 관리 서비스 계정 컴퓨터 계정을 보호 하는 방법에 대 한 가이드입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4c1adddbf4b13f8e299bd656443c9aaab1d55b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644830"
---
# <a name="securing-group-managed-service-accounts"></a>그룹 관리 서비스 계정 보안

GMSAs (그룹 관리 서비스 계정)는 서비스 보안에 사용 되는 관리 되는 도메인 계정입니다. gMSAs는 단일 서버 또는 서버 팜 (예: 네트워크 Load Balancer (NLB) 또는 인터넷 정보 서비스 (IIS) 서버 뒤의 시스템)에서 실행할 수 있습니다. GMSA 보안 주체를 사용 하도록 서비스를 구성 하면 Windows에서 해당 계정에 대 한 암호 관리를 처리 합니다.

## <a name="benefits-of-using-gmsas"></a>GMSAs 사용의 이점

gMSAs는 보안이 강화 된 단일 id 솔루션을 제공 하 고 다음을 통해 관리 오버 헤드를 줄입니다.

* **강력한 암호를 설정** 합니다. gMSAs는 240 바이트 임의로 생성 된 복잡 한 암호를 사용 합니다. GMSA 암호의 복잡성과 길이는 무차별 암호 대입 또는 사전 공격으로 인해 서비스가 손상 될 가능성을 최소화 합니다.

* **정기적으로 암호 순환** Windows로의 gMSAs 이동 암호 관리-30 일 마다 암호를 변경 합니다. 서비스 및 도메인 관리자는 더 이상 암호 변경을 예약 하거나 서비스 중단을 관리 하 여 서비스 계정을 안전 하 게 유지할 필요가 없습니다. 

* **서버 팜에 대 한 배포 지원**. GMSAs를 여러 서버에 배포 하는 기능을 사용 하면 여러 호스트에서 동일한 서비스를 실행 하는 부하 분산 솔루션을 지원할 수 있습니다. 

* **간소화 된 SPN (서버 보안 주체 이름) 관리 지원**. 계정을 만들 때 PowerShell을 사용 하 여 SPN을 설정할 수 있습니다. 또한 gMSA에 대 한 자동 SPN 등록을 지 원하는 서비스는 gMSA 권한이 올바르게 설정 된 경우에도이 작업을 수행할 수 있습니다. 

## <a name="when-to-use-gmsas"></a>GMSAs를 사용 하는 경우

장애 조치 (Failover) 클러스터링과 같은 서비스에서 지원 하지 않는 한, gMSAs를 온-프레미스 서비스에 대 한 기본 계정 유형으로 사용 합니다.

> [!IMPORTANT]
> 프로덕션 환경에 배포 하기 전에 gMSAs를 사용 하 여 서비스를 테스트 해야 합니다. 이렇게 하려면 테스트 환경을 설정 하 고 응용 프로그램이 gMSA를 사용 하 고 액세스 해야 하는 리소스에 액세스할 수 있는지 확인 합니다. 자세한 내용은 [그룹 관리 서비스 계정에 대 한 지원](/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019)을 참조 하세요.


서비스에서 gMSAs를 사용 하는 것을 지원 하지 않는 경우 다음으로 가장 좋은 옵션은 sMSA (독립 실행형 관리 서비스 계정)를 사용 하는 것입니다. sMSAs는 gMSA와 동일한 기능을 제공 하지만 단일 서버에만 배포 하기 위한 것입니다.

서비스에서 gMSA 또는 sMSA를 사용할 수 없는 경우 서비스를 표준 사용자 계정으로 실행 하도록 구성 해야 합니다. 서비스 및 도메인 관리자는 계정을 안전 하 게 유지 하기 위해 강력한 암호 관리 프로세스를 관찰 해야 합니다.

## <a name="assess-the-security-posture-of-gmsas"></a>GMSAs 보안 상태를 평가 합니다.

gMSAs는 표준 사용자 계정 보다 안전 하며, 계속 해 서 암호 관리가 필요 합니다. 그러나 전반적인 보안 상태를 살펴보면 gMSAs 액세스 범위를 고려 하는 것이 중요 합니다.

다음 표에서는 gMSAs를 사용 하는 경우 발생할 수 있는 보안 문제 및 완화 방법을 보여 줍니다.

| 보안 문제| 해결 방법 |
| - | - |
| gMSA은 권한 있는 그룹의 멤버입니다. | 그룹 멤버 자격을 검토 합니다. 이렇게 하려면 모든 그룹 멤버 자격을 열거 하는 PowerShell 스크립트를 만든 다음 gMSA 파일의 이름으로 결과 CSV 파일을 필터링 하면 됩니다. <br>권한 있는 그룹에서 gMSA를 제거 합니다.<br> 서비스를 실행 하는 데 필요한 권한 및 권한만 gMSA에 부여 합니다 (서비스 공급 업체에 문의). 
| gMSA에는 중요 한 리소스에 대 한 읽기/쓰기 권한이 있습니다. | 중요 한 리소스에 대 한 액세스를 감사 합니다. 분석을 위해 Azure Log Analytics 또는 Azure 센티널와 같은 SIEM에 감사 로그를 보관 합니다. 원치 않는 액세스 수준이 검색 되는 경우 불필요 한 리소스 권한을 제거 합니다. |


## <a name="find-gmsas"></a>GMSAs 찾기

조직에 이미 gMSAs 생성 된 것 같습니다. 다음 PowerShell cmdlet을 실행 하 여 이러한 계정을 검색 합니다.

효과적으로 작업 하려면 gMSAs가 관리 되는 서비스 계정 OU (조직 구성 단위)에 있어야 합니다.

  
![관리 서비스 계정 OU의 스크린샷](./media/securing-service-accounts/secure-gmsa-image-1.png)

여기에 없을 수 있는 서비스 MSAs를 찾으려면 다음 명령을 참조 하세요.

**GMSAs 및 sMSAs를 비롯 한 모든 서비스 계정을 찾으려면 다음을 수행 합니다.**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>GMSAs 관리

GMSAs를 관리 하는 데 다음과 같은 Active Directory PowerShell cmdlet을 사용할 수 있습니다.

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Windows Server 2012부터 *-Uninstall-adserviceaccount cmdlet은 기본적으로 gMSAs 함께 작동 합니다. 위의 cmdlet을 사용 하는 방법에 대 한 자세한 내용은 [**그룹 관리 서비스 계정 시작**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)을 참조 하세요.

## <a name="move-to-a-gmsa"></a>GMSA으로 이동
gMSAs는 온-프레미스 요구 사항에 가장 안전한 유형의 서비스 계정입니다. 로 이동할 수 있는 경우를 지정 해야 합니다. 또한 서비스를 Azure로 이동 하 고 서비스 계정을 Azure Active directory로 이동 하는 것이 좋습니다.

1.  [KDS 루트 키가 포리스트에 배포 되어](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)있는지 확인 합니다. 이 작업은 한 번만 수행하면 됩니다.

2. [새 gMSA을 만듭니다](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. 서비스를 실행 하는 각 호스트에 새 gMSA를 설치 합니다.
   > [!NOTE] 
   > GMSA를 사용 하도록 서비스를 구성 하기 전에 호스트에서 gMSA을 만들고 설치 하는 방법에 대 한 자세한 내용은 [그룹 관리 서비스 계정 시작](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)) 을 참조 하세요.

 
4. 서비스 id를 gMSA로 변경 하 고 빈 암호를 지정 합니다.

5. 서비스가 새 gMSA id로 작동 하는지 확인 합니다.

6. 이전 서비스 계정 id를 삭제 합니다.

 

## <a name="next-steps"></a>다음 단계
서비스 계정 보안 설정에 대 한 다음 문서를 참조 하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [보안 그룹 관리 서비스 계정](service-accounts-group-managed.md)

* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)

* [보안 컴퓨터 계정](service-accounts-computer.md)

* [보안 사용자 계정](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)