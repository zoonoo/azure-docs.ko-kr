---
title: Azure Active Directory B2C의 사용자 흐름 버전 | Microsoft Docs
description: Azure Active Directory B2C에서 사용할 수 있는 사용자 흐름 버전을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed57a9fa3b041961ce220e8f10d9aed5e7bef60e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511934"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 흐름 버전

>[!IMPORTANT]
> 문서에 나열된 모든 사용자 흐름은 **추천**으로 식별되지 않는 한 공개 미리 보기로 있는 것으로 간주됩니다. 프로덕션 애플리케이션에는 추천되는 사용자 흐름만 사용해야 합니다.

Azure AD(Azure Active Directory) B2C의 사용자 흐름을 사용하면 고객 ID 환경을 완벽하게 설명하는 일반 [정책](active-directory-b2c-reference-policies.md)을 설정할 수 있습니다. 이러한 환경에는 가입, 로그인, 암호 재설정 또는 프로필 편집이 포함됩니다. Azure AD B2C에서는 추천 사용자 흐름과 미리 보기 사용자 흐름 모두의 컬렉션에서 선택할 수 있습니다. 

새 사용자 흐름은 새 버전으로 추가됩니다. 안정적인 사용자 흐름이 되면 사용하도록 추천됩니다. 사용자 흐름이 완전히 테스트되면 **추천**으로 표시됩니다. 사용자 흐름은 '추천'으로 표시될 때까지 미리 보기로 간주됩니다. 프로덕션 애플리케이션에는 추천 사용자 흐름을 사용하지만, 새 기능을 사용할 수 있게 되면 다른 버전에서 선택하여 테스트합니다. 이전 버전의 추천 사용자 흐름은 사용하면 안됩니다.

## <a name="v1"></a>V1

| 사용자 흐름 | 권장 | 설명 |
| --------- | ----------- | ----------- |
| 암호 재설정 | 예 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| 프로필 편집 | 예 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](active-directory-b2c-reference-tokens.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| 리소스 소유자 | 아닙니다. | 로컬 계정이 있는 사용자가 네이티브 애플리케이션에서 직접 로그인할 수 있도록 합니다(브라우저 필요 없음). 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](active-directory-b2c-reference-tokens.md)</li><li>토큰 호환성 설정</li></ul> |
| 로그인 | 아닙니다. | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[토큰 수명](active-directory-b2c-reference-tokens.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>로그인 차단</li><li>암호 재설정 강제 적용</li><li>KMSI(로그인 유지)</ul><br>이 사용자 흐름을 사용하여 사용자 인터페이스를 사용자 지정할 수 없습니다. |
| 등록 | 아닙니다. | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[토큰 수명](active-directory-b2c-reference-tokens.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| 등록 및 로그인 | 예 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[토큰 수명](active-directory-b2c-reference-tokens.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| 사용자 흐름 | 권장 | 설명 |
| --------- | ----------- | ----------- |
| v2 암호 재설정 | 아닙니다. | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>토큰 호환성 설정</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| v2 로그인 | 아닙니다. | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[토큰 수명](active-directory-b2c-reference-tokens.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>로그인 페이지 사용자 지정</li></ul> |
| v2 가입 | 아닙니다. | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[토큰 수명](active-directory-b2c-reference-tokens.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| v2 가입 및 로그인 | 아닙니다. | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](active-directory-b2c-reference-password-complexity.md)</li></ul> |
