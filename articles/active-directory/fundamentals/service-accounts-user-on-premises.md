---
title: 사용자 기반 서비스 계정 보안 | Azure Active Directory
description: 사용자 기반 서비스 계정 보안에 대한 가이드입니다.
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
ms.openlocfilehash: 79e0dc10aa9cb5fb67812cca31d2cd892afcccbe
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108208098"
---
# <a name="secure-user-based-service-accounts-in-active-directory"></a>Active Directory의 사용자 기반 서비스 계정 보안

온-프레미스 사용자 계정을 사용하는 것은 Windows에서 실행되는 보안 서비스를 지원하는 기존 방식입니다. 이러한 계정은 gMSA(그룹 관리 서비스 계정) 및 sMSA(독립 실행형 관리 서비스 계정)가 서비스에서 지원되지 않는 경우에만 마지막 수단으로 사용합니다. 사용하기에 가장 적합한 계정 유형을 선택하는 방법에 대한 자세한 내용은 [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)를 참조하세요. 

관리 ID 또는 서비스 주체와 같은 Azure 서비스 계정을 사용하도록 서비스를 이동할 수 있을지 여부를 알아보는 것도 좋습니다. 

온-프레미스 사용자 계정을 만들어서 계정이 로컬 및 네트워크 리소스에 액세스하는 데 필요한 서비스 및 권한에 대한 보안 컨텍스트를 제공할 수 있습니다. 온-프레미스 사용자 계정에는 다른 Active Directory 사용자 계정과 마찬가지로 수동 암호 관리가 필요합니다. 서비스 및 도메인 관리자는 해당 계정을 안전하게 유지하기 위해 강력한 암호 관리 프로세스를 준수해야 합니다.

사용자 계정을 서비스 계정으로 생성하는 경우에는 단일 서비스용으로만 사용합니다. 해당 계정이 서비스 계정이라는 것과 어떤 서비스를 위한 계정인지 명확히 알려주는 방식으로 이름을 설정합니다. 

## <a name="benefits-and-challenges"></a>이점 및 과제

온-프레미스 사용자 계정은 상당한 이점을 제공할 수 있습니다. 서비스에서 가장 다목적으로 사용할 수 있는 계정 유형입니다. 서비스 계정으로 사용되는 사용자 계정은 일반 사용자 계정을 관리하는 모든 정책에 따라 제어될 수 있습니다. 하지만 MSA를 사용할 수 없는 경우에만 사용해야 합니다. 또한 컴퓨터 계정이 더 나은 옵션인지 평가할 필요가 있습니다. 

온-프레미스 사용자 계정 사용과 관련된 문제는 다음 표에 요약되어 있습니다.

| 과제 | 완화 방법 |
| - | - |
| 암호 관리는 보안을 약화시키고 서비스 가동 중지 시간을 유발할 수 있는 수동 프로세스입니다.| <li>강력한 암호를 사용하여 정기적인 업데이트를 보장하는 강력한 프로세스를 통해 암호 복잡성 및 암호 변경이 관리되도록 해야 합니다.<li>서비스의 암호 업데이트를 사용하여 암호 변경을 조정하면, 서비스 가동 중지 시간을 줄이는 데 도움이 됩니다. |
| 서비스 계정으로 작동하는 온-프레미스 사용자 계정을 식별하는 것은 어려울 수 있습니다. | <li>환경에 배포된 서비스 계정의 기록을 문서화하고 유지 관리합니다.<li>계정 이름 및 액세스 권한이 할당된 리소스를 추적합니다.<li>서비스 계정으로 사용되는 모든 사용자 계정에 "svc-" 접두사를 추가하는 것이 좋습니다. |
| | |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>서비스 계정으로 사용되는 온-프레미스 사용자 계정 찾기

온-프레미스 사용자 계정은 다른 Active Directory 사용자 계정과 같습니다. 이러한 계정은 찾기 어려울 수 있습니다. 사용자 계정의 특성 중 서비스 계정으로 식별할 수 있는 특성이 하나도 없기 때문입니다. 

서비스 계정으로 사용하는 사용자 계정에 대해서는 쉽게 식별할 수 있는 명명 규칙을 만드는 것이 좋습니다. 예를 들어 "svc-"를 접두사로 추가하고 서비스 이름을 "svc-HRDataConnector"로 지정할 수 있습니다.

다음 조건 중 일부를 사용하면 이러한 서비스 계정을 찾을 수 있습니다. 단, 이 방식은 일부 계정을 찾지 못할 수도 있습니다. 예를 들면:

* 위임용으로 트러스트된 계정  
* 서비스 사용자 이름이 있는 계정입니다.  
* 암호가 만료되지 않도록 설정된 계정

서비스용으로 만든 온-프레미스 사용자 계정을 찾으려면 다음 PowerShell 명령을 실행하면 됩니다.

위임용으로 트러스트된 계정을 찾으려면:

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

서비스 사용자 이름이 있는 계정을 찾으려면:

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

암호가 만료되지 않도록 설정된 계정을 찾으려면:

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```

또한 중요한 리소스에 대한 액세스를 감사하고, SIEM(보안 정보 및 이벤트 관리) 시스템에 감사 로그를 보관할 수 있습니다. Azure Log Analytics 또는 Azure Sentinel과 같은 시스템을 사용하여 서비스 계정을 검색하고 분석할 수 있습니다.

## <a name="assess-the-security-of-on-premises-user-accounts"></a>온-프레미스 사용자 계정의 보안 평가

다음 기준을 사용하여 서비스 계정으로 사용되는 온-프레미스 사용자 계정의 보안을 평가할 수 있습니다.

* 암호 관리 정책이 무엇인가요?  
* 계정이 권한 있는 그룹의 멤버인가요?  
* 계정에 중요한 리소스에 대한 읽기/쓰기 권한이 있나요?

### <a name="mitigate-potential-security-issues"></a>잠재적인 보안 문제 완화

다음 표에는 온-프레미스 사용자 계정에 대한 잠재적인 보안 문제 및 완화 방법이 요약되어 있습니다.

| 보안 문제 | 완화 방법 |
| - | - |
| 암호 관리| <li>암호 복잡성 및 암호 변경은 정기적인 업데이트와 강력한 암호 요구 사항을 포함하는 강력한 프로세스를 통해 관리되도록 합니다.<li>암호 업데이트로 암호 변경을 조정하여 서비스 가동 중지 시간을 최소화합니다. |
| 계정이 권한 있는 그룹의 멤버입니다.| <li>그룹 멤버 자격을 검토합니다.<li>권한 있는 그룹에서 계정을 제거합니다.<li>서비스를 실행하는 데 필요한 권리 및 권한만 계정에 부여합니다(서비스 공급 업체에 문의). 예를 들어, 로컬에 로그인을 거부하거나 대화형 로그인을 거부할 수도 있습니다. |
| 중요한 리소스에 대한 읽기/쓰기 권한이 계정에 있습니다.| <li>중요한 리소스에 대한 액세스를 감사합니다.<li>분석을 위해 SIEM(Azure Log Analytics 또는 Azure Sentinel)에 감사 로그를 보관합니다.<li>원치 않는 액세스 수준이 검색되는 경우 리소스 권한을 교정합니다. |
| | |


## <a name="move-to-more-secure-account-types"></a>더 안전한 계정 유형으로 이동

온-프레미스 사용자 계정은 서비스 계정으로 사용하지 않는 것이 좋습니다. 이러한 유형의 계정을 사용하는 서비스의 경우, gMSA 또는 sMSA를 사용하도록 구성할 수 있는지 여부를 평가합니다.

또한 보다 안전한 서비스 계정 유형을 사용할 수 있도록 서비스 자체를 Azure로 이동할 수 있는지 여부를 평가합니다. 

## <a name="next-steps"></a>다음 단계

서비스 계정을 보호하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)  
* [그룹 관리 서비스 계정 보안](service-accounts-group-managed.md)  
* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)  
* [컴퓨터 계정 보안](service-accounts-computer.md)  
* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)

 
