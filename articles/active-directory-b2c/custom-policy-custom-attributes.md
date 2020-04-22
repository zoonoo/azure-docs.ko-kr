---
title: 사용자 지정 정책에 고유한 특성 추가
titleSuffix: Azure AD B2C
description: 확장 속성, 사용자 지정 특성을 사용하고 사용자 인터페이스에 포함하는 방법에 대한 연습입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b5990f79891a9cbc0d18c3499691a3d7ef309a73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678264"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: 사용자 지정 프로필 정책에서 사용자 지정 특성 사용

사용자 지정 정책 문서를 [사용하여 클레임 추가 및 사용자 입력을 사용자 지정하려면](custom-policy-configure-user-input.md) 기본 제공 사용자 프로필 특성을 사용하는 방법을 알아봅니다. [user profile attributes](user-profile-attributes.md) 이 문서에서는 Azure Active Directory B2C(Azure AD B2C) 디렉터리에서 사용자 지정 특성을 사용하도록 설정합니다. 나중에 새 특성을 [사용자 흐름](user-flow-overview.md) 또는 사용자 [지정 정책에서](custom-policy-get-started.md) 사용자 지정 클레임으로 동시에 사용할 수 있습니다.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>사전 요구 사항

[Azure Active Directory B2C: 사용자 지정 정책 시작](custom-policy-get-started.md) 문서의 단계를 따릅니다.

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>사용자 지정 특성을 사용하여 고객에 대한 정보 수집 

Azure AD B2C 디렉터리에는 [기본 제공 특성 집합이](user-profile-attributes.md)함께 제공됩니다. 그러나 다음과 같은 경우와 같은 특정 시나리오를 관리하기 위해 고유한 특성을 만들어야 하는 경우가 많습니다.

* 고객 대면 응용 프로그램은 **LoyaltyId** 특성을 유지해야 합니다.
* ID 공급자에는 고유 사용자 식별자인 **고유UserGUID가**있습니다.
* 사용자 지정 사용자 이동은 다른 논리에서 작동하려면 사용자 **마이그레이션Status의**상태를 유지해야 합니다.

Azure AD B2C를 사용하면 각 사용자 계정에 저장된 특성 집합을 확장할 수 있습니다. [Microsoft 그래프 API를](manage-user-accounts-graph-api.md)사용하여 이러한 특성을 읽고 쓸 수도 있습니다.

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 확장 앱

확장 특성은 사용자에 대한 데이터를 포함할 수 있더라도 응용 프로그램 개체에만 등록할 수 있습니다. 확장 특성은 b2c 확장 앱이라는 응용 프로그램에 첨부됩니다. Azure AD B2C에서 사용자 데이터를 저장하는 데 사용되므로 이 응용 프로그램을 수정하지 마십시오. Azure AD B2C, 앱 등록에서 이 응용 프로그램을 찾을 수 있습니다.

‘확장 속성’, ‘사용자 지정 특성’ 및 ‘사용자 지정 클레임’은 이 문서의 컨텍스트에서 동일한 항목을 참조합니다.****** 이름은 컨텍스트(예: 애플리케이션, 개체 또는 정책)에 따라 달라집니다.

## <a name="get-the-application-properties"></a>응용 프로그램 속성 받기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록(미리 보기)을**선택한 다음 **모든 응용 프로그램을 선택합니다.**
1. 응용 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 프로그램을 선택합니다.
1. 다음 식별자를 클립보드에 복사하고 저장합니다.
    * **응용 프로그램 ID .** 예: `11111111-1111-1111-1111-111111111111`.
    * **개체 ID**. 예: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>사용자 지정 정책 수정

정책에서 사용자 지정 특성을 사용하려면 AAD 공통 기술 프로필 메타데이터에 **응용 프로그램 ID** 및 응용 프로그램 개체 **ID를** 제공합니다. *AAD-Common* 기술 프로필은 기본 [Azure Active Directory](active-directory-technical-profile.md) 기술 프로필에 있으며 Azure AD 사용자 관리에 대한 지원을 제공합니다. 다른 Azure AD 기술 프로필에는 구성을 활용하는 AAD-Common이 포함됩니다. 확장자 파일에서 AAD 공통 기술 프로파일을 재정의합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어, <em> `SocialAndLocalAccounts/` </em>.
1. ClaimsProviders 요소를 찾습니다. 클레임 공급자 요소에 새 클레임 공급자를 추가합니다.
1. 이전에 `ApplicationObjectId` 기록한 개체 ID로 바꿉습니다. 그런 `ClientId` 다음 아래 코드 조각에 이전에 기록한 응용 프로그램 ID로 바꿉습니다.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>맞춤 정책 업로드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택하여 Azure AD 테넌트를 포함하는 디렉터리를 사용하고 있는지 확인합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. **사용자 지정 정책 업로드를**선택한 다음 변경한 TrustFrameworkExtensions.xml 정책 파일을 업로드합니다.

> [!NOTE]
> Azure AD 기술 프로필이 처음으로 디렉터리에 대한 클레임을 유지하면 사용자 지정 특성이 있는지 여부를 확인합니다. 그렇지 않은 경우 사용자 지정 특성을 만듭니다.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Azure 포털을 통해 사용자 지정 특성 만들기

기본 제공 및 사용자 지정 정책 간에 동일한 확장 특성이 공유됩니다. 포털 환경을 통해 사용자 지정 특성을 추가하면 모든 B2C 테넌트에 있는 b2c 확장 앱을 사용하여 해당 **특성이 등록됩니다.**

사용자 지정 정책에서 사용하기 전이나 후에 포털 UI를 사용하여 이러한 특성을 만들 수 있습니다. [Azure Active Directory B2C](user-flow-custom-attributes.md)에서 사용자 지정 특성을 정의하는 방법에 대한 지침을 따릅니다. 포털에서 속성 **loyaltyId를** 만들 때 다음과 같이 참조해야 합니다.

|이름     |사용 대상 |
|---------|---------|
|`extension_loyaltyId`  | 사용자 지정 정책|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

다음 예제에서는 Azure AD B2C 사용자 지정 정책 클레임 정의에서 사용자 지정 특성의 사용을 보여 줍니다.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

다음 예제에서는 기술 프로필, 입력, 출력 및 지속된 클레임에서 Azure AD B2C 사용자 지정 정책에서 사용자 지정 특성의 사용을 보여 줍니다.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>정책에서 사용자 지정 특성 사용

사용자 지정 정책을 [사용하여 클레임을 추가하고 사용자 입력을 사용자 지정하는](custom-policy-configure-user-input.md)방법에 대한 지침을 따르십시오. 이 샘플은 '도시'라는 기본 제공 클레임을 사용합니다. 사용자 지정 특성을 사용하려면 '도시'를 사용자 지정 특성으로 바꿉습니다.


## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Azure AD B2C 사용자 프로필 특성](user-profile-attributes.md)
- [확장 특성 정의](user-profile-attributes.md#extension-attributes)
- [마이크로 소프트 그래프와 Azure AD B2C 사용자 계정 관리](manage-user-accounts-graph-api.md)
