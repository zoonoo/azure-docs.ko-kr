---
title: 사용자 인터페이스 사용자 지정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용하는 응용 프로그램에 대한 사용자 인터페이스를 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 839e13dffc7d15b8cd258dd4b7dda6776223d052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051732"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 인터페이스 사용자 지정

Azure Active Directory B2C(Azure AD B2C)가 고객에게 표시하는 사용자 인터페이스를 브랜딩하고 사용자 지정하면 응용 프로그램에서 원활한 사용자 환경을 제공하는 데 도움이 됩니다. 이러한 경험에는 등록, 로그인, 프로필 편집 및 암호 재설정이 포함됩니다. 이 문서에서는 사용자 흐름 및 사용자 지정 정책 모두에 대한 사용자 인터페이스(UI) 사용자 지정 방법을 소개합니다.

## <a name="ui-customization-in-different-scenarios"></a>다양한 시나리오의 UI 사용자 지정

사용자 환경의 UI를 사용자 지정하는 방법에는 여러 가지가 있으며 각 응용 프로그램은 서로 다른 시나리오에 적합합니다.

### <a name="user-flows"></a>사용자 흐름

[사용자 흐름을](user-flow-overview.md)사용하는 경우 기본 제공 페이지 *레이아웃 템플릿을*사용하거나 고유한 HTML 및 CSS를 사용하여 사용자 흐름 페이지의 모양을 변경할 수 있습니다. 두 방법 모두 이 문서의 후반부에서 설명합니다.

[Azure 포털을](tutorial-customize-ui.md) 사용하여 사용자 흐름에 대한 UI 사용자 지정을 구성합니다.

> [!TIP]
> 사용자 흐름 페이지의 배너 로고, 배경 이미지 및 배경 색만 수정하려면 이 문서의 후반부에서 설명하는 [회사 브랜딩(미리 보기)](#company-branding-preview) 기능을 사용해 볼 수 있습니다.

### <a name="custom-policies"></a>사용자 지정 정책

[사용자 지정 정책을](custom-policy-overview.md) 사용하여 응용 프로그램에서 등록 또는 등록, 암호 재설정 또는 프로필 편집을 제공하는 경우 정책 파일을 사용하여 [UI를 사용자 지정합니다.](custom-policy-ui-customization.md)

고객의 결정에 따라 동적 콘텐츠를 제공해야 하는 경우 쿼리 문자열로 전송되는 매개 변수에 따라 [페이지 콘텐츠를 동적으로 변경할](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) 수 있는 사용자 지정 정책을 사용합니다. 예를 들어 웹 또는 모바일 응용 프로그램에서 전달하는 매개 변수에 따라 Azure AD B2C 등록 또는 로그인 페이지의 배경 이미지를 변경할 수 있습니다.

### <a name="javascript"></a>JavaScript

[사용자 흐름](user-flow-javascript-overview.md) 및 [사용자 지정 정책](page-layout.md)모두에서 클라이언트 측 JavaScript 코드를 활성화할 수 있습니다.

### <a name="sign-in-only-ui-customization"></a>로그인 전용 UI 사용자 지정

함께 제공되는 암호 재설정 페이지 및 확인 이메일과 함께 로그인전용을 제공하는 경우 [Azure AD 로그인 페이지에](../active-directory/fundamentals/customize-branding.md)사용되는 것과 동일한 사용자 지정 단계를 사용합니다.

고객이 로그인하기 전에 프로필을 편집하려고 하면 Azure AD 로그인 페이지를 사용자 지정하는 데 사용되는 것과 동일한 단계를 사용하여 사용자 지정하는 페이지로 리디렉션됩니다.

## <a name="page-layout-templates"></a>페이지 레이아웃 템플릿

사용자 흐름은 사용자 환경 페이지를 전문적인 모양으로 제공하기 위해 선택할 수 있는 몇 가지 기본 제공 템플릿을 제공합니다. 이러한 레이아웃 템플릿은 사용자 고유의 사용자 지정을 위한 시작점으로도 사용할 수 있습니다.

왼쪽 메뉴에서 **사용자 지정에서** **페이지 레이아웃을** 선택한 다음 **템플릿**을 선택합니다.

![Azure 포털의 사용자 흐름 페이지에서 템플릿 선택 드롭다운](media/customize-ui-overview/template-selection.png)

다음으로 목록에서 템플릿을 선택합니다. 다음은 각 템플릿에 대한 로그인 페이지의 예입니다.

| 오션 블루 | 슬레이트 그레이 | 클래식 |
|:-:|:-:|:-:|
|![등록 페이지에 등록 할 때 렌더링 된 오션 블루 템플릿의 예](media/customize-ui-overview/template-ocean-blue.png)|![등록 페이지에서 렌더링된 슬레이트 회색 템플릿의 예](media/customize-ui-overview/template-slate-gray.png)|![등록 페이지에서 렌더링된 클래식 템플릿의 예](media/customize-ui-overview/template-classic.png)|

템플릿을 선택하면 선택한 레이아웃이 사용자 흐름의 모든 페이지에 적용되고 각 페이지의 URI는 **사용자 지정 페이지 URI** 필드에 표시됩니다.

## <a name="custom-html-and-css"></a>사용자 지정 HTML 및 CSS

사용자 지정된 HTML 및 CSS를 사용하여 고유한 정책 레이아웃을 디자인하려는 경우 정책에 있는 각 레이아웃 이름에 대해 "사용자 지정 페이지 콘텐츠 사용" 토글을 전환하여 디자인할 수 있습니다. 사용자 지정 레이아웃 구성에 관한 아래 지침을 따르십시오.

Azure AD B2C는 [CORS(원본 간 리소스 공유)라는](https://www.w3.org/TR/cors/)방법을 사용하여 고객의 브라우저에서 코드를 실행합니다.

런타임시 사용자 흐름 또는 사용자 지정 정책에서 지정한 URL에서 콘텐츠가 로드됩니다. 사용자 환경의 각 페이지는 해당 페이지에 대해 지정한 URL에서 해당 콘텐츠를 로드합니다. URL에서 콘텐츠를 로드하면 Azure AD B2C에 의해 삽입된 HTML 조각과 병합된 다음 페이지가 고객에게 표시됩니다.

사용자 고유의 HTML 및 CSS 파일을 사용하여 UI를 사용자 지정하기 전에 다음 지침을 검토하십시오.

- Azure AD B2C는 HTML 콘텐츠를 페이지에 **병합합니다.** Azure AD B2C가 제공하는 기본 콘텐츠를 복사하여 변경하지 마세요. 처음부터 HTML 콘텐츠를 빌드하고 기본 콘텐츠를 참조로 사용하는 것이 가장 좋습니다.
- **자바 스크립트는** [사용자 흐름과](user-flow-javascript-overview.md) 사용자 정의 정책 모두에 대한 사용자 정의 콘텐츠에 포함 될 수 [있습니다.](javascript-samples.md)
- 지원되는 **브라우저 버전은 다음과 같습니다.**
  - 인터넷 익스플로러 11, 10, 그리고 마이크로소프트 에지
  - Internet Explorer 9 및 8에 대한 제한된 지원
  - Google Chrome 42.0 이상
  - Mozilla Firefox 38.0 이상
  - iOS 및 macOS용 사파리, 버전 12 이상
- HTML에 **양식 태그를** 포함하지 마세요. 양식 태그는 Azure AD B2C에서 주입한 HTML에서 생성된 POST 작업을 방해합니다.

### <a name="where-do-i-store-ui-content"></a>UI 콘텐츠는 어디에 저장하나요?

고유한 HTML 및 CSS 파일을 사용하여 UI를 사용자 지정하는 경우 CORS를 지원하는 공개적으로 사용 가능한 HTTPS 끝점에서 UI 콘텐츠를 호스팅할 수 있습니다. 예를 들어 [Azure Blob 저장소,](../storage/blobs/storage-blobs-introduction.md)웹 서버, CDN, AWS S3 또는 파일 공유 시스템입니다.

중요한 점은 CORS를 사용하도록 설정한 상태에서 공개적으로 사용 가능한 HTTPS 엔드포인트에서 콘텐츠를 호스팅하는 것입니다. 콘텐츠에 지정하는 경우 절대 URL을 사용해야 합니다.

## <a name="get-started-with-custom-html-and-css"></a>사용자 지정 HTML 및 CSS 시작

다음 지침을 따라 사용자 환경 페이지에서 고유한 HTML 및 CSS 사용을 시작하십시오.

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

- (허용된 CORS로)HTTPS 엔드포인트의 콘텐츠를 호스트합니다. CORS를 구성할 때 GET 및 OPTIONS 요청 메서드를 둘 다 사용할 수 있어야 합니다.
- 사용자 흐름 또는 사용자 지정 정책을 작성하거나 편집하여 만든 콘텐츠를 사용합니다.

### <a name="html-fragments-from-azure-ad-b2c"></a>Azure AD B2C의 HTML 조각

다음 표에는 Azure AD B2C가 콘텐츠에 있는 `<div id="api"></div>` 요소에 병합하는 HTML 조각이 나와 있습니다.

| 삽입된 페이지 | HTML 설명 |
| ------------- | ------------------- |
| ID 공급자 선택 영역 | 등록 또는 로그인 중에 고객이 선택할 수 있는 ID 공급자에 대한 단추 목록을 포함합니다. 이러한 단추에는 Facebook, Google 또는 로컬 계정(이메일 주소 또는 사용자 이름 기반)과 같은 소셜 ID 공급자가 포함됩니다. |
| 로컬 계정 등록 | 이메일 주소 또는 사용자 이름을 기준으로 하는 로컬 계정 등록 양식을 포함합니다. 양식은 텍스트 입력 상자, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같은 다른 입력 제어를 포함할 수 있습니다. |
| 소셜 계정 등록 | Facebook 또는 Google과 같은 소셜 ID 공급자의 기존 계정을 사용하여 등록하는 경우 나타날 수 있습니다. 등록 양식을 사용하여 고객으로부터 추가 정보를 수집해야 하는 경우에 사용됩니다. |
| 통합 등록 또는 로그인 | Facebook, Google 또는 로컬 계정과 같은 소셜 ID 공급자를 사용할 수 있는 고객의 등록과 로그인을 모두 다룹니다. |
| Multi-Factor Authentication | 고객은 등록 또는 로그인 중에 전화 번호(텍스트 또는 음성 사용)를 확인할 수 있습니다. |
| Error | 고객에게 오류 정보를 제공합니다. |

## <a name="company-branding-preview"></a>회사 브랜딩(미리 보기)

Azure Active Directory [회사 브랜딩을](../active-directory/fundamentals/customize-branding.md)사용하여 배너 로고, 배경 이미지 및 배경 색으로 사용자 흐름 페이지를 사용자 지정할 수 있습니다.

사용자 흐름 페이지를 사용자 지정하려면 먼저 Azure Active Directory에서 회사 브랜딩을 구성한 다음 Azure AD B2C에서 사용자 흐름의 페이지 레이아웃에서 사용하도록 설정합니다.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>회사 브랜딩 구성

먼저 **회사 브랜딩**내에서 배너 로고, 배경 이미지 및 배경 색을 설정합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure 포털에서 **Azure AD B2C를**검색하고 선택합니다.
1. **관리에서** **회사 브랜딩을 선택합니다.**
1. 조직의 Azure [Active Directory 로그인 페이지에 브랜딩 추가 페이지의 단계를 따릅니다.](../active-directory/fundamentals/customize-branding.md)

Azure AD B2C에서 회사 브랜딩을 구성할 때 이러한 사항을 염두에 두십시오.

* Azure AD B2C의 회사 브랜딩은 현재 **배경 이미지,** **배너 로고**및 **배경 색** 사용자 지정으로 제한됩니다. 회사 브랜딩 창의 다른 속성(예: **고급 설정)은** *지원되지 않습니다.*
* 사용자 흐름 페이지에서 배경 이미지가 로드되기 전에 배경 색이 표시됩니다. 더 부드러운 로딩 환경을 위해 배경 이미지의 색상과 밀접하게 일치하는 배경 색을 선택하는 것이 좋습니다.
* 배너 로고는 사용자가 등록 사용자 흐름을 시작할 때 사용자에게 전송된 확인 이메일에 나타납니다.

### <a name="enable-branding-in-user-flow-pages"></a>사용자 흐름 페이지에서 브랜딩 사용

회사 브랜딩을 구성한 후에는 사용자 흐름에서 사용하도록 설정합니다.

1. Azure 포털의 왼쪽 메뉴에서 **Azure AD B2C를**선택합니다.
1. **정책에서** **사용자 흐름(정책)을 선택합니다.**
1. 회사 브랜딩을 사용하도록 설정하려는 사용자 흐름을 선택합니다. *v1* 로그인 및 *프로필 편집 v1* 사용자 흐름 유형에 대해 회사 브랜딩이 **지원되지 않습니다.**
1. **맞춤 설정에서** **페이지 레이아웃을**선택한 다음 브랜드할 레이아웃을 선택합니다. 예를 들어 **통합 등록을 선택하거나 페이지에 로그인합니다.**
1. 페이지 **레이아웃 버전(미리 보기)의**경우 버전 **1.2.0** 이상을 선택합니다.
1. **저장**을 선택합니다.

사용자 흐름의 모든 페이지를 브랜딩하려면 사용자 흐름의 각 페이지 레이아웃에 대한 페이지 레이아웃 버전을 설정합니다.

![Azure 포털의 Azure AD B2C의 페이지 레이아웃 선택](media/customize-ui-overview/portal-02-page-layout-select.png)

이 추가된 예제에서는 Ocean Blue 템플릿을 사용하는 사용자 흐름 페이지에 *등록 및 로그인에* 사용자 배너 로고 및 배경 이미지를 보여 주었습니다.

![Azure AD B2C에서 제공하는 브랜드 등록/로그인 페이지](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>사용자 지정 HTML에서 회사 브랜딩 자산 사용

사용자 지정 HTML에서 회사 브랜딩 에셋을 사용하려면 `<div id="api">` 태그 외부에 다음 태그를 추가합니다.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

이미지 소스가 배경 이미지와 배너 로고로 대체됩니다. 사용자 지정 [HTML 및 CSS](#get-started-with-custom-html-and-css) 시작 섹션에서 설명한 대로 CSS 클래스를 사용하여 에셋의 스타일을 지정하고 페이지의 에셋을 배치합니다.

## <a name="localize-content"></a>콘텐츠 지역화

Azure AD B2C 테넌트에서 [사용자 언어 지정](user-flow-language-customization.md)을 사용하여 HTML 콘텐츠를 지역화합니다. 이 기능을 사용하면 Azure AD B2C가 OpenID `ui-locales` Connect 매개 변수를 끝점으로 전달할 수 있습니다. 콘텐츠 서버는 이 매개 변수를 사용하여 언어별 HTML 페이지를 제공할 수 있습니다.

사용되는 로캘에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정합니다. 와일드 카드 값 `{Culture:RFC5646}`을 사용하는 경우 적합한 구조를 호출합니다.

예를 들어 사용자 지정 페이지 URI는 다음과 같을 수 있습니다.

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

다음에서 콘텐츠를 끌어들여 프랑스어로 페이지를 로드할 수 있습니다.

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>예

GitHub의 [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) 리포지토리에서 여러 샘플 템플릿 파일을 찾을 수 있습니다.

템플릿의 샘플 HTML 및 CSS 파일은 [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) 디렉터리에 있습니다.

## <a name="next-steps"></a>다음 단계

- **사용자 흐름을**사용하는 경우 자습서를 사용하여 UI를 사용자 지정할 수 있습니다.

    [Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정](tutorial-customize-ui.md).
- **사용자 지정 정책을**사용하는 경우 문서로 UI 를 사용자 지정할 수 있습니다.

    [Azure Active Directory B2C의 사용자 지정 정책을 사용하여 응용 프로그램의 사용자 인터페이스를 사용자 지정합니다.](custom-policy-ui-customization.md)
