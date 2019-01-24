---
title: Speech Service REST API - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech-to-Text 및 Text-to-Speech REST API를 사용하는 방법을 알아봅니다. 이 문서에서는 권한 부여 옵션, 쿼리 옵션, 요청을 구성하고 응답을 받는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b7f5d4683f0042b95399b86cd4f53c93518c3c56
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330677"
---
# <a name="speech-service-rest-apis"></a>Speech Service REST API

[음성 SDK](speech-sdk.md)의 대안으로 Speech Service를 사용하면 REST API 세트를 통해 음성 텍스트 변환 및 텍스트 음성 변환을 수행할 수 있습니다. 액세스 가능한 각 엔드포인트는 지역과 연결됩니다. 사용하려는 엔드포인트에 대한 구독 키가 애플리케이션에 필요합니다.

REST API를 사용하기 전에 다음 사항을 이해하세요.
* REST API를 사용한 음성 텍스트 변환 요청에는 10초 길이의 녹음된 오디오만 포함할 수 있습니다.
* Speech-to-Text REST API는 최종 결과만 반환합니다. 부분 결과는 제공되지 않습니다.
* Text-to-Speech REST API에는 인증 헤더가 필요합니다. 즉, 서비스에 액세스하기 위해 토큰 교환을 완료해야 합니다. 자세한 내용은 [인증](#authentication)을 참조하세요.

## <a name="authentication"></a>인증

Speech-to-Text 또는 Text-to-Speech REST API에 대한 각 요청에는 인증 헤더가 필요합니다. 이 표에서는 각 서비스에 대해 지원되는 헤더를 보여 줍니다.

| 지원되는 인증 헤더 | 음성 텍스트 변환 | 텍스트 음성 변환 |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | 예 | 아니요 |
| 권한 부여: 전달자 | 예 | 예 |

`Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 구독 키만 제공하면 됩니다. 예: 

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

`Authorization: Bearer` 헤더를 사용하는 경우 `issueToken` 엔드포인트에 요청해야 합니다. 이 요청에서는 10분 동안 유효한 액세스 토큰에 대한 구독 키를 교환합니다. 다음 몇 개의 섹션에서는 토큰을 가져오고, 토큰을 사용하고, 토큰을 새로 고치는 방법을 알아보겠습니다.

### <a name="how-to-get-an-access-token"></a>액세스 토큰을 가져오는 방법

액세스 토큰을 가져오려면 `Ocp-Apim-Subscription-Key` 및 구독 키를 사용하여 `issueToken` 엔드포인트에 요청해야 합니다.

다음 지역 및 엔드포인트가 지원됩니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

이 샘플을 사용하여 액세스 토큰 요청을 만듭니다.

#### <a name="http-sample"></a>HTTP 샘플

이 예제는 토큰을 가져오는 간단한 HTTP 요청입니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 `Host` 헤더를 사용자 지역의 호스트 이름으로 바꿉니다.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

응답 본문에는 JWT(Java Web Token) 형식의 액세스 토큰이 포함됩니다.

#### <a name="powershell-sample"></a>PowerShell 샘플

이 예제는 액세스 토큰을 가져오는 간단한 PowerShell 스크립트입니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 사용자 구독과 일치하는 지역에 맞는 엔드포인트를 사용해야 합니다. 이 예제는 현재 미국 서부로 설정되어 있습니다.

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

#### <a name="curl-sample"></a>cURL 샘플

cURL은 Linux(Linux용 Windows 하위 시스템)에서 사용할 수 있는 명령줄 도구입니다. 이 cURL 명령은 액세스 토큰을 가져오는 방법을 설명합니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 사용자 구독과 일치하는 지역에 맞는 엔드포인트를 사용해야 합니다. 이 예제는 현재 미국 서부로 설정되어 있습니다.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# 샘플

이 C# 클래스는 액세스 토큰을 가져오는 방법을 설명합니다. 이 클래스를 인스턴스화할 때 Speech Service 구독 키를 전달합니다. 구독이 미국 서부 지역이 아닌 경우 `FetchTokenUri`의 값을 구독의 지역과 일치하도록 변경합니다.

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

### <a name="how-to-use-an-access-token"></a>액세스 토큰을 사용하는 방법

액세스 토큰은 `Authorization: Bearer <TOKEN>` 헤더로 서비스에 전송되어야 합니다. 각 액세스 토큰은 10분 동안 유효합니다. 언제든지 새 토큰을 가져올 수 있지만, 네트워크 트래픽 및 대기 시간을 최소화하려면 동일한 토큰을 9분 동안 사용하는 것이 좋습니다.

다음은 Text-to-Speech REST API에 대한 샘플 HTTP 요청입니다.

```http
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

### <a name="how-to-renew-an-access-token-using-c"></a>C#을 사용하여 액세스 토큰을 갱신하는 방법

이 C# 코드는 앞에 제공된 클래스를 즉시 대체합니다. `Authentication` 클래스는 타이머를 사용하여 9분 간격으로 새 액세스 토큰을 자동으로 가져옵니다. 이 방법을 사용하면 프로그램이 실행되는 동안 유효한 토큰을 항상 사용할 수 있습니다.

> [!NOTE]
> 타이머를 사용하는 대신, 마지막 토큰을 가져왔을 때의 타임스탬프를 저장할 수 있습니다. 그런 다음 만료에 가까워진 경우에만 새 토큰을 요청할 수 있습니다. 이 방법은 불필요하게 새 토큰을 요청하는 경우를 방지하며, 음성을 자주 요청하지 않는 프로그램에 좀 더 적합할 수 있습니다.

이전처럼 `FetchTokenUri` 값이 사용자 구독 지역과 일치하는지 확인합니다. 이 클래스를 인스턴스화할 때 구독 키를 전달합니다.

```cs
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

## <a name="speech-to-text-api"></a>Speech-to-Text API

Speech-to-Text REST API는 짧은 발화만 지원합니다. 요청은 최대 10초의 오디오를 포함할 수 있고, 총 지속 기간은 14초입니다. REST API는 최종 결과만 반환하며, 부분 결과나 중간 결과는 반환하지 않습니다.

애플리케이션을 위해 더 긴 오디오를 보내야 하는 경우 [음성 SDK](speech-sdk.md) 또는 [일괄 처리 전사](batch-transcription.md)를 사용하는 것이 좋습니다.

### <a name="regions-and-endpoints"></a>지역 및 엔드포인트

이러한 지역은 REST API를 사용한 음성 텍스트 변환 전사에 대해 지원됩니다. 사용자 구독 지역과 일치하는 엔드포인트를 선택해야 합니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>쿼리 매개 변수

이 매개 변수는 REST 요청의 쿼리 문자열에 포함할 수 있습니다.

| 매개 변수 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `language` | 인식되는 음성 언어를 식별합니다. [지원되는 언어](language-support.md#speech-to-text)를 참조하세요. | 필수 |
| `format` | 결과 형식을 지정합니다. 허용되는 값은 `simple` 및 `detailed`입니다. simple 결과에는 `RecognitionStatus`, `DisplayText`, `Offset` 및 `Duration`이 포함됩니다. detailed 응답에는 신뢰도 값 및 4가지 다른 표현과 함께 여러 결과가 포함됩니다. 기본 설정은 `simple`입니다. | 옵션 |
| `profanity` | 인식 결과에서 욕설의 처리 방법을 지정합니다. 허용되는 값은 욕설을 별표로 바꾸는 `masked`, 결과에서 모든 욕설을 제거하는 `removed` 또는 욕설을 결과에 포함하는 `raw`입니다. 기본 설정은 `masked`입니다. | 옵션 |

### <a name="request-headers"></a>헤더 요청

이 표에는 음성 텍스트 변환 요청에 대한 필수 헤더 및 선택적 헤더가 나와 있습니다.

|헤더| 설명 | 필수/선택 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Speech Service 구독 키입니다. | 이 헤더 또는 `Authorization`가 필요합니다. |
| `Authorization` | 앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 자세한 내용은 [인증](#authentication)을 참조하세요. | 이 헤더 또는 `Ocp-Apim-Subscription-Key`가 필요합니다. |
| `Content-type` | 제공된 오디오 데이터의 형식과 코덱을 설명합니다. 허용되는 값은 `audio/wav; codecs=audio/pcm; samplerate=16000` 및 `audio/ogg; codecs=opus`입니다. | 필수 |
| `Transfer-Encoding` | 단일 파일이 아닌 청크 분할된 오디오 데이터가 전송되고 있음을 지정합니다. 오디오 데이터를 청크 분할하는 경우에만 이 헤더를 사용합니다. | 옵션 |
| `Expect` | 청크 분할된 전송을 사용하는 경우 `Expect: 100-continue`를 전송합니다. Speech Service는 초기 요청을 인식하고 추가 데이터를 대기합니다.| 청크 분할된 오디오 데이터를 전송하는 경우에 필요합니다. |
| `Accept` | 제공하는 경우 `application/json`이어야 합니다. Speech Service는 JSON으로 결과를 제공합니다. 값을 지정하지 않을 경우 일부 웹 요청 프레임워크는 호환되지 않는 기본값을 지정하므로, 항상 `Accept`를 포함하는 것이 좋습니다. | 선택 사항이지만 권장됩니다. |

### <a name="audio-formats"></a>오디오 형식

오디오는 HTTP `POST` 요청 본문에서 전송됩니다. 오디오는 이 테이블의 형식 중 하나여야 합니다.

| 형식 | Codec | Bitrate | 샘플링 주기 |
|--------|-------|---------|-------------|
| WAV | PCM | 16비트 | 16kHz, mono |
| OGG | OPUS | 16비트 | 16kHz, mono |

>[!NOTE]
>위의 형식은 음성 서비스의 REST API 및 WebSocket을 통해 지원됩니다. [음성 SDK](speech-sdk.md)는 현재 PCM 코덱을 사용하는 WAV 형식만 지원합니다.

### <a name="sample-request"></a>샘플 요청

일반적인 HTTP 요청입니다. 아래 샘플에는 호스트 이름 및 필수 헤더가 포함되어 있습니다. 서비스는 이 샘플에 포함되지 않은 오디오 데이터도 예상한다는 것에 유의해야 합니다. 앞에서 언급한 대로 청크 분할은 권장되지만 필수는 아닙니다.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>HTTP 상태 코드

각 응답의 HTTP 상태 코드는 성공 또는 일반 오류를 나타냅니다.

| HTTP 상태 코드 | 설명 | 가능한 원인 |
|------------------|-------------|-----------------|
| 100 | 계속 | 초기 요청이 수락되었습니다. 나머지 데이터의 전송을 계속합니다. (청크 분할 전송으로 사용함) |
| 200 | 확인 | 요청이 성공했습니다. 응답 본문이 JSON 개체입니다. |
| 400 | 잘못된 요청 | 언어 코드가 제공되지 않았거나 지원되지 않는 언어입니다. 오디오 파일이 잘못되었습니다. |
| 401 | 권한 없음 | 구독 키 또는 권한 부여 토큰이 지정된 지역에서 올바르지 않거나 엔드포인트가 올바르지 않습니다. |
| 403 | 사용할 수 없음 | 구독 키 또는 권한 부여 토큰이 없습니다. |

### <a name="chunked-transfer"></a>청크 분할 전송

청크 분할 전송(`Transfer-Encoding: chunked`)은 Speech Service가 오디오 파일을 전송하는 동안 처리를 시작할 수 있으므로 인식 대기 시간을 줄이는 데 도움이 됩니다. REST API는 부분 또는 중간 결과를 제공하지 않습니다. 이 옵션은 응답성을 향상하기 위한 용도로만 사용됩니다.

이 코드 샘플은 오디오를 청크로 보내는 방법을 보여 줍니다. 오직 첫 번째 청크만 오디오 파일의 헤더를 포함해야 합니다. `request`는 적절한 REST 엔드포인트에 연결된 HTTPWebRequest 개체입니다. `audioFile`은 디스크에서 오디오 파일의 경로입니다.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>응답 매개 변수

결과는 JSON으로 제공됩니다. `simple` 형식에는 이러한 최상위 수준 필드가 포함됩니다.

| 매개 변수 | 설명  |
|-----------|--------------|
|`RecognitionStatus`|상태(예: 인식 성공에 `Success`)입니다. 다음 표를 참조하세요.|
|`DisplayText`|대문자로 표시, 문장 부호, 역 텍스트 정규화(”two hundred”를 “200”, 또는 “doctor smith”를 “Dr. Smith"로, 발화된 텍스트를 더 짧은 형태로 변환 ) 및 욕설 마스킹 후에 인식된 텍스트입니다. 성공 시만 표시합니다.|
|`Offset`|인식된 음성이 오디오 스트림에서 시작하는 시간(100나노초 단위)입니다.|
|`Duration`|오디오 스트림에서 인식된 음성의 기간(100나노초 단위)입니다.|

`RecognitionStatus` 필드에는 다음 값이 포함될 수 있습니다.

| 상태 | 설명 |
|--------|-------------|
| `Success` | 성공적으로 인식했고 `DisplayText` 필드가 있습니다. |
| `NoMatch` | 오디오 스트림에서 음성이 감지되었지만 대상 언어의 단어가 일치하지 않습니다. 일반적으로 인식 언어는 사용자가 말하는 것과 다른 언어를 의미합니다. |
| `InitialSilenceTimeout` | 오디오 스트림의 시작 부분에는 묵음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `BabbleTimeout` | 오디오 스트림의 시작 부분에는 소음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `Error` | 인식 서비스에서 내부 오류가 발생하여 계속할 수 없습니다. 가능한 경우 다시 시도하세요. |

> [!NOTE]
> 오디오가 욕설로만 구성되어 있고 `profanity` 쿼리 매개 변수가 `remove`로 설정되어 있는 경우 서비스는 음성 결과를 변환하지 않습니다.

`detailed` 형식에는 `simple` 형식과 동일한 데이터 및 동일한 음성 인식 결과의 대체 해석 목록인 `NBest`가 포함됩니다. 이러한 결과에는 가장 가능성이 높은 항목부터 가장 낮은 항목 순으로 순위가 지정되어 있으며, 첫 번째 항목은 기본 인식 결과와 동일합니다.  `detailed` 형식을 사용하는 경우 `DisplayText`는 `NBest` 목록의 각 결과에 대한 `Display`로 제공됩니다.

`NBest` 목록의 각 개체에는 다음이 포함됩니다.

| 매개 변수 | 설명 |
|-----------|-------------|
| `Confidence` | 0.0(신뢰도 없음)에서 1.0(완전 신뢰도)까지 항목의 신뢰도 점수입니다. |
| `Lexical` | 인식된 텍스트의 어휘 형태, 즉 인식된 실제 단위입니다. |
| `ITN` | 전화 번호, 숫자, 축약어("doctor smith"가 "dr smith")가 포함된 인식된 텍스트의 역 텍스트 정규화된("기본형") 형태와 적용된 기타 변형입니다. |
| `MaskedITN` | 요청된 경우 욕설 마스킹이 적용된 ITN 형태입니다. |
| `Display` | 문장 부호 및 대문자로 표시가 추가된 인식된 텍스트의 표시 형태입니다. 이 매개 변수는 형식이 `simple`로 설정된 경우에 제공되는 `DisplayText`와 동일합니다. |

### <a name="sample-responses"></a>샘플 응답

`simple` 인식에 대한 일반적인 응답입니다.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` 인식에 대한 일반적인 응답입니다.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>Text-to-Speech API

텍스트를 음성으로 변환 REST API는 인공신경망 및 표준 텍스트를 음성으로 보이스를 지원하며, 해당 음성 각각은 로캘로 식별되는 특정 언어를 지원합니다.

* 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.
* 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#text-to-speech)을 참조하세요.

### <a name="request-headers"></a>헤더 요청

이 표에는 음성 텍스트 변환 요청에 대한 필수 헤더 및 선택적 헤더가 나와 있습니다.

| 헤더 | 설명 | 필수/선택 |
|--------|-------------|---------------------|
| `Authorization` | 앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 자세한 내용은 [인증](#authentication)을 참조하세요. | 필수 |
| `Content-Type` | 제공된 텍스트의 콘텐츠 형식을 지정합니다. 허용되는 값: `application/ssml+xml`. | 필수 |
| `X-Microsoft-OutputFormat` | 오디오 출력 형식을 지정합니다. 허용되는 값의 전체 목록은 [오디오 출력](#audio-outputs)을 참조하세요. | 필수 |
| `User-Agent` | 애플리케이션 이름입니다. 255자 미만이어야 합니다. | 필수 |

### <a name="audio-outputs"></a>오디오 출력

각 요청에서 `X-Microsoft-OutputFormat` 헤더로 전송되는 지원되는 오디오 형식 목록입니다. 각 항목에 전송률 및 인코딩 형식이 포함됩니다. Speech Service는 24KHz 및 16KHz 오디오 출력을 지원합니다.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-mulaw` | `riff-16khz-16bit-mono-pcm` |
| `audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3` |
| `audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm` |
| `riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3` |
| `audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3` |

> [!NOTE]
> 선택한 음성 및 출력 형식의 비트 전송률이 다른 경우 필요에 오디오가 다시 샘플링됩니다. 단, 24khz 음성은 `audio-16khz-16kbps-mono-siren` 및 `riff-16khz-16kbps-mono-siren` 출력 형식을 지원하지 않습니다.

### <a name="request-body"></a>요청 본문

각 `POST` 요청의 본문은 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)로 전송됩니다. SSML에서는 텍스트 음성 변환 서비스에서 반환한 합성된 음성의 목소리와 언어를 선택할 수 있습니다. 지원되는 목소리의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.

> [!NOTE]
> 사용자 지정 목소리를 사용하는 경우에는 요청 본문을 일반 텍스트(ASCII 또는 UTF-8)로 전송할 수 있습니다.

### <a name="sample-request"></a>샘플 요청

이 HTTP 요청은 SSML을 사용하여 음성 및 언어를 지정합니다. 본문은 1,000자를 초과할 수 없습니다.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP 상태 코드

각 응답의 HTTP 상태 코드는 성공 또는 일반 오류를 나타냅니다.

| HTTP 상태 코드 | 설명 | 가능한 원인 |
|------------------|-------------|-----------------|
| 200 | 확인 | 요청이 성공했습니다. 응답 본문이 오디오 파일입니다. |
| 400 | 잘못된 요청 | 필수 매개 변수가 없거나 비어 있거나 null입니다. 또는 필수 또는 선택적 매개 변수에 전달된 값이 올바르지 않습니다. 일반적인 문제는 헤더가 너무 긴 경우입니다. |
| 401 | 권한 없음 | 요청에 권한이 없습니다. 구독 키 또는 토큰이 유효하고 올바른 영역에 있는지 확인하세요. |
| 413 | 요청 엔터티가 너무 큼 | SSML 입력이 1024자보다 깁니다. |
| 429 | 너무 많은 요청 | 구독에 허용되는 요청의 할당량 또는 속도가 초과되었습니다. |
| 502 | 잘못된 게이트웨이 | 네트워크 또는 서버 쪽 문제입니다. 잘못된 헤더를 나타낼 수도 있습니다. |

HTTP 상태가 `200 OK`인 경우 응답 본문은 요청된 형식으로 오디오 파일을 포함합니다. 이 파일은 전송 시 재생하거나, 버퍼에 저장하거나, 파일에 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
