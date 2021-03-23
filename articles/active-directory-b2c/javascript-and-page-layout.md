---
title: JavaScript 및 페이지 레이아웃 버전
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 JavaScript를 사용 하도록 설정 하 고 페이지 레이아웃 버전을 사용 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d234e57f7c11b0d9f2cd212bde93a8b8e478ef41
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781367"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 JavaScript 및 페이지 레이아웃 버전

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C은 사용자 흐름 및 사용자 지정 정책의 사용자 인터페이스 요소에 대 한 HTML, CSS 및 JavaScript를 포함 하는 패키지 콘텐츠 집합을 제공 합니다. 응용 프로그램에 JavaScript를 사용 하도록 설정 하려면

::: zone pivot="b2c-user-flow"

* [페이지 레이아웃](page-layout.md) 선택
* Azure Portal를 사용 하 여 사용자 흐름에서 사용 하도록 설정
* 요청에서 [b2clogin.com](b2clogin.md) 사용

::: zone-end

::: zone pivot="b2c-custom-policy"

* [페이지 레이아웃](page-layout.md) 선택
* [사용자 지정 정책](custom-policy-overview.md) 에 요소 추가
* 요청에서 [b2clogin.com](b2clogin.md) 사용

::: zone-end

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>페이지 레이아웃 버전 선택

JavaScript 클라이언트 쪽 코드를 사용 하도록 설정 하려는 경우 JavaScript를 기반으로 하는 요소를 변경할 수 없어야 합니다. 변경할 수 없으면 사용자 페이지에서 예기치 않은 동작이 발생할 수 있습니다. 이러한 문제를 방지 하려면 페이지 레이아웃 사용을 적용 하 고 JavaScript를 기반으로 하는 콘텐츠 정의를 변경할 수 없도록 페이지 레이아웃 버전을 지정 합니다. JavaScript를 사용 하지 않으려는 경우에도 페이지에 대 한 페이지 레이아웃 버전을 지정할 수 있습니다.

::: zone pivot="b2c-user-flow"

사용자 흐름 페이지에 대 한 페이지 레이아웃 버전을 지정 하려면: 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. **페이지 레이아웃** 을 선택 합니다. **레이아웃 이름을** 선택 하 고 **페이지 레이아웃 버전 (미리 보기)** 을 선택 합니다.

여러 페이지 레이아웃 버전에 대 한 자세한 내용은 [페이지 레이아웃 버전 변경 로그](page-layout.md)를 참조 하세요.

![페이지 레이아웃 버전 드롭다운을 보여 주는 포털의 페이지 레이아웃 설정](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

응용 프로그램의 사용자 인터페이스 요소에 대 한 [페이지 레이아웃](contentdefinitions.md#select-a-page-layout) 을 선택 합니다.

[](contentdefinitions.md#migrating-to-page-layout) `contract` 사용자 지정 정책의 *모든* 콘텐츠 정의에 대해 페이지 버전으로 페이지 레이아웃 버전을 정의 합니다. 값 형식에는 `contract` _urn: com: microsoft: aad: b2c: elements:**contract**:p age-name: version_ 이라는 단어가 포함 되어야 합니다. 페이지 버전으로 [페이지 레이아웃으로 마이그레이션하](contentdefinitions.md#migrating-to-page-layout) 는 방법에 대해 알아봅니다.

다음 예제에서는 페이지 계약이 포함 된 콘텐츠 정의 식별자와 해당 하는 **Datauri** 를 보여 줍니다. 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>JavaScript 사용

::: zone pivot="b2c-user-flow"

사용자 흐름 **속성** 에서 JavaScript를 사용 하도록 설정할 수 있습니다. JavaScript를 사용 하도록 설정 하면 페이지 레이아웃 사용도 적용 됩니다. 그런 다음, 다음 섹션에 설명 된 대로 사용자 흐름에 대 한 페이지 레이아웃 버전을 설정할 수 있습니다.

![JavaScript 설정 사용이 강조 표시 된 사용자 흐름 속성 페이지](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

[RelyingParty](relyingparty.md) 요소에 **ScriptExecution** 요소를 추가하여 스크립트 실행을 가능하게 합니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
1. **Scriptexecution** 요소를 **RelyingParty** 요소에 추가 합니다.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. 파일을 저장하고 업로드합니다.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>JavaScript 사용 지침

JavaScript를 사용하여 애플리케이션의 인터페이스를 사용자 지정하는 경우 다음 지침을 따르세요.

- `<a>` HTML 요소에 클릭 이벤트를 바인딩하지 않습니다.
- Azure AD B2C 코드 또는 주석에 대한 종속성을 사용하지 않습니다.
- Azure AD B2C HTML 요소의 순서나 계층 구조를 변경하지 않습니다. Azure AD B2C 정책을 사용하여 UI 요소의 순서를 제어합니다.
- 다음 사항을 고려하여 RESTful 서비스를 호출할 수 있습니다.
    - 클라이언트 쪽 HTTP 호출을 허용하도록 RESTful 서비스 CORS를 설정해야 할 수 있습니다.
    - RESTful 서비스가 안전하고 HTTPS 프로토콜만 사용하는지 확인합니다.
    - JavaScript를 사용하여 Azure AD B2C 엔드포인트를 직접 호출하지 않습니다.
- JavaScript를 포함하거나 외부 JavaScript 파일에 연결할 수 있습니다. 외부 JavaScript 파일을 사용하는 경우 상대 URL이 아닌 절대 URL을 사용해야 합니다.
- JavaScript 프레임워크:
    - Azure AD B2C는 [jQuery의 특정 버전](page-layout.md#jquery-version)을 사용 합니다. 다른 버전의 jQuery를 포함하지 않습니다. 동일한 페이지에 둘 이상의 버전을 사용하면 문제가 발생합니다.
    - RequireJS 사용은 지원되지 않습니다.
    - 대부분의 JavaScript 프레임워크는 Azure AD B2C에서 지원되지 않습니다.
- `window.SETTINGS`, `window.CONTENT` 개체를 호출하여 현재 UI 언어와 같은 Azure AD B2C 설정을 읽을 수 있습니다. 이러한 개체의 값을 변경하지 않습니다.
- Azure AD B2C 오류 메시지를 사용자 지정하려면 정책의 지역화를 사용합니다.
- 정책을 통해 수행할 수 있는 작업이 있다면 일반적으로 정책을 사용하는 것이 좋습니다.

## <a name="javascript-samples"></a>JavaScript 샘플

### <a name="show-or-hide-a-password"></a>암호 표시 또는 숨기기

고객이 가입에 성공하도록 지원하는 일반적인 방법은 고객이 암호로 입력한 내용을 확인할 수 있도록 하는 것입니다. 이 옵션은 사용자가 필요한 경우 쉽게 암호를 보고 수정할 수 있도록 하여 가입을 지원합니다. 암호 유형의 필드에는 **암호 표시** 레이블이 지정된 확인란이 있습니다.  사용자는 이 확인란을 통해 암호를 일반 텍스트로 볼 수 있습니다. 자체 어설션된 페이지의 가입 또는 로그인 템플릿에 다음 코드 조각을 포함합니다.

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>사용 약관 추가

**사용 약관** 확인란을 포함하려는 페이지에 다음 코드를 포함합니다. 이 확인란은 일반적으로 로컬 계정 가입 및 소셜 계정 가입 페이지에 필요합니다.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

코드에서 `termsOfUseUrl`을 해당 사용 약관 링크로 바꿉니다. 디렉터리에 대해 **termsOfUse** 라는 새 사용자 특성을 만든 다음 **termsOfUse** 를 사용자 특성으로 포함 합니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램의 사용자 인터페이스를 사용자 지정 하는 방법에 대 한 자세한 내용은 [Azure Active Directory B2C에서 응용 프로그램의 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)을 확인 하세요.
