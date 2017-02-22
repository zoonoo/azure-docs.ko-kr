---
title: "Azure Active Directory에서 구성 가능한 토큰 수명 | Microsoft Docs"
description: "관리자와 개발자는 이 기능을 사용하여 Azure AD에서 발급된 토큰의 수명을 지정할 수 있습니다."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: b520b4672dd403981d218c9855c3beb09ef55021
ms.openlocfilehash: 6da28e6273d92445e4b14ea22752a6e59b1dd93a


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Azure Active Directory에서 구성 가능한 토큰 수명(공개 미리 보기)
> [!NOTE]
> 이 기능은 현재 공개 미리 보기로 제공되고 있습니다.  변경 내용을 되돌리거나 제거할 준비를 해야 합니다.  모든 사람이 공개 미리 보기 중에 사용해 볼 수 있도록 이 기능을 공개하지만 일반 공급 후에는 특정 측면에 [Azure AD Premium 구독](active-directory-get-started-premium.md)이 필요할 수 있습니다.
> 
> 

## <a name="introduction"></a>소개
관리자와 개발자는 이 기능을 사용하여 Azure AD에서 발급된 토큰의 수명을 지정할 수 있습니다. 토큰 수명은 조직의 모든 앱, 다중 테넌트(다중 조직) 응용 프로그램 또는 조직의 특정 서비스 주체에 대해 구성할 수 있습니다.

Azure AD에서 정책 개체는 개별 응용 프로그램 또는 조직의 모든 응용 프로그램에 적용되는 규칙 집합을 나타냅니다.  각 유형의 정책에는 할당된 개체에 적용되는 속성 집합이 포함된 고유한 구조가 있습니다.

정책은 조직의 기본값으로 지정할 수 있습니다. 따라서 이 정책은 더 높은 우선 순위의 정책으로 재정의되지 않는 한 해당 조직 내의 모든 응용 프로그램에 적용됩니다. 특정 응용 프로그램에 정책을 할당할 수도 있습니다. 우선 순위는 정책 유형에 따라 다릅니다.

새로 고침 토큰, 액세스 토큰, 세션 토큰 및 ID 토큰에 대한 토큰 수명 정책을 구성할 수 있습니다.

### <a name="access-tokens"></a>액세스 토큰
액세스 토큰은 클라이언트에서 보호된 리소스에 액세스하는 데 사용됩니다. 사용자, 클라이언트 및 리소스의 특정 조합에만 액세스 토큰을 사용할 수 있습니다. 액세스 토큰은 해지될 수 없으며 만료될 때까지 유효합니다. 액세스 토큰을 획득한 악의적인 행위자는 수명 범위 동안 이를 사용할 수 있습니다.  액세스 토큰의 수명을 조정하는 것은 시스템 성능을 개선하는 것과 사용자 계정이 비활성화된 후 클라이언트에서 액세스를 유지하는 기간을 늘리는 것 간의 절충 작업입니다.  시스템 성능을 개선하려면 클라이언트에서 새 액세스 토큰을 획득해야 하는 횟수를 줄이면 됩니다. 

### <a name="refresh-tokens"></a>새로 고침 토큰
클라이언트에서 보호된 리소스에 액세스하기 위해 액세스 토큰을 획득하는 경우 새로 고침 토큰과 액세스 토큰이 둘 다 제공됩니다. 새로 고침 토큰은 현재 액세스 토큰이 만료된 경우 새 액세스/새로 고침 토큰 쌍을 얻는 데 사용됩니다. 새로 고침 토큰은 사용자와 클라이언트의 조합에 바인딩됩니다. 이 토큰은 해지될 수 있으며 사용될 때마다 유효성이 검사됩니다.

비밀 클라이언트와 공용 클라이언트를 구분해야 합니다. 다양한 유형의 클라이언트에 대한 자세한 내용은 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)를 참조하세요.

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>기밀 클라이언트 새로 고침 토큰의 토큰 수명
기밀 클라이언트는 요청이 악의적인 행위자가 아니라 클라이언트 응용 프로그램에서 들어온 것임을 증명할 수 있도록 클라이언트 암호(비밀)를 안전하게 저장할 수 있는 응용 프로그램입니다. 예를 들어 웹앱은 웹 서버에 클라이언트 암호를 저장할 수 있으므로 기밀 클라이언트이며, 따라서 노출되지 않습니다. 이러한 흐름은 보다 안전하기 때문에 이러한 흐름에 발급된 새로 고침 토큰의 기본 수명은 더 길고 정책을 사용하여 변경할 수 없습니다.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>공용 클라이언트 새로 고침 토큰의 토큰 수명 

공용 클라이언트는 클라이언트 암호(비밀)를 안전하게 저장할 수 없습니다. 예를 들어 iOS/Android 앱은 리소스 소유자의 암호를 난독 처리할 수 없으므로 공용 클라이언트로 간주됩니다.  지정된 기간보다 오래된 공용 클라이언트의 새로 고침 토큰이 새 액세스/새로 고침 토큰 쌍을 얻는 것을 방지하기 위해 리소스에 대한 정책을 설정할 수 있습니다(새로 고침 토큰 최대 비활성 시간).  또한 정책을 사용하여 새로 고침 토큰이 더 이상 허용되지 않는 기간을 설정할 수 있습니다(새로 고침 토큰 최대 기간).  새로 고침 토큰의 수명을 조정하여 공용 클라이언트 응용 프로그램을 사용할 때 자동으로 재인증되는 대신 사용자가 자격 증명을 다시 입력해야 하는 경우 및 빈도를 제어할 수 있습니다.

### <a name="id-tokens"></a>ID 토큰
ID 토큰은 웹 사이트 및 네이티브 클라이언트에 전달되며, 사용자에 대한 프로필 정보를 포함합니다. ID 토큰은 사용자와 클라이언트의 특정 조합에 바인딩되며, 만료될 때까지 유효합니다.  일반적으로 웹 응용 프로그램은 응용 프로그램의 사용자 세션 수명을 해당 사용자에 대해 발급된 ID 토큰의 수명과 일치시킵니다.  ID 토큰의 수명을 조정하여 웹 응용 프로그램이 응용 프로그램 세션을 만료하는 빈도 및 사용자에게 Azure AD에 다시 인증(자동 또는 대화형)하도록 요구하는 빈도를 제어할 수 있습니다.

### <a name="single-sign-on-session-token"></a>Single Sign-On 세션 토큰
사용자가 Azure AD에 인증하고 "로그인 유지" 상자를 선택하면 사용자의 브라우저와 Azure AD에 Single Sign-On 세션이 설정됩니다.  Single Sign-On 세션 토큰은 쿠키 형식으로 이 세션을 나타냅니다. SSO 세션 토큰은 특정 리소스/클라이언트 응용 프로그램에 바인딩되지 않습니다. SSO 세션 토큰은 해지될 수 있으며 사용될 때마다 유효성이 검사됩니다.

SSO 세션 토큰에는 두 종류가 있습니다. 영구 세션 토큰은 브라우저에 의해 영구 쿠키로 저장되고, 비영구 세션 토큰은 세션 쿠키로 저장됩니다(브라우저가 닫히면 삭제됨).

비영구 세션 토큰의 수명은 24시간인 반면, 영구 토큰의 수명은 180일입니다. SSO 세션 토큰은 유효 기간 내에 언제든지 사용할 수 있으며, 유효 기간은 추가 24시간 또는 180일 동안 연장됩니다. 유효 기간 내에 SSO 세션 토큰을 사용하지 않으면 만료된 것으로 간주되며 더 이상 허용되지 않습니다. 

정책을 사용하여 첫 세션 토큰이 발급된 후 일정 기간이 지나면 세션 토큰이 더 이상 허용되지 않도록 설정할 수 있습니다(세션 토큰 최대 기간).  세션 토큰의 수명을 조정하여 웹 클라이언트 응용 프로그램을 사용할 때 자동으로 인증되는 대신 사용자가 자격 증명을 다시 입력해야 하는 경우 및 빈도를 제어할 수 있습니다.

### <a name="token-lifetime-policy-properties"></a>토큰 수명 정책 속성
토큰 수명 정책은 토큰 수명 규칙을 포함하는 정책 개체의 형식입니다.  정책의 속성은 지정된 토큰 수명을 제어하는 데 사용됩니다.  설정된 정책이 없는 경우 시스템에서 기본 수명값을 적용합니다.

### <a name="configurable-token-lifetime-properties"></a>구성 가능한 토큰 수명 속성
| 속성 | 정책 속성 문자열 | 영향 | 기본값 | 최소 | 최대 |
| --- | --- | --- | --- | --- | --- |
| 액세스 토큰 수명 |AccessTokenLifetime |액세스 토큰, ID 토큰, SAML2 토큰 |1시간 |10분 |1일 |
| 새로 고침 토큰 최대 비활성 시간 |MaxInactiveTime |새로 고침 토큰 |14일 |10분 |90일 |
| 단일 단계 새로 고침 토큰 최대 기간 |MaxAgeSingleFactor |새로 고침 토큰(모든 사용자) |90일 |10분 |Until-revoked* |
| 다단계 새로 고침 토큰 최대 기간 |MaxAgeMultiFactor |새로 고침 토큰(모든 사용자) |90일 |10분 |Until-revoked* |
| 단일 단계 세션 토큰 최대 기간 |MaxAgeSessionSingleFactor** |세션 토큰(영구 및 비영구적) |Until-revoked |10분 |Until-revoked* |
| 다단계 세션 토큰 최대 기간 |MaxAgeSessionMultiFactor*** |세션 토큰 (영구 및 비영구적) |Until-revoked |10분 |Until-revoked* |

* *이러한 특성에 대해 설정할 수 있는 명시적인 최대 기간은&365;일입니다.
* **MaxAgeSessionSingleFactor가 설정되지 않은 경우 이 값은 MaxAgeSingleFactor 값을 사용합니다. 두 매개 변수 모두 설정되지 않은 경우에는 이 속성에 기본값(Until-revoked)이 사용됩니다.
* ***MaxAgeSessionMultiFactor가 설정되지 않은 경우 이 값은 MaxAgeMultiFactor 값을 사용합니다. 두 매개 변수 모두 설정되지 않은 경우에는 이 속성에 기본값(Until-revoked)이 사용됩니다.

### <a name="exceptions"></a>예외
| 속성 | 영향 | 기본값 |
| --- | --- | --- |
| 새로 고침 토큰 최대 비활성 시간(해지 정보가 부족한 페더레이션된 사용자) |새로 고침 토큰(해지 정보가 부족한 페더레이션된 사용자에 대해 발급됨) |12시간 |
| 새로 고침 토큰 최대 비활성 시간(비밀 클라이언트) |새로 고침 토큰(비밀 클라이언트에 대해 발급됨) |90일 |
| 새로 고침 최대 기간(비밀 클라이언트에 대해 발급됨) |새로 고침 토큰(비밀 클라이언트에 대해 발급됨) |Until-revoked |

### <a name="priority-and-evaluation-of-policies"></a>정책의 우선 순위 및 평가
토큰 수명 정책을 만들어 특정 응용 프로그램, 조직 및 서비스 주체에 할당할 수 있습니다. 즉, 여러 정책을 특정 응용 프로그램에 적용할 수 있습니다. 적용되는 토큰 수명 정책은 다음 규칙을 따릅니다.

* 정책이 서비스 사용자에게 명시적으로 할당된 경우 해당 정책이 적용됩니다. 
* 서비스 주체에 명시적으로 할당된 정책이 없는 경우 서비스 주체의 상위 조직에 명시적으로 할당된 정책이 적용됩니다. 
* 서비스 주체 또는 조직에 명시적으로 할당된 정책이 없는 경우 응용 프로그램에 할당된 정책이 적용됩니다. 
* 서비스 주체, 조직 또는 응용 프로그램 개체에 할당된 정책이 없는 경우 기본값이 적용됩니다(위 표 참조).

Azure AD의 응용 프로그램 개체와 서비스 사용자 간 관계에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](active-directory-application-objects.md)를 참조하세요.

토큰의 유효성은 사용되는 시점에 평가됩니다. 액세스하는 응용 프로그램에 대한 우선 순위가 가장 높은 정책이 적용됩니다.

> [!NOTE]
> 예제
> 
> 사용자가 두 개의 웹 응용 프로그램(A와 B)에 액세스하려면 합니다. 
> 
> * 두 응용 프로그램은 모두 동일한 상위 조직에 있습니다. 
> * 세션 토큰 최대 기간이 8시간인 토큰 수명 정책 1이 상위 조직의 기본값으로 설정됩니다.
> * 웹 응용 프로그램 A는 어떤 정책에도 연결되지 않은 일반적인 용도의 웹 응용 프로그램입니다. 
> * 웹 응용 프로그램 B는 매우 중요한 프로세스에 사용되며, 해당 서비스 사용자는 세션 토큰 처리 기간이 30분인 토큰 수명 정책 2에 연결됩니다.
> 
> 오후 12시에 사용자는 새 브라우저 세션을 열고 웹 응용 프로그램 A에 액세스하려고 합니다. 사용자는 Azure AD로 리디렉션되며 로그인하라는 메시지가 표시됩니다. 그러면 세션 토큰이 있는 쿠키가 브라우저에서 삭제됩니다. 사용자는 응용 프로그램에 액세스할 수 있는 ID 토큰으로 웹 응용 프로그램 A로 다시 리디렉션됩니다.
> 
> 오후 12시 15분에 사용자는 웹 응용 프로그램 B에 액세스하려고 합니다. 브라우저는 세션 쿠키를 검색하는 Azure AD로 리디렉션합니다. 웹 응용 프로그램 B의 서비스 주체는 정책 2에 연결되지만 기본 정책 1이 적용되는 상위 조직의 일부이기도 합니다. 서비스 주체에게 연결된 정책이 조직 기본 정책보다 우선 순위가 높기 때문에 정책 2가 적용됩니다. 세션 토큰은 지난 30분 이내에 처음 발급되었기 때문에 유효한 것으로 간주됩니다. 사용자는 액세스 권한을 부여하는 ID 토큰으로 웹 응용 프로그램 B로 리디렉션됩니다.
> 
> 오후 1시에 사용자는 웹 응용 프로그램 A를 탐색하려고 합니다. 사용자는 Azure AD로 리디렉션됩니다. 웹 응용 프로그램 A는 어떤 정책에도 연결되어 있지 않지만 기본 정책 1을 사용하는 조직에 있기 때문에 이 정책이 적용됩니다. 세션 쿠키는 지난 8시간 이내에 처음 발급된 것으로 검색되며, 사용자는 인증할 필요가 없는 새 ID 토큰으로 웹 응용 프로그램 A로 자동으로 다시 리디렉션됩니다.
> 
> 사용자는 웹 응용 프로그램 B에 즉시 액세스하려고 합니다. 사용자는 Azure AD로 리디렉션됩니다. 이전과 마찬가지로 정책 2가 적용됩니다. 토큰이 발급된지 30분이 넘은 경우 사용자에게 자격 증명을 다시 입력하라는 메시지가 표시되며, 새로운 세션 및 ID 토큰이 발급됩니다. 그런 다음 사용자는 웹 응용 프로그램 B에 액세스할 수 있습니다.
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>구성 가능한 정책 속성: 세부 정보
### <a name="access-token-lifetime"></a>액세스 토큰 수명
**문자열:** AccessTokenLifetime

**영향:** 액세스 토큰, ID 토큰

**요약:** 이 정책은 이 리소스에 대한 액세스 및 ID 토큰이 유효한 것으로 간주되는 기간을 제어합니다. 액세스 토큰 수명을 줄이면 악의적인 행위자가 연장된 기간 동안(해지할 수 없음) 액세스하거나 ID 토큰을 사용할 위험이 줄어들며 토큰을 더 자주 교체해야 하므로 성능에 저하됩니다.

### <a name="refresh-token-max-inactive-time"></a>새로 고침 토큰 최대 비활성 시간
**문자열:** MaxInactiveTime

**영향:** 새로 고침 토큰

**요약:** 이 정책은 클라이언트에서 이 리소스에 액세스하려고 할 때 새 액세스/새로 고침 토큰을 검색하는 데 새로 고침 토큰을 더 이상 사용할 수 없을 때까지의 기간을 제어합니다. 새로 고침 토큰이 사용될 때 일반적으로 새로운 새로 고침 토큰이 반환되기 때문에 클라이언트는 이 정책에 따라 액세스가 방지되기 전에 지정된 기간 동안 현재 새로 고침 토큰을 사용하여 리소스에 연결해서는 안 됩니다. 

이 정책은 클라이언트에서 활성화되지 않은 사용자가 새로운 새로 고침 토큰을 검색하려면 다시 인증해야 하도록 합니다. 

새로 고침 토큰 최대 비활성 시간은 단일 단계 토큰 최대 기간 및 다단계 새로 고침 토큰 최대 기간보다 낮은 값으로 설정해야 합니다.

### <a name="single-factor-refresh-token-max-age"></a>단일 단계 새로 고침 토큰 최대 기간
**문자열:** MaxAgeSingleFactor

**영향:** 새로 고침 토큰

**요약:** 이 정책은 사용자가 단일 단계만 사용하여 마지막으로 인증에 성공한 후 계속해서 새로 고침 토큰을 사용하여 새로운 액세스/새로 고침 토큰을 얻을 수 있는 기간을 제어합니다. 사용자가 인증하고 새로운 새로 고침 토큰을 받은 후에는 지정된 기간 동안 새로 고침 토큰을 사용할 수 있습니다(현재 새로 고침 토큰이 해지되거나 비활성 시간보다 오랫동안 사용되지 않은 상태로 남아 있지 않은 경우). 이때 사용자는 새로운 새로 고침 토큰을 받기 위해 다시 인증해야 합니다. 

최대 기간을 줄이면 사용자가 더 자주 인증해야 합니다. 단일 단계 인증은 다단계 인증보다 안전하지 않은 것으로 간주되므로 이 정책을 다단계 새로 고침 토큰 최대 기간 정책보다 작거나 같은 값으로 설정하는 것이 좋습니다.

### <a name="multi-factor-refresh-token-max-age"></a>다단계 새로 고침 토큰 최대 기간
**문자열:** MaxAgeMultiFactor

**영향:** 새로 고침 토큰

**요약:** 이 정책은 사용자가 다단계를 사용하여 마지막으로 인증에 성공한 후 계속해서 새로 고침 토큰을 사용하여 새로운 액세스/새로 고침 토큰 쌍을 얻을 수 있는 기간을 제어합니다. 사용자가 인증하고 새로운 새로 고침 토큰을 받은 후에는 지정된 기간 동안 새로 고침 토큰을 사용할 수 있습니다(현재 새로 고침 토큰이 해지되거나 비활성 시간보다 오랫동안 사용되지 않은 상태로 남아 있지 않은 경우). 이때 사용자는 새로운 새로 고침 토큰을 받기 위해 다시 인증해야 합니다. 

최대 기간을 줄이면 사용자가 더 자주 인증해야 합니다. 단일 단계 인증은 다단계 인증보다 안전하지 않은 것으로 간주되므로 이 정책을 단일 단계 새로 고침 토큰 최대 기간 정책보다 크거나 같은 값으로 설정하는 것이 좋습니다.

### <a name="single-factor-session-token-max-age"></a>단일 단계 세션 토큰 최대 기간
**문자열:** MaxAgeSessionSingleFactor

**영향:** 세션 토큰(영구 및 비영구)

**요약:** 이 정책은 사용자가 단일 단계만 사용하여 마지막으로 인증에 성공한 후 계속해서 세션 토큰을 사용하여 새로운 ID 및 세션 토큰을 얻을 수 있는 기간을 제어합니다. 사용자가 인증하고 새로운 세션 토큰을 받은 후에는 지정된 기간 동안 세션 토큰을 사용할 수 있습니다(현재 세션 토큰이 해지되거나 만료되지 않은 경우). 이때 사용자는 새로운 세션 토큰을 받기 위해 다시 인증해야 합니다. 

최대 기간을 줄이면 사용자가 더 자주 인증해야 합니다. 단일 단계 인증은 다단계 인증보다 안전하지 않은 것으로 간주되므로 이 정책을 다단계 세션 토큰 최대 기간 정책보다 작거나 같은 값으로 설정하는 것이 좋습니다.

### <a name="multi-factor-session-token-max-age"></a>다단계 세션 토큰 최대 기간
**문자열:** MaxAgeSessionMultiFactor

**영향:** 세션 토큰(영구 및 비영구)

**요약:** 이 정책은 사용자가 다단계를 사용하여 마지막으로 인증에 성공한 후 계속해서 세션 토큰을 사용하여 새로운 ID 및 세션 토큰을 얻을 수 있는 기간을 제어합니다. 사용자가 인증하고 새로운 세션 토큰을 받은 후에는 지정된 기간 동안 세션 토큰을 사용할 수 있습니다(현재 세션 토큰이 해지되거나 만료되지 않은 경우). 이때 사용자는 새로운 세션 토큰을 받기 위해 다시 인증해야 합니다. 

최대 기간을 줄이면 사용자가 더 자주 인증해야 합니다. 단일 단계 인증은 다단계 인증보다 안전하지 않은 것으로 간주되므로 이 정책을 단일 단계 세션 토큰 최대 기간 정책보다 크거나 같은 값으로 설정하는 것이 좋습니다.

## <a name="sample-token-lifetime-policies"></a>샘플 토큰 수명 정책
앱, 서비스 주체 및 전체 조직의 토큰 수명을 만들고 관리할 수 있다는 것은 Azure AD에서 가능한 모든 종류의 새로운 시나리오를 보여 줍니다.  새 규칙을 적용하는 데 도움이 되는 몇 가지 일반적인 정책 시나리오를 살펴보겠습니다.

* 토큰 수명
* 토큰 최대 비활성 시간
* 토큰 최대 기간

다음을 비롯한 몇 가지 시나리오를 살펴보겠습니다.

* 조직의 기본 정책 관리
* 웹 로그인에 대한 정책 만들기
* Web API를 호출하는 네이티브 앱에 대한 정책 만들기
* 고급 정책 관리 

### <a name="prerequisites"></a>필수 조건
샘플 시나리오에서는 앱, 서비스 주체 및 전체 조직에 대한 정책을 만들고, 업데이트하고, 연결하고, 삭제합니다.  Azure AD를 처음 접하는 경우 이러한 샘플을 진행하기 전에 [이 문서](active-directory-howto-tenant.md)를 확인하는 것이 시작하는 데 도움이 됩니다.  

1. 시작하려면 최신 [Azure AD PowerShell Cmdlet 미리 보기](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드합니다. 
2. Azure AD PowerShell Cmdlet을 다운로드한 후에는 Connect 명령을 실행하여 Azure AD 관리 계정에 로그인합니다. 새 세션을 시작할 때마다 이 작업을 수행해야 합니다.
   
     Connect-AzureAD -Confirm
3. 다음 명령을 실행하여 조직에서 만든 정책을 모두 확인합니다.  이 명령은 다음 시나리오에서 대부분의 작업 후에 사용됩니다.  또한 정책의 **개체 ID**를 가져오는 데 도움이 됩니다. 
   
     Get-AzureADPolicy

### <a name="sample-managing-a-organizations-default-policy"></a>샘플: 조직의 기본 정책 관리
이 샘플에서는 사용자가 전체 조직에서 더 적은 빈도로 로그인할 수 있도록 하는 정책을 만듭니다. 

이렇게 하려면 전체 조직에 적용되는 단일 단계 새로 고침 토큰에 대한 토큰 수명 정책을 만듭니다. 이 정책은 조직의 모든 응용 프로그램과 아직 정책이 설정되지 않은 각 서비스 주체에 적용됩니다. 

1. **토큰 수명 정책을 만듭니다.** 

단일 단계 새로 고침 토큰을 "until-revoked"로 설정합니다. 이는 액세스가 해지될 때까지 만료되지 않음을 의미합니다.  만들려는 정책 정의는 다음과 같습니다.

        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

이제 다음 명령을 실행하여 이 정책을 만듭니다. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName OrganizationDefaultPolicyScenario -IsOrganizationDefault $true -Type TokenLifetimePolicy

새 정책을 보고 해당 ObjectID를 가져오려면 다음 명령을 실행합니다.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **정책을 업데이트합니다.**

첫 번째 정책이 서비스에 필요한 것만큼 엄격하지 않다고 결정하여 단일 단계 새로 고침 토큰을 2일 후에 만료되도록 설정하기로 결정했습니다. 다음 명령을 실행합니다. 

    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName OrganizationDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")

&nbsp;&nbsp;3. **완료되었습니다!** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>샘플: 웹 로그인에 대한 정책 만들기
이 샘플에서는 사용자가 웹앱에 보다 자주 인증하도록 요구하는 정책을 만듭니다. 이 정책은 액세스/ID 토큰의 수명 및 다단계 세션 토큰의 최대 기간을 웹앱의 서비스 사용자로 설정합니다.

1. **토큰 수명 정책을 만듭니다.**

웹 로그인에 대한 이 정책은 액세스/ID 토큰 수명 및 최대 단일 단계 세션 토큰 기간을 2시간으로 설정합니다.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsOrganizationDefault $false -Type TokenLifetimePolicy

새 정책을 보고 해당 ObjectID를 가져오려면 다음 명령을 실행합니다.

    Get-AzureADPolicy
&nbsp;&nbsp;2.    **서비스 사용자에게 정책을 할당합니다.**

이 새 정책을 서비스 사용자와 연결합니다.  서비스 사용자의 **ObjectId**에 액세스하는 방법도 필요합니다. [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)를 쿼리하거나 [Graph 탐색기 도구](https://graphexplorer.cloudapp.net/)로 이동하고 Azure AD 계정에 로그인하여 조직의 모든 서비스 주체를 확인할 수 있습니다. 

**ObjectId**가 있는 경우 다음 명령을 실행합니다.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3.    **완료되었습니다!** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>샘플: Web API를 호출하는 네이티브 앱에 대한 정책 만들기
이 샘플에서는 사용자가 인증해야 하는 횟수를 줄이고 다시 인증하지 않고도 비활성 상태로 있을 수 있는 기간을 늘리는 정책을 만듭니다. 이 정책은 Web API에 적용됩니다. 즉, 네이티브 앱에서 Web API를 리소스로 요청하는 경우 이 정책이 적용됩니다.

1. **토큰 수명 정책을 만듭니다.** 

이 명령은 Web API에 대한 엄격한 정책을 만듭니다. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsOrganizationDefault $false -Type TokenLifetimePolicy

새 정책을 보고 해당 ObjectID를 가져오려면 다음 명령을 실행합니다.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **Web API에 정책을 할당합니다.**

이 새 정책을 응용 프로그램과 연결합니다.  응용 프로그램의 **ObjectId**에 액세스하는 방법도 필요합니다. 앱의 **ObjectId**를 찾는 가장 좋은 방법은 [Azure Portal](https://portal.azure.com/)을 사용하는 것입니다. 

**ObjectId**가 있는 경우 다음 명령을 실행합니다.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **완료되었습니다!** 

### <a name="sample-managing-an-advanced-policy"></a>샘플: 고급 정책 관리
이 샘플에서는 우선 순위 시스템이 작동하는 방식 및 여러 개체에 적용되는 여러 정책을 관리하는 방법을 보여 주기 위해 몇 가지 정책을 만듭니다. 이는 위에서 설명한 정책의 우선 순위에 몇 가지 정보를 얻고 보다 복잡한 시나리오를 관리하는데 도움이 됩니다. 

1. **토큰 수명 정책을 만듭니다.**

아주 간단합니다. 단일 단계 새로 고침 토큰 수명을 30일로 설정하는 조직 기본 정책을 만들었습니다. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsOrganizationDefault $true -Type TokenLifetimePolicy
새 정책을 보고 해당 ObjectID를 가져오려면 다음 명령을 실행합니다.

    Get-AzureADPolicy

&nbsp;&nbsp;2.    **서비스 사용자에게 정책을 할당합니다.**

이제 전체 조직에 대한 정책이 있습니다.  특정 서비스 주체에 대해 이 30일 정책을 유지하지만 조직 기본 정책을 "until-revoked"의 상한으로 변경하려고 한다고 가정해 보겠습니다. 

먼저 이 새 정책을 서비스 사용자와 연결합니다.  서비스 사용자의 **ObjectId**에 액세스하는 방법도 필요합니다. [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)를 쿼리하거나 [Graph 탐색기 도구](https://graphexplorer.cloudapp.net/)로 이동하고 Azure AD 계정에 로그인하여 조직의 모든 서비스 주체를 확인할 수 있습니다. 

**ObjectId**가 있는 경우 다음 명령을 실행합니다.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.    **다음 명령을 사용하여 IsOrganizationDefault 플래그를 false로 설정합니다.** 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsOrganizationDefault $false
&nbsp;&nbsp;4.    **새 조직 기본 정책 만들기**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsOrganizationDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5.     **완료되었습니다!** 

이제 서비스 주체에 연결된 원래 정책과 조직 기본 정책으로 설정된 새 정책이 있습니다.  서비스 주체에 적용되는 정책은 조직 기본 정책보다 우선한다는 점을 기억해야 합니다. 

## <a name="cmdlet-reference"></a>Cmdlet 참조
### <a name="manage-policies"></a>정책 관리
다음 cmdlet을 사용하여 정책을 관리할 수 있습니다.</br></br>

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy
새 정책을 만듭니다.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type> 

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -Definition |정책의 모든 규칙을 포함하는 문자열로 변환된 JSON의 배열입니다. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -DisplayName |정책 이름의 문자열입니다. |-DisplayName MyTokenPolicy |
| -IsOrganizationDefault |true이면 정책을 조직의 기본 정책으로 설정하고, false이면 아무 작업도 수행하지 않습니다. |-IsOrganizationDefault $true |
| -Type |정책의 유형입니다. 토큰 수명의 경우 항상 "TokenLifetimePolicy"를 사용합니다. |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [선택 사항] |정책에 대한 대체 ID를 설정합니다. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
모든 AzureAD 정책 또는 지정된 정책을 가져옵니다. 

    Get-AzureADPolicy 

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId [선택 사항] |가져오려는 정책의 개체 ID입니다. |-ObjectId &lt;정책의 ObjectID&gt; |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
정책에 연결된 모든 앱 및 서비스 사용자를 가져옵니다.

    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |가져오려는 정책의 개체 ID입니다. |-ObjectId &lt;정책의 ObjectID&gt; |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
기존 정책을 업데이트합니다.

    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |가져오려는 정책의 개체 ID입니다. |-ObjectId &lt;정책의 ObjectID&gt; |
| -DisplayName |정책 이름의 문자열입니다. |-DisplayName MyTokenPolicy |
| -Definition [선택 사항] |정책의 모든 규칙을 포함하는 문자열로 변환된 JSON의 배열입니다. |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -IsOrganizationDefault [선택 사항] |true이면 정책을 조직의 기본 정책으로 설정하고, false이면 아무 작업도 수행하지 않습니다. |-IsOrganizationDefault $true |
| -Type [선택 사항] |정책의 유형입니다. 토큰 수명의 경우 항상 "TokenLifetimePolicy"를 사용합니다. |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [선택 사항] |정책에 대한 대체 ID를 설정합니다. |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
지정된 정책을 삭제합니다.

     Remove-AzureADPolicy -ObjectId <object id of policy>

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |가져오려는 정책의 개체 ID입니다. |-ObjectId &lt;정책의 ObjectID&gt; |

</br></br>

### <a name="application-policies"></a>응용 프로그램 정책
응용 프로그램 정책에 사용할 수 있는 cmdlet은 다음과 같습니다.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
지정된 정책을 응용 프로그램에 연결합니다.

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |응용 프로그램의 개체 ID입니다. |-ObjectId &lt;응용 프로그램의 ObjectID&gt; |
| -RefObjectId |정책의 개체 ID입니다. |-RefObjectId &lt;정책의 ObjectID&gt; |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
응용 프로그램에 할당된 정책을 가져옵니다.

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |응용 프로그램의 개체 ID입니다. |-ObjectId &lt;응용 프로그램의 ObjectID&gt; |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
응용 프로그램에서 정책을 제거합니다.

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |응용 프로그램의 개체 ID입니다. |-ObjectId &lt;응용 프로그램의 ObjectID&gt; |
| -PolicyId |정책의 ObjectId입니다. |-PolicyId &lt;정책의 ObjectID&gt; |

</br></br>

### <a name="service-principal-policies"></a>서비스 사용자 정책
서비스 사용자 정책에 사용할 수 있는 cmdlet은 다음과 같습니다.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
지정된 정책을 서비스 사용자에게 연결합니다.

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |응용 프로그램의 개체 ID입니다. |-ObjectId &lt;응용 프로그램의 ObjectID&gt; |
| -RefObjectId |정책의 개체 ID입니다. |-RefObjectId &lt;정책의 ObjectID&gt; |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
지정된 서비스 사용자에게 연결된 모든 정책을 가져옵니다.

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |응용 프로그램의 개체 ID입니다. |-ObjectId &lt;응용 프로그램의 ObjectID&gt; |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
지정된 서비스 사용자에서 정책을 제거합니다.

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>

| 매개 변수 | 설명 | 예제 |
| --- | --- | --- |
| -ObjectId |응용 프로그램의 개체 ID입니다. |-ObjectId &lt;응용 프로그램의 ObjectID&gt; |
| -PolicyId |정책의 ObjectId입니다. |-PolicyId &lt;정책의 ObjectID&gt; |




<!--HONumber=Jan17_HO3-->


