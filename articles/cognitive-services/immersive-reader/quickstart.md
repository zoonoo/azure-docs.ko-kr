---
title: '빠른 시작: 몰입형 판독기를 시작하는 웹앱 만들기(C#)'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 판독기 기능을 추가합니다.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 77d95383c801038c256ccb2bf386ddf06048cf78
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311800"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>빠른 시작: 몰입형 판독기를 시작하는 웹앱 만들기(C#)

[몰입형 판독기](https://www.onenote.com/learningtools)는 읽기 이해도를 향상시키기 위해 검증된 기술을 구현하는 포괄적으로 설계된 도구입니다.

이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 판독기 SDK를 사용하여 몰입형 판독기를 통합합니다. 이 빠른 시작의 전체 작동 샘플은 [여기](https://github.com/Microsoft/immersive-reader-sdk/samples/quickstart-csharp)에서 확인할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* 몰입형 판독기에 대한 구독 키 [이 지침](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)에 따라 확보합니다.

## <a name="create-a-web-app-project"></a>웹앱 프로젝트 만들기

기본 제공되는 Model-View-Controller와 함께 ASP.NET Core 웹 애플리케이션 템플릿을 사용하여 Visual Studio에서 새 프로젝트를 만듭니다.

![새 프로젝트](./media/vswebapp.png)

![새 ASP.NET Core 웹 애플리케이션](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>액세스 토큰 획득

이 다음 단계에는 구독 키 및 엔드포인트가 필요합니다. Azure Portal에서 몰입형 판독기 리소스의 키 페이지에서 구독 키를 찾을 수 있습니다. 개요 페이지에서 엔드포인트를 찾을 수 있습니다.

_Solution Explorer_에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사용자 비밀 관리**를 선택합니다. 그러면 _secrets.json_이라는 파일이 열립니다. 해당 파일의 콘텐츠를 다음으로 교체하고, 해당되는 경우 구독 키와 엔드포인트를 제공합니다.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

_Controllers\HomeController.cs_를 열고 `HomeController` 클래스를 다음 코드로 바꿉니다.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
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
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
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

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
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

* 몰입형 판독기 SDK를 사용하여 수행할 수 있는 다른 작업을 확인하려면 [자습서](./tutorial.md)를 살펴보세요.
* [몰입형 판독기 SDK](https://github.com/Microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기