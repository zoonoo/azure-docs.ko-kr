---
title: C# 서비스 라이브러리를 사용하여 Microsoft Speech Recognition API 시작 | Microsoft Docs
description: Microsoft 음성 인식 서비스 라이브러리를 사용하여 음성 언어를 텍스트로 변환합니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/17/2017
ms.author: zhouwang
ms.openlocfilehash: 0320f41658a7ac4d6bf9e88ed998c853b665d485
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373934"
---
# <a name="get-started-with-the-speech-recognition-service-library-in-c35-for-net-windows"></a>.NET Windows용 C#에서 음성 인식 서비스 라이브러리 시작

서비스 라이브러리는 자신의 클라우드 서비스가 있고 이 서비스에서 Speech Service를 호출하려는 개발자를 위한 것입니다. 장치에 바인딩된 응용 프로그램에서 음성 인식 서비스를 호출하려면 이 SDK를 사용하지 마세요. 대신 다른 클라이언트 라이브러리 또는 REST API를 사용합니다.

C# 서비스 라이브러리를 사용하려면 [Microsoft.Bing.Speech NuGet 패키지](https://www.nuget.org/packages/Microsoft.Bing.Speech/)를 설치합니다. 라이브러리 API 참조는 [Microsoft Speech C# 서비스 라이브러리](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)를 참조하세요.

다음 섹션에서는 C# 서비스 라이브러리를 사용하여 C# 샘플 응용 프로그램을 설치, 빌드 및 실행하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

다음 예제는 [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)을 사용하여 Windows 8 이상 및 .NET Framework 4.5 이상용으로 개발되었습니다.

### <a name="get-the-sample-application"></a>샘플 응용 프로그램 가져오기

[Speech C# 서비스 라이브러리 샘플](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) 리포지토리에서 샘플을 복제합니다.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Speech Recognition API 구독 및 평가판 구독 키 가져오기

Speech API는 Cognitive Services(이전의 Project Oxford)의 일부입니다. [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지에서 평가판 구독 키를 가져올 수 있습니다. Speech API를 선택한 후에 **API 키 가져오기**를 선택하여 키를 가져옵니다. 기본 및 보조 키를 반환합니다. 두 키는 모두 동일한 할당량에 연결되므로 두 키 중 하나를 사용할 수 있습니다.

> [!IMPORTANT]
> * 구독 키를 가져오세요. Speech 클라이언트 라이브러리를 사용하려면 먼저 [구독 키](https://azure.microsoft.com/try/cognitive-services/)가 있어야 합니다.
>
> * 구독 키를 사용하세요. 제공된 C# 서비스 라이브러리 샘플 응용 프로그램을 사용하여 구독 키를 명령줄 매개 변수 중 하나로 제공해야 합니다. 자세한 내용은 [샘플 응용 프로그램 실행](#step-3-run-the-sample-application)을 참조하세요.

## <a name="step-1-install-the-sample-application"></a>1단계: 샘플 응용 프로그램 설치

1. Visual Studio 2015를 시작하고, **파일** > **열기** > **프로젝트/솔루션**을 차례로 선택합니다.

2. SpeechClient.sln이라는 Visual Studio 2015 솔루션(.sln) 파일을 두 번 클릭하여 엽니다. 솔루션이 Visual Studio에서 열립니다.

## <a name="step-2-build-the-sample-application"></a>2단계: 샘플 응용 프로그램 빌드

Ctrl+Shift+B를 누르거나 리본 메뉴에서 **빌드**를 선택합니다. 그런 다음, **솔루션 빌드**를 선택합니다.

## <a name="step-3-run-the-sample-application"></a>3단계: 샘플 응용 프로그램 실행

1. 빌드가 완료되면 F5 키를 누르거나 리본 메뉴에서 **시작**을 선택하여 예제를 실행합니다.

2. 샘플에 대한 출력 디렉터리(예: SpeechClientSample\bin\Debug)를 엽니다. Shift+마우스 오른쪽 단추를 클릭하고, **여기서 명령 창 열기**를 선택합니다.

3. 다음 인수를 사용하여 `SpeechClientSample.exe`를 실행합니다.

   * Arg[0]: 입력 오디오 WAV 파일을 지정합니다.
   * Arg [1]: 오디오 로캘을 지정합니다.
   * Arg[2]: 인식 모드를 지정합니다(`ShortPhrase` 모드의 경우 *Short*, `LongDictation` 모드의 경우 *Long*).
   * Arg [3]: 음성 인식 서비스에 액세스하기 위한 구독 키를 지정합니다.

## <a name="samples-explained"></a>샘플 설명

### <a name="recognition-modes"></a>인식 모드

* `ShortPhrase` 모드: 최대 15초가 걸리는 발화입니다. 데이터가 서버로 보내지면 클라이언트에서 여러 개의 부분 결과 및 하나의 최종 결과를 받습니다.
* `LongDictation` 모드: 최대 10분이 걸리는 발화입니다. 데이터가 서버로 보내지면 서버에서 문장 일시 중지를 나타내는 위치에 따라 클라이언트에서 여러 개의 부분 결과 및 여러 개의 최종 결과를 받습니다.

### <a name="supported-audio-formats"></a>지원되는 오디오 형식

Speech API에서 오디오/WAV를 지원하기 위해 사용하는 코덱은 다음과 같습니다.

* PCM 단일 채널
* Siren
* SirenSR

### <a name="preferences"></a>Preferences(기본 설정) 개체

SpeechClient를 만들려면 먼저 Preferences 개체를 만들어야 합니다. Preferences 개체는 음성 서비스의 동작을 구성하는 매개 변수의 집합입니다. 구성되는 필드는 다음과 같습니다.

* `SpeechLanguage`: 음성 서비스로 보내는 오디오의 로캘입니다.
* `ServiceUri`: 음성 서비스를 호출하는 데 사용되는 엔드포인트입니다.
* `AuthorizationProvider`: 음성 서비스에 액세스하기 위해 토큰을 가져오는 데 사용되는 IAuthorizationProvider 구현입니다. 샘플에서는 Cognitive Services 권한 부여 공급자를 제공하지만, 토큰 캐싱을 처리할 수 있는 사용자 고유의 구현을 만드는 것이 좋습니다.
* `EnableAudioBuffering`: 고급 옵션입니다. [연결 관리](#connection-management)를 참조하세요.

### <a name="speech-input"></a>음성 입력

SpeechInput 개체는 다음 두 개의 필드로 구성됩니다.

* **Audio**: SDK에서 오디오를 끌어오는 스트림 구현입니다. 읽기를 지원하는 [스트림](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) 중 하나일 수 있습니다.
   > [!NOTE]
   > 스트림의 읽기에서 **0**을 반환할 때 SDK에서 스트림의 끝을 감지합니다.

* **RequestMetadata**: 음성 요청에 대한 메타데이터입니다. 자세한 내용은 [참조](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)를 참조하세요.

### <a name="speech-request"></a>음성 요청

SpeechClient 및 SpeechInput 개체가 인스턴스화되면 RecognizeAsync를 사용하여 Speech Service에 요청합니다.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

요청이 완료되면 RecognizeAsync에서 반환된 작업이 완료됩니다. 마지막 RecognitionResult는 인식의 끝입니다. 서비스 또는 SDK가 예기치 않게 실패하면 작업이 실패할 수 있습니다.

### <a name="speech-recognition-events"></a>음성 인식 이벤트

#### <a name="partial-results-event"></a>부분 결과 이벤트

이 이벤트는 말하기를 끝내거나(`MicrophoneRecognitionClient`를 사용하는 경우) 데이터 보내기를 끝내기(`DataRecognitionClient`를 사용하는 경우) 전에도 Speech Service에서 사용자가 말할 수 있는 내용을 예측할 때마다 호출됩니다. `SpeechClient.SubscribeToPartialResult()`를 사용하여 이벤트를 구독할 수 있습니다. 또는 일반 이벤트 구독 메서드인 `SpeechClient.SubscribeTo<RecognitionPartialResult>()`를 사용할 수 있습니다.

**반환 형식** | 설명 |
------|------
**LexicalForm** | 이 형식은 처리되지 않은 원시 음성 인식 결과가 필요한 응용 프로그램에서 사용하기에 적합합니다.
**DisplayText** | 역 텍스트 정규화, 대문자 표시, 문장 부호 및 불경한 언어 마스킹이 적용된 인식된 구입니다. 불경한 언어는 첫 번째 문자 뒤에 별표로 마스킹됩니다(예: "d***"). 이 형식은 음성 인식 결과를 사용자에게 표시하는 응용 프로그램에서 사용하기에 적합합니다.
**신뢰도** | 인식된 구가 음성 인식 서버에서 정의한 대로 관련 오디오를 나타내는 신뢰도 수준입니다.
**MediaTime** | 오디오 스트림의 시작을 기준으로 한 현재 시간입니다(100나노초 시간 단위).
**MediaDuration** | 오디오 세그먼트를 기준으로 한 현재 구의 기간/길이(시간의 100나노초 단위)입니다.

#### <a name="result-event"></a>결과 이벤트
말하기를 끝내면(`ShortPhrase` 모드에서) 이 이벤트가 호출됩니다. 결과에 대한 N 상위 선택 항목이 제공됩니다. `LongDictation` 모드에서는 서버에서 문장 일시 중지를 나타내는 위치에 따라 이벤트가 여러 번 호출될 수 있습니다. `SpeechClient.SubscribeToRecognitionResult()`를 사용하여 이벤트를 구독할 수 있습니다. 또는 일반 이벤트 구독 메서드인 `SpeechClient.SubscribeTo<RecognitionResult>()`를 사용할 수 있습니다.

**반환 형식** | 설명 |
------|------|
**RecognitionStatus** | 인식이 생성된 방법에 상태입니다. 예를 들어 성공적인 인식 결과 또는 연결 취소 결과 등으로 생성된 것이 있습니다.
**Phrases** | 인식 신뢰도를 사용하여 인식된 N 상위 구의 집합입니다.

인식 결과에 대한 자세한 내용은 [출력 형식](../Concepts.md#output-format)을 참조하세요.

### <a name="speech-recognition-response"></a>음성 인식 응답

음성 응답 예제는 다음과 같습니다.
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High) 
```

## <a name="connection-management"></a>연결 관리

API는 요청당 하나의 WebSocket 연결을 사용합니다. 최적의 사용자 경험을 위해 SDK에서 Speech Service에 다시 연결하고, 마지막으로 받은 RecognitionResult에서 인식을 시작하려고 합니다. 예를 들어 오디오 요청에 2분이 걸리고, SDK가 1분 표식에서 RecognitionEvent를 받고, 5초 후에 네트워크 오류가 발생한 경우, SDK는 1분 표식부터 시작하는 새 연결을 시작합니다.

>[!NOTE]
>스트림에서 검색을 지원하지 않을 수 있으므로 SDK는 1분 표식까지 다시 검색하지 않습니다. 대신, SDK는 오디오를 버퍼링하는 데 사용하는 내부 버퍼를 유지하고, RecognitionResult 이벤트를 받을 때 버퍼를 지웁니다.

## <a name="buffering-behavior"></a>버퍼링 동작

기본적으로 SDK는 네트워크 중단 발생 시 복구할 수 있도록 오디오를 버퍼링합니다. 네트워크 연결이 끊겨 있는 동안 손실된 오디오를 삭제하고 연결을 다시 시작하는 것이 바람직한 시나리오에서는 Preferences 개체의 `EnableAudioBuffering`을 `false`로 설정하여 오디오 버퍼링을 사용하지 않는 것이 가장 좋습니다.

## <a name="related-topics"></a>관련된 항목

[Microsoft Speech C# 서비스 라이브러리 참조](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
