---
title: 사용자 인터페이스 사용자 지정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용 하는 응용 프로그램에 대 한 사용자 인터페이스를 사용자 지정 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d737d010f323a5d5b230091ad07ba530d25d6e51
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949409"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 인터페이스 사용자 지정

고객에 게 표시 되 Azure Active Directory B2C는 사용자 인터페이스 (Azure AD B2C)의 브랜딩 및 사용자 지정은 응용 프로그램에서 원활한 사용자 환경을 제공 하는 데 도움이 됩니다. 이러한 환경에는 등록, 로그인, 프로필 편집 및 암호 재설정이 포함 됩니다. 이 문서에서는 사용자 흐름과 사용자 지정 정책에 대 한 UI (사용자 인터페이스) 사용자 지정 방법을 소개 합니다.

## <a name="ui-customization-in-different-scenarios"></a>다양 한 시나리오에서 UI 사용자 지정

응용 프로그램의 UI를 사용자 지정 하는 방법에는 여러 가지가 있으며, 각 시나리오에 적합 합니다.

### <a name="user-flows"></a>사용자 흐름

[사용자 흐름](active-directory-b2c-reference-policies.md)을 사용 하는 경우 기본 제공 *페이지 레이아웃 템플릿을*사용 하거나 고유한 HTML 및 CSS를 사용 하 여 사용자 흐름 페이지의 모양을 변경할 수 있습니다. 두 방법 모두이 문서의 뒷부분에서 설명 합니다.

[Azure Portal](tutorial-customize-ui.md) 를 사용 하 여 사용자 흐름에 대 한 UI 사용자 지정을 구성할 수 있습니다.

### <a name="custom-policies"></a>사용자 지정 정책

[사용자 지정 정책을](active-directory-b2c-overview-custom.md) 사용 하 여 응용 프로그램에서 등록 또는 로그인, 암호 재설정 또는 프로필 편집 기능을 제공 하 [는 경우 정책 파일을 사용 하 여 UI를 사용자 지정](active-directory-b2c-ui-customization-custom.md)합니다.

고객의 결정에 따라 동적 콘텐츠를 제공 해야 하는 경우 쿼리 문자열에 전송 된 매개 변수에 따라 [페이지 콘텐츠를 동적으로 변경할](active-directory-b2c-ui-customization-custom-dynamic.md) 수 있는 사용자 지정 정책을 사용 합니다. 예를 들어 웹 또는 모바일 응용 프로그램에서 전달 하는 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지에서 배경 이미지를 변경할 수 있습니다.

### <a name="javascript"></a>JavaScript

[사용자 흐름과](user-flow-javascript-overview.md) [사용자 지정 정책](page-layout.md)모두에서 클라이언트 쪽 JavaScript 코드를 사용 하도록 설정할 수 있습니다.

### <a name="sign-in-only-ui-customization"></a>로그인 전용 UI 사용자 지정

함께 제공 되는 암호 재설정 페이지 및 확인 메일과 함께 로그인만 제공 하는 경우에는 [AZURE AD 로그인 페이지](../active-directory/fundamentals/customize-branding.md)에 사용 되는 것과 동일한 사용자 지정 단계를 사용 합니다.

사용자가 로그인 하기 전에 프로필을 편집 하려고 하면 Azure AD 로그인 페이지를 사용자 지정 하는 데 사용 되는 것과 동일한 단계를 사용 하 여 사용자 지정 하는 페이지로 리디렉션됩니다.

## <a name="page-layout-templates"></a>페이지 레이아웃 템플릿

사용자 흐름은 사용자 환경 페이지에 전문적인 모양을 제공 하기 위해 선택할 수 있는 몇 가지 기본 제공 템플릿을 제공 합니다. 이러한 레이아웃 템플릿은 사용자 고유의 사용자 지정을 위한 시작 지점으로 사용할 수도 있습니다.

왼쪽 메뉴의 **사용자 지정** 에서 **페이지 레이아웃** 을 선택 하 고 **템플릿**을 선택 합니다.

![Azure Portal의 사용자 흐름 페이지에서 템플릿 선택 드롭다운](media/customize-ui-overview/template-selection.png)

그런 다음 목록에서 템플릿을 선택 합니다. 각 템플릿에 대 한 로그인 페이지의 예는 다음과 같습니다.

| 바다색 | 슬레이트 회색 | 클래식 |
|:-:|:-:|:-:|
|![등록 로그인 페이지에서 렌더링 된 대양 Blue 템플릿의 예](media/customize-ui-overview/template-ocean-blue.png)|![로그인 페이지에서 렌더링 되는 슬레이트 회색 템플릿의 예](media/customize-ui-overview/template-slate-gray.png)|![등록 로그인 페이지에서 렌더링 된 클래식 템플릿의 예](media/customize-ui-overview/template-classic.png)|

템플릿을 선택하면 선택한 레이아웃이 사용자 흐름의 모든 페이지에 적용되고 각 페이지의 URI는 **사용자 지정 페이지 URI** 필드에 표시됩니다.

## <a name="custom-html-and-css"></a>사용자 지정 HTML 및 CSS

Azure AD B2C [CORS (원본 간 리소스 공유)](https://www.w3.org/TR/cors/)라는 방법을 사용 하 여 고객의 브라우저에서 코드를 실행 합니다.

런타임에 사용자 흐름이 나 사용자 지정 정책에서 지정한 URL에서 콘텐츠가 로드 됩니다. 사용자 환경의 각 페이지는 해당 페이지에 대해 지정한 URL에서 해당 콘텐츠를 로드 합니다. URL에서 콘텐츠를 로드 한 후에는 Azure AD B2C에 의해 삽입 된 HTML 조각과 병합 되 고,이 페이지는 고객에 게 표시 됩니다.

사용자 고유의 HTML 및 CSS 파일을 사용 하 여 UI를 사용자 지정 하기 전에 다음 지침을 검토 합니다.

- Azure AD B2C HTML 콘텐츠를 페이지에 **병합** 합니다. Azure AD B2C가 제공하는 기본 콘텐츠를 복사하여 변경하지 마세요. 처음부터 HTML 콘텐츠를 빌드하고 기본 콘텐츠를 참조로 사용하는 것이 가장 좋습니다.
- [사용자 흐름과](user-flow-javascript-overview.md) 사용자 [지정 정책](javascript-samples.md)모두에 대 한 사용자 지정 콘텐츠에 **JavaScript** 를 포함할 수 있습니다.
- 지원 되는 **브라우저 버전** 은 다음과 같습니다.
  - Internet Explorer 11, 10 및 Microsoft Edge
  - Internet Explorer 9 및 8에 대한 제한된 지원
  - Google Chrome 42.0 이상
  - Mozilla Firefox 38.0 이상
- HTML에 **form 태그** 를 포함 하지 않습니다. 양식 태그는 Azure AD B2C으로 삽입 된 HTML에서 생성 된 POST 작업을 방해 합니다.

### <a name="where-do-i-store-ui-content"></a>UI 콘텐츠는 어디에 저장하나요?

고유한 HTML 및 CSS 파일을 사용 하 여 UI를 사용자 지정 하는 경우 CORS를 지 원하는 공개적으로 사용 가능한 HTTPS 끝점에서 UI 콘텐츠를 호스트할 수 있습니다. 예를 들어 [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md), 웹 서버, CDNS, AWS S3 또는 파일 공유 시스템입니다.

중요한 점은 CORS를 사용하도록 설정한 상태에서 공개적으로 사용 가능한 HTTPS 엔드포인트에서 콘텐츠를 호스팅하는 것입니다. 콘텐츠에 지정하는 경우 절대 URL을 사용해야 합니다.

## <a name="get-started-with-custom-html-and-css"></a>사용자 지정 HTML 및 CSS 시작

사용자 환경 페이지에서 다음 지침에 따라 고유한 HTML 및 CSS를 사용 하 여 시작 하세요.

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
- 사용자 흐름이 나 사용자 지정 정책을 만들거나 편집 하 여 만든 콘텐츠를 사용 합니다.

### <a name="html-fragments-from-azure-ad-b2c"></a>Azure AD B2C에서 HTML 조각

다음 표에는 Azure AD B2C가 콘텐츠에 있는 `<div id="api"></div>` 요소에 병합하는 HTML 조각이 나와 있습니다.

| 삽입된 페이지 | HTML 설명 |
| ------------- | ------------------- |
| ID 공급자 선택 영역 | 등록 또는 로그인 중에 고객이 선택할 수 있는 ID 공급자에 대한 단추 목록을 포함합니다. 이러한 단추에는 Facebook, Google 또는 로컬 계정(이메일 주소 또는 사용자 이름 기반)과 같은 소셜 ID 공급자가 포함됩니다. |
| 로컬 계정 등록 | 이메일 주소 또는 사용자 이름을 기준으로 하는 로컬 계정 등록 양식을 포함합니다. 양식은 텍스트 입력 상자, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같은 다른 입력 제어를 포함할 수 있습니다. |
| 소셜 계정 등록 | Facebook 또는 Google과 같은 소셜 ID 공급자의 기존 계정을 사용하여 등록하는 경우 나타날 수 있습니다. 등록 양식을 사용 하 여 고객 으로부터 추가 정보를 수집 해야 하는 경우에 사용 됩니다. |
| 통합 등록 또는 로그인 | Facebook, Google 또는 로컬 계정과 같은 소셜 ID 공급자를 사용할 수 있는 고객의 등록과 로그인을 모두 다룹니다. |
| Multi-factor authentication | 고객은 등록 또는 로그인 중에 전화 번호(텍스트 또는 음성 사용)를 확인할 수 있습니다. |
| 오류 | 고객에게 오류 정보를 제공합니다. |

## <a name="localize-content"></a>콘텐츠 지역화

Azure AD B2C 테넌트에서 [사용자 언어 지정](active-directory-b2c-reference-language-customization.md)을 사용하여 HTML 콘텐츠를 지역화합니다. 이 기능을 사용 하도록 설정 하면 Azure AD B2C Openid connect Connect 매개 변수 `ui-locales`를 끝점으로 전달할 수 있습니다. 콘텐츠 서버는 이 매개 변수를 사용하여 언어별 HTML 페이지를 제공할 수 있습니다.

사용되는 로캘에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정합니다. 와일드 카드 값 `{Culture:RFC5646}`을 사용하는 경우 적합한 구조를 호출합니다.

예를 들어 사용자 지정 페이지 URI는 다음과 같습니다.

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

다음에서 콘텐츠를 당겨 프랑스어로 페이지를 로드할 수 있습니다.

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>예시

GitHub의 [B2C](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) 저장소에서 여러 샘플 템플릿 파일을 찾을 수 있습니다.

템플릿의 샘플 HTML 및 CSS 파일은 [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) 디렉터리에 있습니다.

## <a name="next-steps"></a>다음 단계

- **사용자 흐름**을 사용 하는 경우 자습서를 사용 하 여 UI 사용자 지정을 시작할 수 있습니다.

    [Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정](tutorial-customize-ui.md).
- **사용자 지정 정책을**사용 하는 경우 다음 문서를 사용 하 여 UI 사용자 지정을 시작할 수 있습니다.

    [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](active-directory-b2c-ui-customization-custom.md).
