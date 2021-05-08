---
title: 사용자 기반 서비스 계정 보안 설정 | Azure Active Directory
description: 온-프레미스 사용자 계정 보안 설정 지침
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417467"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Active Directory에서 사용자 기반 서비스 계정 보안 설정

온-프레미스 사용자 계정은 Windows에서 실행되는 서비스의 보안을 설정하는 일반적인 방법입니다. 해당 계정은 gMSA(전체 관리 서비스 계정) 및 sMSA(독립 실행형 관리 서비스 계정)가 서비스에서 지원되지 않는 경우에 마지막 수단으로 사용합니다. 사용할 최상의 계정 유형을 선택하는 방법에 대한 자세한 내용은 온-프레미스 서비스 계정 개요를 참조하세요. 또한 관리 ID 또는 서비스 사용자와 같은 Azure 서비스 계정을 사용하기 위해 서비스를 이동할 수 있는지 조사합니다. 

서비스에 대한 보안 컨텍스트를 제공하고 서비스에서 로컬 및 네트워크 리소스에 액세스하는 데 필요한 권한을 부여하기 위해 온-프레미스 사용자 계정을 만들 수 있습니다. 다른 모든 AD(Active Directory) 사용자 계정과 마찬가지로 수동 암호 관리가 필요합니다. 서비스 및 도메인 관리자는 해당 계정을 안전하게 유지하기 위해 강력한 암호 관리 프로세스를 준수해야 합니다.

사용자 계정을 서비스 계정으로 사용하는 경우 단일 서비스용으로만 사용합니다. 서비스 계정이라는 것과 어떤 서비스에 대한 것인지 명확히 알려주는 방식으로 이름을 설정합니다. 

## <a name="benefits-and-challenges"></a>이점 및 과제

이점

온-프레미스 사용자 계정은 서비스에 사용할 수 있는 가장 다용도의 계정 유형입니다. 서비스 계정으로 사용되는 사용자 계정은 일반 사용자 계정을 제어하는 모든 정책에 따라 제어될 수 있습니다. 단, MSA를 사용할 수 없는 경우에만 사용합니다. 또한 컴퓨터 계정이 더 나은 옵션인지 평가합니다. 

온-프레미스 사용자 계정에 대한 과제

온-프레미스 사용자 계정 사용과 관련된 과제는 다음과 같습니다.

| 과제| 해결 방법 |
| - | - |
| 암호 관리는 더 약한 보안 및 서비스 가동 중지 시간을 유발할 수 있는 수동 프로세스입니다.| 강력한 암호를 사용하여 정기적인 업데이트를 보장하는 강력한 프로세스에서 암호 복잡성 및 암호 변경을 제어하는지 확인합니다. <br> 서비스의 암호 업데이트를 사용하여 암호 변경을 조정하면, 서비스 가동 중지 시간이 발생합니다. |
| 서비스 계정으로 작동하는 온-프레미스 사용자 계정을 식별하는 것은 어려울 수 있습니다.| 사용자 환경에 배포된 서비스 계정의 레코드를 문서화하고 유지 관리합니다. <br> 계정 이름 및 액세스 권한이 할당된 리소스를 추적합니다. <br> 서비스 계정으로 사용되는 모든 사용자 계정에 svc_ 접두사를 추가하는 것이 좋습니다. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>서비스 계정으로 사용되는 온-프레미스 사용자 계정 찾기

온-프레미스 사용자 계정은 다른 AD 사용자 계정과 동일합니다. 결과적으로, 서비스 계정으로 식별하는 사용자 계정의 단일 특성이 없으므로 해당 계정을 찾기가 어려울 수 있습니다. 

서비스 계정으로 사용되는 모든 사용자 계정에 대해 쉽게 식별할 수 있는 명명 규칙을 만드는 것이 좋습니다.

예를 들어 “service-”를 접두사로 추가하고 서비스 이름을 “HRDataConnector”로 추가합니다.

아래 지표 중 일부를 사용하여 해당 서비스 계정을 찾을 수 있지만 모두 찾지는 못할 수 있습니다.

* 위임용으로 트러스트 된 계정입니다.

* 서비스 사용자 이름이 있는 계정입니다.

* 암호가 만료되지 않도록 설정된 계정입니다.

다음 PowerShell 명령을 실행하여 서비스에 대해 생성된 온-프레미스 사용자 계정을 찾을 수 있습니다.

### <a name="find-accounts-trusted-for-delegation"></a>위임용으로 트러스트 된 계정 찾기

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>서비스 사용자 이름이 있는 계정 찾기

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>암호가 만료되지 않도록 설정된 계정 찾기

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


또한 중요한 리소스에 대한 액세스를 감사하고, SIEM(보안 정보 및 이벤트 관리) 시스템에 감사 로그를 보관할 수 있습니다. Azure Log Analytics 또는 Azure Sentinel과 같은 시스템을 사용하여 서비스 계정을 검색하고 분석할 수 있습니다.

## <a name="assess-security-of-on-premises-user-accounts"></a>온-프레미스 사용자 계정의 보안 평가

다음 기준을 사용하여 서비스 계정으로 사용되는 온-프레미스 사용자 계정의 보안을 평가합니다.

* 암호 관리 정책이 무엇인가요?

* 계정이 권한 있는 그룹의 멤버인가요?

* 계정에 중요한 리소스에 대한 읽기/쓰기 권한이 있나요?

### <a name="mitigate-potential-security-issues"></a>잠재적인 보안 문제 완화

다음 표는 잠재적인 보안 문제와 온-프레미스 사용자 계정에 대하여 해당되는 완화 방법을 보여줍니다.

| 보안 문제| 해결 방법 |
| - | - |
| 암호 관리|* 강력한 암호 요구 사항을 사용하여 정기적인 업데이트를 보장하는 강력한 프로세스에서 암호 복잡성 및 암호 변경을 제어하는지 확인합니다. <br> * 서비스 가동 중지 시간을 최소화하기 위해 암호 업데이트를 사용하여 암호 변경을 조정합니다. |
| 계정이 권한 있는 그룹의 멤버입니다.| 그룹 멤버 자격을 검토합니다. 권한 있는 그룹에서 계정을 제거합니다. 서비스를 실행하는 데 필요한 권리 및 권한만 계정에 부여합니다(서비스 공급 업체에 문의). 예를 들어, 로컬에서 로그인을 거부하거나 대화형 로그인을 거부할 수 있습니다. |
| 계정에 중요한 리소스에 대한 읽기/쓰기 권한이 있습니다.| 중요한 리소스에 대한 액세스를 감사합니다. 분석을 위해 SIEM(Azure Log Analytics 또는 Azure Sentinel)에 감사 로그를 보관합니다. 원치 않는 액세스 수준이 검색되는 경우 리소스 권한을 교정합니다. |


## <a name="move-to-more-secure-account-types"></a>더 안전한 계정 유형으로 이동

Microsoft는 고객이 온-프레미스 사용자 계정을 서비스 계정으로 사용하는 것을 권장하지 않습니다. 이러한 유형의 계정을 사용하는 모든 서비스에 대하여, gMSA 또는 sMSA를 사용하도록 구성될 수 있는지 평가합니다.

또한 보다 안전한 서비스 계정 유형을 사용할 수 있도록 서비스 자체를 Azure로 이동할 수 있는지 평가합니다. 

## <a name="next-steps"></a>다음 단계
서비스 계정 보안 설정에 대한 다음 문서를 참조하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [그룹 관리 서비스 계정 보안](service-accounts-group-managed.md)

* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)

* [컴퓨터 계정 보안](service-accounts-computer.md)

* [사용자 계정 보안](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)

 
