---
title: "Azure Active Directory 및 API Management로 Web API 백 엔드 보호 | Microsoft Docs"
description: "Azure Active Directory 및 API 관리로 Web API 백 엔드를 보호하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: f856ff03-64a1-4548-9ec4-c0ec4cc1600f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 3dd583c47fd2d9133c8a07e7bedcd49750ffdce4
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Azure Active Directory 및 API 관리로 Web API 백 엔드를 보호하는 방법
다음 비디오에서는 Web API 백 엔드 빌드 및 Azure Active Directory 및 API 관리로 OAuth 2.0 프로토콜을 사용하여 보호하는 방법을 보여줍니다.  이 문서에서는 비디오로 해당 단계에 대한 개요 및 추가 정보를 제공합니다. 이 24분 비디오에서는 다음을 수행할 수 있는 방법을 보여줍니다.

* Web API 백 엔드 빌드 및 AAD로 보안 유지 - 1시 30분에 시작
* API를 API 관리로 가져오기 - 7시 10분에 시작
* API를 호출하도록 개발자 포털 구성 - 9시 9분에 시작
* API를 호출하도록 데스크톱 응용 프로그램 구성 - 18시 8분에 시작
* 미리 요청 권한을 부여하도록 JWT 유효성 검사 정책 구성 - 20시 47분에 시작

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>Azure AD 디렉터리 만들기
Azure Active Directory를 사용하여 Web API 백 엔드를 보호하려면 먼저 AAD 테넌트가 있어야 합니다. 이 비디오에서는 **APIMDemo** 라는 테넌트가 사용됩니다. AAD 테넌트를 만들려면 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인하고 **새로 만들기**->**App Services**->**Active Directory**->**디렉터리**->**사용자 지정 만들기**를 클릭합니다. 

![Azure Active Directory][api-management-create-aad-menu]

이 예에서 **APIMDemo**라는 디렉터리는 **DemoAPIM.onmicrosoft.com**이라는 기본 도메인 이름으로 만들어집니다. 이 디렉터리는 비디오 전체에서 사용됩니다.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Azure Active Directory로 보안이 유지된 Web API 서비스 만들기
이 단계에서는 Web API 백 엔드는 Visual Studio 2013을 사용하여 생성됩니다. 이 단계의 비디오는 1분 30초에 시작됩니다. Visual Studio에서 Web API 백 엔드 프로젝트를 만들려면 **파일**->**새로 만들기**->**프로젝트**를 클릭하고 **웹** 템플릿 목록에서 **ASP.NET 웹 응용 프로그램**을 선택합니다. 이 비디오에서 프로젝트의 이름은 **APIMAADDemo**입니다. **확인** 을 클릭하여 프로젝트를 만듭니다. 

![Visual Studio][api-management-new-web-app]

**템플릿 목록 선택**에서 **Web API**를 클릭하여 Web API 프로젝트를 만듭니다. Azure 디렉터리의 인증을 구성하려면 **인증 변경**을 클릭합니다.

![새 프로젝트][api-management-new-project]

**조직 계정**을 클릭하고 AAD 테넌트의 **도메인**을 지정합니다. 이 예에서 도메인은 **DemoAPIM.onmicrosoft.com**입니다. 디렉터리의 도메인은 디렉터리의 **도메인** 탭에서 가져올 수 있습니다.

![도메인][api-management-aad-domains]

**인증 변경** 대화 상자에서 원하는 설정을 구성하고 **확인**을 클릭합니다.

![인증 변경][api-management-change-authentication]

**확인** 을 클릭하면 Visual Studio는 Azure AD 디렉터리로 응용 프로그램을 등록하려고 하며 Visual Studio에서 로그인하라는 메시지가 표시될 수 있습니다. 디렉터리에 대한 관리 계정을 사용하여 로그인합니다.

![Visual Studio에 로그인][api-management-sign-in-vidual-studio]

Azure Web API로 이 프로젝트를 구성하려면 **클라우드에서 호스트** 상자를 선택하고 **확인**을 클릭합니다.

![새 프로젝트][api-management-new-project-cloud]

Azure에 로그인하라는 메시지가 표시될 수 있으며, 다음 웹앱을 구성할 수 있습니다.

![구성][api-management-configure-web-app]

이 예에서는 **APIMAADDemo**라는 새 **App Service 계획**이 지정됩니다.

**확인** 을 클릭하여 웹앱을 구성하고 프로젝트를 만듭니다.

## <a name="add-the-code-to-the-web-api-project"></a>Web API 프로젝트에 코드를 추가합니다.
비디오의 다음 단계는 Web API 프로젝트에 코드를 추가합니다. 이 단계는 4분 35초에 시작됩니다.

이 예에서 Web API는 모델과 컨트롤러를 사용하여 기본 계산기 서비스를 구현합니다. 서비스에 대한 모델을 추가하려면 **솔루션 탐색기**에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 선택합니다. 클래스의 이름을 `CalcInput` 로 지정하고 **추가**를 클릭합니다.

다음 `using` 명령문을 `CalcInput.cs` 파일의 맨 위에 추가합니다.

```c#
using Newtonsoft.Json;
```

생성된 클래스를 다음 코드로 바꿉니다.

```c#
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

**솔루션 탐색기**에서 **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 **추가**->**컨트롤러**를 선택합니다. **Web API 2 컨트롤러 - 비어 있음**을 선택하고 **추가**를 클릭합니다. 컨트롤러 이름으로 **CalcController**를 입력하고 **추가**를 클릭합니다.

![컨트롤러 추가][api-management-add-controller]

다음 `using` 명령문을 `CalcController.cs` 파일의 맨 위에 추가합니다.

```c#
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

생성된 컨트롤러를 다음 코드로 바꿉니다. 이 코드는 기본 계산기 API의 `Add`, `Subtract`, `Multiply` 및 `Divide` 작업을 구현합니다.

```c#
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

**F6** 를 눌러 솔루션을 빌드하고 확인합니다.

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시
이 단계에서 Visual Studio 프로젝트는 Azure에 게시됩니다. 이 단계의 비디오는 5분 45초에 시작됩니다.

프로젝트를 Azure에 게시하려면 Visual Studio에서 **APIMAADDemo** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. **웹 게시** 대화 상자에서 기본 설정을 유지하고 **게시**를 클릭합니다.

![웹 게시][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Azure AD 백 엔드 서비스 응용 프로그램에 권한을 부여합니다.
백 엔드 서비스에 대한 새 응용 프로그램은 Web API 프로젝트의 구성 및 게시 프로세스의 일부로 Azure AD 디렉터리에 만들어집니다. 6분 13초에 시작하는 비디오의 이 단계에서는 Web API 백 엔드에 권한이 부여됩니다.

![응용 프로그램][api-management-aad-backend-app]

필요한 권한을 구성하려면 응용 프로그램의 이름을 클릭합니다. **구성** 탭으로 이동하고 **다른 응용 프로그램에 권한 부여** 섹션으로 아래로 스크롤합니다. **Windows** **Azure Active Directory** 옆의 **응용 프로그램 사용 권한** 드롭다운을 클릭하고 **디렉터리 데이터 읽기** 상자를 선택한 다음 **저장**을 클릭합니다.

![권한 추가][api-management-aad-add-permissions]

> [!NOTE]
> **Windows** **Azure Active Directory**가 다른 응용 프로그램에 대한 사용 권한에 나열되지 않으면 **응용 프로그램 추가**를 클릭하고 목록에서 추가합니다.
> 
> 

Azure AD 응용 프로그램이 API 관리 개발자 포털에 대해 구성된 경우, 다음 단계에서 사용하도록 **앱 ID URI** 를 기록합니다.

![앱 ID URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Web API를 API 관리로 가져오기
API는 Azure Portal을 통해 액세스할 수 있는 API 게시자 포털에서 구성됩니다. 이 위치로 이동하려면 API Management 서비스의 도구 모음에서 **게시자 포털**을 클릭하세요. 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [첫 번째 API Management][Manage your first API] 자습서의 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.

![게시자 포털][api-management-management-console]

작업을 [수동으로 API에 추가](api-management-howto-add-operations.md)하거나 가져올 수 있습니다. 이 비디오에서는 작업을 6분 40초부터 Swagger 형식으로 가져옵니다.

다음 내용을 포함한 `calcapi.json` 이라는 파일을 만들고 컴퓨터에 저장합니다. `host` 특성이 Web API 백 엔드를 가리키는지 확인합니다. 이 예에서는 `"host": "apimaaddemo.azurewebsites.net"` 가 사용됩니다.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

계산기 API를 가져오려면 왼쪽의 **API Management** 메뉴에서 **API**를 클릭한 다음 **API 가져오기**를 클릭합니다.

![API 가져오기 단추][api-management-import-api]

다음 단계를 수행하여 계산기 API를 구성합니다.

1. **파일에서**를 클릭하고 저장한 `calculator.json` 파일로 이동한 다음 **Swagger** 라디오 단추를 클릭합니다.
2. **웹 API URL 접미사** 텍스트 상자에 **calc**를 입력합니다.
3. **제품(선택 사항)** 상자를 클릭하고 **시작**을 선택합니다.
4. **저장** 을 클릭하여 API를 가져옵니다.

![새 API 추가][api-management-import-new-api]

API를 가져오면 API에 대한 요약 페이지가 게시자 포털에 표시됩니다.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>개발자 포털에서 API 호출 실패
이 시점에서 API를 API 관리로 가져오지만, 백 엔드 서비스는 Azure AD 인증으로 보호되어야 하기 때문에 개발자 포털에서 호출할 수 없습니다. 다음 단계를 통해 7분 40초부터 시작되는 비디오에서 볼 수 있습니다.

게시자 포털의 오른쪽 위에 있는 메뉴에서 **개발자 포털** 을 클릭합니다.

![개발자 포털][api-management-developer-portal-menu]

**API**를 클릭하고 **계산기** API를 클릭합니다.

![개발자 포털][api-management-dev-portal-apis]

**사용해 보세요**를 클릭합니다.

![시도][api-management-dev-portal-try-it]

**보내기**를 클릭하여 **401 권한 없음**의 응답 상태를 기록합니다.

![보내기][api-management-dev-portal-send-401]

백 엔드 API는 Azure Active Directory로 보호되기 때문에 요청이 인증되지 않습니다. API를 성공적으로 호출하기 전에 개발자 포털은 OAuth 2.0을 사용하여 개발자에게 권한을 부여하도록 구성되어야 합니다. 이 프로세스는 다음 섹션에 설명되어 있습니다.

## <a name="register-the-developer-portal-as-an-aad-application"></a>AAD 응용 프로그램으로 개발자 포털 등록
개발자가 OAuth 2.0을 사용하여 권한을 부여하도록 개발자 포털을 구성하는 첫 번째 단계는 AAD 응용 프로그램으로 개발자 포털을 등록하는 것입니다. 8분 27초에 시작되는 비디오에서 보여줍니다.

이 비디오의 첫 번째 단계에서 Azure AD 테넌트로 이동하고, 이 예제 **APIMDemo**에서 **응용 프로그램** 탭 으로 이동합니다.

![새 응용 프로그램][api-management-aad-new-application-devportal]

**추가** 단추를 클릭하여 새 Azure Active Directory 응용 프로그램을 만들고 **내 조직에서 개발 중인 응용 프로그램 추가**를 선택합니다.

![새 응용 프로그램][api-management-new-aad-application-menu]

**웹 응용 프로그램 및/또는 Web API**를 선택하고 이름을 입력한 후 다음 화살표를 클릭합니다. 이 예에서는 **APIMDeveloperPortal** 이 사용됩니다.

![새 응용 프로그램][api-management-aad-new-application-devportal-1]

**로그인 URL**로 API Management 서비스의 URL을 입력하고 `/signin`을 추가합니다. 이 예에서는 `https://contoso5.portal.azure-api.net/signin` 가 사용됩니다.

**앱 ID URL**로 API Management 서비스의 URL을 입력하고 일부 고유 문자를 추가합니다. 원하는 문자를 사용할 수 있으며 이 예에서는 `https://contoso5.portal.azure-api.net/dp`이(가) 사용됩니다. 원하는 **앱 속성**이 구성되면, 확인 표시를 클릭하여 응용 프로그램을 만듭니다.

![새 응용 프로그램][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>API 관리 OAuth 2.0 권한 부여 서버 구성
다음 단계는 API 관리에서의 OAuth 2.0 권한 부여 서버 구성입니다. 이 단계는 9분 43초에 시작되는 비디오에서 보여 줍니다.

왼쪽의 API Management 메뉴에서 **보안**을 클릭하고 **OAuth 2.0**을 클릭한 다음 **권한 부여 서버 추가**를 클릭합니다.

![권한 부여 서버 추가][api-management-add-authorization-server]

**이름** 필드에 이름을 입력하고 원하는 경우 **설명** 필드에 설명을 입력합니다. 이러한 필드는 API 관리 서비스 인스턴스 내에서 OAuth 2.0 권한 부여 서버를 식별하는 데 사용됩니다. 이 예에서 **권한 부여 서버 데모** 가 사용됩니다. 나중에 API에 대한 인증으로 사용되도록 OAuth 2.0 서버를 지정하면 이 이름을 선택합니다.

**클라이언트 등록 페이지 URL**로 `http://localhost`와 같은 자리 표시자 값을 입력합니다.  **클라이언트 등록 페이지 URL** 은 사용자가 계정의 사용자 관리를 지원하는 OAuth 2.0 공급자에 대한 계정을 만들고 구성하는 데 사용할 수 있는 페이지를 가리킵니다. 이 예에서 자리 표시자를 사용하도록 사용자는 자신의 계정을 만들거나 구성하지 않습니다.

![권한 부여 서버 추가][api-management-add-authorization-server-1]

그런 다음 **권한 부여 끝점 URL** 및**토큰 끝점 URL**을 지정합니다.

![권한 부여 서버][api-management-add-authorization-server-1a]

이 값은 개발자 포털에 대해 만든 AAD 응용 프로그램의 **앱 끝점** 페이지에서 검색할 수 있습니다. 끝점에 액세스하려면 AAD 응용 프로그램에 대한 **구성** 탭으로 이동하고 **끝점 보기**를 클릭합니다.

![응용 프로그램][api-management-aad-devportal-application]

![끝점 보기][api-management-aad-view-endpoints]

**OAuth 2.0 권한 부여 끝점**을 복사하여 **권한 부여 끝점 URL** 텍스트 상자에 붙여 넣습니다.

![권한 부여 서버 추가][api-management-add-authorization-server-2]

**OAuth 2.0 토큰 끝점**을 복사하여 **토큰 끝점 URL** 텍스트 상자에 붙여 넣습니다.

![권한 부여 서버 추가][api-management-add-authorization-server-2a]

토큰 끝점에 붙여넣기 외에도 **리소스**라는 추가 본문 매개 변수를 추가하고 값으로 Visual Studio 프로젝트를 게시할 때 만들어진 백 엔드 서비스에 대한 AAD 응용 프로그램에서 **앱 ID URI**를 사용합니다.

![앱 ID URI][api-management-aad-sso-uri]

다음으로 클라이언트 자격 증명을 지정합니다. 액세스할 리소스의 자격 증명이며,이 경우 개발자 포털입니다.

![클라이언트 자격 증명][api-management-client-credentials]

**클라이언트 ID**를 가져오려면 개발자 포털에 대한 AAD 응용 프로그램의 **구성** 탭으로 이동하여 **클라이언트 ID**를 복사합니다.

**클라이언트 암호**를 가져오려면, **키** 섹션의 **기간 선택** 드롭다운을 클릭하고 간격을 지정합니다. 이 예제에서는 1년을 사용합니다.

![클라이언트 ID][api-management-aad-client-id]

**저장** 을 클릭하여 구성을 저장하고 키를 표시합니다. 

> [!IMPORTANT]
> 이 키를 기록해 둡니다. Azure Active Directory 구성 창을 닫으면 키를 다시 표시할 수 없습니다.
> 
> 

키를 클립보드에 복사하고, 게시자 포털로 다시 전환하고 키를 **클라이언트 암호** 텍스트 상자에 붙여 넣은 후 **저장**을 클릭합니다.

![권한 부여 서버 추가][api-management-add-authorization-server-3]

클라이언트 자격 증명 바로 다음이 인증 코드 권한입니다. 이 권한 부여 코드를 복사하고 Azure AD 개발자 포털 응용 프로그램 구성 페이지로 다시 전환하여 권한 부여를 **회신 URL** 필드에 붙여 넣고 **저장**을 다시 클릭합니다.

![회신 URL][api-management-aad-reply-url]

개발자 포털 AAD 응용 프로그램에 대한 권한 구성이 다음 단계입니다. **응용 프로그램 사용 권한**을 클릭하고 **디렉터리 데이터 읽기** 확인란을 선택합니다. **저장**을 클릭하여 이 변경 내용을 저장한 다음 **응용 프로그램 추가**를 클릭합니다.

![권한 추가][api-management-add-devportal-permissions]

검색 아이콘을 클릭하고 형식**APIM**을 시작 상자에 입력하고, **APIMAADDemo**를 선택하고 확인 표시를 클릭하여 저장합니다.

![권한 추가][api-management-aad-add-app-permissions]

**APIMAADDemo**에 대해 **권한 위임**을 클릭하고 **APIMAADDemo 액세스** 확인란을 선택하고 **저장**을 클릭합니다. 이렇게 하면 개발자 포털 응용 프로그램이 백 엔드 서비스에 액세스할 수 있습니다.

![권한 추가][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>계산기 API에 대해 OAuth 2.0 사용자 권한 부여 사용
이제 OAuth 2.0 서버가 구성되었으므로 API에 대한 보안 설정을 지정할 수 있습니다. 이 단계는 14분 30초에 시작되는 비디오에서 보여 줍니다.

왼쪽 메뉴에서 **API**를 클릭하고 **계산기**를 클릭하여 해당 설정을 보고 구성합니다.

![계산기 API][api-management-calc-api]

**보안** 탭으로 이동하고 **OAuth 2.0** 확인란을 선택한 다음, 원하는 권한 부여 서버를 **권한 부여 서버** 드롭다운에서 선택하고 **저장**을 클릭합니다.

![계산기 API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>개발자 포털에서 계산기 API를 성공적으로 호출
이제 OAuth 2.0 권한 부여가 API에서 구성되었으므로, 개발자 센터에서 해당 작업을 성공적으로 호출할 수 있습니다. 이 단계는 15분에 시작되는 비디오에서 보여 줍니다.

개발자 포털에서 계산기 서비스의 **두 개의 정수 추가**로 다시 이동하여 **사용해 보세요**를 클릭합니다. 방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.

![계산기 API][api-management-calc-authorization-server]

권한 부여 드롭다운 목록에서 **권한 부여 코드** 를 선택하고 사용할 계정 자격 증명을 입력합니다. 이미 이 계정으로 로그인한 경우 메시지가 나타나지 않을 수 있습니다.

![계산기 API][api-management-devportal-authorization-code]

**보내기**를 클릭하고 **200 확인**의 **응답 상태** 및 응답 내용에 대한 작업의 결과를 참고합니다.

![계산기 API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>API를 호출하도록 데스크톱 응용 프로그램 구성
비디오의 다음 절차는 16분 30초부터 시작하며 API를 호출하는 간단한 데스크톱 응용 프로그램을 구성합니다. Azure AD에서 데스크톱 응용 프로그램을 등록하고 디렉터리와 백 엔드 서비스에 액세스 권한을 부여하는 것이 첫 번째 단계입니다. 18분 25초에 계산기 API에 대한 작업을 호출하는 데스크톱 응용 프로그램의 데모가 시작됩니다.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>미리 요청 권한을 부여하도록 JWT 유효성 검사 정책 구성
이 비디오에서 최종 절차는 20분 48초에 시작하며 [JWT의 유효성 검사](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) 정책을 사용하여 들어오는 각 요청의 액세스 토큰의 유효성을 검사하는 방법을 보여 줍니다. 요청의 유효성을 JWT 정책으로 검사하지 않은 경우, 요청은 API 관리로 차단되며 백 엔드를 따라 전달되지 않습니다.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

이 정책 구성 및 사용에 대한 다른 데모는 [클라우드 표지 에피소드 177: 추가 API 관리 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) 을 참고하여 13분 50초로 빨리 감기합니다. 15분으로 빨리 감기하여 정책 편집기에 구성된 정책을 본 다음 18분 50초로 빨리 감기하여 필수 권한 부여 토큰 포함 및 제외 모두의 경우로 개발자 포털에서 작업 호출의 데모를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
* API 관리에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md
