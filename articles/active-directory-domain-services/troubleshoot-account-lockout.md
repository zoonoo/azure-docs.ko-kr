---
title: Azure AD 도메인 서비스의 계정 잠금 문제 해결 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스에서 사용자 계정을 잠그게 하는 일반적인 문제를 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 2e274aa353f6c3e485ae10a6a67ee2940eb88b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246322"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인의 계정 잠금 문제 해결

반복되는 악의적인 로그인 시도를 방지하기 위해 Azure AD DS는 정의된 임계값 이후에 계정을 잠급합니다. 이 계정 잠금은 로그인 공격 인시던트 없이 실수로 발생할 수도 있습니다. 예를 들어 사용자가 잘못된 암호를 반복적으로 입력하거나 서비스가 이전 암호를 사용하려고 하면 계정이 잠깁니다.

이 문제 해결 문서에서는 계정 잠금이 발생하는 이유와 동작을 구성하는 방법 및 보안 감사를 검토하여 잠금 이벤트를 해결하는 방법을 설명합니다.

## <a name="what-is-an-account-lockout"></a>계정 잠금이란 무엇입니까?

실패한 로그인 시도에 대한 정의된 임계값이 충족되면 Azure AD DS의 사용자 계정이 잠깁니다. 이 계정 잠금 동작은 자동화된 디지털 공격을 나타낼 수 있는 반복적인 무차별 암호 대입 로그인 시도로부터 사용자를 보호하기 위해 고안되었습니다.

**기본적으로 2분 동안 5번의 잘못된 암호 시도가 있는 경우 계정은 30분 동안 잠깁니다.**

기본 계정 잠금 임계값은 세분화된 암호 정책을 사용하여 구성됩니다. 특정 요구 사항 집합이 있는 경우 이러한 기본 계정 잠금 임계값을 재정의할 수 있습니다. 그러나 계정 잠금 수를 줄이려면 임계값 제한을 늘리지 않는 것이 좋습니다. 먼저 계정 잠금 동작의 원인을 해결합니다.

### <a name="fine-grained-password-policy"></a>세분화된 암호 정책

세분화된 암호 정책(FGPP)을 사용하면 도메인의 다른 사용자에게 암호 및 계정 잠금 정책에 대한 특정 제한을 적용할 수 있습니다. FGPP는 Azure AD DS에서 만든 사용자에게만 영향을 줍니다. Azure AD에서 Azure AD DS 관리 도메인에 동기화된 클라우드 사용자 및 도메인 사용자는 암호 정책의 영향을 받지 않습니다.

정책은 Azure AD DS 관리 도메인의 그룹 연결을 통해 배포되며 변경 한 내용은 다음 사용자 로그인시 적용됩니다. 정책을 변경해도 이미 잠긴 사용자 계정의 잠금이 해제되지 않습니다.

세분화된 암호 정책에 대한 자세한 내용은 [암호 및 계정 잠금 정책 구성을][configure-fgpp]참조하십시오.

## <a name="common-account-lockout-reasons"></a>일반적인 계정 잠금 이유

악의적인 의도 나 요인없이 계정을 잠그는 가장 일반적인 이유는 다음과 같은 시나리오를 포함합니다.

* **사용자가 자신을 잠급전지 못하게 했습니다.**
    * 최근 암호가 변경된 후 사용자가 이전 암호를 계속 사용합니까? 2분 동안 5번의 실패한 시도의 기본 계정 잠금 정책은 사용자가 실수로 이전 암호를 다시 시도하여 발생할 수 있습니다.
* **이전 암호가 있는 응용 프로그램 이나 서비스가 있습니다.**
    * 응용 프로그램이나 서비스에서 계정을 사용하는 경우 해당 리소스는 이전 암호를 사용하여 반복적으로 로그인을 시도할 수 있습니다. 이 동작으로 인해 계정이 잠깁니다.
    * 여러 다른 응용 프로그램 이나 서비스에서 계정 사용을 최소화 하 고 자격 증명이 사용 되는 위치를 기록 하려고 합니다. 계정 암호가 변경된 경우 그에 따라 연결된 응용 프로그램 이나 서비스를 업데이트합니다.
* **다른 환경에서 암호가 변경되었으며 새 암호가 아직 동기화되지 않았습니다.**
    * 온프레미 AD DS 환경과 같이 Azure AD DS 외부에서 계정 암호가 변경되는 경우 암호 변경이 Azure AD를 통해 Azure AD DS로 동기화되는 데 몇 분 정도 걸릴 수 있습니다.
    * 암호 동기화 프로세스가 완료되기 전에 Azure AD DS를 통해 리소스에 로그인하려고 시도하는 사용자는 해당 계정이 잠깁니다.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>보안 감사를 통해 계정 잠금 문제 해결

계정 잠금 이벤트가 발생하고 어디에서 발생하는지 문제를 해결하려면 [Azure AD DS에 대한 보안 감사를 사용하도록 설정합니다.][security-audit-events] 감사 이벤트는 기능을 사용하도록 설정한 시점부터만 캡처됩니다. 이상적으로는 문제 해결을 위해 계정 잠금 문제가 발생하기 *전에* 보안 감사를 사용하도록 설정해야 합니다. 사용자 계정에 잠금 문제가 반복적으로 발생하는 경우 다음에 상황이 발생할 때 보안 감사를 준비할 수 있습니다.

보안 감사를 사용하도록 설정한 후 다음 샘플 쿼리에서는 *계정 잠금 이벤트,* 코드 *4740을*검토하는 방법을 보여 주며 있습니다.

지난 7일 동안의 모든 계정 잠금 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

*driley라는*계정의 마지막 7일 동안의 모든 계정 잠금 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

2019년 6월 26일 오전 9시 사이에 발생한 모든 계정 잠금 이벤트를 확인합니다. 2019년 7월 1일 자정, 날짜와 시간으로 오름차순 정렬:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>다음 단계

계정 잠금 임계값을 조정하기 위한 세분화된 암호 정책에 대한 자세한 내용은 [암호 및 계정 잠금 정책 구성을][configure-fgpp]참조하십시오.

VM을 Azure AD DS 관리 도메인에 가입하는 데 여전히 문제가 있는 경우 [도움말을 찾아 Azure Active Directory 에 대한 지원 티켓을 엽니다.][azure-ad-support]

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
