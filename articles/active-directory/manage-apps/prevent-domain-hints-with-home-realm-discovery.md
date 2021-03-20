---
title: 홈 영역 검색 정책을 사용 하 여 Azure AD에서 로그인 자동 가속화 방지
description: 페더레이션 IDPs로 domain_hint 자동 가속을 방지 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589381"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>사용자가 홈 영역 검색 정책을 사용 하 여 로그인 하는 동안 페더레이션된 IDP 자동 가속 사용 안 함

Hrd ( [홈 영역 검색 정책](/graph/api/resources/homeRealmDiscoveryPolicy) )은 관리자에 게 사용자가 인증 하는 방법 및 위치를 제어 하는 여러 방법을 제공 합니다. `domainHintPolicy`HRD 정책의 섹션은 AZURE AD 로그인 페이지를 항상 방문 하 고 도메인 힌트로 인해 페더레이션된 IDP로 자동 가속화 되지 않도록 하 여 페더레이션된 사용자를 [FIDO](../authentication/howto-authentication-passwordless-security-key.md)와 같은 클라우드 관리 자격 증명으로 마이그레이션하는 데 사용 됩니다.

이 정책은 응용 프로그램에서 관리자가 로그인 중에 도메인 힌트 추가를 제어 하거나 업데이트할 수 없는 경우에 필요 합니다.  예를 들어는 `outlook.com/contoso.com` `&domain_hint=contoso.com` 도메인에 대 한 페더레이션된 IDP 직접 사용자를 자동으로 가속화 하기 위해 매개 변수가 추가 된 로그인 페이지로 사용자를 보냅니다 `contoso.com` . 페더레이션된 IDP로 전송 된 관리 되는 자격 증명을 가진 사용자는 관리 되는 자격 증명을 사용 하 여 로그인 할 수 없으므로 보안 및 사용자가 임의 로그인 환경을 사용 하는 경우를 줄일 수 관리자가 관리 되는 자격 증명을 롤아웃하는 경우에도 사용자가 관리 되는 자격 증명을 항상 사용할 수 있도록 [이 정책을 설정 해야](#suggested-use-within-a-tenant) 합니다.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy 세부 정보

HRD 정책의 DomainHintPolicy 섹션은 관리자가 도메인 힌트 사용에서 특정 도메인 및 응용 프로그램을 옵트아웃 (opt out) 할 수 있도록 하는 JSON 개체입니다.  이를 통해 Azure AD 로그인 페이지가 `domain_hint` 로그인 요청에 대 한 매개 변수가 없는 것 처럼 동작 하도록 지시할 수 있습니다.

### <a name="the-respect-and-ignore-policy-sections"></a>정책 고려 및 무시 섹션

|섹션 | 의미 | 값 |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |이 도메인 힌트가 요청에서 전송 되 면이를 무시 합니다. |도메인 주소의 배열입니다 (예: `contoso.com` ). 도 지원 `all_domains`|
|`RespectDomainHintForDomains`| 이 도메인 힌트가 요청에서 전송 된 경우에도이 도메인 힌트가 `IgnoreDomainHintForApps` 요청에서 앱이 자동으로 가속 되지 않음을 나타내는 경우에도이를 준수 합니다. 네트워크 내에서 사용 중단 도메인 힌트의 출시 속도를 저하 하는 데 사용 됩니다. 일부 도메인은 여전히 가속화 되어야 함을 나타낼 수 있습니다. | 도메인 주소의 배열입니다 (예: `contoso.com` ). 도 지원 `all_domains`|
|`IgnoreDomainHintForApps`| 이 응용 프로그램의 요청에 도메인 힌트가 제공 되는 경우이를 무시 합니다. | 응용 프로그램 Id (Guid)의 배열입니다. 도 지원 `all_apps`|
|`RespectDomainHintForApps` |이 응용 프로그램의 요청이 도메인 힌트와 함께 제공 되는 경우이 도메인을 포함 하는 경우에도이를 준수 합니다 `IgnoreDomainHintForDomains` . 도메인 힌트가 없으면 중단을 발견할 경우 일부 앱이 계속 작동 하는지 확인 하는 데 사용 됩니다. | 응용 프로그램 Id (Guid)의 배열입니다. 도 지원 `all_apps`|

### <a name="policy-evaluation"></a>정책 평가

DomainHintPolicy 논리는 도메인 힌트가 포함 된 들어오는 각 요청에서 실행 되 고 요청에 포함 된 두 개의 데이터 (도메인 힌트의 도메인 및 클라이언트 ID (앱))에 따라 가속화 됩니다. 도메인 또는 앱에 대 한 "관점"은 지정 된 도메인 또는 응용 프로그램에 대 한 도메인 힌트를 "무시" 하는 명령 보다 우선적으로 적용 됩니다.

1. 도메인 힌트 정책이 없는 경우 또는 4 개 섹션에서 언급 된 앱 또는 도메인 힌트를 참조 하지 않는 경우 [HRD 정책의 나머지가 평가](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies)됩니다.
1. 또는 섹션 중 하나 (또는 둘 다 `RespectDomainHintForApps` ) `RespectDomainHintForDomains` 가 요청에 앱 또는 도메인 힌트를 포함 하는 경우 요청 된 대로 사용자가 페더레이션된 IDP 자동 가속화 됩니다.
1. 또는 중 하나 (또는 둘 다) `IgnoreDomainHintsForApps` 가 `IgnoreDomainHintsForDomains` 요청에서 앱 또는 도메인 힌트를 참조 하 고 "적용" 섹션에서 참조 하지 않는 경우 요청을 자동으로 가속화 하지 않으며 사용자는 Azure AD 로그인 페이지에서 사용자 이름을 제공 하는 상태로 유지 됩니다.

사용자가 로그인 페이지에서 사용자 이름을 입력 하면 관리 되는 자격 증명 (등록 된 경우)을 사용할 수 있습니다.  관리 되는 자격 증명을 사용 하지 않거나 등록 된 자격 증명을 사용 하지 않도록 선택 하는 경우 일반적으로 자격 증명 항목에 대해 페더레이션된 IDP로 이동 합니다.

## <a name="suggested-use-within-a-tenant"></a>테 넌 트 내에서 제안 사용

페더레이션된 도메인의 관리자는 4 단계 계획에서 HRD 정책의이 섹션을 설정 해야 합니다. 이 계획의 목표는 궁극적으로 테 넌 트의 모든 사용자가 도메인 또는 응용 프로그램에 관계 없이 관리 되는 자격 증명을 사용할 수 있도록 하 고, 사용에 하드 종속성이 있는 앱을 저장 하는 것입니다 `domain_hint` .  이 계획을 통해 관리자는 이러한 앱을 찾고, 새 정책에서 예외를 제외 하 고, 테 넌 트의 나머지 부분에 대 한 변경 내용을 계속 롤아웃할 수 있습니다.

1. 처음에이 변경 내용을 롤백할 도메인을 선택 합니다.  이는 테스트 도메인 이므로 UX에서 변경 내용에 더 receptive 수 있는 하나를 선택 합니다 (즉, 다른 로그인 페이지 보기).  그러면이 도메인 이름을 사용 하는 모든 응용 프로그램에서 모든 도메인 힌트가 무시 됩니다. 테 넌 트에서이 정책을 [설정](#configuring-policy-through-graph-explorer) 합니다. 기본 hrd 정책:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. 테스트 도메인 사용자의 의견을 수집 합니다. 이러한 변경의 결과로 중단 된 응용 프로그램에 대 한 세부 정보를 수집 합니다 .이는 도메인 힌트 사용에 대 한 종속성을 가지 며 업데이트 해야 합니다. 지금은 섹션에 추가 합니다 `RespectDomainHintForApps` .

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. 새 도메인에 대 한 정책 출시를 계속 확장 하 여 더 많은 피드백을 수집 합니다.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. 롤아웃 대상 모든 도메인을 완료 하 여 계속 가속화 해야 하는 모든 도메인을 제외.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4 단계를 완료 한 후에는 `guestHandlingDomain.com` 도메인 힌트가 페더레이션 IDP 자동 가속화 되는 경우를 제외 하 고의 모든 사용자가 AZURE AD 로그인 페이지에서 로그인 할 수 있습니다.  이에 대 한 예외는 로그인을 요청 하는 앱이 제외 된 앱 인 경우 해당 앱에 대해 모든 도메인 힌트가 계속 허용 되는 경우입니다.

## <a name="configuring-policy-through-graph-explorer"></a>그래프 탐색기를 통해 정책 구성

Microsoft Graph를 사용 하 여 평소와 같이 [Hrd 정책을](/graph/api/resources/homeRealmDiscoveryPolicy) 설정 합니다.  

1. [그래프 탐색기](https://developer.microsoft.com/graph/graph-explorer)에서 정책. ReadWrite 구성 권한을 부여 합니다.  
1. URL 사용 `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. 이 URL에 새 정책을 게시 하거나 `/policies/homerealmdiscoveryPolicies/{policyID}` 기존 정책을 덮어쓰는 경우 패치 합니다.

게시 또는 패치 내용:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

그래프를 사용 하는 경우 슬래시를 사용 하 여 JSON 섹션을 이스케이프 해야 합니다 `Definition` .  

`isOrganizationDefault` 는 true 여야 하지만 displayName 및 정의는 변경 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [암호 없는 보안 키 로그인 사용](../authentication/howto-authentication-passwordless-security-key.md)
* [Microsoft Authenticator 앱에서 암호 없는 로그인을 사용 하도록 설정](../authentication/howto-authentication-passwordless-phone.md)