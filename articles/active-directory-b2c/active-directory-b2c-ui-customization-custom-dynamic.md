---
title: "Azure Active Directory B2C: 사용자 지정 정책을 사용하여 동적으로 Azure AD B2C UI(사용자 인터페이스)를 사용자 지정"
description: "런타임 시 동적으로 변경되는 HTML/CSS 콘텐츠가 있는 여러 브랜딩 환경을 지원하는 방법"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책을 사용하여 동적 콘텐츠를 포함한 UI 구성
Azure AD B2C 사용자 지정 정책을 사용하면 쿼리 문자열에 있는 매개 변수를 통해 보낼 수 있습니다. 매개 변수는 HTML 끝점에 전달되고 페이지 콘텐츠를 동적으로 변경할 수 있습니다. 예를 들어 웹/모바일 응용 프로그램에서 전달한 매개 변수를 기준으로 B2C 등록 또는 로그인 배경 이미지를 변경할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
이 문서는 사용자 지정 정책을 사용하여 **동적 콘텐츠**를 포함한 Azure AD B2C 사용자 인터페이스를 사용자 지정하는 방법에 중점을 둡니다. 사용자 지정 정책 UI 사용자 지정을 시작하려면 [사용자 지정 정책에서 UI 사용자 지정](active-directory-b2c-ui-customization-custom.md) 문서를 참조하세요. 

>[!NOTE]
>
>  [사용자 지정 정책에서 UI 사용자 지정 구성](active-directory-b2c-ui-customization-custom.md) 문서에서는 사용자 지정 정책을 사용하여 Azure AD B2C UI 사용자 지정의 기본 사항에 대해 설명합니다.
> * 페이지 UI(사용자 인터페이스) 사용자 지정 기능
> * 샘플 콘텐츠를 사용하여 페이지 UI 사용자 지정 기능을 테스트하는 데 도움이 되는 도구입니다.
> * 페이지의 각 형식에서 코어 UI 요소입니다.
> * 이 기능을 실행할 때 모범 사례입니다.

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>HTML5/CSS 템플릿에 대한 링크를 사용자 경험에 추가

사용자 지정 정책에서 콘텐츠 정의는 지정된 UI 단계에 사용되는 HTML5 페이지 URI를 정의합니다. 예를 들어, 로그인 또는 등록 페이지입니다. 기본 정책에서는 HTML5 파일(해당 CSS 참조)의 URI를 가리켜서 기본 디자인을 정의합니다. 확장 정책에서 해당 HTML5 파일에 대한 LoadUri를 재정의하여 디자인을 수정할 수 있습니다. 따라서 콘텐츠 정의에는 적절하게 HTML5/CSS 파일을 선별하여 정의된 외부 콘텐츠에 대한 URL이 포함됩니다. 

`ContentDefinitions` 섹션에는 일련의 `ContentDefinition` XML 요소가 포함됩니다. `ContentDefinition` 요소의 ID 특성은 콘텐츠 정의에 관련된 페이지의 형식을 지정합니다. 따라서 컨텍스트에서 사용자 지정 HTML5/CSS 템플릿을 사용하려고 합니다. 다음 표에서는 IEF 엔진이 인식할 수 있는 콘텐츠 정의 ID 집합과 이에 관련된 페이지 형식을 설명합니다.

| 콘텐츠 정의 ID | 기본 HTML5 템플릿| 설명 | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **오류 페이지**입니다. 예외 또는 오류가 발생하면 이 페이지가 표시됩니다. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **ID 공급자 선택 페이지**입니다. 이 페이지는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 옵션은 일반적으로 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **등록을 위한 ID 공급자 선택 페이지**입니다. 이 페이지는 등록하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 옵션은 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **암호 찾기 페이지**입니다. 사용자가 암호 재설정을 시작하기 위해 완성해야 하는 양식이 있습니다.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **로컬 계정 로그인 페이지**입니다. 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정으로 로그인하기 위한 양식이 있습니다. 양식에는 텍스트 입력 상자 및 암호 입력란이 포함될 수 있습니다. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **로컬 계정 등록 페이지** 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정을 등록하기 위한 양식이 있습니다. 양식에는 텍스트 입력란, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같이 다양한 입력 컨트롤이 포함될 수 있습니다. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 페이지**입니다. 등록 또는 로그인 중에 사용자가 이 페이지에서 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **소셜 계정 등록 페이지**입니다. 이 페이지에는 소셜 ID 공급자에서 기존 계정을 사용하여 등록할 때 사용자가 완성해야 하는 등록 양식이 있습니다. 암호 입력 필드를 제외하고는 위의 소셜 계정 등록 페이지와 비슷합니다. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **프로필 업데이트 페이지**입니다. 사용자가 자신의 프로필을 업데이트하는 데 사용할 수 있는 양식이 있습니다. 암호 입력 필드를 제외하고는 소셜 계정 등록 페이지와 비슷합니다. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **통합 등록 또는 로그인 페이지**입니다. 이 페이지는 사용자의 등록 및 로그인을 모두 처리합니다. 사용자는 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정을 사용할 수 있습니다.  |

## <a name="serving-dynamic-content"></a>동적 콘텐츠 제공
이전 문서 [사용자 지정 정책에서 UI 사용자 지정 구성](active-directory-b2c-ui-customization-custom.md)에서는 Azure Blob Storage에 HTML5 파일을 업로드했습니다. 해당 HTML5 파일은 고정적이고 각 요청에 동일한 HTML 콘텐츠를 렌더링합니다. 이 문서에서는 쿼리 문자열 매개 변수를 허용하고 적절하게 대응할 수 있는 ASP.Net 웹앱을 사용합니다. 이 연습에서는 다음을 수행합니다.
* HTML5 템플릿을 호스팅하는 ASP.NET Core 웹 응용 프로그램 만들기 
* 사용자 지정 HTML5 템플릿 추가 _unified.cshtml_ 
* Azure 서비스 앱에 웹앱 게시 
* 웹앱에 CORS 설정
* `LoadUri` 요소를 재정의하여 HTML5 파일 가리키기

## <a name="step-1-create-an-aspnet-web-app"></a>1단계: ASP.NET 웹앱 만들기

1.  Visual Studio에서 **파일 > 새로 만들기 > 프로젝트**를 선택하여 프로젝트를 만듭니다.
2.  **새 프로젝트** 대화 상자에서 **Visual C# > 웹 > ASP.NET Core 웹 응용 프로그램(.NET Core)**을 선택합니다.
3.  응용 프로그램 이름(예: Contoso.AADB2C.UI)을 지정한 후 **확인**을 선택합니다.

    ![새 Visual Studio 프로젝트를 만듭니다.](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  **웹 응용 프로그램** 템플릿을 선택합니다.
5.  인증을 **인증 없음**으로 설정해야 합니다.

    ![Web API 템플릿을 선택합니다.](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  **확인**을 클릭하여 프로젝트를 만듭니다.

## <a name="step-2-create-mvc-view"></a>2단계: MVC 뷰 만들기
### <a name="step-21-download-b2c-built-in-html5-template"></a>2.1단계: B2C 기본 제공 HTML5 템플릿 다운로드
사용자 지정 HTML5 템플릿은 B2C 기본 제공 HTML5 템플릿을 기반으로 합니다. [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml)을 다운로드하거나 [시작 팩](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)에서 다운로드할 수 있습니다. 통합 등록 또는 로그인 페이지에 이 HTML5 파일을 사용합니다.

### <a name="step-22-add-mvc-view"></a>2.2단계 MVC 보기 추가
1. Views/Home 폴더 및 **추가 > 새 항목**을 마우스 오른쪽 단추로 클릭합니다.
 ![MVC 새 항목 추가](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. **새 항목 추가 - Contoso.AADB2C.UI** 대화 상자에서 **웹 > ASP > NET**을 선택합니다.
3. **MVC 보기 페이지**를 누릅니다.
4. **이름** 상자에서 이름을 _unified.cshtml_로 변경합니다.
5. **추가**
 ![MVC 보기 추가](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)를 클릭합니다.
6.  이 파일이 열려 있지 않으면 파일을 두 번 클릭하여 엽니다. _unified.cshtml_의 내용을 정리합니다.
7. 데모 목적으로 레이아웃 페이지에 대한 참조를 제거합니다. 다음 코드 조각을 _unified.cshtml_에 추가합니다.

    ```C#
    @{
        Layout = null;
    }
    ```

8. AAD B2C 기본 제공 HTML5 템플릿에서 다운로드한 _unified.cshtml_ 파일을 엽니다.
9. `@` 서명을 찾아 `@@`로 바꿉니다.
10. 파일 내용을 복사하고 레이아웃 정의 아래에 붙여넣습니다. 코드는 ![HTML5를 추가한 후 unified.cshtml 파일](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)과 비슷해야 합니다
### <a name="step-23-change-the-background-image"></a>2.3단계 배경 이미지 변경
10. `background_background_image` ID를 사용하여 `<img>` 요소를 찾고 `src`를 _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_ 또는 원하는 다른 배경 이미지로 바꿉니다.
![페이지 배경 변경](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>2.4단계 MVC 컨트롤러에 보기 추가
**Controllers\HomeController.cs**를 열고 다음 메서드를 추가합니다. 
```C
public IActionResult unified()
{
    return View();
}
```
이 코드에서는 메서드가 브라우저에 대한 응답을 렌더링하는 보기 템플릿 파일을 사용하도록 지정했습니다. 보기 템플릿 파일의 이름을 명시적으로 지정하지 않기 때문에 /Views/Home 폴더에서 _unified.cshtml_  보기 파일을 사용하여 MVC를 기본값으로 지정합니다.

_통합_ 메서드를 추가한 후에 코드는 다음과 비슷해야 합니다. ![보기를 렌더링하도록 컨트롤러 변경](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

웹앱을 디버그하고 _통합_ 페이지에 액세스할 수 있도록 합니다. 예를 들어 `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>2.5단계 Azure에 게시
1.  **솔루션 탐색기**에서 **Contoso.AADB2C.UI** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

    ![Microsoft Azure App Service에 게시](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  **Microsoft Azure App Service**를 선택했는지 확인하고 **게시**를 선택합니다.

    ![Microsoft Azure App Service 만들기](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **App Service 만들기** 대화 상자를 통해 Azure에서 ASP.NET 웹앱을 실행하는 데 필요한 모든 Azure 리소스를 만들 수 있습니다.

> [!NOTE]
>
>게시 방법에 대한 자세한 내용은 [Azure에서 ASP.NET 웹앱 만들기](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)를 참조하세요.

3.  **웹앱 이름**에서 고유한 앱 이름을 입력합니다(유효한 문자는 `a-z`, `0-9` 및 `-`). 웹앱의 URL은 `http://<app_name>.azurewebsites.NET`이며, 여기서 `<app_name>`은 웹앱 이름입니다. 자동으로 생성된 이름을 적용할 수 있습니다. 이 이름은 고유합니다.

    **만들기**를 선택하여 Azure 리소스 만들기를 시작합니다.

    ![App Service 속성 제공](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  마법사가 완료되면 Azure에 ASP.NET 웹앱을 게시한 다음 기본 브라우저에서 앱을 시작하고 _통합_ 페이지의 URL을 복사합니다. 예: _https://<app_name>.azurewebsites.net/home/unified_

## <a name="step-3-configure-cors-in-azure-app-service"></a>3단계: Azure App Service에서 CORS 구성
1. 브라우저에서 [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. **앱 서비스**를 클릭한 다음 API 앱의 이름을 클릭합니다.

    ![포털에서 API 앱 선택](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. **설정** 섹션에서 아래로 스크롤하여 **API** 섹션을 찾은 다음 **CORS**를 클릭합니다.

    ![CORS 설정 선택](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. 텍스트 상자에서 가져올 JavaScript 호출을 허용하려는 URL을 입력합니다.
또는 별표(*)를 입력하여 모든 원본 도메인이 허용되도록 지정합니다.

5. **Save**를 클릭합니다.

    ![저장을 클릭합니다.](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

저장을 클릭하면 API 앱이 지정된 URL의 JavaScript 호출을 수락합니다. 

## <a name="step-4-html5-template-validation"></a>4단계: HTML5 템플릿 유효성 검사
HTML5 템플릿을 사용할 준비가 되었습니다.  그러나 `ContentDefinition`에서 사용할 수 없습니다. `ContentDefinition`을 사용자 정책에 추가하기 전에 다음을 수행해야 합니다.
* 콘텐츠가 HTML5 호환되고 액세스 가능해야 함
* 콘텐츠 서버가 CORS에 대해 사용하도록 설정되어야 함

>[!NOTE]
>
>콘텐츠를 호스팅하는 사이트에 CORS가 활성화되어 있고 CORS 요청을 테스트했는지 확인하려면 http://test-cors.org/ 사이트를 사용할 수 있습니다. 

* 제공된 콘텐츠는 **HTTPS**를 통해 보호됩니다.
* 모든 링크 및 CSS 콘텐츠와 이미지에 **절대 URL**(예: https://yourdomain/content)을 사용합니다.

## <a name="step-5-configure-your-content-definition"></a>5단계: 콘텐츠 정의 구성
`ContentDefinition`을 구성하려면
1.  정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.
2.  `<ContentDefinitions>` 요소를 찾고 `<ContentDefinitions>` 노드의 전체 콘텐츠를 복사합니다.
3.  확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<BuildingBlocks>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4.  복사한 `<ContentDefinitions>` 노드의 전체 콘텐츠를 `<BuildingBlocks>` 요소의 자식으로 붙여넣습니다. 
5.  복사한 XML에서 `Id="api.signuporsignin"`이 포함된 `<ContentDefinition>` 노드를 찾습니다.
6.  `LoadUri` 값을 _~/tenant/default/unified_에서 _https://app_name.azurewebsites.net/home/unified_로 변경합니다. 사용자 지정 정책이 다음과 비슷해야 합니다. ![콘텐츠 정의](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6단계: 테넌트에 정책 업로드
1.  [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환하고 **Azure AD B2C**를 엽니다.
2.  **ID 경험 프레임워크**를 선택합니다.
3.  **모든 정책**을 엽니다.
4.  **정책 업로드**를 선택합니다.
5.  **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
6.  TrustFrameworkExtensions.xml을 **업로드**하고 유효성 검사가 실패하지 않았는지 확인합니다.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7단계: 지금 실행을 사용하여 사용자 지정 정책 테스트
1.  **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.

    >[!NOTE]
    >
    >    **지금 실행**을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 미리 등록되어 있어야 합니다. 
    >    응용 프로그램을 등록하는 방법은 Azure AD B2C [시작](active-directory-b2c-get-started.md) 문서 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서를 참조하세요.
    >

2.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 엽니다. **지금 실행**을 선택합니다.
3.  앞의 ![등록 또는 로그인 정책](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)에서 만든 백그라운드를 사용하여 사용자 지정 사용자 HTML5를 볼 수 있어야 합니다.

## <a name="step-8-adding-dynamic-content"></a>8단계: 동적 콘텐츠 추가
이 섹션에서는 _campaignId_라는 쿼리 문자열 매개 변수에 기반한 백그라운드를 변경합니다. 신뢰 당사자 응용 프로그램(웹/모바일 앱)이 AAD B2C에 매개 변수를 보냅니다. 정책에서는 매개 변수를 읽고 HTML5 템플릿에 해당 값을 보냅니다. 


### <a name="step-81-adding-content-definition-parameter"></a>8.1단계 콘텐츠 정의 매개 변수 추가

`ContentDefinitionParameters` 요소를 추가하려면 다음을 수행합니다.
1.  정책의 SignUpOrSignin 파일(예: SignUpOrSignin.xml)을 엽니다.
2.  `<UserJourneyBehaviors>` 노드를 찾습니다. 
4.  `<UserJourneyBehaviors>` 내부에서 다음 XML 코드 조각을 추가합니다. 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>8.2단계 쿼리 문자열 매개 변수를 수용하고 배경 이미지를 적절하게 대체하도록 코드 변경
이 섹션에서는 HomeController _통합_ 메서드를 수정하여 campaignId 매개 변수를 수용합니다. 그런 다음 메서드는 해당 값을 확인하고 `ViewData["background"]` 변수를 적절하게 설정합니다.

1. **Controllers\HomeController.cs**를 열고 다음 코드 조각을 사용하여 `unified` 메서드를 변경합니다.

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. `background_background_image` ID를 사용하여 `<img>` 요소를 찾고 `src`를 `@ViewData["background"]`로 바꿉니다.

    ![페이지 배경 변경](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3 변경 내용 업로드 및 정책 게시
1. Azure 서비스 앱에 Visual Studio 프로젝트를 게시합니다.
2. SignUpOrSignin.xml 정책을 AAD B2C에 업로드합니다.
3.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 엽니다. **지금 실행**을 선택합니다. 전과 동일한 배경 이미지가 표시됩니다.
4. 브라우저의 주소 표시줄에서 URL을 복사합니다.
5. URI에 _campaignId_ 쿼리 문자열 매개 변수를 추가합니다. 예를 들어 다음 그림에 표시된 대로 `&campaignId=hawaii`를 추가합니다. ![페이지 배경 변경](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. **Enter** 키를 누르면 페이지는 하와이로 배경을 보여줍니다. ![페이지 배경 변경](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. 이제 값을 *도쿄*로 변경하고 **Enter** 키를 누릅니다. 페이지는 도쿄 배경을 보여줍니다. ![페이지 배경 변경](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>9단계: 사용자 경험의 나머지 변경
로그인 페이지로 이동하는 경우 **등록 지금** 링크를 클릭하고 정의한 배경이 아니라 기본 배경을 확인합니다. 이 동작은 등록 또는 로그인 페이지를 변경했기 때문에 수행합니다. 하지만 셀프 어설션 콘텐츠 정의의 나머지 부분도 변경해야 합니다. 이렇게 하려면 단계를 수행합니다.
* #2단계에서:
    * **selfasserted** 파일을 다운로드합니다.
    * 파일 내용을 복사합니다.
    * 새 보기 **selfasserted**를 만듭니다.
    * **selfasserted**를 **홈** 컨트롤러에 추가합니다.
* #4단계에서 
    * 확장 정책에서 `Id="api.selfasserted"`, `Id="api.localaccountsignup"` 및 `Id="api.localaccountpasswordreset"`를 포함하는 `<ContentDefinition>` 노드를 찾습니다.
    *  `LoadUri` 특성을 selfasserted URI로 설정합니다.
* #8.2단계에서 쿼리 문자열 매개 변수를 허용하도록 코드를 변경합니다. 
* TrustFrameworkExtensions.xml 정책을 업로드하고 유효성 검사가 실패하지 않았는지 확인합니다.
* 정책 테스트를 실행하고 **지금 등록**을 클릭하여 결과를 확인합니다.

## <a name="optional-download-the-complete-policy-files-and-code"></a>[선택 사항]완성 정책 파일 및 코드 다운로드
* 이러한 샘플 파일을 사용하는 대신 사용자 지정 정책을 사용하여 시작을 완료한 후에 고유한 사용자 지정 정책 사용하여 시나리오를 빌드하는 것이 좋습니다.  [참조할 샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* 여기에서 전체 코드를 다운로드할 수 있습니다. [참조를 위한 Visual Studio 솔루션 샘플](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)




