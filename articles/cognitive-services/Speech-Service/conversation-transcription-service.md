---
title: 대화 기록-음성 서비스
titleSuffix: Azure Cognitive Services
description: 대화 기록에는 실시간 음성 인식, 화자 식별 및 diarization 결합 하는 음성 서비스의 고급 기능입니다. 대화 기록 현지의 회의 발표자를 구분 하는 기능을 사용 하 여 복사에 대 한 완벽 한, 회의 빠르게 집중할 수 있도록 참가자 다음 단계에 따라 무엇을 그리고 언제를 언급 하는 알 수 있습니다. 이 기능에는 내게 필요한 옵션 향상 됩니다. 기록, 사용 하 여 적극적으로 청각 장애가 있는 참가자를 참여할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: a31921e229a4bbfccd6fdd871666aad1eeef3232
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243857"
---
# <a name="what-is-conversation-transcription"></a>대화 기록 란?

대화 기록에는 실시간 음성 인식, 화자 식별 및 diarization 결합 하는 음성 서비스의 고급 기능입니다. 대화 기록 현지의 회의 발표자를 구분 하는 기능을 사용 하 여 복사에 대 한 완벽 한, 회의 빠르게 집중할 수 있도록 참가자 다음 단계에 따라 무엇을 그리고 언제를 언급 하는 알 수 있습니다. 이 기능에는 내게 필요한 옵션 향상 됩니다. 기록, 사용 하 여 적극적으로 청각 장애가 있는 참가자를 참여할 수 있습니다.   

대화 기록 산업 및 회사 관련 어휘를 이해 하려면 조정할 수 있는 사용자 지정 가능한 음성 모델을 사용 하 여 정확 하 게 인식 기능을 제공 합니다. 또한 다중 마이크 장치에 대 한 환경을 최적화 하는 음성 장치 SDK를 사용 하 여 대화 기록 페어링할 수 있습니다.

>[!NOTE]
> 현재 대화 기록 작은 모임에 권장 됩니다. 확장 규모의 대규모 회의 대 한 대화 기록 하려는 경우 문의해 주세요.

이 다이어그램은 하드웨어, 소프트웨어 및 대화 기록 함께 작동 하는 서비스를 보여 줍니다.

![가져오기 대화 기록 다이어그램](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 특정 기 하 도형 구성 하는 순환 7 마이크 배열 반드시 입력 해야 합니다. 사양 및 디자인 세부 정보를 참조 하세요 [Microsoft 음성 장치 SDK 마이크](https://aka.ms/cts/microphone)합니다. 자세히 알아보거나 개발 키트 구매를 참조 하세요 [Microsoft 음성 장치 SDK 가져오기](https://aka.ms/cts/getsdk)합니다.

## <a name="get-started-with-conversation-transcription"></a>대화 기록 시작

대화 기록 시작 하는 데 필요한 세 가지 단계가 있습니다.

1. 사용자의 음성 샘플을 수집 합니다.
2. 사용자 음성 샘플을 사용 하 여 사용자 프로필 생성
3. Speech SDK를 사용 하 여 (스피커) 사용자를 식별 및 음성 기록

## <a name="collect-user-voice-samples"></a>사용자 음성 샘플 수집

첫 번째 단계는 각 사용자의 오디오 녹음/녹화를 수집 하는 것입니다. 사용자 음성 배경 노이즈 없이 훈련 기록 되어야 합니다. 각 오디오 샘플에 대 한 권장 되는 길이 30 초에서 2 분 사이입니다. 긴 오디오 샘플 정확성 향상된 화자를 식별 하는 경우 발생 합니다. 오디오 16 KHz 샘플링 주기를 사용 하 여 mono 채널 이어야 합니다.

오디오 기록 되 고 있습니다-달려 저장 하는 방법에 앞서 언급 한 지침을 넘어 안전한 데이터베이스 권장 됩니다. 다음 섹션에서는이 오디오 Speech SDK를 사용 하 여 화자를 인식 하는 데 사용 되는 사용자 프로필을 생성 하는 방법을 살펴보겠습니다.

## <a name="generate-user-profiles"></a>사용자 프로필 생성

오디오 녹음/녹화를 전송 해야 하는 다음으로, 오디오를 유효성 검사 및 사용자 프로필을 생성 하려면 서명 생성 서비스에 수집 했습니다. 합니다 [서명 생성 서비스](https://aka.ms/cts/signaturegenservice) 는 생성 하 고 사용자 프로필을 검색할 수 있는 REST Api의 집합입니다.

사용자 프로필을 만들려면 사용 해야는 `GenerateVoiceSignature` API. 사양 세부 정보 및 샘플 코드에 사용할 수 있습니다.

> [!NOTE]
> "EN-US" 및에 "ZH-CN" 다음 지역에서 현재 사용할 수 있는 대화 기록 됩니다. `centralus` 및 `eastasia`합니다.

* [REST 사양](https://aka.ms/cts/signaturegenservice)
* [대화 기록을 사용 하는 방법](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>기록 및 화자를 식별

대화 기록에 기록 생성 화자를 식별 하는 입력으로 다중 채널 오디오 스트림 및 사용자 프로필은 필요 합니다. 오디오 및 사용자 프로필 데이터는 음성 장치 SDK를 사용 하 여 대화 기록 서비스에 전송 됩니다. 언급 한 대로 순환 7 마이크 배열 및 음성 장치 SDK를 사용 하려면 필요 대화 기록 합니다.

>[!NOTE]
> 사양 및 디자인 세부 정보를 참조 하세요 [Microsoft 음성 장치 SDK 마이크](https://aka.ms/cts/microphone)합니다. 자세히 알아보거나 개발 키트 구매를 참조 하세요 [Microsoft 음성 장치 SDK 가져오기](https://aka.ms/cts/getsdk)합니다.

음성 장치 SDK를 사용 하 여 대화 기록을 사용 하는 방법에 알아보려면 참조 [conversation 기록을 사용 하는 방법을](https://aka.ms/cts/howto)합니다.


## <a name="quick-start-with-a-sample-app"></a>샘플 앱을 사용한 빠른 시작

Microsoft 음성 장치 SDK에 빠른 시작 샘플 앱을 모든 장치 관련 샘플입니다. 대화 기록 하 중 하나입니다. 찾을 수 있습니다 [음성 장치 SDK android 빠른 시작](https://aka.ms/sdsdk-quickstart) 샘플 앱 및 해당 소스 코드에 대 한 참조를 사용 하 여 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 장치 SDK에 자세히 알아보기](speech-devices-sdk.md)
