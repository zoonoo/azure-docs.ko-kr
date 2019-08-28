---
title: '빠른 시작: C#을 사용하여 몰입형 리더를 시작하는 웹앱 만들기'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 리더 API 기능을 추가합니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ed49f834cb7cd4f649d84aea9e549e212771eead
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899361"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>빠른 시작: 몰입형 판독기를 시작하는 웹앱 만들기(C#)

[몰입형 판독기](https://www.onenote.com/learningtools)는 읽기 이해도를 향상시키기 위해 검증된 기술을 구현하는 포괄적으로 설계된 도구입니다.

이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 판독기 SDK를 사용하여 몰입형 판독기를 통합합니다. 이 빠른 시작의 전체 작동 샘플은 [여기](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)에서 확인할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Azure AD(Azure Active Directory) 인증에 대해 구성된 몰입형 리더 리소스입니다. [다음 지침](./azure-active-directory-authentication.md)에 따라 설정하세요. 샘플 프로젝트 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.

## <a name="create-a-web-app-project"></a>웹앱 프로젝트 만들기

기본 제공되는 Model-View-Controller와 함께 ASP.NET Core 웹 애플리케이션 템플릿을 사용하여 Visual Studio에서 새 프로젝트를 만듭니다.

![새 프로젝트](./media/vswebapp.png)

![새 ASP.NET Core 웹 애플리케이션](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 인증 토큰 획득

이 부분에는 위의 Azure AD 인증 구성 필수 요소 단계의 값이 필요합니다. 해당 세션에서 저장한 텍스트 파일을 다시 참조합니다.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

_Solution Explorer_에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사용자 비밀 관리**를 선택합니다. 그러면 _secrets.json_이라는 파일이 열립니다. 파일의 콘텐츠를 다음으로 바꾸고, 위의 사용자 지정 속성 값을 입력합니다.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

_Controllers\HomeController.cs_를 열고 파일을 다음 코드로 바꿉니다.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Microsoft.IdentityModel.Clients.ActiveDirectory NuGet 패키지 추가

위의 코드는 **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet 패키지의 개체를 사용하므로, 프로젝트에서 해당 패키지에 대한 참조를 추가해야 합니다.

**도구 -> NuGet 패키지 관리자 - > 패키지 관리자 콘솔**에서 NuGet 패키지 관리자 콘솔을 열고 다음을 입력합니다.

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>샘플 콘텐츠 추가

이제 이 웹앱에 일부 샘플 콘텐츠를 추가합니다. _Views\Home\Index.cshtml_을 열고 자동으로 생성된 코드를 다음 샘플로 바꿉니다.

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

메뉴 모음에서 **디버그 > 디버깅 시작**을 선택하거나 **F5** 키를 눌러 애플리케이션을 시작합니다.

브라우저에서 다음이 표시됩니다.

![샘플 앱](./media/quickstart-result.png)

“몰입형 판독기” 단추를 클릭하면 페이지의 콘텐츠와 함께 몰입형 판독기가 시작됩니다.

![몰입형 판독기](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>다음 단계

* Node.js를 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Node.js 자습서](./tutorial-nodejs.md)를 살펴보세요.
* Python을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Python 자습서](./tutorial-python.md)를 살펴보세요.
* Swift를 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [iOS 자습서](./tutorial-ios-picture-immersive-reader.md)를 살펴보세요.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기
