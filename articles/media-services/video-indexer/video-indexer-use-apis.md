---
title: Video Indexer API 사용
titleSuffix: Azure Media Services
description: 이 문서에서는 Azure Media Services Video Indexer API를 시작 하는 방법을 설명 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 8b6d160f71bfe8b2e5c447296d511b54ce6542c8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989776"
---
# <a name="tutorial-use-the-video-indexer-api"></a>자습서: Video Indexer API 사용

Video Indexer는 Microsoft에서 제공 하는 다양 한 오디오 및 비디오 인공 지능 (AI) 기술을 하나의 통합 된 서비스로 통합 하 여 개발을 간소화 합니다. Api는 개발자가 클라우드 플랫폼의 규모, 글로벌 접근, 가용성 및 안정성을 걱정 하지 않고 미디어 AI 기술 사용에 집중할 수 있도록 설계 되었습니다. API를 사용 하 여 파일을 업로드 하 고, 자세한 비디오 정보를 가져오고, 포함할 수 있는 정보 및 플레이어 위젯의 Url을 가져올 수 있습니다.

Video Indexer 계정을 만들 때 무료 평가판 계정 (특정 개수의 무료 인덱싱 시간 (분)을 얻을 수 있음) 또는 유료 옵션 (할당량으로 제한 되지 않음)을 선택할 수 있습니다. 무료 평가판을 사용 Video Indexer 하면 웹 사이트 사용자에 게 최대 600 분의 무료 인덱싱이 제공 되며 API 사용자에 대 한 무료 인덱싱이 최대 2400 분까지 제공 됩니다. 유료 옵션을 사용 하면 [Azure 구독 및 Azure Media Services 계정에 연결](connect-to-azure.md)된 Video Indexer 계정을 만들 수 있습니다. 인덱싱 시간(분) 및 Azure Media Services 계정과 관련된 요금을 지불합니다.

이 문서에서는 개발자가 [Video Indexer API](https://api-portal.videoindexer.ai/)를 활용하는 방법을 보여 줍니다.

## <a name="subscribe-to-the-api"></a>API 구독

1. [Video Indexer 개발자 포털](https://api-portal.videoindexer.ai/)에 로그인합니다.
    
    ![Video Indexer 개발자 포털에 로그인](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Video Indexer에 가입할 때 사용한 것과 동일한 공급자를 사용해야 합니다.
   > * 개인 Google 및 Microsoft (Outlook/Live) 계정은 평가판 계정에만 사용할 수 있습니다. Azure에 연결되는 계정에는 Azure AD가 필요합니다.
   > * 전자 메일 마다 하나의 활성 계정만 있을 수 있습니다. 사용자가 LinkedIn에 대 한 user@gmail.com를 사용 하 여 로그인 하 고 나중에 Google 용 user@gmail.com를 사용 하 여 로그인 하는 경우 후자는 이미 존재 하는 오류 페이지를 표시 합니다.

2. 가입합니다.

    [제품](https://api-portal.videoindexer.ai/products) 탭을 선택 합니다. 그런 다음 권한 부여 및 구독을 선택 합니다.
    
    ![Video Indexer 개발자 포털의 제품 탭](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > 새 사용자는 자동으로 권한 부여에 가입됩니다.
    
    구독 하면 구독과 기본 키 및 보조 키를 볼 수 있습니다. 키는 보호해야 하고, 서버 코드에서만 사용할 수 있습니다. 클라이언트 쪽 (.js, .html 등)에서 사용할 수 없습니다.

    ![Video Indexer 개발자 포털의 구독 및 키](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Video Indexer 사용자는 단일 구독 키를 사용하여 여러 Video Indexer 계정에 연결할 수 있습니다. 그런 다음, 이러한 Video Indexer 계정을 다른 Media Services 계정에 연결할 수 있습니다.

## <a name="obtain-access-token-using-the-authorization-api"></a>권한 부여 API를 사용하여 액세스 토큰 가져오기

권한 부여 API를 구독 하면 액세스 토큰을 가져올 수 있습니다. 이러한 액세스 토큰은 작업 API와 대조하여 인증하는 데 사용됩니다.

작업 API에 대한 각 호출은 호출의 권한 부여 범위와 일치하는 액세스 토큰과 연결되어야 합니다.

- 사용자 수준: 사용자 수준 액세스 토큰을 통해 **사용자** 수준에 대 한 작업을 수행할 수 있습니다. 예를 들어 연결된 계정 가져오기가 있습니다.
- 계정 수준: 계정 수준 액세스 토큰을 사용 하 여 **계정** 수준 또는 **비디오** 수준에서 작업을 수행할 수 있습니다. 예를 들어 비디오를 업로드 하 고, 모든 비디오를 나열 하 고, 비디오 정보를 가져옵니다.
- 비디오 수준: 비디오 수준 액세스 토큰을 사용 하 여 특정 **비디오**에 대 한 작업을 수행할 수 있습니다. 예를 들어, 비디오 정보 가져오기, 캡션 다운로드, 위젯 가져오기 등이 있습니다.

이러한 토큰이 읽기 전용인 지 또는 **Allowedit = true/false**를 지정 하 여 편집이 허용 되는지 여부를 제어할 수 있습니다.

대부분의 서버 간 시나리오의 경우 **계정** 작업 및 **비디오** 작업을 모두 포함 하므로 동일한 **계정** 토큰을 사용 하 게 될 것입니다. 그러나 JavaScript에서와 같이 Video Indexer에 대 한 클라이언트 쪽 호출을 수행 하려는 경우에는 **비디오** 액세스 토큰을 사용 하 여 클라이언트가 전체 계정에 액세스 하지 못하도록 할 수 있습니다. 클라이언트에 Video Indexer 클라이언트 코드를 포함 하는 경우 (예: **Insights 위젯 가져오기** 또는 **플레이어 위젯 가져오기**) **비디오** 액세스 토큰을 제공 해야 하는 이유 이기도 합니다.

작업을 더 쉽게 수행하려면 **권한 부여** API > **GetAccounts**를 사용하여 먼저 사용자 토큰을 가져오지 않고 계정을 가져올 수 있습니다. 유효한 토큰이 있는 계정을 가져오도록 요청하여 추가 호출을 건너뛰고 계정 토큰을 가져올 수 있습니다.

액세스 토큰은 1시간 후에 만료됩니다. 작업 API를 사용하기 전에 먼저 액세스 토큰이 유효한지 확인해야 합니다. 만료 되 면 권한 부여 API를 다시 호출 하 여 새 액세스 토큰을 가져옵니다.

API와 통합을 시작할 준비가 되었습니다. [각 Video Indexer REST API에 대한 자세한 설명](https://api-portal.videoindexer.ai/)을 참조하세요.

## <a name="account-id"></a>계정 ID

계정 ID 매개 변수는 모든 작업 API 호출에 필요합니다. 계정 ID는 다음 방법 중 하나를 사용하여 얻을 수 있는 GUID입니다.

* **Video Indexer 웹 사이트**를 사용하여 계정 ID를 가져옵니다.

    1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
    2. **설정** 페이지로 이동합니다.
    3. 계정 ID를 복사합니다.

        ![Video Indexer 설정 및 계정 ID](./media/video-indexer-use-apis/account-id.png)

* **Video Indexer 개발자 포털**을 사용하여 계정 ID를 프로그래밍 방식으로 가져옵니다.

    [계정 가져오기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) API를 사용 합니다.

    > [!TIP]
    > `generateAccessTokens=true`를 정의하여 계정에 대한 액세스 토큰을 생성할 수 있습니다.

* 계정에 있는 플레이어 페이지의 URL에서 계정 ID를 가져옵니다.

    비디오를 시청할 때 ID는 `accounts` 섹션 뒤 및 `videos` 섹션 앞에 표시됩니다.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>권장 사항

이 섹션에는 Video Indexer API를 사용할 때의 몇 가지 추천 사항이 나와 있습니다.

- 비디오를 업로드할 계획인 경우 일부 공용 네트워크 위치 (예: OneDrive)에 파일을 배치 하는 것이 좋습니다. 비디오에 대한 링크를 가져오고, URL을 파일 업로드 매개 변수로 제공합니다.

    Video Indexer에 제공된 URL은 미디어(오디오 또는 비디오) 파일을 가리켜야 합니다. OneDrive에서 생성된 링크 중 일부는 파일이 포함된 HTML 페이지를 위한 것입니다. URL을 쉽게 확인할 수 있습니다. 브라우저에 붙여 넣는 것입니다. 파일이 다운로드를 시작 하면 좋은 URL 일 수 있습니다. 브라우저에서 일부 시각화를 렌더링 하는 경우 파일에 대 한 링크가 아닌 HTML 페이지가 될 수 있습니다.

- 지정된 비디오에 대한 비디오 인사이트를 가져오는 API를 호출하면 자세한 JSON 출력을 응답 콘텐츠로 가져옵니다. [이 항목에서 반환된 JSON에 대한 자세한 내용을 참조하세요](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>코드 샘플

다음 C# 코드 조각에서는 모든 Video Indexer API를 사용하는 방법을 보여 줍니다.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>참고 항목

- [Video Indexer 개요](video-indexer-overview.md)
- [지역](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>다음 단계

- [출력 JSON의 세부 정보를 검사 합니다.](video-indexer-output-json-v2.md)
- 비디오 업로드 및 인덱싱의 중요 한 측면을 보여 주는 [샘플 코드](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/API) 를 확인 하세요. 코드 커밋되지를 따르면 기본 기능을 위해 API를 사용 하는 방법을 알 수 있습니다. 인라인 주석을 읽고 모범 사례 자세히 알려줍니다를 확인 하세요.

