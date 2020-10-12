---
title: Azure Active Directory B2C의 레거시 사용자 흐름 버전 Microsoft Docs
description: Azure Active Directory B2C에서 사용할 수 있는 레거시 버전의 사용자 흐름에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3471b9988b154ccb38dc56545e769a7179a4026f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258730"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 레거시 사용자 흐름 버전

> [!IMPORTANT]
> 이 문서에서는 사용자 흐름의 V1 (프로덕션 준비) 버전 및 V 1.1 및 V2 (preview) 버전을 제공 하는 사용자 흐름에 대 한 레거시 버전 관리 방법을 설명 합니다. Azure 공용 클라우드가 아닌 환경에서는이 레거시 버전 관리 방법을 계속 사용 합니다. Azure 공용 클라우드에서이 메서드는 [새로운 **권장** 및 **미리 보기** 버전](user-flow-versions.md)으로 대체 됩니다.
> 
Azure Active Directory B2C (Azure AD B2C)의 사용자 흐름은 고객 id 환경을 완벽 하 게 설명 하는 공통 [정책을](user-flow-overview.md) 설정 하는 데 도움이 됩니다. 이러한 환경에는 가입, 로그인, 암호 재설정 또는 프로필 편집이 포함됩니다.

아래 표에서 사용자 흐름이 **권장**되는 것으로 확인 되는 경우를 제외 하 고 *미리 보기*상태인 것으로 간주 됩니다. 프로덕션 응용 프로그램에 대해 권장 되는 사용자 흐름이 사용 되어야 합니다.

## <a name="v1"></a>V1

| 사용자 흐름 | 권장 | 설명 |
| --------- | ----------- | ----------- |
| 암호 재설정 | 예 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 프로필 편집 | 예 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| ROPC를 사용 하 여 로그인 | 아니요 | 로컬 계정이 있는 사용자가 네이티브 애플리케이션에서 직접 로그인할 수 있도록 합니다(브라우저 필요 없음). 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li></ul> |
| 로그인 | 아니요 | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>로그인 차단</li><li>암호 재설정 강제 적용</li><li>KMSI(로그인 유지)</ul><br>이 사용자 흐름을 사용하여 사용자 인터페이스를 사용자 지정할 수 없습니다. |
| 등록 | 아니요 | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 등록 및 로그인 | 예 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| 사용자 흐름 | 권장 | 설명 |
| --------- | ----------- | ----------- |
| 암호 재설정 v. 1.1 | 아니요 | 사용자가 전자 메일을 확인 한 후 새 암호를 선택할 수 있습니다 (새 페이지 레이아웃 사용 가능). 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| 사용자 흐름 | 권장 | 설명 |
| --------- | ----------- | ----------- |
| v2 암호 재설정 | 아니요 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 프로필 편집 v2 | 예 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| v2 로그인 | 아니요 | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>로그인 페이지 사용자 지정</li></ul> |
| v2 가입 | 아니요 | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| v2 가입 및 로그인 | 아니요 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |