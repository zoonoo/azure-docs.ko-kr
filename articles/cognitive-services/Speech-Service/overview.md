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
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445637"
---
# <a name="what-is-the-speech-service-preview"></a>Speech Service(미리 보기)란?

Speech Service는 Cortana 및 Microsoft Office를 비롯한 다른 Microsoft 제품에 사용되는 기술을 기반으로 합니다.  이 동일한 서비스는 Cognitive Service로 모든 고객에게 사용할 수 있습니다. 

> [!NOTE]
> Speech Service는 현재 공개 미리 보기로 제공됩니다. 주기적으로 여기로 돌아와 문서 업데이트, 추가 코드 샘플 등을 확인하세요.

하나의 구독을 통해 Speech Service는 응용 프로그램에 강력한 음성 지원 기능을 제공하는 손쉬운 방법을 개발자에게 제공합니다. 이제 앱에서 음성 명령, 전사, 받아쓰기, 음성 합성 및 번역을 제공할 수 있습니다.

## <a name="speech-service-features"></a>음성 서비스 기능

|함수|설명|
|-|-|
|[Speech-to-text](speech-to-text.md)| 오디오 스트림을 응용 프로그램에서 입력으로 받아 들일 수 있는 텍스트로 전사합니다. LUIS([Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/))와 통합하여 발화에서 사용자 의도를 유추합니다.|
|[Text-to-speech](text-to-speech.md)| 일반 텍스트를 오디오 파일 형태로 응용 프로그램에 전달되는 자연스러운 음성으로 변환합니다. 여러 지원되는 언어에 대해 성별과 억양이 다른 다양한 음성을 사용할 수 있습니다. |
|[Speech-translation](speech-translation.md)| 스트리밍 오디오를 거의 실시간으로 번역하거나 녹음된 음성을 처리하는 데 사용할 수 있습니다. |
|[Speech Devices SDK](speech-devices-sdk.md)| Microsoft와 Microsoft 파트너는 통합형 Speech Service를 도입하여 음성 지원 장치 개발용으로 최적화된 통합 하드웨어/소프트웨어 플랫폼을 제공합니다. |

## <a name="using-the-speech-service"></a>Speech Service 사용

Speech Service는 두 가지 방법으로 사용할 수 있습니다. [SDK](speech-sdk.md)는 네트워크 프로토콜의 세부 정보를 추상화합니다. [REST API](rest-apis.md)는 프로그래밍 언어를 사용하지만 SDK에서 제공하는 일부 기능을 제공하지 않습니다.

|<br>방법|음성<br>to Text|Text to<br>음성|음성<br>Translation|<br>설명|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|yes|아니요|yes|개발을 단순화하는 특정 프로그래밍 언어의 라이브러리입니다.|
|[REST](rest-apis.md)|yes|yes|아니요|응용 프로그램에 음성을 쉽게 추가할 수 있는 단순한 HTTP 기반 API입니다.|

## <a name="next-steps"></a>다음 단계

Speech Service에 대한 평가판 구독 키를 가져옵니다.

> [!div class="nextstepaction"]
> [Speech Service 체험해 보기](get-started.md)
