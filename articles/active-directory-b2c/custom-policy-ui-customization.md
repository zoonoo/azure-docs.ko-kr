---
title: 사용자 지정 정책으로 앱의 사용자 인터페이스 사용자 지정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 사용자 인터페이스를 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87c3a3a904705b9fcb702c4745c4c80a4b447e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476725"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서의 단계를 완료하면 브랜드 및 모양과 함께 등록 및 로그인 맞춤 정책을 만듭니다. Azure AD B2C(Azure Active Directory B2C)를 사용하면 사용자에게 제공되는 HTML 및 CSS 콘텐츠를 거의 완벽하게 제어할 수 있습니다. 사용자 지정 정책을 사용하는 경우 Azure Portal의 컨트롤을 사용하는 대신 XML로 UI 사용자 지정을 구성합니다.

## <a name="prerequisites"></a>사전 요구 사항

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

## <a name="4-modify-the-extensions-file"></a>4. 확장 파일 수정

UI 사용자 지정을 구성하려면 기본 파일에서 확장 파일로 **ContentDefinition** 및 해당 자식 요소를 복사합니다.

1. 정책의 기본 파일을 엽니다(예: 예를 들어, <em> `SocialAndLocalAccounts/` </em>. 이 기본 파일은 필수 구성 조건인 [사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)팩에 포함된 정책 파일 중 하나입니다.
1. **ContentDefinitions** 요소의 전체 내용을 검색한 후 복사합니다.
1. 확장 파일을 엽니다(예: 예: *TrustFrameworkExtensions.xml* **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 복사한 **ContentDefinitions**의 전체 내용을 **BuildingBlocks** 요소의 자식으로 붙여 넣습니다.
1. 복사한 XML에서 `Id="api.signuporsignin"`을 포함하는 **ContentDefinition** 요소를 검색합니다.
1. **LoadUri** 값을 스토리지에 업로드한 HTML 파일의 URL로 변경합니다. `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`)을 입력합니다.

    사용자 지정 정책은 다음과 같은 코드 코드 조각과 같아야 합니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. 확장 파일을 저장합니다.

## <a name="5-upload-and-test-your-updated-custom-policy"></a>5. 업데이트된 사용자 지정 정책을 업로드하고 테스트합니다.

### <a name="51-upload-the-custom-policy"></a>5.1 사용자 지정 정책 업로드

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure AD B2C를**검색하고 선택합니다.
1. **정책에서** **ID 환경 프레임워크를**선택합니다.
1. **사용자 지정 정책 업로드를 선택합니다.**
1. 이전에 변경한 확장 파일을 업로드합니다.

### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 **지금 실행을** 사용하여 사용자 지정 정책 테스트

1. 업로드한 정책을 선택한 다음 지금 **실행을**선택합니다.
1. 이메일 주소를 사용하여 등록할 수 있습니다.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>동적 사용자 지정 페이지 콘텐츠 URI 구성

Azure AD B2C 사용자 지정 정책을 사용하여 URL 경로또는 쿼리 문자열에서 매개 변수를 보낼 수 있습니다. 매개 변수를 HTML 엔드포인트로 전달하면 페이지 콘텐츠를 동적으로 변경할 수 있습니다. 예를 들어 웹 또는 모바일 애플리케이션에서 전달한 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지에서 배경 이미지를 변경할 수 있습니다. 매개 변수는 응용 프로그램 ID, 언어 ID 또는 사용자 지정 쿼리 문자열 `campaignId`매개 변수와 같은 모든 [클레임 확인자일](claim-resolver-overview.md)수 있습니다.

### <a name="sending-query-string-parameters"></a>쿼리 문자열 매개 변수 보내기

쿼리 문자열 매개 변수를 보내려면 [relyparty 정책에서](relyingparty.md)아래와 같이 `ContentDefinitionParameters` 요소를 추가합니다.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

콘텐츠 정의에서 의 값을 `LoadUri` 로 `https://<app_name>.azurewebsites.net/home/unified`변경합니다. 사용자 지정 `ContentDefinition` 정책은 다음과 같은 코드 코드 조각과 같아야 합니다.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C가 페이지를 로드할 때 웹 서버 끝점을 호출합니다.

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>동적 페이지 콘텐츠 URI

콘텐츠는 사용되는 매개 변수에 따라 다른 위치에서 끌어당길 수 있습니다. CORS 지원 끝점에서 콘텐츠를 호스트할 폴더 구조를 설정합니다. 예를 들어 다음 구조로 콘텐츠를 구성할 수 있습니다. *언어 / HTML 파일 당*루트 폴더 / 폴더 . 예를 들어 사용자 지정 페이지 URI는 다음과 같을 수 있습니다.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C는 프랑스어에 대해 언어에 `fr` 대한 두 문자 ISO 코드를 보냅니다.

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>다음 단계

사용자 지정할 수 있는 UI 요소에 대한 자세한 내용은 [사용자 흐름에 대한 UI 사용자 지정에 대한 참조 가이드를](customize-ui-overview.md)참조하십시오.
