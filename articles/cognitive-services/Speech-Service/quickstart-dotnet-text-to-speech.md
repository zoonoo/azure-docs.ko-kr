---
title: '빠른 시작: 텍스트 음성 변환, .NET Core - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Text-to-Speech REST API를 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다. 이 가이드에 포함된 샘플 텍스트는 SSML(Speech Synthesis Markup Language)로 구조화되어 있습니다. 이를 통해 음성 응답의 음성 및 언어를 선택할 수 있습니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: erhopf
ms.openlocfilehash: 3a0304a69cdff15cddf325be8abe336a42ea4fc1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53089873"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>빠른 시작: .NET Core를 사용하여 텍스트 음성 변환

이 빠른 시작에서는 .NET Core 및 Text-to-Speech REST API를 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다. 이 가이드에 있는 샘플 텍스트는 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)로 구조화되어 있으므로, 응답의 음성 및 언어를 선택할 수 있습니다.

이 빠른 시작에는 Speech Service 리소스와 함께 [Azure Cognitive Services 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 필요합니다. 계정이 없는 경우 [평가판](https://azure.microsoft.com/try/cognitive-services/)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) 또는 즐겨 사용하는 텍스트 편집기
* Speech Service에 대한 Azure 구독 키

## <a name="create-a-net-core-project"></a>.NET Core 프로젝트 만들기

새 명령 프롬프트(또는 터미널 세션)를 열고 이 명령을 실행합니다.

```console
dotnet new console -o tts-sample
cd tts-sample
```

첫 번째 명령은 두 가지 작업을 수행합니다. 새 .NET 콘솔 애플리케이션을 만들고 `tts-sample`이라는 디렉터리를 만듭니다. 두 번째 명령은 프로젝트의 디렉터리로 변경합니다.

## <a name="select-the-c-language-version"></a>C# 언어 버전 선택

이 빠른 시작에는 C# 7.1 이상이 필요합니다. 프로젝트를 위해 C# 버전을 변경하는 몇 가지 방법이 있습니다. 이 가이드에서는 `tts-sample.csproj` 파일을 조정하는 방법을 설명합니다. Visual Studio에서 사용 가능한 모든 옵션(예: 언어 변경)에 대해서는 [C# 언어 버전 선택](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)을 참조하세요.

프로젝트를 연 다음 `tts-sample.csproj`를 엽니다. `LangVersion`이 7.1 이상으로 설정되어 있는지 확인합니다. 언어 버전에 대한 속성 그룹이 없는 경우 다음 행을 추가합니다.

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

변경 내용을 저장합니다.

## <a name="add-required-namespaces-to-your-project"></a>프로젝트에 필요한 네임스페이스 추가

이전에 실행한 `dotnet new console` 명령은 `Program.cs`를 포함한 프로젝트를 만듭니다. 이 파일은 애플리케이션 코드가 보관되는 곳입니다. `Program.cs`를 열고 문을 사용하여 기존 항목을 바꿉니다. 다음 문은 샘플 앱을 빌드하고 실행하는 데 필요한 모든 형식에 액세스할 수 있는지 확인합니다.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>토큰 교환을 위한 클래스 만들기

Text-to-Speech REST API에는 인증을 위한 액세스 토큰이 필요합니다. 액세스 토큰을 가져오려면 교환이 필요합니다. 이 샘플에서는 `issueToken` 엔드포인트를 사용하여 액세스 토큰의 Speech Service 구독 키를 교환합니다.

이 샘플에서는 Speech Service 구독이 미국 서부 지역에 있다고 가정합니다. 다른 지역을 사용하는 경우 `FetchTokenUri` 값을 업데이트합니다. 전체 목록은 [지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)을 참조하세요.

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> 인증에 대한 자세한 내용은 [액세스 토큰을 가져오는 방법](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#how-to-get-an-access-token)을 참조하세요.

## <a name="get-an-access-token-and-set-the-host-url"></a>액세스 토큰 가져오기 및 호스트 URL 설정

`static void Main(string[] args)`을 찾아 `static async Task Main(string[] args)`으로 바꿉니다.

다음으로, 이 코드를 기본 메서드에 복사합니다. 몇 가지 중요한 작업을 수행하지만, 가장 중요하게는 텍스트를 입력으로 사용해 `Authentication` 함수를 호출하여 구독 키를 액세스 토큰으로 교환합니다. 문제가 발생하면 콘솔에 오류가 인쇄됩니다.

앱을 실행하기 전에 구독 키를 추가해야 합니다.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

그런 다음, 텍스트 음성 변환을 위한 호스트 및 경로를 설정합니다.

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>SSML 요청을 작성합니다.

텍스트가 `POST` 요청의 본문으로 전송됩니다. SSML을 사용하면 음성 및 언어를 지정할 수 있습니다. 이 빠른 시작에서는 언어가 `en-US`로, 음성이 `ZiraRUS`로 설정된 SSML을 사용합니다. 요청에 맞게 SSML을 구성해 보겠습니다.

```csharp
string body = @"<speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> 이 샘플에서는 `ZiraRUS` 음성 글꼴을 사용합니다. Microsoft 제공 음성/언어의 전체 목록은 [언어 지원](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/language-support)을 참조하세요. 브랜드의 고유하고 인식 가능한 음성을 만들려면 [사용자 지정 음성 글꼴 만들기](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font)를 참조하세요.

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>클라이언트 인스턴스화, 요청하기, 합성 오디오를 파일에 저장

이 코드 샘플에 많은 사항이 있습니다. 어떤 작업이 이루어지는지 간략히 살펴보겠습니다.

* 클라이언트 및 요청이 인스턴스화됩니다.
* HTTP 메서드가 `POST`로 설정됩니다.
* 요청에 필수 헤더가 추가됩니다.
* 요청이 전송되고 상태 코드가 확인됩니다.
* 응답은 비동기적으로 읽고 sample.wav라는 파일에 기록됩니다.

이 코드를 프로젝트에 복사합니다. `User-Agent` 헤더의 값을 Azure Portal의 리소스 이름으로 바꿔야 합니다.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

이제 텍스트 음성 변환 앱을 실행할 준비가 되었습니다. 명령줄(또는 터미널 세션)에서 프로젝트 디렉터리로 이동하고 다음을 실행합니다.

```console
dotnet run
```

성공하면 음성 파일이 프로젝트 폴더에 저장됩니다. 선호하는 미디어 플레이어를 사용하여 재생합니다.

## <a name="clean-up-resources"></a>리소스 정리

구독 키를 프로그램으로 하드 코딩한 경우 이 빠른 시작을 마치면 구독 키를 반드시 제거하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트를 음성으로 변환 API 참조](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>참고 항목

* [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)
* [사용자 지정 음성을 만들기 위한 음성 샘플 녹음](record-custom-voice-samples.md)
