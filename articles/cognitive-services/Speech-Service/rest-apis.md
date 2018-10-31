---
title: Speech Service REST API
description: Speech Service의 REST API에 대한 참조입니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: erhopf
ms.openlocfilehash: 7f3daf71f4d94371af5f7d98c4e03761d7217a2a
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025840"
---
# <a name="speech-service-rest-apis"></a>Speech Service REST API

Azure Cognitive Services 음성 서비스의 REST API는 [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech)가 제공하는 API와 비슷합니다. 엔드포인트는 Bing Speech Service에서 사용되는 엔드포인트와 다릅니다. 지역별 엔드포인트는 사용할 수 없으며, 사용 중인 엔드포인트에 상응하는 구독 키를 사용해야 합니다.

## <a name="speech-to-text"></a>음성을 텍스트로 변환

Speech to Text REST API에 대한 엔드포인트는 다음 표에 표시됩니다. 사용자 구독 지역과 일치하는 끝점을 사용하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> 음향 모델 또는 언어 모델이나 발음을 사용자 지정한 경우, 대신 사용자 지정 엔드포인트를 사용합니다.

이 API는 짧은 발화만 지원합니다. 요청은 최대 10초의 오디오를 포함할 수 있고 전체적으로 최대 14초 동안 지속될 수 있습니다. REST API는 부분적 또는 중간 결과가 아닌 최종 결과만 반환합니다. Speech Service에는 긴 오디오를 전사할 수 있는 [일괄 전사](batch-transcription.md) API도 있습니다.


### <a name="query-parameters"></a>쿼리 매개 변수

다음 매개 변수를 REST 요청의 쿼리 문자열에 포함할 수 있습니다.

|매개 변수 이름|필수/선택|의미|
|-|-|-|
|`language`|필수|인식할 언어의 식별자입니다. [지원되는 언어](language-support.md#speech-to-text)를 참조하세요.|
|`format`|옵션<br>기본값: `simple`|결과 형식, `simple` 또는 `detailed`입니다. 단순 결과는 `RecognitionStatus`, `DisplayText`, `Offset` 및 기간을 포함합니다. 상세한 결과는 신뢰도 값 및 4가지 다른 표현과 함께 여러 후보를 포함합니다.|
|`profanity`|옵션<br>기본값: `masked`|인식 결과에서 욕설을 처리하는 방법입니다. `masked`(별표로 욕설 대체), `removed`(욕설 모두 삭제) 또는 `raw`(욕설 포함)가 될 수 있습니다.

### <a name="request-headers"></a>헤더 요청

다음 필드가 HTTP 요청 헤더에서 전송됩니다.

|헤더|의미|
|------|-------|
|`Ocp-Apim-Subscription-Key`|Speech Service 구독 키입니다. 이 헤더 또는 `Authorization` 중에서 제공해야 합니다.|
|`Authorization`|앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 이 헤더 또는 `Ocp-Apim-Subscription-Key` 중에서 제공해야 합니다. [인증](#authentication)을 참조하세요.|
|`Content-type`|오디오 데이터의 형식과 코덱을 설명합니다. 현재 이 값은 `audio/wav; codec=audio/pcm; samplerate=16000`이 되어야 합니다.|
|`Transfer-Encoding`|선택 사항입니다. 지정된 경우 단일 파일 대신 여러 작은 청크로 오디오 데이터를 전송할 수 있도록 `chunked`여야 합니다.|
|`Expect`|청크 분할된 전송을 사용하는 경우 `Expect: 100-continue`를 전송합니다. Speech Service는 초기 요청을 인식하고 추가 데이터를 대기합니다.|
|`Accept`|선택 사항입니다. 제공하는 경우 Speech Service가 JSON 형식으로 결과를 제공하므로 `application/json`을 포함해야 합니다. (일부 웹 요청 프레임워크는 값을 지정하지 않을 경우 호환되지 않는 기본값을 지정하므로, 항상 `Accept`을 포함하는 것는 이 좋습니다.)|

### <a name="audio-format"></a>오디오 형식

오디오는 HTTP `POST` 요청 본문에서 전송됩니다. 다음 형식/인코딩의 16KHz에서 PCM 단일 채널(모노)이 포함된 16비트 WAV 형식이어야 합니다.

* PCM 코덱을 사용하는 WAV 형식
* OPUS 코덱을 사용하는 Ogg 형식

>[!NOTE]
>위의 형식은 음성 서비스의 REST API 및 WebSocket을 통해 지원됩니다. [음성 SDK](/index.yml)는 현재 PCM 코덱을 사용하는 WAV 형식만 지원합니다.

### <a name="chunked-transfer"></a>청크 분할 전송

청크 분할 전송(`Transfer-Encoding: chunked`)은 Speech Service가 오디오 파일을 전송하는 동안 처리를 시작할 수 있으므로 인식 대기 시간을 줄이는 데 도움이 됩니다. REST API는 부분 또는 중간 결과를 제공하지 않습니다. 이 옵션은 응답성을 향상하기 위한 용도로만 사용됩니다.

다음 코드는 오디오를 청크로 전송하는 방법을 보여 줍니다. 오직 첫 번째 청크만 오디오 파일의 헤더를 포함해야 합니다. `request`는 적절한 REST 엔드포인트에 연결된 HTTPWebRequest 개체입니다. `audioFile`은 디스크에서 오디오 파일의 경로입니다.

```csharp
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

### <a name="example-request"></a>요청 예

다음은 일반적인 요청입니다.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>HTTP 상태입니다.

응답의 HTTP 상태는 성공 또는 일반적인 오류 조건을 나타냅니다.

HTTP 코드|의미|가능한 원인
-|-|-|
100|계속|초기 요청이 수락되었습니다. 나머지 데이터의 전송을 계속합니다. (청크 분할 전송으로 사용함)
200|확인|요청이 성공했습니다. 응답 본문이 JSON 개체입니다.
400|잘못된 요청|언어 코드가 제공되지 않았거나 지원되지 않는 언어입니다. 오디오 파일이 잘못되었습니다.
401|권한 없음|구독 키 또는 권한 부여 토큰이 지정된 지역에서 올바르지 않거나 엔드포인트가 올바르지 않습니다.
403|사용할 수 없음|구독 키 또는 권한 부여 토큰이 없습니다.

### <a name="json-response"></a>JSON 응답

결과는 JSON 형식으로 반환됩니다. `simple` 형식은 다음 최상위 필드만을 포함합니다.

|필드 이름|Content|
|-|-|
|`RecognitionStatus`|상태(예: 인식 성공에 `Success`)입니다. 다음 표를 참조하세요.|
|`DisplayText`|대문자로 표시, 문장 부호, 역 텍스트 정규화(”two hundred”를 “200”, 또는 “doctor smith”를 “Dr. Smith"로, 발화된 텍스트를 더 짧은 형태로 변환 ) 및 욕설 마스킹 후에 인식된 텍스트입니다. 성공 시만 표시합니다.|
|`Offset`|인식된 음성이 오디오 스트림에서 시작하는 시간(100나노초 단위)입니다.|
|`Duration`|오디오 스트림에서 인식된 음성의 기간(100나노초 단위)입니다.|

`RecognitionStatus` 필드는 다음 값을 포함할 수 있습니다.

|상태 값|설명
|-|-|
| `Success` | 성공적으로 인식했고 DisplayText 필드가 있습니다. |
| `NoMatch` | 오디오 스트림에서 음성이 감지되었지만 대상 언어의 단어가 일치하지 않습니다. 일반적으로 인식 언어는 사용자가 말하는 것과 다른 언어를 의미합니다. |
| `InitialSilenceTimeout` | 오디오 스트림의 시작 부분에는 묵음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `BabbleTimeout` | 오디오 스트림의 시작 부분에는 소음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `Error` | 인식 서비스에서 내부 오류가 발생하여 계속할 수 없습니다. 가능한 경우 다시 시도하세요. |

> [!NOTE]
> 오디오가 욕설로만 구성되어 있고 `profanity` 쿼리 매개 변수가 `remove`로 설정되어 있는 경우 서비스는 음성 결과를 변환하지 않습니다.


`detailed` 형식은 `NBest` 필드와 함께 `simple` 형식과 동일한 필드를 포함합니다. `NBest` 필드는 가능성이 가장 높은 순위에서 가능성이 가장 높은 순위까지, 동일한 음성의 대체 해석 목록입니다. 첫 번째 항목은 기본 인식 결과와 같습니다. 각 항목에는 다음 필드가 포함됩니다.

|필드 이름|Content|
|-|-|
|`Confidence`|0.0(신뢰도 없음)에서 1.0(완전 신뢰도)까지 항목의 신뢰도 점수입니다.
|`Lexical`|인식된 텍스트의 어휘 형태, 즉 인식된 실제 단위입니다.
|`ITN`|전화 번호, 숫자, 축약어("doctor smith"가 "dr smith")가 포함된 인식된 텍스트의 역 텍스트 정규화된("기본형") 형태와 적용된 기타 변형입니다.
|`MaskedITN`| 요청된 경우 욕설 마스킹이 적용된 ITN 형태입니다.
|`Display`| 문장 부호 및 대문자로 표시가 추가된 인식된 텍스트의 표시 형태입니다. 최상위 결과에서 `DisplayText`와 동일합니다.

### <a name="sample-responses"></a>샘플 응답

다음은 `simple` 인식에 대한 일반적인 응답입니다.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

다음은 `detailed` 인식에 대한 일반적인 응답입니다.

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

## <a name="text-to-speech"></a>텍스트에서 음성 변환

다음은 Speech Service의 Text to Speech API에 대한 REST 엔드포인트입니다. 사용자 구독 지역과 일치하는 엔드포인트를 사용하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Speech Service는 Bing Speech에서 지원하는 16Khz 출력뿐만 아니라 24KHz 오디오 출력을 지원합니다. 2개의 24KHz 음성, `Jessa24kRUS` 및 `Guy24kRUS`가 있으므로, `X-Microsoft-OutputFormat` HTTP 헤더에는 4개의 24KHz 출력 형식이 사용 가능합니다.

로캘 | 언어   | 성별 | 서비스 이름 매핑
-------|------------|--------|------------
ko-KR  | 영어 | Female | “Microsoft Server Speech Text to Speech Voice(en-US, Jessa24kRUS)”
ko-KR  | 영어 | Male   | “Microsoft Server Speech Text to Speech Voice(en-US, Guy24kRUS)”

사용 가능한 음성의 전체 목록은 [지원되는 언어](language-support.md#text-to-speech)에 나와 있습니다.

### <a name="request-headers"></a>헤더 요청

다음 필드가 HTTP 요청 헤더에서 전송됩니다.

|헤더|의미|
|------|-------|
|`Authorization`|앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 필수 사항입니다. [인증](#authentication)을 참조하세요.|
|`Content-Type`|입력 콘텐츠 형식 `application/ssml+xml`입니다.|
|`X-Microsoft-OutputFormat`|오디오 출력 형식입니다. 다음 표를 참조하세요.|
|`User-Agent`|응용 프로그램 이름입니다. 필수 필드입니다. 255자 미만을 포함해야 합니다.|

사용 가능한 오디오 출력 형식(`X-Microsoft-OutputFormat`)은 비트 전송률과 인코딩을 모두 통합합니다.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> 선택한 음성 및 출력 형식의 비트 전송률이 다른 경우 필요에 오디오가 다시 샘플링됩니다. 단, 24khz 음성은 `audio-16khz-16kbps-mono-siren` 및 `riff-16khz-16kbps-mono-siren` 출력 형식을 지원하지 않습니다.

### <a name="request-body"></a>요청 본문

음성으로 변환할 텍스트는 일반 텍스트(ASCII 또는 UTF-8) 또는 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md) 형식(UTF-8)으로 HTTP `POST` 요청의 본문으로 전송됩니다. 일반 텍스트 요청은 서비스의 기본 음성 및 언어를 사용합니다. 다른 음성을 사용하려면 SSML을 전송합니다.

### <a name="sample-request"></a>샘플 요청

다음 HTTP 요청은 SSML 본문을 사용하여 음성을 선택합니다. 본문은 1,000자 이하여야 합니다.

```xml
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

### <a name="http-response"></a>HTTP 응답

응답의 HTTP 상태는 성공 또는 일반적인 오류 조건을 나타냅니다.

HTTP 코드|의미|가능한 원인
-|-|-|
200|확인|요청이 성공했습니다. 응답 본문이 오디오 파일입니다.
400 |잘못된 요청 |필수 매개 변수가 없거나 비어 있거나 null입니다. 또는 필수 또는 선택적 매개 변수에 전달된 값이 올바르지 않습니다. 일반적인 문제는 헤더가 너무 긴 경우입니다.
401|권한 없음 |요청에 권한이 없습니다. 구독 키 또는 토큰이 유효하고 올바른 영역에 있는지 확인하세요.
413|요청 엔터티가 너무 큼|SSML 입력이 1024자보다 깁니다.
429|너무 많은 요청|구독에 허용되는 요청의 할당량 또는 속도가 초과되었습니다.
502|잘못된 게이트웨이 | 네트워크 또는 서버 쪽 문제입니다. 잘못된 헤더를 나타낼 수도 있습니다.

HTTP 상태가 `200 OK`인 경우 응답 본문은 요청된 형식으로 오디오 파일을 포함합니다. 이 파일은 전송될 때 재생되거나 이후 재생 또는 다른 용도로 사용하기 위해 버퍼 또는 파일로 저장할 수 있습니다.

## <a name="authentication"></a>인증

Speech Service의 REST API로 요청을 전송하려면 구독 키 또는 액세스 토큰이 필요합니다. 일반적으로, 구독 키를 직접 전송하는 것이 가장 쉽습니다. 그러면 Speech Service는 사용자를 위한 액세스 토큰을 가져옵니다. 응답 시간을 최소화하기 위해 대신 액세스 토큰을 사용할 수도 있습니다.

토큰을 가져오려면 아래 표와 같이 지역별 Speech Service `issueToken` 엔드포인트에 구독 키를 제공하세요. 사용자 구독 지역과 일치하는 엔드포인트를 사용하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

각 액세스 토큰은 10분 동안 유효합니다. 언제든지 새 토큰을 가져올 수 있습니다. 원하는 경우 모든 Speech REST API 요청 직전에 토큰을 가져올 수 있습니다. 네트워크 트래픽 및 대기 시간을 최소화하려면 9분 동안 동일한 토큰을 사용하는 것이 좋습니다.

다음 섹션에서는 토큰을 획득하는 방법과 요청에서 사용하는 방법을 보여 줍니다.

### <a name="get-a-token-http"></a>토큰 가져오기: HTTP

다음 예제는 토큰을 획득하기 위한 샘플 HTTP 요청입니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 `Host` 헤더를 사용자 지역의 호스트 이름으로 바꿉니다.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

이 요청에 대한 응답 본문은 JWT(Java 웹 토큰) 형식의 액세스 토큰입니다.

### <a name="get-a-token-powershell"></a>토큰 가져오기: PowerShell

다음 Windows PowerShell 스크립트는 액세스 토큰을 획득하는 방법을 보여 줍니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 지정된 URI의 호스트 이름을 그에 맞게 변경합니다.

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

### <a name="get-a-token-curl"></a>토큰 가져오기: cURL

cURL은 Linux(Linux용 Windows 하위 시스템)에서 사용할 수 있는 명령줄 도구입니다. 다음 cURL 명령은 액세스 토큰을 획득하는 방법을 보여 줍니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 지정된 URI의 호스트 이름을 그에 맞게 변경합니다.

> [!NOTE]
> 이 명령은 가독성을 위해 여러 줄에 표시되지만, 셸 프롬프트에서는 단일 줄에 입력해야 합니다.

```
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
 -H "Content-type: application/x-www-form-urlencoded"
 -H "Content-Length: 0"
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>토큰 가져오기: C#

다음 C# 클래스는 액세스 토큰을 가져오는 방법을 보여 줍니다. 이 클래스를 인스턴스화할 때 Speech Service 구독 키를 전달합니다. 구독이 미국 서부 지역이 아닌 경우 `FetchTokenUri`의 호스트 이름을 그에 맞게 변경합니다.

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

### <a name="use-a-token"></a>토큰 사용

REST API 요청에서 토큰을 사용하려면 `Authorization` 헤더에 제공하고 뒤에 단어 `Bearer`를 입력합니다. 다음은 토큰을 포함하는 샘플 Text to Speech REST 요청입니다. 실제 토큰을 `YOUR_ACCESS_TOKEN`으로 대체합니다. `Host` 헤더에서 올바른 호스트 이름을 사용합니다.

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

### <a name="renew-authorization"></a>권한 부여 갱신

권한 부여 토큰은 10분 후에 만료됩니다. 만료되기 전에 새 토큰을 획득하여 권한 부여를 갱신합니다. 예를 들어, 9분 후 새 토큰을 가져올 수 있습니다.

다음 C# 코드는 앞에 제공된 클래스로 즉시 대체됩니다. `Authentication` 클래스는 타이머를 사용하여 9분 간격으로 새 액세스 토큰을 자동으로 획득합니다. 이 방법을 사용하면 프로그램이 실행되는 동안 유효한 토큰을 항상 사용할 수 있습니다.

> [!NOTE]
> 타이머를 사용하는 대신, 마지막 토큰을 가져왔을 때의 타임스탬프를 저장할 수 있습니다. 그런 다음 만료에 가까워진 경우에만 새 토큰을 요청할 수 있습니다. 이 방법은 불필요하게 새 토큰을 요청하는 경우를 방지하며, 음성을 자주 요청하지 않는 프로그램에 좀 더 적합할 수 있습니다.

이전처럼 `FetchTokenUri` 값이 사용자 구독 지역과 일치하는지 확인합니다. 이 클래스를 인스턴스화할 때 구독 키를 전달합니다.

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
