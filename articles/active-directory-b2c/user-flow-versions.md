---
title: Azure Active Directory B2C의 사용자 흐름 버전 | Microsoft Docs
description: Azure Active Directory B2C에서 사용할 수 있는 사용자 흐름 버전을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67949c31c710d88a05e1e110860fe703caf66d04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87481328"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 흐름 버전

Azure Active Directory B2C (Azure AD B2C)의 사용자 흐름은 고객 id 환경을 완벽 하 게 설명 하는 공통 [정책을](user-flow-overview.md) 설정 하는 데 도움이 됩니다. 이러한 환경에는 가입, 로그인, 암호 재설정 또는 프로필 편집이 포함됩니다. 아래 표에는 Azure AD B2C에서 사용할 수 있는 사용자 흐름이 설명 되어 있습니다.

> [!IMPORTANT]
> 사용자 흐름 버전을 참조하는 방법이 변경되었습니다. 이전에는 V1(프로덕션 준비) 버전과 V1.1 및 V2(미리 보기) 버전을 제공했습니다. 이제 사용자 흐름을 두 가지 버전으로 통합 했습니다.
>
>- **권장** 사용자 흐름은 사용자 흐름의 새로운 미리 보기 버전입니다. 이러한 기능은 철저 하 게 테스트 되었으며 레거시 **V2** 및 **v 1.1** 버전의 모든 기능을 결합 합니다. 향후에는 새로운 권장 사용자 흐름이 유지 되 고 업데이트 됩니다. 이러한 새로운 권장 사용자 흐름으로 이동 하면 새 기능이 출시 될 때 새 기능에 액세스할 수 있습니다.
>- 이전의 **V1**이라고 하는 **표준** 사용자 흐름이 일반적으로 사용 가능한 프로덕션 맞춤형 사용자 흐름입니다. 사용자 흐름이 중요 하 고 안정적인 버전에 따라 달라 지는 경우 표준 사용자 흐름을 계속 사용할 수 있으므로 이러한 버전이 유지 되 고 업데이트 되지 않습니다.
>
>모든 레거시 미리 보기 사용자 흐름 (V 1.1 및 V2)은 **2021 년 8 월 1 일부 터**사용 중단에 대 한 경로에 있습니다. 가능 하면 항상 최신 기능과 업데이트를 활용할 수 있도록 가능한 한 빨리 [새로운 **권장** 버전으로 전환](#how-to-switch-to-a-new-recommended-user-flow) 하는 것이 좋습니다. *이러한 변경 내용은 Azure 공용 클라우드에만 적용 됩니다. 다른 환경에서는 계속 [레거시 사용자 흐름 버전 관리](user-flow-versions-legacy.md)를 사용 합니다.*

## <a name="recommended-user-flows"></a>권장 사용자 흐름

권장 사용자 흐름은 새로운 기능을 레거시 V2 및 V 1.1 기능과 결합 하는 미리 보기 버전입니다. 앞으로는 권장 사용자 흐름이 유지 되 고 업데이트 됩니다.

| 사용자 흐름 | 설명 |
| --------- | ----------- |
| 암호 재설정 (미리 보기) | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 프로필 편집 (미리 보기) | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| 로그인 (미리 보기) | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>로그인 페이지 사용자 지정</li></ul> |
| 등록 (미리 보기) | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 등록 및 로그인 (미리 보기) | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[나이 제한](basic-age-gating.md)</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>표준 사용자 흐름

표준 사용자 흐름 (이전의 V1 이라고 함)은 일반적으로 프로덕션이 준비 된 사용자 흐름입니다. 표준 사용자 흐름은 향후 업데이트 되지 않습니다.

| 사용자 흐름 | 설명 |
| --------- | ----------- | ----------- |
| 암호 재설정 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 프로필 편집 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| 로그인 | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>로그인 차단</li><li>암호 재설정 강제 적용</li><li>KMSI(로그인 유지)</ul><br>이 사용자 흐름을 사용하여 사용자 인터페이스를 사용자 지정할 수 없습니다. |
| 등록 | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul> |
| 등록 및 로그인 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](custom-policy-multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](user-flow-password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>새 권장 사용자 흐름으로 전환 하는 방법

이전 버전의 사용자 흐름에서 새로운 **권장** 미리 보기 버전으로 전환 하려면 다음 단계를 수행 합니다.

1. [자습서: 사용자 흐름 만들기 Azure Active Directory](tutorial-create-user-flows.md)의 단계를 수행 하 여 새 사용자 흐름 정책을 만듭니다. 사용자 흐름을 만드는 동안 **권장** 버전을 선택 합니다.

3. 레거시 정책에 구성 된 설정과 동일한 설정을 사용 하 여 새 사용자 흐름을 구성 합니다.

4. 응용 프로그램 로그인 URL을 새로 만든 정책으로 업데이트 합니다.

5. 사용자 흐름을 테스트 하 고 작동을 확인 한 후에는 다음 단계에 따라 레거시 사용자 흐름을 삭제 합니다.
   1. Azure AD B2C 테 넌 트 개요 메뉴에서 **사용자 흐름**을 선택 합니다.
   2. 삭제 하려는 사용자 흐름을 찾습니다.
   3. 마지막 열에서 상황에 맞는 메뉴 (**...**)를 선택 하 고 **삭제**를 선택 합니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>여전히 레거시 V2 및 V 1.1 사용자 흐름을 만들 수 있나요?

기존 V2 및 V 1.1 버전을 기반으로 새 사용자 흐름을 만들 수는 없지만 현재 사용 중인 기존 V2 및 V 1.1 사용자 흐름을 계속 읽고 업데이트 하 고 삭제할 수 있습니다.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>기존 V2 및 V 1.1 사용자 흐름을 계속 사용 하는 이유가 있나요?

그렇지는 않아요. 새로운 **권장** 미리 보기 버전에는 레거시 V2 및 v 1.1 버전과 동일한 기능이 포함 되어 있습니다. 제거 된 항목이 없으므로 이제 추가 기능을 포함 합니다.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>레거시 V2 및 V 1.1 정책에서 전환 하지 않는 경우 응용 프로그램에 어떤 영향을 미칩니까?

레거시 V2 또는 V 1.1 사용자 흐름을 사용 하는 경우 응용 프로그램은이 버전 관리 변경의 영향을 받지 않습니다. 그러나 앞으로 새 기능이 나 정책 변경 내용에 액세스 하려면 새로운 **권장** 버전으로 전환 해야 합니다.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft에서 여전히 레거시 V2 또는 V 1.1 사용자 흐름 정책을 지원 하나요?

사용자 흐름의 레거시 V2 및 V 1.1 버전은 계속 완벽 하 게 지원 됩니다.
