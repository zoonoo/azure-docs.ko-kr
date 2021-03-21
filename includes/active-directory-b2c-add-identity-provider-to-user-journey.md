---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674240"
---
## <a name="add-a-user-journey"></a>사용자 경험 추가 

이 시점에서 id 공급자가 설정 되었지만 아직 로그인 페이지에서 사용할 수 없습니다. 사용자 지정 사용자 경험을 사용 하지 않는 경우 기존 템플릿 사용자 경험의 복제본을 만듭니다. 그렇지 않은 경우 다음 단계를 계속 진행 합니다. 

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
1. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
1. *TrustFrameworkExtensions.xml* 을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
1. 사용자 경험의 Id 이름을 바꿉니다. 예: `Id="CustomSignUpSignIn"`

## <a name="add-the-identity-provider-to-a-user-journey"></a>사용자 경험에 id 공급자 추가 

사용자 경험을 만들었으므로 이제 사용자 경험에 새 id 공급자를 추가 합니다. 먼저 로그인 단추를 추가 하 고 단추를 작업에 연결 합니다. 작업은 이전에 만든 기술 프로필입니다.

1. `Type="CombinedSignInAndSignUp"`또는 사용자 경험에를 포함 하는 오케스트레이션 단계 요소를 찾습니다 `Type="ClaimsProviderSelection"` . 일반적으로 첫 번째 오케스트레이션 단계입니다. **ClaimsProviderSelections** 요소는 사용자가 로그인 할 수 있는 id 공급자의 목록을 포함 합니다. 요소의 순서는 사용자에 게 표시 되는 로그인 단추의 순서를 제어 합니다. **ClaimsProviderSelection** XML 요소를 추가 합니다. **Targetclaimsexchangeid** 의 값을 친근 한 이름으로 설정 합니다.

1. 다음 오케스트레이션 단계에서 **Claim이상 변경** 요소를 추가 합니다. **Id** 를 대상 클레임 교환 id의 값으로 설정 합니다. **TechnicalProfileReferenceId** 의 값을 이전에 만든 기술 프로필의 id로 업데이트 합니다.

다음 XML은 id 공급자를 사용 하 여 사용자 경험의 처음 두 오케스트레이션 단계를 보여 줍니다.