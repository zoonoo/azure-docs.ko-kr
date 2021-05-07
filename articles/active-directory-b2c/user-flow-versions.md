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
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517857"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 흐름 버전

Azure AD B2C(Azure Active Directory B2C)의 사용자 흐름은 고객 ID 환경을 완벽하게 설명하는 공통 [정책](user-flow-overview.md)을 설정하는 데 도움이 됩니다. 이러한 환경에는 가입, 로그인, 암호 재설정 또는 프로필 편집이 포함됩니다. 아래 표에는 Azure AD B2C에서 사용할 수 있는 사용자 흐름이 설명되어 있습니다.

> [!IMPORTANT]
> 사용자 흐름 버전을 참조하는 방법이 변경되었습니다. 이전에는 V1(프로덕션 준비) 버전과 V1.1 및 V2(미리 보기) 버전을 제공했습니다. 이제 사용자 흐름을 두 가지 버전으로 통합했습니다.
>
>- **권장** 사용자 흐름은 사용자 흐름의 새로운 미리 보기 버전입니다. 철저한 테스트를 거쳐 레거시 **V2** 및 **V1.1** 버전의 모든 기능을 결합합니다. 향후에는 새로운 권장 사용자 흐름이 유지되고 업데이트됩니다. 새로운 권장 사용자 흐름으로 이동하면 출시되는 새로운 기능에 액세스할 수 있습니다.
>- 이전에 **V1** 이라고 하는 **표준** 사용자 흐름은 일반적으로 사용 가능한 프로덕션 준비 사용자 흐름입니다. 사용자 흐름이 업무상 중요하고 매우 안정적인 버전에 따라 달라지는 경우 표준 사용자 흐름을 계속 사용할 수 있으므로 해당 버전이 유지 및 업데이트되지 않습니다.
>
>모든 레거시 미리 보기 사용자 흐름(V1.1 및 V2)은 **2021년 8월 1일** 에 사용 중단될 예정입니다. 가능하면 항상 최신 기능과 업데이트를 활용할 수 있도록 최대한 빨리 [새로운 **권장** 버전으로 전환](#how-to-switch-to-a-new-recommended-user-flow)하는 것이 좋습니다. *해당 변경 내용은 Azure 퍼블릭 클라우드에만 적용됩니다. 다른 환경에서는 [레거시 사용자 흐름 버전 관리](user-flow-versions-legacy.md)를 계속 사용합니다.*

## <a name="recommended-user-flows"></a>권장 사용자 흐름

권장 사용자 흐름은 새로운 기능을 레거시 V2 및 V1.1 기능과 결합하는 미리 보기 버전입니다. 앞으로는 권장 사용자 흐름이 유지되고 업데이트됩니다.

| 사용자 흐름 | Description |
| --------- | ----------- |
| 암호 재설정(미리 보기) | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[나이 제한](age-gating.md)</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| 프로필 편집(미리 보기) | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| 로그인(미리 보기) | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](age-gating.md)</li><li>로그인 페이지 사용자 지정</li></ul> |
| 가입(미리 보기) | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](age-gating.md)</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| 가입 및 로그인(미리 보기) | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[나이 제한](age-gating.md)</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>표준 사용자 흐름

표준 사용자 흐름(이전에 V1이라고 함)은 일반적으로 사용 가능한 프로덕션 준비 사용자 흐름입니다. 표준 사용자 흐름은 향후 업데이트되지 않습니다.

| 사용자 흐름 | Description |
| --------- | ----------- | ----------- |
| 암호 재설정 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| 프로필 편집 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| 로그인 | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>로그인 차단</li><li>암호 재설정 강제 적용</li><li>KMSI(로그인 유지)</ul><br>이 사용자 흐름을 사용하여 사용자 인터페이스를 사용자 지정할 수 없습니다. |
| 가입 | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| 가입 및 로그인 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>새로운 권장 사용자 흐름으로 전환하는 방법

이전 버전의 사용자 흐름에서 새로운 **권장** 미리 보기 버전으로 전환하려면 다음 단계를 수행합니다.

1. [자습서: Azure Active Directory에서 사용자 흐름 만들기](tutorial-create-user-flows.md)의 단계에 따라 새 사용자 흐름 정책을 만듭니다. 사용자 흐름을 만드는 동안 **권장** 버전을 선택합니다.

3. 레거시 정책에 구성된 설정과 동일한 설정을 사용하여 새 사용자 흐름을 구성합니다.

4. 애플리케이션 로그인 URL을 새로 만든 정책으로 업데이트합니다.

5. 사용자 흐름을 테스트하고 작동하는지 확인한 후에는 다음 단계에 따라 레거시 사용자 흐름을 삭제합니다.
   1. Azure AD B2C 테넌트 개요 메뉴에서 **사용자 흐름** 을 선택합니다.
   2. 삭제할 사용자 흐름을 찾습니다.
   3. 마지막 열에서 상황에 맞는 메뉴( **...** )를 선택한 다음 **삭제** 를 선택합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>레거시 V2 및 V1.1 사용자 흐름을 계속 만들 수 있나요?

레거시 V2 및 V1.1 버전을 기반으로 새 사용자 흐름을 만들 수는 없지만, 현재 사용 중인 레거시 V2 및 V1.1 사용자 흐름을 계속 읽고 업데이트하고 삭제할 수 있습니다.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>레거시 V2 및 V1.1 사용자 흐름을 계속 사용하는 이유가 있나요?

그렇지는 않아요. 새로운 **권장** 미리 보기 버전에는 레거시 V2 및 V1.1 버전과 동일한 기능이 포함되어 있습니다. 제거된 항목이 없으며, 이제 추가 기능이 포함됩니다.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>레거시 V2 및 V1.1 정책에서 전환하지 않는 경우 애플리케이션에 어떤 영향을 미치나요?

레거시 V2 또는 V1.1 사용자 흐름을 사용하는 경우 애플리케이션은 이 버전 관리 변경으로 인해 영향을 받지 않습니다. 그러나 앞으로 새 기능이나 정책 변경 내용에 액세스하려면 새로운 **권장** 버전으로 전환해야 합니다.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft에서 레거시 V2 또는 V1.1 사용자 흐름 정책을 계속 지원하나요?

사용자 흐름의 레거시 V2 및 V1.1 버전은 계속 완벽하게 지원됩니다.
