---
title: 그룹 관리 서비스 계정 보호 | Azure Active Directory
description: gMSA(그룹 관리 서비스 계정) 컴퓨터 계정을 보호하는 방법에 대한 가이드입니다.
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
ms.openlocfilehash: 9df8679079d4d3496ceeff4ac8a4acd9142ba867
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206568"
---
# <a name="secure-group-managed-service-accounts"></a>그룹 관리 서비스 계정 보호

gMSA(그룹 관리 서비스 계정)는 서비스를 보호하는 데 사용하는 관리되는 도메인 계정입니다. gMSA는 단일 서버 또는 서버 팜(예: 네트워크 부하 분산 또는 IIS(인터넷 정보 서비스) 서버 내부의 시스템)에서 실행할 수 있습니다. gMSA 보안 주체를 사용하도록 서비스가 구성되면 Windows 운영 체제에서 해당 계정에 대한 암호 관리를 처리합니다.

## <a name="benefits-of-using-gmsas"></a>gMSAs 사용의 장점

gMSA는 보안이 강화된 단일 ID 솔루션을 제공합니다. 동시에 관리 오버헤드를 줄이기 위해 다음과 같은 작업을 수행합니다.

* **강한 암호 설정**: gMSA는 임의로 생성된 240바이트의 복잡한 암호를 사용합니다. gMSA 암호의 복잡성과 길이는 무차별 암호 대입 또는 디렉터리 공격으로 인해 서비스가 손상될 가능성을 최소화합니다.

* **정기적인 암호 주기**: gMSA는 암호 관리를 Windows 운영 체제로 전환하여 암호를 30일마다 변경합니다. 서비스 및 도메인 관리자는 서비스 계정을 안전하게 유지하기 위해 더 이상 암호 변경을 예약하거나 서비스 중단을 관리할 필요가 없습니다. 

* **서버 팜에 대한 배포 지원**: gMSA를 여러 서버에 배포하는 기능을 통해 여러 호스트에서 동일한 서비스를 실행하는 부하 분산 솔루션을 지원할 수 있습니다. 

* **간소화된 SPN(서비스 사용자 이름) 관리 지원**: 계정을 만들 때 PowerShell을 사용하여 SPN을 설정할 수 있습니다. 또한 gMSA 권한이 올바르게 설정된 경우 자동 SPN 등록을 지원하는 서비스에서 gMSA에 대해 이 작업을 수행할 수 있습니다. 

## <a name="when-to-use-gmsas"></a>gMSAs를 사용하는 경우

장애 조치(Failover) 클러스터링과 같은 서비스에서 지원하지 않는 한, gMSAs를 온-프레미스 서비스에 대한 기본 계정 유형으로 사용합니다.

> [!IMPORTANT]
> 서비스를 프로덕션에 배포하기 전에 gMSA를 사용하여 해당 서비스를 테스트해야 합니다. 이렇게 하려면 애플리케이션에서 gMSA를 사용할 수 있는지 확인한 다음, 액세스해야 하는 리소스에 액세스할 수 있도록 테스트 환경을 설정합니다. 자세한 내용은 [그룹 관리 서비스 계정 지원](/system-center/scom/support-group-managed-service-accounts)을 참조하세요.


서비스에서 gMSA 사용을 지원하지 않는 경우 다음으로 가장 좋은 옵션은 sMSA(독립 실행형 관리 서비스 계정)를 사용하는 것입니다. sMSA는 gMSA와 동일한 기능을 제공하지만 단일 서버에만 배포하기 위한 것입니다.

서비스에서 지원하는 gMSA 또는 sMSA를 사용할 수 없는 경우 표준 사용자 계정으로 실행되도록 해당 서비스를 구성해야 합니다. 서비스 및 도메인 관리자는 계정을 안전하게 유지하기 위해 강한 암호 관리 프로세스를 준수해야 합니다.

## <a name="assess-the-security-posture-of-gmsas"></a>GMSAs 보안 상태를 평가합니다

gMSA 계정은 본질적으로 암호를 지속적으로 관리해야 하는 표준 사용자 계정보다 더 안전합니다. 그러나 전체 보안 상태를 살펴볼 때 gMSA의 액세스 범위를 고려하는 것이 중요합니다.

다음 표에는 gMSA를 사용하는 경우 발생할 수 있는 보안 문제 및 완화 방법이 나와 있습니다.

| 보안 문제| 완화 방법 |
| - | - |
| gMSA가 권한 있는 그룹의 멤버입니다. | <li>그룹 구성원 확인. 이렇게 하려면 모든 그룹 멤버 자격을 열거하는 PowerShell 스크립트를 만듭니다. 그러면 gMSA 파일의 이름을 기준으로 결과 CSV 파일을 필터링할 수 있습니다.<li>권한 있는 그룹에서 gMSA를 제거합니다.<li>서비스를 실행하는 데 필요한 권리 및 권한만 gMSA에 부여합니다(서비스 공급 업체에 문의). 
| gMSA에 중요한 리소스에 대한 읽기/쓰기 권한이 있습니다. | <li>중요한 리소스에 대한 액세스를 감사합니다.<li>분석을 위해 감사 로그를 SIEM(예: Azure Log Analytics 또는 Azure Sentinel)에 보관합니다.<li>원치 않는 액세스 수준이 검색되면 불필요한 리소스 권한을 제거합니다. |
| | |


## <a name="find-gmsas"></a>gMSAs 찾기

조직에서 이미 gMSA를 만들었을 수 있습니다. 이러한 계정을 검색하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Get-ADServiceAccount 
Install-ADServiceAccount 
New-ADServiceAccount 
Remove-ADServiceAccount 
Set-ADServiceAccount 
Test-ADServiceAccount 
Uninstall-ADServiceAccount
```


효과적으로 작동하려면 gMSA가 관리 서비스 계정의 OU(조직 구성 단위)에 있어야 합니다.

  
![관리 서비스 계정 OU의 gMSA 계정에 대한 스크린샷](./media/securing-service-accounts/secure-gmsa-image-1.png)

목록에 없을 수 있는 서비스 MSA를 찾으려면 다음 명령을 실행합니다.

```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all managed service accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>gMSAs 관리

gMSA 계정을 관리하기 위해 사용할 수 있는 Active Directory PowerShell cmdlet은 다음과 같습니다.

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Windows Server 2012부터 *-ADServiceAccount cmdlets은 기본값으로 gMSAs와 함께 작동합니다. 위의 cmdlet을 사용하는 방법에 대한 자세한 내용은 [그룹 관리 서비스 계정 시작](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)을 참조하세요.

## <a name="move-to-a-gmsa"></a>gMSA으로 이동
gMSA 계정은 온-프레미스 요구 사항에 맞는 가장 안전한 유형의 서비스 계정입니다. 한 곳으로 이동할 수 있는 경우, 그렇게 해야 합니다. 또한 서비스를 Azure로 이동하고, 서비스 계정을 Azure Active Directory로 이동하는 것이 좋습니다. gMSA 계정으로 이동하려면 다음을 수행합니다.

1. [KDS(키 배포 서비스) 루트 키](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)가 포리스트에 배포되어 있는지 확인합니다. 이 작업은 한 번만 수행하면 됩니다.

1. [새 gMSA 만들기](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

1. 서비스를 실행하는 각 호스트에 새 gMSA를 설치합니다.
   > [!NOTE] 
   > gMSA를 사용하도록 서비스를 구성하기 전에 호스트에서 gMSA를 만들고 설치하는 방법에 대한 자세한 내용은 [그룹 관리 서비스 계정 시작](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))을 참조하세요.

1. 서비스 ID를 gMSA로 변경하고, 빈 암호를 지정합니다.

1. 서비스가 새 gMSA ID로 작동하는지 확인합니다.

1. 이전 서비스 계정 ID를 삭제합니다.

 

## <a name="next-steps"></a>다음 단계

서비스 계정을 보호하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)  
* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)  
* [컴퓨터 계정 보안](service-accounts-computer.md)  
* [사용자 계정 보안](service-accounts-user-on-premises.md)  
* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)
