---
title: 독립 실행형 관리 서비스 계정 보안 | Azure Active Directory
description: 독립 실행형 관리 서비스 계정에 보안을 설정하는 방법을 안내합니다.
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
ms.openlocfilehash: ea3bd3e6fc971901bf69c053088678e8f0f718d0
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206676"
---
# <a name="secure-standalone-managed-service-accounts"></a>독립 실행형 관리 서비스 계정 보안

독립 실행형 관리 서비스 계정(sMSA)은 서버에서 실행되는 하나 이상의 서비스를 보호하는 데 사용되는 관리되는 도메인 계정입니다. 여러 서버에서 다시 사용할 수는 없습니다. sMSA는 자동 암호 관리, 간소화된 서비스 사용자 이름(SPN) 관리 및 다른 관리자에게 관리를 위임하는 기능을 제공합니다. 

Active Directory에서 sMSA는 서비스를 실행하는 특정 서버에 연결됩니다. Microsoft Management Console의 Active Directory 사용자 및 컴퓨터 스냅인에서 나열된 이러한 계정을 찾을 수 있습니다.

![관리 서비스 계정 OU를 표시하는 Active Directory 사용자 및 컴퓨터 스냅인의 스크린샷.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

관리 서비스 계정은 Windows Server 2008 R2 Active Directory 스키마에서 도입되었으며 Windows Server 2008 R2 이상이 필요합니다. 

## <a name="benefits-of-using-smsas"></a>sMSA 사용의 이점

sMSA는 서비스 계정으로 사용되는 사용자 계정보다 더 높은 보안을 제공합니다. 동시에 관리 오버헤드를 줄이기 위해 다음과 같은 작업을 수행합니다.

* **강력한 암호 설정**: sMSA는 임의로 생성된 복잡한 240바이트 암호를 사용합니다. sMSA 암호의 복잡성과 길이는 무차별 암호 대입 또는 디렉터리 공격으로 인해 서비스가 손상될 가능성을 최소화합니다.

* **정기적 암호 순환**: Windows에서 자동으로 30일마다 sMSA 암호를 변경합니다. 서비스 및 도메인 관리자는 암호 변경을 예약하거나 연결된 가동 중지 시간을 관리할 필요가 없습니다.

* **SPN 관리 간소화**: 서비스 사용자 이름은 도메인 기능 수준이 Windows Server 2008 R2인 경우 자동으로 업데이트됩니다. 예를 들어 서비스 사용자 이름은 다음과 같은 경우 자동으로 업데이트됩니다.
   * 호스트 컴퓨터 계정의 이름이 변경된 경우.  
   * 호스트 컴퓨터의 DNS(도메인 이름 서버) 이름을 변경하는 경우.  
   * [PowerShell](/powershell/module/activedirectory/set-adserviceaccount)을 사용하여 다른 sam-accountname 또는 dns-hostname 매개 변수를 추가하거나 제거하는 경우.

## <a name="when-to-use-smsas"></a>sMSA를 사용해야 하는 경우

sMSAs는 관리 및 보안 작업을 간소화할 수 있습니다. 단일 서버에 하나 이상의 서비스를 배포하고 gMSA(그룹 관리 서비스 계정)를 사용할 수 없는 경우 sMSA를 사용합니다. 

> [!NOTE] 
> 2개 이상의 서비스에 sMSA를 사용할 수 있지만 각 서비스에는 감사 목적으로 고유한 ID가 있는 것이 좋습니다. 

소프트웨어 작성자가 MSA를 사용할 수 있는지 여부를 알 수 없는 경우 애플리케이션을 테스트해야 합니다. 이렇게 하려면 테스트 환경을 만들고 필요한 모든 리소스에 액세스할 수 있는지 확인합니다. 자세한 내용은 [sMSA 만들기 및 설치](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting)를 참조하세요.

### <a name="assess-the-security-posture-of-smsas"></a>sMSA 보안 상태 평가

sMSA는 계속해서 암호를 관리해야 하는 표준 사용자 계정보다 안전합니다. 그러나 전체 보안 상태의 일부로 sMSA의 액세스 범위를 고려하는 것이 중요합니다.

sMSA로 인해 나타나는 잠재적인 보안 문제를 완화하는 방법을 알아보려면 다음 표를 참조하세요.

| 보안 문제| 완화 방법 |
| - | - |
| sMSA는 권한 있는 그룹의 구성원입니다. | <li>고급 권한 그룹(예: 도메인 관리자)에서 sMSA를 제거합니다.<li>*최소 권한* 모델을 사용하고 해당 서비스를 실행하는 데 필요한 권한 및 액세스 권한만 sMSA에 부여합니다.<li>필요한 권한에 대해 잘 모르겠다면 서비스 작성자에게 문의하세요. |
| sMSA에 중요한 리소스에 대한 읽기/쓰기 권한이 있습니다. | <li>중요한 리소스에 대한 액세스를 감사합니다.<li>분석을 위해 Azure Log Analytics 또는 Azure Sentinel과 같은 SIEM(보안 정보 및 이벤트 관리) 프로그램에 감사 로그를 보관합니다.<li>원치 않는 액세스 수준이 검색되는 경우 리소스 권한을 교정합니다. |
| 기본적으로 sMSA 암호 롤오버 주기는 30일입니다. | 그룹 정책을 사용하여 엔터프라이즈 보안 요구 사항에 따라 기간을 조정할 수 있습니다. 암호 만료 기간을 설정하려면 다음 경로를 사용할 수 있습니다.<br>*Computer Configuration\Policies\Windows Settings\Security Settings\Security Options*. 도메인 구성원의 경우 **최대 컴퓨터 계정 암호 사용 기간** 을 사용합니다. |
| | |



### <a name="challenges-with-smsas"></a>sMSA와 관련된 과제

sMSA와 관련된 과제는 다음과 같습니다.

| 과제| 완화 방법 |
| - | - |
| sMSA는 단일 서버에서만 사용할 수 있습니다. | 서버 간에 계정을 사용해야 한다면 gMSA를 사용합니다. |
| sMSA는 도메인 간에는 사용할 수 없습니다. | 도메인 간에 계정을 사용해야 한다면 gMSA를 사용합니다. |
| 모든 애플리케이션에서 sMSA를 지원하지는 않습니다. | 가급적 gMSA를 사용합니다. 그렇지 않으면 애플리케이션 작성자가 권장하는 대로 표준 사용자 계정 또는 컴퓨터 계정을 사용합니다. |
| | |


## <a name="find-smsas"></a>sMSA 찾기

모든 도메인 컨트롤러에서 DSA.msc를 실행한 다음, 관리 서비스 계정 컨테이너를 확장하여 모든 sMSA를 확인합니다. 

Active Directory 도메인에서 모든 sMSA 및 gMSA를 반환하려면 다음 PowerShell 명령을 실행합니다. 

`Get-ADServiceAccount -Filter *`

Active Directory 도메인의 sMSA만 반환하려면 다음 명령을 실행합니다.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>sMSA 관리

sMSA를 관리하기 위해 다음 Active Directory PowerShell cmdlet을 사용할 수 있습니다.

`Get-ADServiceAccount`  
` Install-ADServiceAccount`  
` New-ADServiceAccount`  
` Remove-ADServiceAccount`  
`Set-ADServiceAccount`  
`Test-ADServiceAccount`  
`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>sMSA로 이동

애플리케이션 서비스가 sMSA를 지원하지만 gMSA를 지원하지 않고, 현재 보안 컨텍스트에 대한 사용자 계정 또는 컴퓨터 계정을 사용하고 있는 경우, 서버에서 [sMSA를 만들고 설치](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting)합니다. 

리소스를 Azure로 이동하고 Azure Managed ID 또는 서비스 사용자를 사용하는 것이 가장 좋습니다.

## <a name="next-steps"></a>다음 단계

서비스 계정 보안에 대한 자세한 내용은 다음 문서를 참조하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)  
* [그룹 관리 서비스 계정 보안](service-accounts-group-managed.md)  
* [컴퓨터 계정 보안](service-accounts-computer.md)  
* [사용자 계정 보안](service-accounts-user-on-premises.md)  
* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)
