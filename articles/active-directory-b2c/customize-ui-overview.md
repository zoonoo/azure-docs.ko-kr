---
title: Azure Active Directory B2C에서 사용자 인터페이스 사용자 지정 정보| Microsoft Docs
description: Azure Active Directory B2C를 사용하는 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6c9109cf4d6d67d3d8001a9de1d54e24622a9286
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511187"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 인터페이스 사용자 지정 정보

Azure AD(Azure Active Directory) B2C가 애플리케이션에 제공하는 UI(사용자 인터페이스)를 브랜드하고 사용자 지정하는 기능은 고객에게 원활한 환경을 제공하는 데 중요합니다. 이러한 환경에는 등록, 로그인, 프로필 편집 및 암호 재설정이 포함됩니다. 이 문서에서는 애플리케이션의 UI를 사용자 지정하는 데 유용한 정보를 제공합니다.

이러한 환경에 있어 필요에 따라 다양한 방식으로 애플리케이션의 UI를 사용자 지정할 수 있습니다. 예를 들면 다음과 같습니다.

- [사용자 흐름](active-directory-b2c-reference-policies.md)을 사용하여 애플리케이션에 등록 또는 로그인, 암호 재설정 또는 프로필 편집 환경을 제공하는 경우 [UI를 사용자 지정하는 Azure Portal](tutorial-customize-ui.md)을 사용합니다.
- v2 사용자 흐름을 사용하는 경우 [페이지 레이아웃 템플릿](#page-layout-templates)을 사용하여 추가 사용자 지정 없이 사용자 흐름 페이지의 모양을 변경할 수 있습니다. 예를 들어 사용자 흐름의 모든 페이지에 바다색 또는 녹회색 테마를 적용할 수 있습니다.
- 로그인 전용, 첨부된 암호 재설정 페이지와 확인 이메일을 제공하는 경우 [Azure AD 로그인 페이지](../active-directory/fundamentals/customize-branding.md)에 사용되는 것과 동일한 사용자 지정 단계를 사용합니다.
- 고객이 로그인하기 전에 프로필을 편집하려는 경우 Azure AD 로그인 페이지를 사용자 지정하는 데 사용되는 것과 동일한 단계를 사용하여 사용자 지정하는 페이지로 리디렉션됩니다.
- [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 애플리케이션에서 등록 또는 로그인, 암호 재설정 또는 프로필 편집을 제공하는 경우 [UI를 사용자 지정하는 정책 파일](active-directory-b2c-ui-customization-custom.md)을 사용합니다.
- 고객의 결정에 따라 동적 콘텐츠를 제공해야 하는 경우 쿼리 문자열에 전송된 매개 변수에 따라 [페이지 콘텐츠를 변경할 수 있는 사용자 지정 정책](active-directory-b2c-ui-customization-custom-dynamic.md) 을 사용합니다. 예를 들어 Azure AD B2C 등록 또는 로그인 페이지의 배경 이미지는 웹 또는 모바일 애플리케이션에서 전달한 매개 변수에 따라 변경됩니다.
- Azure AD B2C [사용자 흐름](user-flow-javascript-overview.md) 또는 [사용자 지정 정책](page-contract.md)에서 JavaScript 클라이언트 쪽 코드를 사용할 수 있습니다.

Azure AD B2C는 고객의 브라우저에서 코드를 실행하고 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)라는 최신 방법을 사용합니다. 런타임 시 사용자 흐름이나 정책에서 지정하는 URL에서 콘텐츠를 로드합니다. 다른 페이지에 다른 URL을 지정합니다. URL에서 콘텐츠가 로드된 후 Azure AD B2C에서 삽입된 HTML 조각과 병합된 다음, 고객에게 표시됩니다.

사용자 고유의 HTML 및 CSS 파일을 사용하여 UI를 사용자 지정할 경우 시작하기 전에 다음 지침을 검토하세요.

- Azure AD B2C는 HTML 콘텐츠를 페이지에 병합합니다. Azure AD B2C가 제공하는 기본 콘텐츠를 복사하여 변경하지 마세요. 처음부터 HTML 콘텐츠를 빌드하고 기본 콘텐츠를 참조로 사용하는 것이 가장 좋습니다.
- JavaScript는 이제 사용자 지정 콘텐츠에 포함될 수 있습니다.
- 지원되는 브라우저 버전은 다음과 같습니다. 
    - Internet Explorer 11, 10 및 Microsoft Edge
    - Internet Explorer 9 및 8에 대한 제한된 지원
    - Google Chrome 42.0 이상
    - Mozilla Firefox 38.0 이상
- Azure AD B2C에서 삽입된 HTML에 의해 생성되는 POST 작업을 방해하므로 HTML에 양식 태그가 포함되지 않도록 합니다.

## <a name="page-layout-templates"></a>페이지 레이아웃 템플릿

v2 사용자 흐름의 경우 기본 페이지를 더 잘 보이도록 하고 나만의 사용자 지정을 위한 좋은 기초가 되는 미리 디자인된 템플릿을 선택할 수 있습니다.

왼쪽 메뉴의 **사용자 지정**에서 **페이지 레이아웃**을 선택합니다. 그런 다음, **템플릿(미리 보기)** 을 선택합니다.

![페이지 레이아웃 템플릿 선택](media/customize-ui-overview/template.png)

목록에서 템플릿을 선택합니다. 예를 들어 **Ocean Blue** 템플릿은 다음 레이아웃을 사용자 흐름 페이지에 적용합니다.

![Ocean Blue 템플릿](media/customize-ui-overview/ocean-blue.png)

템플릿을 선택하면 선택한 레이아웃이 사용자 흐름의 모든 페이지에 적용되고 각 페이지의 URI는 **사용자 지정 페이지 URI** 필드에 표시됩니다.

## <a name="where-do-i-store-ui-content"></a>UI 콘텐츠는 어디에 저장하나요?

사용자 고유의 HTML 및 CSS 파일을 사용하여 UI를 사용자 지정하면 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), 웹 서버, CDN, AWS S3 또는 파일 공유 시스템과 같이 어느 곳에나 UI 콘텐츠를 호스트할 수 있습니다. 중요한 점은 CORS를 사용하도록 설정한 상태에서 공개적으로 사용 가능한 HTTPS 엔드포인트에서 콘텐츠를 호스팅하는 것입니다. 콘텐츠에 지정하는 경우 절대 URL을 사용해야 합니다.

## <a name="how-do-i-get-started"></a>어떻게 시작하나요?

UI를 사용자 지정하려면 다음을 수행합니다.

- `<body>` 어딘가에 있는 빈 `<div id="api"></div>` 요소를 사용하여 잘 구성된 HTML 콘텐츠를 만듭니다. 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 표시합니다. 다음 예제에서는 최소 페이지를 보여줍니다.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- (허용된 CORS로)HTTPS 엔드포인트의 콘텐츠를 호스트합니다. CORS를 구성할 때 GET 및 OPTIONS 요청 메서드를 둘 다 사용할 수 있어야 합니다.
- CSS를 사용하여 Azure AD B2C가 페이지에 삽입하는 UI 요소의 스타일을 지정합니다. 다음 예제에서는 등록된 HTML 요소에 대한 설정도 포함하는 간단한 CSS 파일을 보여줍니다.

    ```css 
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- 생성한 콘텐츠를 사용하는 정책을 만들거나 편집합니다.

다음 표에는 Azure AD B2C가 콘텐츠에 있는 `<div id="api"></div>` 요소에 병합하는 HTML 조각이 나와 있습니다.

| 삽입된 페이지 | HTML 설명 |
| ------------- | ------------------- |
| ID 공급자 선택 영역 | 등록 또는 로그인 중에 고객이 선택할 수 있는 ID 공급자에 대한 단추 목록을 포함합니다. 이러한 단추에는 Facebook, Google 또는 로컬 계정(이메일 주소 또는 사용자 이름 기반)과 같은 소셜 ID 공급자가 포함됩니다. |
| 로컬 계정 등록 | 이메일 주소 또는 사용자 이름을 기준으로 하는 로컬 계정 등록 양식을 포함합니다. 양식은 텍스트 입력 상자, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같은 다른 입력 제어를 포함할 수 있습니다. |
| 소셜 계정 등록 | Facebook 또는 Google과 같은 소셜 ID 공급자의 기존 계정을 사용하여 등록하는 경우 나타날 수 있습니다. 등록 양식을 사용하여 고객으로부터 추가 정보를 수집해야 할 때 사용됩니다. |
| 통합 등록 또는 로그인 | Facebook, Google 또는 로컬 계정과 같은 소셜 ID 공급자를 사용할 수 있는 고객의 등록과 로그인을 모두 다룹니다. |
| Multi-Factor Authentication | 고객은 등록 또는 로그인 중에 전화 번호(텍스트 또는 음성 사용)를 확인할 수 있습니다. |
| 오류 | 고객에게 오류 정보를 제공합니다. |


## <a name="how-do-i-localize-content"></a>콘텐츠를 어떻게 지역화하나요?

Azure AD B2C 테넌트에서 [사용자 언어 지정](active-directory-b2c-reference-language-customization.md)을 사용하여 HTML 콘텐츠를 지역화합니다. 이 기능을 사용하도록 설정하면 Azure AD B2C가 Open ID Connect 매개 변수 `ui-locales`를 엔드포인트로 전달할 수 있습니다. 콘텐츠 서버는 이 매개 변수를 사용하여 언어별 HTML 페이지를 제공할 수 있습니다.

사용되는 로캘에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정합니다. 와일드카드 값 {Culture:RFC5646}을 사용하는 경우 적합한 구조를 호출합니다. 예를 들어 사용자 지정 페이지에 URI는 `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`과 같을 수 있습니다. `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`에서 콘텐츠를 끌어서 페이지를 프랑스어로 로드할 수 있습니다.

## <a name="examples"></a>예

사용자 지정 예제의 경우 다음 [샘플 템플릿 파일](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)을 다운로드하여 검토합니다.

## <a name="next-steps"></a>다음 단계

- 사용자 흐름을 사용하는 경우 다음과 같은 자습서를 사용하여 UI를 사용자 지정할 수 있습니다. [Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정](tutorial-customize-ui.md).
- 사용자 지정 정책을 사용하는 경우 다음과 같은 문서를 사용하여 UI를 사용자 지정할 수 있습니다. [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](active-directory-b2c-ui-customization-custom.md).

