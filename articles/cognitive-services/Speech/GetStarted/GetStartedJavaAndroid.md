---
title: Android의 Java에서 Microsoft Speech Recognition API 시작 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft Speech API를 사용하여 음성 오디오를 텍스트로 변환하는 Android 애플리케이션을 개발합니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 4c5243ec14a4494222168bb33b3e840b96f8465e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345257"
---
[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>빠른 시작: Android의 Java에서 Bing Speech Recognition API 사용

Bing Speech Recognition API를 사용하면 클라우드 기반 Bing Speech 서비스를 사용하여 음성 오디오를 텍스트로 변환하는 Android 애플리케이션을 개발할 수 있습니다. API는 실시간 스트리밍을 지원하므로 애플리케이션에서 오디오를 서비스에 보내는 동시에 부분 인식 결과를 동시에 비동기적으로 받을 수 있습니다.

이 문서에서는 샘플 애플리케이션을 사용하여 Android용 Speech 클라이언트 라이브러리를 통해 Android 디바이스용 Java에서 음성 - 텍스트 변환 애플리케이션을 개발하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

이 샘플은 Windows용 [Android Studio](http://developer.android.com/sdk/index.html)를 통해 Java에서 개발되었습니다.

### <a name="get-the-client-library-and-sample-application"></a>클라이언트 라이브러리 및 샘플 애플리케이션 가져오기

Android용 Speech 클라이언트 라이브러리 및 샘플은 [Android용 Speech 클라이언트 SDK](https://github.com/microsoft/cognitive-speech-stt-android)에서 사용할 수 있습니다. 빌드 가능한 샘플은 samples/SpeechRecoExample 디렉터리에서 찾을 수 있습니다. 사용자 고유의 앱에서 사용해야 하는 두 개의 라이브러리는 armeabi 및 x86 폴더의 SpeechSDK/libs에서 찾을 수 있습니다. libandroid_platform.so 파일의 크기는 22MB이지만 배포 시 4MB로 줄어듭니다.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Speech API 구독 및 평가판 구독 키 가져오기

Speech API는 Cognitive Services(이전의 Project Oxford)의 일부입니다. [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지에서 평가판 구독 키를 가져올 수 있습니다. Speech API를 선택한 후에 **API 키 가져오기**를 선택하여 키를 가져옵니다. 기본 및 보조 키를 반환합니다. 두 키는 모두 동일한 할당량에 연결되므로 두 키 중 하나를 사용할 수 있습니다.

*의도가 있는 인식*을 사용하려면 [LUIS(Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)에도 등록해야 합니다.

> [!IMPORTANT]
>* 구독 키를 가져오세요. Speech 클라이언트 라이브러리를 사용하려면 먼저 [구독 키](https://azure.microsoft.com/try/cognitive-services/)가 있어야 합니다.
>
>* 구독 키를 사용하세요. 제공된 Android 샘플 애플리케이션을 사용하여 samples/SpeechRecoExample/res/values/strings.xml 파일을 구독 키로 업데이트합니다. 자세한 내용은 [샘플 빌드 및 실행](#build-and-run-samples)을 참조하세요.

## <a name="use-the-speech-client-library"></a>Speech 클라이언트 라이브러리 사용

애플리케이션에서 클라이언트 라이브러리를 사용하려면 이 [지침](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library)을 따르세요.

[Android용 Speech 클라이언트 SDK](https://github.com/microsoft/cognitive-speech-stt-android)의 docs 폴더에서 Android용 클라이언트 라이브러리 참조를 찾을 수 있습니다.

## <a name="build-and-run-samples"></a>샘플 빌드 및 실행

샘플을 빌드하고 실행하는 방법에 대한 내용은 이 [추가 정보 페이지](https://github.com/microsoft/cognitive-speech-stt-android#the-sample)를 참조하세요.

## <a name="samples-explained"></a>샘플 설명

### <a name="create-recognition-clients"></a>인식 클라이언트 만들기

다음 샘플 코드에서는 사용자 시나리오에 따라 인식 클라이언트 클래스를 만드는 방법을 보여 줍니다.

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

클라이언트 라이브러리는 일반적인 음성 인식 시나리오에 맞게 미리 구현된 인식 클라이언트 클래스를 제공합니다.

* `DataRecognitionClient`: PCM 데이터(예: 파일 또는 오디오 소스에 있음)를 사용하는 음성 인식입니다. 데이터는 버퍼로 분할되고, 각 버퍼는 Speech Service로 보내집니다. 버퍼는 수정되지 않으므로 원하는 경우 사용자가 자신의 묵음 감지를 적용할 수 있습니다. 데이터가 WAV 파일에서 제공되면 데이터를 파일에서 Speech Service로 직접 보낼 수 있습니다. 원시 데이터가 있으면(예: 오디오가 Bluetooth를 통해 들어오는 경우), 먼저 Speech Service에 형식 헤더를 보낸 다음, 데이터를 보냅니다.
* `MicrophoneRecognitionClient`: 마이크에서 나오는 음성을 사용하는 음성 인식입니다. 마이크가 켜져 있고 마이크의 데이터를 음성 인식 서비스로 보내는지 확인합니다. 기본 제공 "묵음 감지기"가 먼저 마이크 데이터에 적용된 후에 인식 서비스로 보내집니다.
* `DataRecognitionClientWithIntent` 및 `MicrophoneRecognitionClientWithIntent`: 이러한 클라이언트는 인식 텍스트 외에도 화자의 의도에 대해 구조화된 정보를 반환합니다. 이 정보는 응용 프로그램에서 추가 작업을 구동하는 데 사용할 수 있습니다. "의도"를 사용하려면 먼저 [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)를 사용하여 모델이 학습되어야 합니다.

### <a name="recognition-language"></a>인식 언어

`SpeechRecognitionServiceFactory`를 사용하여 클라이언트를 만들 때 언어를 선택해야 합니다. Speech Service에서 지원되는 언어의 전체 목록은 [지원되는 언어](../API-Reference-REST/supportedlanguages.md)를 참조하세요.

### `SpeechRecognitionMode`

`SpeechRecognitionServiceFactory`를 사용하여 클라이언트를 만들 때 `SpeechRecognitionMode`도 지정해야 합니다.

* `ShortPhrase`: 최대 15초가 걸리는 발화입니다. 데이터가 서비스로 보내지면 클라이언트에서 여러 개의 부분 결과 및 여러 개의 N 상위 선택 항목이 있는 하나의 최종 결과를 받습니다.
* `LongDictation`: 최대 2분이 걸리는 발화입니다. 데이터가 서비스로 보내지면 서비스에서 문장 일시 중지를 식별하는 위치에 따라 클라이언트에서 여러 개의 부분 결과 및 여러 개의 최종 결과를 받습니다.

### <a name="attach-event-handlers"></a>이벤트 처리기 연결

사용자가 만든 클라이언트에 다양한 이벤트 처리기를 연결할 수 있습니다.

* **부분 결과 이벤트**: 이 이벤트는 말하기를 끝내거나(`MicrophoneRecognitionClient`를 사용하는 경우) 데이터 보내기를 끝내기(`DataRecognitionClient`를 사용하는 경우) 전에도 Speech Service에서 사용자가 말할 수 있는 내용을 예측할 때마다 호출됩니다.
* **오류 이벤트**: 서비스에서 오류를 감지하면 호출됩니다.
* **의도 이벤트**: 최종 인식 결과가 구조화된 JSON 의도로 구문 분석된 후 "WithIntent" 클라이언트(`ShortPhrase` 모드의 경우만)에서 호출됩니다.
* **결과 이벤트**:
  * `ShortPhrase` 모드에서는 이 이벤트가 호출되고, 말하기를 끝낸 후 N 상위 결과를 반환합니다.
  * `LongDictation` 모드에서는 서비스에서 문장 일시 중지를 식별하는 위치에 따라 이벤트 처리기가 여러 번 호출됩니다.
  * **N 상위 선택 항목 각각에 대해** 신뢰도 값 및 인식된 텍스트의 몇 가지 다른 형식이 반환됩니다. 자세한 내용은 [출력 형식](../Concepts.md#output-format)을 참조하세요.

## <a name="related-topics"></a>관련된 항목

* [Android용 클라이언트 라이브러리 참조](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [.NET의 Windows용 C#에서 Microsoft Speech API 시작](GetStartedCSharpDesktop.md)
* [iOS의 Objective-C에서 Microsoft Speech API 시작](Get-Started-ObjectiveC-iOS.md)
* [JavaScript에서 Microsoft Speech API 시작](GetStartedJSWebsockets.md)
* [REST를 통해 Microsoft Speech API 시작](GetStartedREST.md)
