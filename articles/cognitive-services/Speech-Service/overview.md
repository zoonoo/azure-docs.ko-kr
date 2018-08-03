---
title: Speech Service(미리 보기)란? | Microsoft Docs
description: Microsoft Cognitive Services의 일부인 Speech Service는 이전에 개별적으로 사용할 수 있었던 Bing Speech(음성 인식 및 텍스트 음성 변환으로 구성), Custom Speech 및 Speech Translation과 같은 여러 가지 Azure 음성 서비스를 통합합니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: adfc854fc24b9e285c405f3038a21ec84cd2f4c2
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989338"
---
# <a name="what-is-the-speech-service-preview"></a>Speech Service(미리 보기)란?

Microsoft Cognitive Services의 일부인 Speech Service는 이전에 개별적으로 사용할 수 있었던 Bing Speech(음성 인식 및 텍스트 음성 변환으로 구성), Custom Speech 및 Speech Translation과 같은 여러 가지 Azure 음성 서비스를 통합합니다. 이전 서비스와 마찬가지로 Speech Service는 Cortana 및 Microsoft Office를 비롯한 다른 Microsoft 제품에 사용되는 기술을 기반으로 합니다.

> [!NOTE]
> Speech Service는 현재 공개 미리 보기로 제공됩니다. 주기적으로 여기로 돌아와 문서 업데이트, 추가 코드 샘플 등을 확인하세요.

하나의 구독을 통해 통합된 Speech Service는 응용 프로그램에 강력한 음성 지원 기능을 제공하는 손쉬운 방법을 개발자에게 제공합니다. 이제 앱에서 음성 명령, 전사, 받아쓰기, 음성 합성 및 번역을 제공할 수 있습니다.

|함수|설명|
|-|-|
|음성을 텍스트로 변환|응용 프로그램에 입력으로 사용될 수 있는 연속적인 사람의 음성을 텍스트로 변환합니다. LUIS([Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/))와 통합하여 발화에서 사용자 의도를 유추할 수 있습니다.|
|텍스트에서 음성 변환|텍스트를 자연스러운 합성된 음성의 오디오 파일로 변환합니다.|
|Speech&nbsp;Translation|텍스트 또는 음성 출력을 사용하여 다른 언어로 음성 번역을 제공합니다.|

## <a name="using-the-speech-service"></a>Speech Service 사용

Speech Service는 두 가지 방법으로 사용할 수 있습니다. [SDK](speech-sdk.md)는 네트워크 프로토콜의 세부 정보를 추상화합니다. [REST API](rest-apis.md)는 프로그래밍 언어를 사용하지만 SDK에서 제공하는 일부 기능을 제공하지 않습니다.

|<br>방법|음성<br>to Text|Text to<br>음성|음성<br>Translation|<br>설명|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|예|아니오|예|개발을 단순화하는 특정 프로그래밍 언어의 라이브러리입니다.|
|[REST](rest-apis.md)|예|예|아니오|응용 프로그램에 음성을 쉽게 추가할 수 있는 단순한 HTTP 기반 API입니다.|

## <a name="speech-to-text"></a>Speech to Text

STT([Speech to Text](speech-to-text.md)) 또는 음성 인식 API는 오디오 스트림을 응용 프로그램이 입력으로 허용할 수 있는 텍스트로 전사합니다. 이렇게 하면 응용 프로그램에서 예를 들어, 문서에 텍스트를 입력하거나 음성을 명령으로 인식하여 작업을 수행하는 것이 가능합니다.

Speech to Text는 대화형, 대화 및 받아쓰기 시나리오에 개별적으로 최적화되었습니다. 다음은 Speech to Text API에 대한 일반적인 사용 사례입니다. 

* 중간 결과 없이 명령과 같은 간략한 음성을 인식합니다.
* 음성 사서함 메시지와 같이 이미 녹음된 긴 발화 전사
* 받아쓰기를 위해 스트리밍 음성을 실시간으로 전사(부분적인 결과 포함)
* 음성 자연어 요청을 기반으로 사용자가 원하는 것을 판단

Speech to Text API는 실시간 연속 인식 및 중간 결과를 통해 대화형 음성 전사를 지원합니다. 음성 인식 종료, 대/소문자 및 문장 부호 자동 표시(선택 사항), 불경한 언어 마스킹 및 텍스트 정규화도 지원합니다.

특수 어휘, 시끄러운 환경 및 다양한 말하기 방식을 수용하도록 Speech to Text 음향 및 언어 모델을 사용자 지정할 수 있습니다.

## <a name="text-to-speech"></a>텍스트에서 음성 변환

현재 TTS([Text to Speech](text-to-speech.md)) 또는 음성 합성 API는 일반 텍스트를 오디오 파일 형태로 응용 프로그램에 전달되는 자연스러운 음성으로 변환합니다. 여러 지원되는 언어에 대해 성별과 억양이 다른 다양한 음성을 사용할 수 있습니다.

이 API는 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md) 태그를 지원하기 때문에 까다로운 단어에 대한 음성이 정확한 발음을 지정할 수 있습니다. SSML은 텍스트에서 음성 특성(강조, 속도, 볼륨, 성별 및 어조)을 나타낼 수도 있습니다.

다음은 Text to Speech API의 일반적인 사용 사례입니다.

* 시각 장애가 있는 사용자를 위한 대체 화면 출력으로 음성 출력
* 내비게이션과 같은 차량 내 응용 프로그램을 위한 음성 프롬프트 생성
* Speech to Text API를 사용한 콘서트의 대화형 사용자 인터페이스

지원되지 않는 언어가 필요하거나 응용 프로그램에 고유한 음성을 원하는 경우, Text to Speech API는 [사용자 지정 음성 모델](how-to-customize-voice-font.md)을 지원합니다.

## <a name="speech-translation"></a>Speech Translation

[Speech Translation](speech-translation.md) API는 스트리밍 오디오를 거의 실시간으로 번역하거나 녹음된 음성을 처리하는 데 사용할 수 있습니다. 스트리밍 번역 시에 서비스는 사용자에게 번역 진행 상태를 표시할 수 있는 중간 결과를 반환합니다. 결과는 텍스트나 음성으로 반환될 수 있습니다.

Speech Translation에 대한 사용 사례는 다음과 같습니다.

* 여행자를 위한 “대화형” 번역 모바일 앱 또는 장치 구현 
* 녹음 및 녹화 자막에 대한 자동 번역 제공

## <a name="speech-devices-sdk"></a>Speech Devices SDK

Microsoft와 Microsoft 파트너는 통합형 Speech Service를 도입하여 음성 지원 장치 개발용으로 최적화된 통합 하드웨어/소프트웨어 플랫폼인 [Speech Devices SDK](speech-devices-sdk.md)를 제공합니다. 이 SDK는 모든 유형의 응용 프로그램용 스마트 음성 장치를 개발하는 데 적합합니다.

Speech Devices SDK를 사용하면 사용자 지정 절전 모드 해제 단어가 포함된 고유한 앰비언트 장치를 빌드할 수 있으므로 오디오 캡처를 트리거하는 큐가 브랜드에 고유합니다. 또한 보다 정확한 음성 인식을 위해 다중 채널 원본을 통한 잡음 제거, 원거리 음성(far-field voice) 및 빔 형성(beamforming)을 비롯한 탁월한 오디오 처리 기술을 제공합니다.

SDK는 포트 443을 사용하는 웹 소켓을 기준으로 합니다.

## <a name="next-steps"></a>다음 단계

Speech Service에 대한 평가판 구독 키를 가져옵니다.

> [!div class="nextstepaction"]
> [Speech Service 체험해 보기](get-started.md)
