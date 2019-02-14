---
title: Speech Service란?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services의 일부인 Speech Service는 Bing Speech(음성 인식 및 텍스트 음성 변환으로 구성됨), Custom Speech, 음성 번역 등 이전에 별도로 사용할 수 있었던 여러 가지 음성 서비스를 통합합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: d60e5f881e44f397090a3ba5e467c08f20137d72
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858835"
---
# <a name="what-is-speech-services"></a>Speech Service란?

다른 Azure 음성 서비스와 마찬가지로 Speech 서비스는 Cortana 및 Microsoft Office와 같은 제품에 사용되는 음성 기술을 기반으로 합니다.

Speech Service는 이전에 [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) 및 [Custom Voice](http://customvoice.ai/) 서비스를 통해 사용할 수 있었던 Azure 음성 기능을 통합합니다. 이제 하나의 구독으로 이러한 모든 기능에 액세스할 수 있습니다.

## <a name="main-speech-services-functions"></a>주요 Speech Service 기능

Speech Service의 주요 기능은 음성 텍스트 변환(음성 인식 또는 전사라고도 함), 텍스트 음성 변환(음성 합성) 및 음성 번역입니다.

|함수|기능|
|-|-|
|[Speech-to-text](speech-to-text.md)| <li>연속적인 실시간 음성을 텍스트로 변환합니다.<li>오디오 녹음에서 음성으로 일괄 처리로 변환할 수 있습니다. <li>중간 결과, 음성 종료 감지, 자동 텍스트 서식 및 불경한 언어 마스킹을 지원합니다. <li>[Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/)(LUIS)을 사용하여 음성에서 사용자 의도를 추출할 수 있습니다.\*|
|[텍스트 음성 변환](text-to-speech.md)| <li>**신규**: 사람의 음성과 거의 구분되지 않는 인공신경망 텍스트 음성 변환 음성을 제공합니다(영어). <li>텍스트를 자연스럽게 들리는 음성으로 변환합니다. <li>지원되는 많은 언어에 대해 여러 성별 및/또는 방언을 제공합니다. <li>일반 텍스트 입력 또는 SSML(Speech Synthesis Markup Language)을 지원합니다. |
|[음성 번역](speech-translation.md)| <li>스트리밍 오디오를 거의 실시간으로 변환합니다.<li> 녹음된 음성을 처리할 수도 있습니다.<li>결과를 텍스트 또는 합성된 음성으로 제공합니다. |


## <a name="customize-speech-features"></a>음성 사용자 지정 기능

음성 서비스의 Speech to Text 및 Text to speech 기능의 기초가 되는 모델을 교육하기 위해 자신의 데이터를 사용할 수 있습니다.

|기능|모델|목적|
|-|-|-|
|음성 텍스트 변환|[음향 모델](how-to-customize-acoustic-models.md)|자동차 또는 공장과 같은 특정 화자 및 환경을 변환하는 데 도움이 됩니다.|
||[언어 모델](how-to-customize-language-model.md)|의료 또는 IT 전문 용어와 같은 필드 특정 어휘 및 문법을 변환하는 데 도움이 됩니다.|
||[발음 모델](how-to-customize-pronunciation.md)|"I owe you."에 대한 "IOU"와 같은 약어 및 머리글자어를 전사하는 데 도움이 됩니다. |
|텍스트 음성 변환|[음성 글꼴](how-to-customize-voice-font.md)|실제 음성의 샘플에서 모델을 학습하여 앱 고유의 음성을 앱에 제공합니다.|

앱의 Speech to Text 또는 Text to Speech 기능에서 표준 모델을 사용하는 모든 위치에서 사용자 지정 모델을 사용할 수 있습니다.

## <a name="use-the-speech-service"></a>Speech 서비스 사용

음성 지원 애플리케이션의 개발을 간소화하기 위해 Microsoft는 Speech 서비스에서 사용할 [Speech SDK](speech-sdk.md)를 제공합니다. Speech SDK는 C#, C++ 및 Java에 일관된 네이티브 Speech to Text 및 Speech Translation API를 제공합니다. 이러한 언어 중 하나를 사용하여 개발하는 경우 Speech SDK를 통해 네트워크 세부 정보를 처리하여 더 쉽게 개발할 수 있습니다.

또한 Speech Service에는 HTTP 요청을 수행할 수 있는 모든 프로그래밍 언어에서 작동하는 [REST API](rest-apis.md)도 있습니다. REST 인터페이스는 SDK의 실시간 스트리밍 기능을 제공하지 않습니다.

|<br>방법|Speech<br>to Text|Text to<br>Speech|Speech<br>Translation|<br>설명|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|예|no|예|개발 작업을 간소화할 수 있는 C#, C++ 및 Java용 네이티브 API입니다.|
|[REST API](rest-apis.md)|예|예|no|응용 프로그램에 음성을 쉽게 추가할 수 있게 해주는 간단한 HTTP 기반 API입니다.|

### <a name="websockets"></a>WebSockets

Speech Service는 음성 텍스트 변환 및 음성 번역을 스트리밍하기 위한 WebSocket 프로토콜도 지원합니다. Speech SDK는 이러한 프로토콜을 사용하여 Speech 서비스와 통신합니다. Speech 서비스와의 사용자 고유 WebSocket 통신을 구현하는 대신 Speech SDK를 사용합니다.

WebSocket을 통해 Bing Speech 또는 Translator Speech를 사용하는 코드가 이미 있는 경우 해당 코드에서 Speech 서비스를 사용하도록 업데이트할 수 있습니다. WebSocket 프로토콜은 호환 가능하지만, 엔드포인트는 다릅니다.

### <a name="speech-devices-sdk"></a>Speech Devices SDK

[Speech Devices SDK](speech-devices-sdk.md)는 음성 지원 디바이스 개발자를 위한 통합 하드웨어 및 소프트웨어 플랫폼입니다. 하드웨어 파트너는 참조 디자인 및 개발 단위를 제공합니다. Microsoft는 하드웨어 기능을 최대한 활용하는 디바이스에 최적화된 SDK를 제공합니다.


## <a name="speech-scenarios"></a>음성 시나리오

Speech Service의 사용 사례는 다음과 같습니다.

> [!div class="checklist"]
> * 음성 트리거 앱 만들기
> * 호출 센터 녹음 기록
> * 음성 봇 구현

### <a name="voice-user-interface"></a>음성 사용자 인터페이스

음성 입력은 앱을 유연하고 핸즈프리가 가능하며 빠르게 사용할 수 있도록 해주는 좋은 방법입니다. 음성 지원 앱에서 사용자는 원하는 정보를 요청할 수 있습니다.

일반 대중이 사용하는 앱이면 기본 음성 인식 모델을 사용할 수 있습니다. 이러한 모델은 일반적인 환경에서 다양한 화자를 인식합니다.

특정 도메인에서 앱이 사용되는 경우(의약품 또는 IT) [언어 모델](how-to-customize-language-model.md)을 만들 수 있습니다. 이 모델을 사용하여 Speech 서비스에 앱에서 사용하는 특별한 용어를 가르칠 수 있습니다.

공장과 같은 시끄러운 환경에서 앱이 사용되는 경우 사용자 지정 [어쿠스틱 모델](how-to-customize-acoustic-models.md)을 만들 수 있습니다. 이 모델은 Speech 서비스가 말과 소음을 구분하는 데 도움이 됩니다.

### <a name="call-center-transcription"></a>호출 센터 전사

호출 센터 녹음은 주로 호출에 문제가 발생했을 때만 참조됩니다. Speech Service를 사용하면 모든 녹음 내용을 텍스트로 쉽게 기록할 수 있습니다. [전체 텍스트 검색](https://docs.microsoft.com/azure/search/search-what-is-azure-search)을 위해 텍스트를 쉽게 인덱싱하거나 [텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/)을 적용하여 감정, 언어 및 핵심 구문을 감지할 수 있습니다.

콜 센터 녹음이 전문 용어(예: 제품 이름 또는 IT 전문 용어)를 포함하는 경우 [언어 모델](how-to-customize-language-model.md)을 만들어 해당 어휘를 Speech Service에 학습시킬 수 있습니다. 사용자 지정 [어쿠스틱 모델](how-to-customize-acoustic-models.md)은 Speech Service에서 최적 상태가 아닌 전화 연결을 이해하도록 지원할 수 있습니다.

이 시나리오에 대한 자세한 내용은 Speech Service 지원 [일괄 처리 전사](batch-transcription.md)를 참조하세요.

### <a name="voice-bots"></a>음성 봇

[봇](https://dev.botframework.com/)은 사용자가 원하는 정보와 원하는 비즈니스가 있는 고객을 연갈하는 보편적인 방법입니다. 대화형 사용자 인터페이스를 웹 사이트나 앱에 추가하면 기능에 더 쉽고, 더 빠르게 액세스할 수 있습니다. 이 대화는 Speech 서비스를 이용하여 음성 쿼리에 동일하게 응답함으로써 새로운 차원의 유창함을 갖게 됩니다.

음성 지원 봇에 독특한 개성을 추가하기 위해 고유한 음성을 부여할 수 있습니다. 사용자 지정 음성 만들기는 2단계 프로세스입니다. 먼저 사용하려는 음성의 [녹음을 만듭니다](record-custom-voice-samples.md). 그런 다음, Speech 서비스의 [음성 사용자 지정 포털](https://cris.ai/Home/CustomVoice)에 [녹음 내용을 제출](how-to-customize-voice-font.md)(텍스트 기록과 함께)하면 포털에서 나머지 작업을 수행합니다. 사용자 지정 음성을 만든 후에 앱에서 사용하는 단계는 간단합니다.

## <a name="next-steps"></a>다음 단계

Speech Service에 대한 구독 키를 가져옵니다.

> [!div class="nextstepaction"]
> [Speech Service 체험해 보기](get-started.md)
