---
title: 사용자 지정 정책을 사용 하 여 고객 등록 중에 전자 메일 확인 사용 안 함
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 고객 등록 중에 전자 메일 확인을 사용 하지 않도록 설정 하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136145"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 고객 등록 중에 전자 메일 확인 사용 안 함

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>전제 조건

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 소셜 및 로컬 계정을 사용 하 여 등록 및 로그인 하는 데 사용 되는 사용자 지정 정책이 있어야 합니다.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>자체 어설션된 기술 프로필에 메타 데이터 추가

**LocalAccountSignUpWithLogonEmail** 기술 프로필은 등록 흐름 중에 호출 되는 [자체 어설션](self-asserted-technical-profile.md)입니다. 전자 메일 확인을 사용 하지 않도록 설정 하려면 `EnforceEmailVerification` 메타 데이터를 false로 설정 합니다. 확장 파일의 LocalAccountSignUpWithLogonEmail 기술 프로필을 재정의 합니다. 

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em> 합니다.
1. `ClaimsProviders` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. `ClaimsProviders` 요소에 다음 클레임 공급자를 추가 합니다.

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 azure ad 테 넌 트가 포함 된 디렉터리를 선택 하 여 azure ad 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. **사용자 지정 정책 업로드**를 선택한 후 변경한 두 정책 파일을 업로드합니다.
2. 업로드한 등록 또는 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
3. 유효성 검사 없이 전자 메일 주소를 사용 하 여 등록할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- IEF 참조에서 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 에 대해 자세히 알아보세요.
