---
title: REST를 사용하여 Bing Speech Recognition API 시작 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: REST를 통해 Microsoft Cognitive Services의 Speech Recognition API에 액세스하여 음성 오디오를 텍스트로 변환합니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: a9f74f4032a78ee51ea2a8f020cd1418bb3330ca
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345359"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>빠른 시작: Bing Speech Recognition REST API 사용

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

클라우드 기반 Bing Speech Service를 사용하여 REST API를 통해 음성 오디오를 텍스트로 변환함으로써 애플리케이션을 개발할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Speech API 구독 및 평가판 구독 키 가져오기

Speech API는 Cognitive Services(이전의 Project Oxford)의 일부입니다. [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지에서 평가판 구독 키를 가져올 수 있습니다. Speech API를 선택한 후에 **API 키 가져오기**를 선택하여 키를 가져옵니다. 기본 및 보조 키를 반환합니다. 두 키는 모두 동일한 할당량에 연결되므로 두 키 중 하나를 사용할 수 있습니다.

> [!IMPORTANT]
>* 구독 키를 가져오세요. REST API에 액세스하려면 먼저 [구독 키](https://azure.microsoft.com/try/cognitive-services/)가 있어야 합니다.
>
>* 구독 키를 사용하세요. 다음 REST 샘플에서 YOUR_SUBSCRIPTION_KEY를 사용자 고유의 구독 키로 바꿉니다. 
>
>* 구독 키를 가져오는 방법은 [인증](../how-to/how-to-authentication.md) 페이지를 참조하세요.

### <a name="prerecorded-audio-file"></a>미리 녹음된 오디오 파일

여기서는 녹음된 오디오 파일을 사용하여 REST API를 사용하는 방법을 보여 줍니다. 짧은 구를 말하면서 자신의 오디오 파일을 기록합니다. 예를 들어 "오늘 날씨는 어떤가요?" 또는 "감상하려는 재미 있는 영화를 찾아보세요."라고 말합니다. 음성 인식 API는 외부 마이크 입력도 지원합니다.

> [!NOTE]
> 이 예에서는 오디오가 **PCM 단일 채널(모노), 16KHz**의 WAV 파일로 기록되어야 합니다.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>인식 요청 작성 및 음성 인식 서비스로 보내기

음성 인식에 대한 다음 단계는 적절한 요청 헤더와 본문을 사용하여 Speech HTTP 엔드포인트에 POST 요청을 보내는 것입니다.

### <a name="service-uri"></a>서비스 URI

음성 인식 서비스 URI는 [인식 모드](../concepts.md#recognition-modes) 및 [인식 언어](../concepts.md#recognition-languages)를 기반으로 하여 정의됩니다.

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`는 인식 모드를 지정하며, `interactive`, `conversation` 또는 `dictation` 값 중 하나여야 합니다. URI에 필요한 리소스 경로입니다. 자세한 내용은 [인식 모드](../concepts.md#recognition-modes)를 참조하세요.

`<LANGUAGE_TAG>`는 쿼리 문자열의 필수 매개 변수입니다. 오디오 변환에 대한 대상 언어를 정의합니다(예: 영어(미국)의 경우 `en-US`). 자세한 내용은 [인식 언어](../concepts.md#recognition-languages)를 참조하세요.

`<OUTPUT_FORMAT>`은 쿼리 문자열의 선택적 매개 변수입니다. 허용되는 값은 `simple` 및 `detailed`입니다. 기본적으로 서비스는 결과를 `simple` 형식으로 반환합니다. 자세한 내용은 [출력 형식](../concepts.md#output-format)을 참조하세요.

다음 표에는 서비스 URI에 대한 몇 가지 예가 나와 있습니다.

| 인식 모드  | 언어 | 출력 형식 | 서비스 URI |
|---|---|---|---|
| `interactive` | pt-BR | 기본값 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | ko-KR | Detailed |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | 간단한 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> 서비스 URI는 애플리케이션에서 REST API를 사용하여 음성 인식 서비스를 호출하는 경우에만 필요합니다. [클라이언트 라이브러리](GetStartedClientLibraries.md) 중 하나를 사용하는 경우 일반적으로 사용되는 URI에 대해 알고 있을 필요가 없습니다. 클라이언트 라이브러리에서 특정 클라이언트 라이브러리에만 적용할 수 있는 별도의 서비스 URI를 사용할 수 있습니다. 자세한 내용은 선택한 클라이언트 라이브러리를 참조하세요.

### <a name="request-headers"></a>헤더 요청

요청 헤더에 설정해야 하는 필드는 다음과 같습니다.

- `Ocp-Apim-Subscription-Key`: 서비스를 호출할 때마다 `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 전달해야 합니다. Speech Service는 구독 키 대신 권한 부여 토큰을 전달하는 기능도 지원합니다. 자세한 내용은 [인증](../How-to/how-to-authentication.md)을 참조하세요.
- `Content-type`: `Content-type` 필드는 오디오 스트림의 형식과 코덱을 설명합니다. 현재 WAV 파일과 PCM 모노 16000 인코딩만 지원됩니다. 이 형식에 대한 Content-type 값은 `audio/wav; codec=audio/pcm; samplerate=16000`입니다.

`Transfer-Encoding` 필드는 선택 사항입니다. 이 필드가 `chunked`로 설정되면 오디오를 작은 청크로 분할할 수 있습니다. 자세한 내용은 [청크 분할 전송](../How-to/how-to-chunked-transfer.md)을 참조하세요.

요청 헤더 샘플은 다음과 같습니다.

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>서비스에 요청 보내기

다음 예제에서는 Speech REST 엔드포인트에 음성 인식 요청을 보내는 방법을 보여 줍니다. `interactive` 인식 모드를 사용합니다.

> [!NOTE]
> `YOUR_AUDIO_FILE`을 미리 녹음된 오디오 파일의 경로로 바꾸고, `YOUR_SUBSCRIPTION_KEY`를 사용자 고유의 구독 키로 바꾸세요.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

이 예제에서는 bash가 있는 Linux에서 curl을 사용합니다. 플랫폼에서 사용할 수 없는 경우 curl을 설치해야 할 수도 있습니다. 또한 이 예제는 Windows의 Cygwin, Git Bash, zsh 및 다른 셸에서도 작동합니다.

> [!NOTE]
> `--data-binary`의 값이 데이터 대신 파일 이름임을 나타내기 때문에 `YOUR_AUDIO_FILE`를 미리 녹음된 오디오 파일의 경로로 바꿀 때는 오디오 파일 이름 앞에 `@`를 유지하세요.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
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

---

## <a name="process-the-speech-recognition-response"></a>음성 인식 응답 처리

요청이 처리되면 Speech Service에서 응답의 결과를 JSON 형식으로 반환합니다.

> [!NOTE]
> 앞의 코드에서 오류가 반환되면 [문제 해결](../troubleshooting.md)을 참조하여 가능한 원인을 찾습니다.

다음 코드 조각에서는 스트림에서 응답을 읽는 방법에 대한 예를 보여 줍니다.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

이 예제에서는 curl에서 응답 메시지를 문자열로 직접 반환합니다. JSON 형식으로 표시하려면 추가 도구(예: jq)를 사용하면 됩니다.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

JSON 응답 샘플은 다음과 같습니다.

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>제한 사항

REST API에는 몇 가지 제한 사항이 있습니다.

- 최대 15초의 오디오 스트림만 지원합니다.
- 인식하는 동안 중간 결과를 지원하지 않습니다. 사용자가 최종 인식 결과만 받습니다.

이러한 제한 사항을 제거하려면 Speech [클라이언트 라이브러리](GetStartedClientLibraries.md)를 사용합니다. 또는 [Speech WebSocket 프로토콜](../API-Reference-REST/websocketprotocol.md)을 직접 사용할 수도 있습니다.

## <a name="whats-next"></a>다음 단계

- C#, Java 등에서 REST API를 사용하는 방법을 알아보려면 이러한 [샘플 애플리케이션](../samples.md)을 참조하세요.
- 오류를 찾고 수정하려면 [문제 해결](../troubleshooting.md)을 참조하세요.
- 고급 기능을 사용하려면 Speech [클라이언트 라이브러리](GetStartedClientLibraries.md)를 사용하여 시작하는 방법을 참조하세요.

### <a name="license"></a>라이선스

Cognitive Services SDK 및 샘플은 모두 MIT 라이선스를 통해 사용이 허가됩니다. 자세한 내용은 [라이선스](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md)를 참조하세요.
