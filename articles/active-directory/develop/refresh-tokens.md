---
title: Microsoft ID 플랫폼 새로 고침 토큰 | Azure
titleSuffix: Microsoft identity platform
description: Azure AD에서 내보낸 새로 고침 토큰에 대해 알아봅니다.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: shermanouko
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 8b8bda71c637419dbd5b2bf7b181288abd9b965c
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112075244"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Microsoft ID 플랫폼 새로 고침 토큰

클라이언트가 보호된 리소스에 액세스하기 위해 액세스 토큰을 획득할 때 새로 고침 토큰도 받습니다. 새로 고침 토큰은 현재 액세스 토큰이 만료된 경우 새 액세스/새로 고침 토큰 쌍을 얻는 데 사용됩니다. 새로 고침 토큰은 다른 리소스에 대한 추가 액세스 토큰을 얻는 데에도 사용됩니다. 새로 고침 토큰은 사용자와 클라이언트의 조합에 바인딩되며 리소스나 테넌트에 연결되지 않습니다. 이를 통해 클라이언트는 새로 고침 토큰을 사용하여 권한이 있는 리소스와 테넌트의 조합에서 액세스 토큰을 얻을 수 있습니다. 새로 고침 토큰은 암호화되며 Microsoft ID 플랫폼에서만 읽을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 읽기 전에 다음 문서를 먼저 살펴보는 것을 권장합니다.

* Microsoft ID 플랫폼의 [ID 토큰](id-tokens.md)
* Microsoft ID 플랫폼의 [액세스 토큰](access-tokens.md)

## <a name="refresh-token-lifetime"></a>새로 고침 토큰 수명

새로 고침 토큰은 액세스 토큰보다 수명이 훨씬 깁니다. 토큰의 기본 수명은 90일이며 사용할 때마다 새로 고침 토큰으로 자동 대체됩니다. 즉, 새로 고침 토큰을 사용하여 새 액세스 토큰을 획득할 때마다 새 새로 고침 토큰도 발급됩니다. Microsoft ID 플랫폼은 새 액세스 토큰을 가져오는 데 사용되는 경우 이전의 새로 고침 토큰을 취소하지 않습니다. 새 토큰을 획득한 후 이전의 새로 고침 토큰을 안전하게 삭제하세요. 새로 고침 토큰은 액세스 토큰이나 애플리케이션 자격 증명처럼 안전하게 저장해야 합니다. 

## <a name="refresh-token-expiration"></a>새로 고침 토큰 만료

새로 고침 토큰은 시간 제한과 해지를 사유로 언제든지 취소할 수 있습니다. 이 문제가 발생하면 앱이 로그인 서비스의 거부를 정상적으로 처리해야 합니다. 이 작업은 다시 로그인하라는 대화형 로그인 프롬프트를 사용자에게 사용자를 전송하여 수행됩니다. 

### <a name="token-timeouts"></a>토큰 제한 시간

[토큰 수명 구성](active-directory-configurable-token-lifetimes.md#refresh-and-session-token-lifetime-policy-properties)을 사용하여 새로 고침 토큰의 수명을 줄이거나 늘릴 수 있습니다. 이 설정은 새로 고침 토큰을 사용하지 않을 수 있는 기간을 변경합니다. 예를 들어 사용자가 90일 넘게 앱을 열지 않는 시나리오를 가정해 보겠습니다. 앱이 90일이 넘은 새로 고침 토큰을 사용하려고 하면 만료된 상태라는 것을 감지할 수 있습니다. 또한 관리자는 두 번째 요소를 정기적으로 사용하도록 요구할 수 있으므로 사용자가 특정 간격에 따라 수동으로 로그인해야 합니다. 이 시나리오에는 다음이 포함됩니다.

* 비활성: 새로 고침 토큰은`MaxInactiveTime`에 의해 지정된 기간 동안만 유효합니다.  해당 기간 내에 토큰을 사용하지 않고 새 토큰으로 대체하면 더 이상 사용할 수 없습니다.
* 세션 만료: `MaxAgeSessionMultiFactor` 또는 `MaxAgeSessionSingleFactor`가 기본값(취소될 때까지)이 아닌 값으로 설정된 경우 MaxAgeSession*에 설정된 시간이 경과한 후 재인증이 필요합니다.  이는 사용자가 주기적으로 첫 번째 요소나 두 번째 요소로 재인증하도록 하는 데 사용됩니다. 
* 예:
  * 테넌트의 MaxInactiveTime은 5일이고 사용자가 일주일 동안 휴가를 갔습니다. 따라서 Azure AD는 7일간 사용자의 새 토큰 요청을 본 적이 없습니다. 다음에 사용자가 새 토큰을 요청하는 경우 해당 새로 고침 토큰이 해지되었음을 발견하고 자격 증명을 다시 입력해야 합니다.
  * 중요한 애플리케이션에의 `MaxAgeSessionMultiFactor`는 1일입니다. 사용자가 1일 후에 다시 로그인하는 경우에는 대화형 프롬프트를 통해 MFA를 한 번 더 거쳐야 합니다. 예를 들면 사용자가 월요일에 로그인하고 25시간이 지난 후 화요일에 로그인하는 것입니다. 

모든 새로 고침 토큰은 토큰 수명 정책에 설정된 규칙을 따릅니다. 특히, [단일 페이지 앱](reference-third-party-cookies-spas.md)에 사용되는 새로 고침 토큰은 마치 24시간의 `MaxAgeSessionSingleFactor` 정책이 적용된 것과 같이 항상 24시간의 활동으로 제한됩니다. 

### <a name="revocation"></a>해지

자격 증명, 사용자 작업 또는 관리자 작업의 변경을 사유로 서버에서 새로 고침 토큰을 취소할 수 있습니다.  새로 고침 토큰은 기밀 클라이언트가 발급한 토큰(가장 오른쪽 열)과 공용 클라이언트가 발급한 토큰(다른 모든 열)의 두 클래스에 속합니다.

| 변경 | 암호 기반 쿠키 | 암호 기반 토큰 | 비암호 기반 쿠키 | 비암호 기반 토큰 | 기밀 클라이언트 토큰 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 암호 만료 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자에 의해 암호가 변경됨 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 SSPR 수행 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 관리자가 암호 재설정 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 [PowerShell을 통해](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) 새로 고침 토큰 해지 | 해지됨 | 해지됨 | 해지됨 | 해지됨 | 해지됨 |
| 관리자가 [PowerShell을 통해](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 사용자에 대한 모든 새로 고침 토큰 해지 | 해지됨 | 해지됨 |해지됨 | 해지됨 | 해지됨 |
| 웹에서 [Single Sign-Out](v2-protocols-oidc.md#single-sign-out) | 해지됨 | 활성 상태 | 해지됨 | 활성 상태 | 활성 상태 |

## <a name="next-steps"></a>다음 단계

* [구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)에 대해 알아보기
* 기본 새로 고침 토큰에 대한 자세한 내용은 [기본 새로 고침 토큰](../devices/concept-primary-refresh-token.md)을 참조하세요.
