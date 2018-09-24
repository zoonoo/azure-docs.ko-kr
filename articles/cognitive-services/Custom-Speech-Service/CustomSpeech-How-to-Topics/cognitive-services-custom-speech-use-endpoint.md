---
title: Azure에서 Custom Speech Service로 사용자 지정 음성 엔드포인트 사용 | Microsoft Docs
description: Cognitive Services에서 Custom Speech Service를 통해 사용자 지정 음성-텍스트 엔드포인트를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 55583952df3b83331f1f622a4fce269713ecf2a6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966523"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>사용자 지정 음성-텍스트 엔드포인트 사용
기본 Cognitive Services 음성 엔드포인트에서와 같은 방식으로 Azure Custom Speech Service 음성-텍스트 엔드포인트에 요청을 보낼 수 있습니다. 이러한 엔드포인트는 기능적으로 Speech API의 기본 엔드포인트와 동일합니다. 따라서 클라이언트 라이브러리 또는 Speech API에 대한 REST API를 통해 사용할 수 있는 동일한 기능은 사용자 지정 엔드포인트에서도 사용할 수 있습니다.

이 서비스를 사용하여 만드는 엔드포인트는 다양한 수의 동시 요청을 처리할 수 있습니다. 볼륨은 구독과 관련된 가격 책정 계층에 따라 달라집니다. 너무 많은 요청을 수신하면 오류가 발생합니다. 체험판 계층에는 월별 요청 제한이 있습니다.

서비스는 데이터가 실시간으로 전송된다고 가정합니다. 더 빠르게 전송되면 요청은 실제 해당 기간이 지나갈 때까지 실행 중인 것으로 간주됩니다.

> [!NOTE]
> 단, [새 웹 소켓](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol)은 지원합니다. 사용자 지정 음성 엔드포인트를 통해 웹 소켓을 사용하려는 경우 여기 나온 지침을 따릅니다.
>
> 새 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) 지원은 곧 제공될 예정입니다. HTTP를 통해 사용자 지정 음성 엔드포인트를 호출하려는 경우 여기 나온 지침을 따릅니다.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>음성 클라이언트 라이브러리를 사용하여 요청 보내기

음성 클라이언트 라이브러리를 사용하여 사용자 지정 엔드포인트로 요청을 보내려면 인식 클라이언트를 시작합니다. [NuGet](http://nuget.org/)에서 클라이언트 음성 SDK를 사용합니다. *음성 인식*을 검색하고 플랫폼의 Microsoft에서 음성 인식 패키지를 선택합니다. 일부 소스 코드는 [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows)에서 찾을 수 있습니다. 클라이언트 음성 SDK는 다음 메서드를 제공하는 팩터리 클래스 **SpeechRecognitionServiceFactory**를 제공합니다.

  *   ```CreateDataClient(...)```: 데이터 인식 클라이언트입니다.
  *   ```CreateDataClientWithIntent(...)```: 의도가 포함된 데이터 인식 클라이언트입니다.
  *   ```CreateMicrophoneClient(...)```: 마이크 인식 클라이언트입니다.
  *   ```CreateMicrophoneClientWithIntent(...)```: 의도가 포함된 마이크 인식 클라이언트입니다.

자세한 설명서는 [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home)를 참조하세요. Custom Speech Service 엔드포인트는 동일한 SDK를 지원합니다.

데이터 인식 클라이언트는 파일 또는 다른 오디오 소스와 같은 데이터에서 음성을 인식하는 데 적합합니다. 마이크 인식 클라이언트는 마이크에서 음성을 인식하는 데 적합합니다. 두 클라이언트에서 인터넷을 사용하면 시나리오에 맞는 LUIS 응용 프로그램을 빌드한 경우 [LUIS(Language Understanding Intelligent Service)](https://www.luis.ai/)에서 구조화된 의도 결과를 반환할 수 있습니다.

클라이언트의 네 가지 유형은 모두 두 가지 방법으로 인스턴스화할 수 있습니다. 첫 번째 방법은 표준 Cognitive Services Speech API를 사용합니다. 두 번째 방법을 사용하면 Custom Speech Service를 사용하여 만든 사용자 지정 엔드포인트에 해당하는 URL을 지정할 수 있습니다.

예를 들어 다음 메서드를 사용하여 사용자 지정 엔드포인트에 요청을 보내는 **DataRecognitionClient**를 만들 수 있습니다.

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

**배포 정보** 페이지에서 **your_subscriptionId** 및 **endpointURL**은 각각 구독 키 및 웹 소켓 URL을 참조합니다.

**AuthenticationUri**는 인증 서비스에서 토큰을 수신하는 데 사용됩니다. 이 URI는 다음 샘플 코드에 나온 것처럼 개별적으로 설정해야 합니다.

이 샘플 코드는 클라이언트 SDK를 사용하는 방법을 보여 줍니다.

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> SDK에서 **Create** 메서드를 사용하는 경우 **Create** 메서드의 오버로드로 인해 구독 ID를 두 번 제공해야 합니다.
>

Custom Speech Service는 짧은 양식과 긴 양식 인식에 대해 두 가지 다른 URL을 사용합니다. 모두 **배포** 페이지에 나열되어 있습니다. 사용하려는 특정 양식에 올바른 엔드포인트 URL을 사용합니다.

사용자 지정 엔드포인트로 다양한 인식 클라이언트를 호출하는 데 관한 자세한 내용은 [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) 클래스를 참조하세요. 이 페이지에 있는 설명서는 어쿠스틱 모델 적응을 참조하지만, Custom Speech Service를 사용하여 만든 모든 엔드포인트에 적용됩니다.

## <a name="send-requests-by-using-the-speech-protocol"></a>음성 프로토콜을 사용하여 요청 보내기

[음성 프로토콜](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol)에 대해 표시되는 엔드포인트는 오픈 소스 웹 소켓 음성 프로토콜에 대한 엔드포인트입니다.

현재 유일한 공식 클라이언트 구현은 [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)입니다. 여기에 제공된 샘플 코드로 시작하려면 코드를 다음과 같이 변경하고 샘플을 다시 빌드합니다.

1. _src\sdk\speech.browser\SpeechConnectionFactory.ts_에서 호스트 이름 “wss://speech.platform.bing.com”을 배포의 세부 정보 페이지에 표시된 호스트 이름으로 바꿉니다. 여기서 전체 URI가 아닌 *wss* 프로토콜 스키마 및 호스트 이름만 삽입합니다. 예: 

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. 요구 사항에 따라 _samples\browser\Samples.html_의 _recognitionMode_ 매개 변수를 설정합니다.
    * _RecognitionMode.Interactive_는 요청을 최대 15초까지 지원합니다.
    * _RecognitionMode.Conversation_ 및 _RecognitionMode.Dictation_(둘 다 Custom Speech Service에서 동일함)은 요청을 최대 10분까지 지원합니다.

3. 사용하기 전에 “gulp build”를 사용하여 샘플을 빌드합니다.

> [!NOTE]
> 이 프로토콜에 대한 올바른 URI를 사용하는지 확인합니다. 필요한 스키마는 *wss*입니다(클라이언트 프로토콜에서와 같이 *http*가 아님). 

자세한 내용은 [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) 설명서를 참조하세요.

## <a name="send-requests-by-using-http"></a>HTTP를 사용하여 요청 보내기

요청을 HTTP post를 사용하여 사용자 지정 엔드포인트로 보내는 것은 요청을 HTTP로 Cognitive Services Bing Speech API에 보내는 것과 비슷합니다. 사용자 지정 배포의 주소를 반영하도록 URL을 수정합니다.

Cognitive Services 음성 엔드포인트 및 이 서비스를 사용하여 만든 사용자 지정 엔드포인트 모두에서 HTTP를 통해 보낸 요청에는 몇 가지 제한 사항이 있습니다. HTTP 요청은 인식 프로세스 도중 일부 결과를 반환할 수 없습니다. 또한 요청 기간은 오디오 콘텐츠에 10초, 전체적으로 14초로 제한됩니다.

post 요청을 만들려면 Cognitive Services 음성 API에 사용하는 동일한 프로세스를 수행합니다.

1. 구독 ID를 사용하여 액세스 토큰을 가져옵니다. 이 토큰은 인식 엔드포인트에 액세스해야 합니다. 10분 동안 다시 사용할 수 있습니다.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      **subscriptionId**는 이 배포에 사용할 구독 ID로 설정해야 합니다. 응답은 다음 요청에 필요한 일반 토큰입니다.

2. POST를 다시 사용하여 엔드포인트에 오디오를 게시합니다.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    **토큰**은 이전 호출로 수신한 액세스 토큰입니다. **https_endpoint**는 **배포 정보** 페이지에 표시된 사용자 지정 음성-텍스트 엔드포인트의 전체 주소입니다.

HTTP post 매개 변수 및 응답 형식에 대한 자세한 내용은 [Microsoft Cognitive Services Bing Speech HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation)를 참조하세요.

## <a name="send-requests-by-using-the-service-library"></a>서비스 라이브러리를 사용하여 요청 보내기
사용자 서비스는 서비스 라이브러리를 통해 Microsoft 음성 전사 클라우드를 사용하여 실시간으로 음성을 텍스트로 변환시킬 수 있습니다. 따라서 클라이언트 앱은 동시에 비동기적으로 오디오를 보내고 특정 인식 결과를 다시 받을 수 있습니다. 서비스 SDK의 세부 정보는 [여기](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)에서 확인할 수 있습니다.

> [!NOTE]
> 서비스 라이브러리를 사용하는 경우 **IAuthorizationProvider**의 구현에서 권한 부여 공급자의 URI를 https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken으로 변경해야 합니다.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 어쿠스틱 모델](cognitive-services-custom-speech-create-acoustic-model.md)로 정확도를 개선합니다.
* [사용자 지정 언어 모델](cognitive-services-custom-speech-create-language-model.md)로 정확도를 개선합니다.
