---
title: Azure Active Directory B2C에서 사용자 지정 특성 정의 | Microsoft Docs
description: Azure Active Directory B2C에서 애플리케이션에 대한 사용자 지정 특성을 정의하여 고객에 대한 정보를 수집합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607920"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 특성 정의

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[사용자 지정 정책을 사용 하 여 클레임 추가 및 사용자 입력 사용자 지정](configure-user-input.md) 문서에서 기본 제공 [사용자 프로필 특성](user-profile-attributes.md)을 사용 하는 방법을 알아봅니다. 이 문서에서는 Azure Active Directory B2C (Azure AD B2C) 디렉터리에서 사용자 지정 특성을 사용 하도록 설정 합니다. 나중에 [사용자 흐름이](user-flow-overview.md) 나 사용자 [지정 정책](custom-policy-get-started.md) 에서 새 특성을 사용자 지정 클레임으로 동시에 사용할 수 있습니다.

Azure AD B2C 디렉터리에는 [기본 제공 특성 집합](user-profile-attributes.md)이 함께 제공됩니다. 그러나 다음과 같은 경우에 특정 시나리오를 관리 하기 위해 고유한 특성을 만들어야 하는 경우가 많습니다.

* 고객 지향 응용 프로그램은 **LoyaltyId** 특성을 유지 해야 합니다.
* Id 공급자에는 유지 되어야 하는 고유한 사용자 id **uniqueUserGUID** 이 있습니다.
* 사용자 지정 사용자 경험에는 다른 논리가 작동 하기 위해 **migrationStatus** 사용자의 상태가 유지 되어야 합니다.

‘확장 속성’, ‘사용자 지정 특성’ 및 ‘사용자 지정 클레임’은 이 문서의 컨텍스트에서 동일한 항목을 참조합니다. 이름은 컨텍스트(예: 애플리케이션, 개체 또는 정책)에 따라 달라집니다.

Azure AD B2C를 사용 하면 각 사용자 계정에 저장 된 특성 집합을 확장할 수 있습니다. 또한 [Microsoft Graph API](microsoft-graph-operations.md)를 사용하여 이러한 특성을 읽고 쓸 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>사용자 지정 특성 만들기

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Portal의 오른쪽 상단 모서리에서 전환하여 Azure AD B2C 테넌트가 있는 디렉터리를 사용하고 있는지 확인합니다. 구독 정보를 선택한 다음, **디렉터리 전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 특성** 을 선택한 후 **추가** 를 선택합니다.
1. 사용자 지정 특성(예: "ShoeSize")에 대해 **이름** 을 제공합니다.
1. **데이터 형식** 을 선택합니다. **문자열**, **부울** 및 **Int** 만 사용할 수 있습니다.
1. 필요에 따라 정보 제공을 위해 **설명** 을 입력합니다.
1. **만들기** 를 클릭합니다.

사용자 지정 특성은 이제 사용자 **특성** 목록에서 사용할 수 있으며 사용자 흐름에서 사용할 수 있습니다. 사용자 지정 특성은 사용자가 사용자 **특성** 목록에 추가할 때가 아닌 사용자 흐름에서 처음 사용 하는 경우에만 생성 됩니다.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>사용자 흐름에 사용자 지정 특성 사용

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. **사용자 특성** 을 선택하고 사용자 지정 특성을 선택합니다(예: "ShoeSize"). **저장** 을 클릭합니다.
1. **애플리케이션 클레임** 을 선택하고 사용자 지정 특성을 선택합니다.
1. **저장** 을 클릭합니다.

새로 만든 사용자 지정 특성을 사용 하는 사용자 흐름을 사용 하 여 새 사용자를 만든 후 [Microsoft Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer)에서 개체를 쿼리할 수 있습니다. 또는 사용자 흐름에서 [사용자 흐름 실행](./tutorial-create-user-flows.md) 기능을 사용 하 여 고객 환경을 확인할 수 있습니다. 이제 등록 과정 동안 수집되는 특성 목록에 **ShoeSize** 가 표시되며 애플리케이션으로 다시 전송되는 토큰에도 표시됩니다.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 확장 앱

확장 특성은 사용자에 대 한 데이터를 포함할 수 있는 경우에도 응용 프로그램 개체에만 등록할 수 있습니다. 확장 특성은 라는 응용 프로그램에 연결 됩니다 `b2c-extensions-app` . 이 애플리케이션은 사용자 데이터 저장을 위해 Azure AD B2C에서 사용하므로 수정하지 않습니다. Azure AD B2C 앱 등록에서이 응용 프로그램을 찾을 수 있습니다. 응용 프로그램 속성을 가져옵니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 를 선택 하 고 **모든 응용 프로그램** 을 선택 합니다.
1. `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 애플리케이션을 선택합니다.
1. 다음 식별자를 클립보드에 복사하고 저장합니다.
    * **애플리케이션 ID**. 예: `11111111-1111-1111-1111-111111111111`.
    * **개체 ID** 입니다. 예: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>사용자 지정 정책 수정

정책에서 사용자 지정 특성을 사용 하도록 설정 하려면 AAD-Common 기술 프로필 메타 데이터에 **응용 프로그램 id** 및 응용 프로그램 **개체 id** 를 제공 합니다. *AAD 일반* 기술 프로필은 기본 [Azure Active Directory](active-directory-technical-profile.md) 기술 프로필에 있으며 Azure AD 사용자 관리에 대 한 지원을 제공 합니다. 다른 Azure AD 기술 프로필에는 해당 구성을 활용 하는 AAD-Common 포함 되어 있습니다. 확장 파일의 AAD-Common 기술 프로필을 재정의 합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.
1. ClaimsProviders 요소를 찾습니다. ClaimsProviders 요소에 새 ClaimsProvider를 추가 합니다.
1. 열기 및 닫기 요소 사이에 이전에 기록한 **응용 프로그램 ID** 를 삽입 합니다 `<Item Key="ClientId">` `</Item>` .
1. 이전에 기록한 **응용 프로그램 ObjectID** 를 여는 `<Item Key="ApplicationObjectId">` 요소와 닫는 요소 사이에 삽입 합니다 `</Item>` .

    ```xml
    <!-- 
    <ClaimsProviders> -->
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
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>사용자 지정 정책 업로드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 하 여 Azure AD 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
4. **ID 경험 프레임워크** 를 선택합니다.
5. **사용자 지정 정책 업로드** 를 선택 하 고 변경한 TrustFrameworkExtensions.xml 정책 파일을 업로드 합니다.

> [!NOTE]
> Azure AD 기술 프로필은 디렉터리에 클레임을 처음으로 유지할 때 사용자 지정 특성이 있는지 여부를 확인 합니다. 그렇지 않으면 사용자 지정 특성을 만듭니다.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Azure Portal를 통해 사용자 지정 특성 만들기

기본 제공 정책과 사용자 지정 정책 간에 동일한 확장 특성을 공유 합니다. 포털 환경을 통해 사용자 지정 특성을 추가 하는 경우 해당 특성은 모든 B2C 테 넌 트에 있는 **b2c 앱** 을 사용 하 여 등록 됩니다.

사용자 지정 정책에서 사용 하기 전이나 후에 포털 UI를 사용 하 여 이러한 특성을 만들 수 있습니다. 포털에서 **loyaltyId** 특성을 만드는 경우 다음과 같이 참조 해야 합니다.

|Name     |사용 대상 |
|---------|---------|
|`extension_loyaltyId`  | 사용자 지정 정책|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

다음 예제에서는 Azure AD B2C 사용자 지정 정책 클레임 정의에서 사용자 지정 특성을 사용 하는 방법을 보여 줍니다.

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

다음 예제에서는 기술 프로필, 입력, 출력 및 지속형 클레임에서 Azure AD B2C 사용자 지정 정책에 사용자 지정 특성을 사용 하는 방법을 보여 줍니다.

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

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>MS Graph API와 함께 사용자 지정 특성 사용

Microsoft Graph API는 확장 특성이 있는 사용자를 만들고 업데이트하는 것을 지원합니다. Graph API의 확장 특성은 규칙을 사용 하 여 이름이 지정 됩니다 `extension_ApplicationClientID_attributename` . 여기서는 `ApplicationClientID` 응용 프로그램의 **응용 프로그램 (클라이언트) ID** 입니다 `b2c-extensions-app` . 확장 특성 이름에 표시 되는 **응용 프로그램 (클라이언트) ID** 에는 하이픈이 포함 되지 않습니다. 예를 들면 다음과 같습니다.

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>다음 단계

클레임을 추가 하 고 사용자 [지정 정책을 사용 하 여 사용자 입력](configure-user-input.md)을 사용자 지정 하는 방법은 지침을 따릅니다. 이 샘플에서는 기본 제공 클레임 ' city '를 사용 합니다. 사용자 지정 특성을 사용 하려면 ' city '를 사용자 지정 특성으로 바꿉니다.
