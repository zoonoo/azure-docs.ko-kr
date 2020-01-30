---
title: Azure Active Directory B2C의 사용자 흐름 버전 | Microsoft Docs
description: Azure Active Directory B2C에서 사용할 수 있는 사용자 흐름 버전을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d7e174245755659494dfe1243c39619ae37f0f33
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840080"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 흐름 버전

Azure Active Directory B2C (Azure AD B2C)의 사용자 흐름은 고객 id 환경을 완벽 하 게 설명 하는 공통 [정책을](user-flow-overview.md) 설정 하는 데 도움이 됩니다. 이러한 환경에는 가입, 로그인, 암호 재설정 또는 프로필 편집이 포함됩니다. Azure AD B2C에서는 추천 사용자 흐름과 미리 보기 사용자 흐름 모두의 컬렉션에서 선택할 수 있습니다.

새 사용자 흐름은 새 버전으로 추가됩니다. 안정적인 사용자 흐름이 되면 사용하도록 추천됩니다. 사용자 흐름이 완전히 테스트되면 **추천**으로 표시됩니다. 사용자 흐름은 '추천'으로 표시될 때까지 미리 보기로 간주됩니다. 프로덕션 애플리케이션에는 추천 사용자 흐름을 사용하지만, 새 기능을 사용할 수 있게 되면 다른 버전에서 선택하여 테스트합니다. 이전 버전의 추천 사용자 흐름은 사용하면 안됩니다.

>[!IMPORTANT]
> 사용자 흐름이 **권장**된 것으로 확인 되지 않으면 *미리 보기*상태인 것으로 간주 됩니다. 프로덕션 응용 프로그램에 대해 권장 되는 사용자 흐름이 사용 되어야 합니다.

## <a name="v1"></a>V1

| 사용자 흐름 | 권장 | Description |
| --------- | ----------- | ----------- |
| 암호 재설정 | 예 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 프로필 편집 | 예 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| ROPC를 사용 하 여 로그인 | 아닙니다. | 로컬 계정이 있는 사용자가 네이티브 애플리케이션에서 직접 로그인할 수 있도록 합니다(브라우저 필요 없음). 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li></ul> |
| 로그인 | 아닙니다. | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>로그인 차단</li><li>암호 재설정 강제 적용</li><li>KMSI(로그인 유지)</ul><br>이 사용자 흐름을 사용하여 사용자 인터페이스를 사용자 지정할 수 없습니다. |
| 계정 등록 | 아닙니다. | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 등록 및 로그인 | 예 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V. 1.1

| 사용자 흐름 | 권장 | Description |
| --------- | ----------- | ----------- |
| 암호 재설정 v. 1.1 | 아닙니다. | 사용자가 전자 메일을 확인 한 후 새 암호를 선택할 수 있습니다 (새 페이지 레이아웃 사용 가능). 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| 사용자 흐름 | 권장 | Description |
| --------- | ----------- | ----------- |
| v2 암호 재설정 | 아닙니다. | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 프로필 편집 v2 | 예 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| v2 로그인 | 아닙니다. | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>로그인 페이지 사용자 지정</li></ul> |
| v2 가입 | 아닙니다. | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| v2 가입 및 로그인 | 아닙니다. | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
