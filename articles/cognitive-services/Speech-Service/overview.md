---
title: Speech Service(미리 보기)란?
description: Microsoft Cognitive Services의 일부인 Speech Service는 이전에 개별적으로 사용할 수 있었던 Bing Speech(음성 인식 및 텍스트 음성 변환으로 구성), Custom Speech 및 Speech Translation과 같은 여러 가지 Azure 음성 서비스를 통합합니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42093834"
---
# <a name="what-is-the-speech-service-preview"></a>Speech Service(미리 보기)란?

하나의 구독을 통해 Speech Service는 응용 프로그램에 강력한 음성 지원 기능을 추가하는 손쉬운 방법을 개발자에게 제공합니다. 이제 앱에서 음성 명령, 전사, 받아쓰기, 음성 합성 및 음성 번역을 제공할 수 있습니다.

Speech Service는 Cortana 및 Microsoft Office를 비롯한 다른 Microsoft 제품에 사용되는 기술을 기반으로 합니다.

> [!NOTE]
> Speech Service는 현재 공개 미리 보기로 제공됩니다. 주기적으로 여기로 돌아와 문서 업데이트, 추가 코드 샘플 등을 확인하세요.

## <a name="speech-service-features"></a>음성 서비스 기능

|함수|설명|
|-|-|
|[Speech-to-text](speech-to-text.md)| 오디오 스트림을 응용 프로그램에서 입력으로 받아 들일 수 있는 텍스트로 전사합니다. LUIS([Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/))와 통합하여 발화에서 사용자 의도를 유추합니다.|
|[Text-to-speech](text-to-speech.md)| 일반 텍스트를 오디오 파일 형태로 응용 프로그램에 전달되는 자연스러운 음성으로 변환합니다. 여러 지원되는 언어에 대해 성별과 억양이 다른 다양한 음성을 사용할 수 있습니다. |
|[Speech-translation](speech-translation.md)| 스트리밍 오디오를 거의 실시간으로 번역하거나 녹음된 음성을 처리하는 데 사용할 수 있습니다. |
|음성 텍스트 변환 사용자 지정|사용자 고유의 [음향](how-to-customize-acoustic-models.md) 및 [언어](how-to-customize-language-model.md) 모델을 만들고 사용자 지정 [발음](how-to-customize-pronunciation.md) 규칙을 지정하여 음성 텍스트 변환을 사용자 지정할 수 있습니다. |
|[텍스트 음성 변환 사용자 지정](how-to-customize-voice-font.md)|텍스트 음성 변환을 위한 사용자 고유의 음성을 만들 수 있습니다.|
|[Speech Devices SDK](speech-devices-sdk.md)| Microsoft와 Microsoft 파트너는 통합형 Speech Service를 도입하여 음성 지원 장치 개발용으로 최적화된 통합 하드웨어/소프트웨어 플랫폼을 제공합니다. |

## <a name="access-to-the-speech-service"></a>Speech Service에 액세스

Speech Service는 두 가지 방법으로 사용할 수 있습니다. [SDK](speech-sdk.md)는 지원되는 플랫폼에서보다 쉽게 개발할 수 있도록 네트워크 프로토콜의 세부 정보를 추상화합니다. [REST API](rest-apis.md)는 프로그래밍 언어를 사용하지만 SDK에서 제공하는 일부 기능을 제공하지 않습니다.

|<br>방법|음성<br>to Text|Text to<br>음성|음성<br>Translation|<br>설명|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|yes|아니요|yes|개발을 단순화하는 Websocket 기반 프로토콜을 활용하는 특정 프로그래밍 언어의 라이브러리입니다.|
|[REST](rest-apis.md)|yes|yes|아니요|응용 프로그램에 음성을 쉽게 추가할 수 있는 단순한 HTTP 기반 API입니다.|

## <a name="speech-scenarios"></a>음성 시나리오

아래에서는 음성 기술의 몇 가지 일반적인 용도에 대해 간략히 설명합니다. [Speech SDK](speech-sdk.md)는 이러한 대부분의 시나리오에 꼭 필요합니다.

> [!div class="checklist"]
> * 음성 트리거 앱 만들기
> * 호출 센터 녹음 기록
> * 음성 봇 구현

### <a name="voice-triggered-apps"></a>음성 트리거 앱

음성 입력은 앱을 유연하고 핸즈프리가 가능하며 빠르게 사용할 수 있도록 해주는 좋은 방법입니다. 음성 지원 앱에서 사용자는 클릭하거나 탭하여 탐색할 필요 없이 원하는 정보를 음성으로 물어보기만 하면 됩니다.

앱이 일반 공개로 사용하도록 제작되었다면 Speech Service가 제공하는 기본 음성 인식 모델을 사용할 수 있습니다. 일반적인 환경에서 다양한 스피커를 인식하는 것이 좋습니다.

앱이 특정 분야(예: 의학 또는 IT)에서 사용될 예정인 경우 Speech Service에 해당 앱에 사용된 특수 용어를 학습시키기 위해 [언어 모델](how-to-customize-language-model.md)을 만들 수 있습니다.

앱이 공장과 같이 시끄러운 환경에서 사용될 예정인 경우 Speech Service가 음성과 소음을 더욱 잘 구분할 수 있도록 사용자 지정 [음향 모델](how-to-customize-acoustic-models.md)을 만들 수 있습니다.

[음성 SDK](speech-sdk.md)를 다운로드하고 관련 [빠른 시작](quickstart-csharp-dotnet-windows.md) 문서를 따르는 것만으로 쉽게 시작할 수 있습니다.

### <a name="transcribe-call-center-recordings"></a>호출 센터 녹음 기록

호출 센터 녹음은 주로 호출에 문제가 발생했을 때만 참조됩니다. Speech Service를 사용하면 모든 녹음 내용을 텍스트로 쉽게 기록할 수 있습니다. 텍스트로 되어 있으면 [전체 텍스트 검색](https://docs.microsoft.com/azure/search/search-what-is-azure-search)을 위해 쉽게 인덱스를 만들거나 [텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/)을 적용하여 감정, 언어 및 핵심 구문을 검색할 수 있습니다.

호출 센터 녹음 내용에 전문 용어(예: 제품 이름 또는 IT 전문 용어)가 자주 포함되는 경우 Speech Service에 어휘를 학습시키기 위해 [언어 모델](how-to-customize-language-model.md)을 만들 수 있습니다. 사용자 지정 [음향 모델](how-to-customize-acoustic-models.md)은 Speech Service가 상태가 최적화되지 않은 전화 연결을 이해하는 데 도움이 됩니다.

이 시나리오에 대한 자세한 내용은 Speech Service 지원 [일괄 처리 전사](batch-transcription.md)를 참조하세요.

### <a name="voice-bots"></a>음성 봇

[봇](https://dev.botframework.com/)은 사용자를 그들이 원하는 정보와 연결하고 고객을 그들이 좋아하는 비즈니스에 연결하는 더욱 보편화되고 있는 방법입니다. 대화형 사용자 인터페이스를 웹 사이트나 앱에 추가하면 기능을 더 쉽게 찾고 더 빨리 액세스할 수 있습니다. Speech Service의 경우, 이 대화는 음성 질의에 대해 합성된 음성으로 실제로 응답함으로써 새로운 차원의 유창함을 나타냅니다.

음성 지원 봇에 독특한 개성을 추가하고 브랜드를 강화하기 위해 고유한 음성을 부여할 수 있습니다. 사용자 지정 음성 만들기는 2단계 프로세스입니다. 먼저, 사용하려는 음성의 [녹음을 만듭니다](record-custom-voice-samples.md). 그런 다음, Speech Service의 [음성 사용자 지정 포털](https://cris.ai/Home/CustomVoice)에 [녹음 내용을 제출](how-to-customize-voice-font.md)(텍스트 기록과 함께)하면 포털에서 나머지 작업을 수행합니다. 사용자 지정 음성을 만든 후에는 앱에서 간단하게 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Speech Service에 대한 구독 키를 가져옵니다.

> [!div class="nextstepaction"]
> [Speech Service 체험해 보기](get-started.md)
