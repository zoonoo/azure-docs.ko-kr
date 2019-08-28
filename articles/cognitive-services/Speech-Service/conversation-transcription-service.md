---
title: 대화 기록-음성 서비스
titleSuffix: Azure Cognitive Services
description: 대화 기록은 실시간 음성 인식, 스피커 식별 및 diarization을 결합 하는 음성 서비스의 고급 기능입니다. 대화에 대 한 정보는 발표자를 구분할 수 있는 찍으면 되므로 간편의 사람에 게 적합 하며, 참가자가 회의에 집중할 수 있도록 하 고 다음 단계를 빠르게 진행할 수 있는 사람을 알 수 있습니다. 이 기능을 통해 접근성도 향상 됩니다. 기록을 사용 하면 청각 장애인에 적극적으로 참가자를 참여 시킬 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562885"
---
# <a name="what-is-conversation-transcription"></a>대화 내용 이란?

대화 기록은 실시간 음성 인식, 스피커 식별 및 diarization을 결합 하는 음성 서비스의 고급 기능입니다. 대화에 대 한 정보는 발표자를 구분할 수 있는 찍으면 되므로 간편의 사람에 게 적합 하며, 참가자가 회의에 집중할 수 있도록 하 고 다음 단계를 빠르게 진행할 수 있는 사람을 알 수 있습니다. 이 기능을 통해 접근성도 향상 됩니다. 기록을 사용 하면 청각 장애인에 적극적으로 참가자를 참여 시킬 수 있습니다.   

대화 기록을 통해 업계 및 회사 관련 어휘를 이해 하도록 조정할 수 있는 사용자 지정 가능한 음성 모델을 사용 하 여 정확한 인식을 제공 합니다. 또한 대화 기록을 음성 장치 SDK와 쌍으로 연결 하 여 다중 마이크 장치에 대 한 환경을 최적화할 수 있습니다.

>[!NOTE]
> 현재 소규모 모임에는 대화 기록을 권장 합니다. 대규모 모임의 대화 기록을 대규모로 확장 하려면 microsoft에 문의 하세요.

이 다이어그램에서는 대화 내용과 함께 작동 하는 하드웨어, 소프트웨어 및 서비스를 보여 줍니다.

![대화 내용 가져오기 다이어그램](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 특정 geometry 구성이 포함 된 원형 일곱 마이크 배열이 필요 합니다. 사양 및 디자인 세부 정보는 [Microsoft 음성 장치 SDK 마이크](https://aka.ms/cts/microphone)를 참조 하세요. 개발 키트에 대해 자세히 알아보거나 구매 하려면 [Microsoft 음성 장치 SDK 다운로드](https://aka.ms/cts/getsdk)를 참조 하세요.

## <a name="get-started-with-conversation-transcription"></a>대화 기록을 시작 합니다.

대화 기록을 시작 하기 위해 수행 해야 하는 세 가지 단계가 있습니다.

1. 사용자의 음성 샘플을 수집 합니다.
2. 사용자 음성 샘플을 사용 하 여 사용자 프로필 생성
3. Speech SDK를 사용 하 여 사용자 (스피커) 및 높여줄 음성 식별

## <a name="collect-user-voice-samples"></a>사용자 음성 샘플 수집

첫 번째 단계는 각 사용자의 오디오 녹음을 수집 하는 것입니다. 사용자 음성이 배경 소음 없이 조용한 환경으로 기록 되어야 합니다. 각 오디오 샘플에 대해 권장되는 길이는 30초에서 2분 사이입니다. 오디오 샘플이 길수록 스피커를 식별할 때 정확도가 향상 됩니다. 오디오는 16Khz 샘플 속도로 mono 채널 이어야 합니다.

위의 지침을 벗어나서 오디오를 기록 하 고 저장 하는 방법은 사용자가 결정 합니다. 즉, 보안 데이터베이스를 권장 합니다. 다음 섹션에서는이 오디오를 사용 하 여 음성 SDK에서 스피커를 인식 하는 데 사용 되는 사용자 프로필을 생성 하는 방법을 검토 합니다.

## <a name="generate-user-profiles"></a>사용자 프로필 생성

다음으로, 수집 된 오디오 녹음을 서명 생성 서비스로 보내 오디오의 유효성을 검사 하 고 사용자 프로필을 생성 해야 합니다. [서명 생성 서비스](https://aka.ms/cts/signaturegenservice) 는 사용자 프로필을 생성 하 고 검색할 수 있도록 하는 REST api 집합입니다.

사용자 프로필을 만들려면 `GenerateVoiceSignature` API를 사용 해야 합니다. 사양 세부 정보 및 샘플 코드를 사용할 수 있습니다.

> [!NOTE]
> 대화 기록을 현재 "en-us" 및 "zh-cn"에서 사용할 수 있습니다. `centralus` `eastasia`

* [REST 사양](https://aka.ms/cts/signaturegenservice)
* [대화 기록을 사용 하는 방법](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>높여줄 및 스피커 식별

대화 기록은 채널 오디오 스트림과 사용자 프로필을 입력으로 예상 하 고,이를 사용 하 여 스피커를 식별 합니다. 음성 장치 SDK를 사용 하 여 오디오 및 사용자 프로필 데이터를 대화 기록 서비스로 보냅니다. 앞에서 설명한 것 처럼, 대화 기록을 사용 하려면 원형 일곱 개의 마이크 배열과 음성 장치 SDK가 필요 합니다.

>[!NOTE]
> 사양 및 디자인 세부 정보는 [Microsoft 음성 장치 SDK 마이크](https://aka.ms/cts/microphone)를 참조 하세요. 개발 키트에 대해 자세히 알아보거나 구매 하려면 [Microsoft 음성 장치 SDK 다운로드](https://aka.ms/cts/getsdk)를 참조 하세요.

음성 장치 SDK에서 대화 기록을 사용 하는 방법에 대 한 자세한 내용은 [대화 기록을 사용 하는 방법](https://aka.ms/cts/howto)을 참조 하세요.


## <a name="quick-start-with-a-sample-app"></a>샘플 앱을 사용 하 여 빠른 시작

Microsoft 음성 장치 SDK에는 모든 장치 관련 샘플에 대 한 빠른 시작 샘플 앱이 있습니다. 대화의 기록 중 하나입니다. 참조를 위해 샘플 앱과 해당 소스 코드를 사용 하 여 [음성 장치 SDK android 빠른](https://aka.ms/sdsdk-quickstart) 시작에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 장치 SDK에 대 한 자세한 정보](speech-devices-sdk.md)
