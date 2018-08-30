---
title: Speech Service REST API
description: Speech Service의 REST API에 대한 참조입니다.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 64dce26303c0e700da54d371af5cb275b1613d70
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122106"
---
# <a name="speech-service-rest-apis"></a>Speech Service REST API

통합 Speech Service의 REST API는 [Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech)(이전의 Bing Speech Service)가 제공하는 API와 비슷합니다. 엔드포인트는 이전 Speech Service에서 사용되는 엔드포인트와 다릅니다.

## <a name="speech-to-text"></a>음성을 텍스트로 변환

Speech to Text API에서는 사용되는 엔드포인트만 이전 Speech Service의 Speech Recognition API와 다릅니다. 새 엔드포인트는 아래 표에 나와 있습니다. 사용자 구독 지역과 일치하는 끝점을 사용하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

그 외에 Speech to Text API는 이전 Speech API에 대한 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest)와 비슷합니다.

Speech to Text REST API는 짧은 발언만 지원합니다. 요청은 최대 10초의 오디오를 포함할 수 있고 전체적으로 최대 14초 동안 지속될 수 있습니다. REST API는 부분적 또는 중간 결과가 아닌 최종 결과만 반환합니다.

> [!NOTE]
> 음향 모델 또는 언어 모델이나 발음을 사용자 지정한 경우, 대신 사용자 지정 엔드포인트를 사용합니다.

## <a name="text-to-speech"></a>텍스트에서 음성 변환

새로운 Text to Speech API는 24KHz 오디오 출력을 지원합니다. 이제 `X-Microsoft-OutputFormat` 헤더에는 다음 값이 포함될 수 있습니다.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

이제 Speech Service에서는 다음 두 가지 24KHz 음성을 제공합니다.

로캘 | 언어   | 성별 | 서비스 이름 매핑
-------|------------|--------|------------
ko-KR  | 영어 | Female | “Microsoft Server Speech Text to Speech Voice(en-US, Jessa24kRUS)” 
ko-KR  | 영어 | Male   | “Microsoft Server Speech Text to Speech Voice(en-US, Guy24kRUS)”

다음은 통합된 Speech service Text to Speech API에 대한 REST 엔드포인트입니다. 사용자 구독 지역과 일치하는 엔드포인트를 사용하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

이전 Speech API에 대한 [REST API 설명서](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput)를 참조할 때는 이러한 차이점에 유의하세요.

## <a name="authentication"></a>인증

Speech Service의 REST API에 요청을 보내려면 액세스 토큰이 필요합니다. 아래 표와 같이 국가별 Speech Service `issueToken` 엔드포인트에 구독 키를 제공하여 토큰을 획득합니다. 사용자 구독 지역과 일치하는 엔드포인트를 사용하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

각 액세스 토큰은 10분 동안 유효합니다. 원할 경우, 모든 Speech REST API 요청 직전을 비롯하여 언제든지 새 토큰을 획득할 수 있습니다. 그러나 네트워크 트래픽 및 대기 시간을 최소화하려면 9분 동안 동일한 토큰을 사용하는 것이 좋습니다.

다음 섹션에서는 토큰을 획득하는 방법과 요청에서 사용하는 방법을 보여 줍니다.

### <a name="getting-a-token-http"></a>토큰 획득: HTTP

다음은 토큰을 획득하기 위한 샘플 HTTP 요청입니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 `Host` 헤더를 사용자 지역의 호스트 이름으로 바꿉니다.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

이 요청에 대한 응답 본문은 JWT(Java 웹 토큰) 형식의 액세스 토큰입니다.

### <a name="getting-a-token-powershell"></a>토큰 획득: PowerShell

아래 Windows PowerShell 스크립트는 액세스 토큰을 획득하는 방법을 보여 줍니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 지정된 URI의 호스트 이름을 그에 맞게 변경합니다.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

### <a name="getting-a-token-curl"></a>토큰 획득: cURL

cURL은 Linux(Linux용 Windows 하위 시스템)에서 사용할 수 있는 명령줄 도구입니다. 아래 cURL 명령은 액세스 토큰을 획득하는 방법을 보여 줍니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 지정된 URI의 호스트 이름을 그에 맞게 변경합니다.

> [!NOTE]
> 이 명령은 가독성을 위해 여러 줄에 표시되지만, 셸 프롬프트에서는 단일 줄로 입력해야 합니다.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>토큰 획득: C#

아래 C# 클래스는 액세스 토큰을 획득하는 방법을 보여 줍니다. 이 클래스를 인스턴스화할 때 Speech Service 구독 키를 제공합니다. 구독이 미국 서부 지역이 아닌 경우 `FetchTokenUri`의 호스트 이름을 그에 맞게 변경합니다.

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri =
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

### <a name="using-a-token"></a>토큰 사용

REST API 요청에서 토큰을 사용하려면 `Authorization` 헤더에 제공하고 뒤에 단어 `Bearer`를 입력합니다. 예를 들어 다음은 토큰을 포함하는 샘플 Text to Speech REST 요청입니다. 실제 토큰을 `YOUR_ACCESS_TOKEN`으로 대체하고, `Host` 헤더에 올바른 호스트 이름을 사용합니다.

```xml
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="renewing-authorization"></a>권한 부여 갱신

권한 부여 토큰은 10분 후에 만료됩니다. 만료되기 전에(예: 9분 후) 새 토큰을 획득하여 권한 부여를 갱신합니다. 

다음 C# 코드는 앞에 제공된 클래스로 즉시 대체됩니다. `Authentication` 클래스는 타이머를 사용하여 9분 간격으로 새 액세스 토큰을 자동으로 획득합니다. 이 방법을 사용하면 프로그램이 실행되는 동안 유효한 토큰을 항상 사용할 수 있습니다.

> [!NOTE]
> 타이머를 사용하는 대신, 현재 토큰을 획득한 타임스탬프를 저장한 후, 현재 토큰 만료가 가까워질 경우에만 새 토큰을 요청할 수 있습니다. 이 방법은 불필요하게 새 토큰을 요청하는 경우를 방지하며, 음성을 자주 요청하지 않는 프로그램에 좀 더 적합할 수 있습니다.

이전처럼 `FetchTokenUri` 값이 사용자 구독 지역과 일치하는지 확인합니다. 이 클래스를 인스턴스화할 때 구독 키를 제공합니다.

```cs
    /*
     * This class demonstrates how to maintain a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = 
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)

