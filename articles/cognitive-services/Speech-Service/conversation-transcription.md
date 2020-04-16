---
title: 대화 녹음 (미리보기) - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 대화 녹음은 인식, 스피커 ID 및 디아리화를 결합하여 모든 대화의 전사를 제공하는 회의를 위한 솔루션입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402506"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>회의에서 대화 녹음은 무엇입니까 (미리보기)?

대화 기록은 음성 인식, 스피커 식별 및 각 스피커에 대한 문장 _귀속(diarization라고도_함)을 결합하여 모든 대화의 실시간 및/또는 비동기 전사를 제공하는 [음성-텍스트](speech-to-text.md) 솔루션입니다. 대화 전사는 대화에서 스피커를 구분하여 누가 언제 무엇을 말했는지 를 결정하고 개발자가 다중 스피커 다이어리화를 수행하는 애플리케이션에 음성-텍스트를 쉽게 추가할 수 있도록 합니다.

## <a name="key-features"></a>주요 기능

- **타임스탬프** - 각 스피커 발언에는 타임스탬프가 있으므로 문구가 말되면 쉽게 찾을 수 있습니다.
- **읽을 수 있는 성적 증명서** - 자막에는 서식과 문장 부호가 자동으로 추가되어 텍스트가 말한 내용과 밀접하게 일치하도록 합니다.
- **사용자 프로필** - 사용자 프로필은 사용자 음성 샘플을 수집하고 서명 생성으로 전송하여 생성됩니다.
- **스피커 식별** - 스피커는 사용자 프로필을 사용하여 식별되고 _스피커 식별자는_ 각각에 할당됩니다.
- **멀티 스피커 디아리화** - 각 스피커 식별자로 오디오 스트림을 합성하여 누가 말한지 결정합니다.
- **실시간 전사** – 대화가 진행되는 동안 누가 언제 무엇을 말하고 있는지에 대한 실시간 녹취록을 제공합니다.
- **비동기 전사** – 멀티채널 오디오 스트림을 사용하여 더 높은 정확도로 성적증명서를 제공합니다.

> [!NOTE]
> 대화 녹음은 방에 있는 스피커 의 수에 제한을 두지 않지만, 세션당 2-10명의 스피커에 최적화되어 있습니다.

## <a name="use-cases"></a>사용 사례

### <a name="inclusive-meetings"></a>포괄적인 회의

청각 장애가 있거나 청각 장애가 있는 참가자와 같은 모든 사람이 모임을 포괄하도록 하려면 실시간으로 전사를 하는 것이 중요합니다. 실시간 모드에서 의 대화 녹음은 회의 오디오를 취하고 모든 회의 참가자가 성적 증명서를 따르고 지체없이 회의에 참여할 수 있도록, 무엇을 말하는 사람을 결정합니다.

### <a name="improved-efficiency"></a>효율성 향상

회의 참가자는 회의에 집중하고 메모 작성을 대화 녹음에 남길 수 있습니다. 참가자는 회의에 적극적으로 참여하고 회의 중에 메모를 하고 잠재적으로 누락된 것을 대신 성적 증명서를 사용하여 다음 단계에 대해 신속하게 후속 조치를 취할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

이것은 대화 녹음의 작동 방식에 대한 높은 수준의 개요입니다.

![가져오기 대화 전사 다이어그램](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>예상 입력

- **멀티 채널 오디오 스트림** - 사양 및 디자인 세부 사항은 [Microsoft 음성 장치 SDK 마이크를](https://aka.ms/cts/microphone)참조하십시오. 자세한 내용을 알아보거나 개발 키트를 구입하려면 [Microsoft 음성 장치 SDK 가져오기를](https://aka.ms/cts/getsdk)참조하십시오.
- **사용자 음성 샘플** - 대화 녹음은 대화 전에 사용자 프로필이 필요합니다. 각 사용자로부터 오디오 녹음을 수집한 다음 레코딩을 [서명 생성 서비스로](https://aka.ms/cts/signaturegenservice) 보내 오디오의 유효성을 검사하고 사용자 프로필을 생성해야 합니다.

## <a name="real-time-vs-asynchronous"></a>실시간 대 비동기

대화 전사는 세 가지 전사 모드를 제공합니다.

### <a name="real-time"></a>실시간

오디오 데이터는 스피커 식별자 + 성적 증명서를 반환하기 위해 라이브로 처리됩니다. 전사 솔루션 요구 사항이 대화 참가자에게 진행 중인 대화의 실시간 성적 증명서 보기를 제공하는 경우 이 모드를 선택합니다. 예를 들어, 청각 장애인과 청각 장애인이 회의에 더 쉽게 접근할 수 있도록 응용 프로그램을 구축하는 것은 실시간 전사에 이상적인 사용 사례입니다.

### <a name="asynchronous"></a>비동기

오디오 데이터는 스피커 식별자 및 성적 증명서를 반환하기 위해 일괄 처리됩니다. 라이브 전사 보기 없이 더 높은 정확도를 제공하는 경우 이 모드를 선택합니다. 예를 들어 모임 참가자가 놓친 모임을 쉽게 파악할 수 있도록 응용 프로그램을 빌드하려는 경우 비동기 전사 모드를 사용하여 높은 정확도의 전사 결과를 얻을 수 있습니다.

### <a name="real-time-plus-asynchronous"></a>실시간 플러스 비동기

오디오 데이터는 스피커 식별자 + 성적 증명서를 반환하기 위해 라이브로 처리되며, 또한 비동기 처리를 통해 고정밀 대본을 얻기 위한 요청이 생성됩니다. 응용 프로그램에 실시간 전사가 필요하지만 대화 나 모임이 발생한 후 사용하기 위해 더 높은 정확도의 성적 증명서가 필요한 경우 이 모드를 선택합니다.

## <a name="language-support"></a>언어 지원

현재, 대화 전사는 다음과 같은 지역에서 "en-US"와 "zh-CN"을 지원합니다: *중앙 및*  *동아시아.* 추가 로캘 지원이 필요한 경우 [대화 전사 기능 팀에](mailto:CTSFeatureCrew@microsoft.com)문의하십시오.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [실시간으로 대화 전사](how-to-use-conversation-transcription-service.md)
