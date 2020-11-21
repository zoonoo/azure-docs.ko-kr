---
title: 대화 내용 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 대화에 대 한 내용은 인식, 발표자 ID 및 diarization을 결합 하 여 대화의 기록을 제공 하는 미팅을 위한 솔루션입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: c27493748381a3aced92aab41745f352f228c50e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024996"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>모임의 대화 내용 (미리 보기)은 무엇입니까?

대화는 음성 인식, 스피커 식별 및 문장 특성을 각 스피커 ( _diarization_ 라고도 함)와 결합 하 여 대화에 대 한 실시간 및/또는 비동기 기록을 제공 하는 [음성-텍스트](speech-to-text.md) 솔루션입니다. 대화 기록을 통해 대화에서 스피커를 구분 하 여 및 시기를 확인 하 고 개발자가 다중 스피커 diarization을 수행 하는 응용 프로그램에 음성 텍스트를 쉽게 추가할 수 있습니다.

## <a name="key-features"></a>주요 특징

- **타임 스탬프** -각 스피커 utterance에는 타임 스탬프가 있으므로 구가 표시 되는 시기를 쉽게 찾을 수 있습니다.
- **읽을 수 있는 성적 증명서** -텍스트의 일치 항목이 무엇 인지를 확인 하기 위해 서식 지정 및 문장 부호가 자동으로 추가 됩니다.
- **사용자 프로필** -사용자 음성 샘플을 수집 하 고 서명 생성으로 전송 하 여 사용자 프로필을 생성 합니다.
- **스피커 식별** -스피커가 사용자 프로필을 사용 하 여 식별 되 고 각각에 게 _스피커 식별자_ 가 할당 됩니다.
- **멀티 스피커 diarization** -각 스피커 식별자로 오디오 스트림을 synthesizing 하는 사람을 결정 합니다.
- **실시간 기록** – 대화가 진행 되는 동안 및 시기를 말하는 사람에 대 한 실시간 기록을 제공 합니다.
- **비동기** 기록 – 다중 채널 오디오 스트림을 사용 하 여 더 높은 정확도로 성적 증명서를 제공 합니다.

> [!NOTE]
> 대화를 통해 회의실의 스피커 수에 제한이 없지만 세션당 2-10 스피커에 대해 최적화 됩니다.

## <a name="get-started"></a>시작하기

시작 하려면 실시간 대화 기록 [빠른](how-to-use-conversation-transcription.md) 시작을 참조 하세요.

## <a name="use-cases"></a>사용 사례

청각 장애인을 위한 참가자와 같이 모든 사용자를 위한 모임이 포함 되도록 하려면 실시간으로 기록을 유지 하는 것이 중요 합니다. 실시간 모드의 대화 기록에서는 오디오를 사용 하 고 무엇 인지를 알려 주는 사람을 결정 하 여 모든 모임 참가자가 성적을 따르고 모임에 참여할 수 있도록 합니다.

### <a name="improved-efficiency"></a>효율성 향상

모임 참가자는 회의에 집중할 수 있으며, 메모를 대화로 가져갈 수 있습니다. 참가자가 회의에 적극적으로 참여할 수 있으며, 회의 중에 메모를 작성 하는 대신 기록을 사용 하 여 다음 단계를 빠르게 진행할 수 있습니다.

## <a name="how-it-works"></a>작동 방식

이는 대화 기록을 작동 하는 방법에 대 한 개략적인 개요입니다.

![대화 내용 가져오기 다이어그램](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>예상 입력

- **멀티 채널 오디오 스트림** -사양 및 디자인 세부 정보는 [MICROSOFT 음성 장치 SDK 마이크](./speech-devices-sdk-microphone.md)를 참조 하세요. 개발 키트에 대해 자세히 알아보거나 구매 하려면 [Microsoft 음성 장치 SDK 다운로드](./get-speech-devices-sdk.md)를 참조 하세요.
- **사용자 음성 샘플** – 대화에 대 한 대화에 앞서 사용자 프로필이 필요 합니다. 각 사용자는 오디오 녹음을 수집한 다음 [서명 생성 서비스로](https://aka.ms/cts/signaturegenservice) 전송 하 여 오디오의 유효성을 검사 하 고 사용자 프로필을 생성 해야 합니다.

> [!NOTE]
> 사용자 음성 샘플은 선택 사항입니다. 이 입력을 사용 하지 않으면 다른 스피커가 표시 되지만 "Speaker1", "Speaker2" 등으로 표시 되는 미리 등록 된 특정 발표자 이름으로 인식 됩니다.


## <a name="real-time-vs-asynchronous"></a>실시간 및 비동기

대화 기록을 통해 세 가지 기록 모드를 제공 합니다.

### <a name="real-time"></a>실시간

오디오 데이터는 라이브 처리 되어 스피커 식별자 + 성적 증명서를 반환 합니다. 기록 솔루션 요구 사항으로 인해 대화 참가자에 게 진행 중인 대화에 대 한 라이브 성적 증명서 보기가 제공 되는 경우이 모드를 선택 합니다. 예를 들어, 회의에 더 쉽게 액세스할 수 있도록 응용 프로그램을 빌드하는 것은 실시간 기록을 위한 좋은 사용 사례입니다.

### <a name="asynchronous"></a>비동기

오디오 데이터는 스피커 식별자 및 성적 증명서를 반환 하기 위해 일괄 처리 됩니다. 기록 솔루션 요구 사항이 라이브 기록 보기 없이 더 높은 정확도를 제공 해야 하는 경우이 모드를 선택 합니다. 예를 들어 모임 참가자가 누락 된 모임을 쉽게 파악할 수 있도록 응용 프로그램을 빌드하려면 비동기 기록 모드를 사용 하 여 정확도가 높은 기록 결과를 얻을 수 있습니다.

### <a name="real-time-plus-asynchronous"></a>실시간 및 비동기

오디오 데이터는 라이브 처리 되어 스피커 식별자 + 성적 증명서를 반환 하며, 또한 비동기 처리를 통해 정확도가 높은 성적 증명서를 가져오도록 요청을 만듭니다. 응용 프로그램에 실시간 기록이 필요 하지만 대화 또는 모임이 발생 한 후에 사용 하기 위해 더 높은 정확도 기록이 필요 하면이 모드를 선택 합니다.

## <a name="language-support"></a>언어 지원

현재 대화 기록을 통해,,, 지역의 [모든 음성 텍스트 언어](language-support.md#speech-to-text) 를 지원 합니다.  `centralus` `eastasia` `eastus` `westeurope` 추가 로캘이 지원 되어야 하는 경우에는 [대화 기록 기능 Crew](mailto:CTSFeatureCrew@microsoft.com)에 문의 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [실시간으로 대화 높여줄](how-to-use-conversation-transcription.md)