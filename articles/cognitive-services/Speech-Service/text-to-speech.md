---
title: Azure 음성 서비스를 사용 하 여 텍스트 음성 변환
titleSuffix: Azure Cognitive Services
description: Azure Speech Services에서 text to speech는 사용자 응용 프로그램, 도구 또는 장치 자연 스러운 인간과 유사한 합성 된 음성 텍스트 변환 수 있도록 하는 REST 기반 서비스. 표준 및 신경망 음성에서 선택 하거나 사용자 고유의 사용자 지정 음성을 고유한 제품 또는 브랜드를 만듭니다. 75 표준 음성 45 개 언어 및 로캘을 제공 되며 5 신경망 음성 4 언어 및 로캘을 사용할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 05028704c08ebd06f9b9e4e3f45c5137eb1e6b58
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226915"
---
# <a name="what-is-text-to-speech"></a>텍스트 음성 변환 이란?

Azure Speech Services에서 text to speech는 사용자 응용 프로그램, 도구 또는 장치 자연 스러운 인간과 유사한 합성 된 음성 텍스트 변환 수 있도록 하는 REST 기반 서비스. 표준 및 신경망 음성에서 선택 하거나 직접 만들 [사용자 지정 음성](#custom-voice-fonts) 제품 또는 브랜드에 고유 합니다. 75 표준 음성 45 개 언어 및 로캘을 제공 되며 5 신경망 음성 4 언어 및 로캘을 사용할 수 있습니다. 전체 목록을 참조 하세요 [지원 되는 언어](language-support.md#text-to-speech)합니다.

텍스트 음성 변환 기술 콘텐츠 작성자가 사용자에 게 다양 한 방법으로 조작할 수 있습니다. Text to speech 음성으로 콘텐츠와 상호 작용 하는 옵션이 사용자에 게 제공 하 여 접근성을 향상할 수 있습니다. 든 사용자가 시각 장애가, 학습 장애가 운전 하면서 탐색 정보가 필요 text to speech 기존 환경을 개선할 수 있습니다. Text to speech 음성 봇 및 가상 도우미에 대 한 유용한 추가 기능을 이기도합니다.

### <a name="neural-voices"></a>인공신경망 음성

인공신경망 음성을 사용하여 챗봇 및 가상 도우미와의 상호 작용에 더 자연스럽게 참여하도록 만들고, 전자책 같은 디지털 텍스트를 오디오북으로 변환하고, 자동차 내부 내비게이션 시스템을 향상시킬 수 있습니다. 인간과 유사한 자연 스러운 prosody와 단어 지우기 할 신경망 음성을 크게 줄일 수신 피로 AI 시스템과 상호 작용 하는 경우. 인공신경망 음성에 대한 자세한 내용은 [지원되는 언어](language-support.md#text-to-speech)를 참조하세요.

### <a name="custom-voices"></a>사용자 지정 음성

음성 사용자 지정 브랜드에 대 한 인식할 수 있는 한 종류 음성을 만들 수 있습니다. 사용자 지정 음성 글꼴을 만들려면 studio 기록을 확인 하 고 학습 데이터와 관련된 스크립트를 업로드 합니다. 그러면 이 서비스는 녹음한 음성에 맞게 튜닝된 고유한 음성 모델을 만듭니다. 음성 합성에이 사용자 지정 음성 글꼴을 사용할 수 있습니다. 자세한 내용은 [사용자 지정 음성](how-to-customize-voice-font.md)합니다.

## <a name="core-features"></a>핵심 기능

이 표에서 텍스트 음성 변환에 대 한 핵심 기능을 보여 줍니다.

| 사용 사례 | SDK) | REST (영문) |
|----------|-----|------|
| 텍스트를 음성으로 변환 합니다. | 아닙니다. | 예 |
| 음성 조정에 대 한 데이터 집합을 업로드 합니다. | 아닙니다. | 예\* |
| 음성 글꼴 모델을 만들고 설정 합니다. | 아닙니다. | 예\* |
| 만들고 음성 글꼴 배포를 관리 합니다. | 아닙니다. | 예\* |
| 음성 글꼴 테스트를 만들고 설정 합니다. | 아닙니다. | 예\* |
| 구독을 관리 합니다. | 아닙니다. | 예\* |

\* *이러한 서비스 cris.ai 끝점을 사용 하 여 사용할 수 있습니다. 참조 [참조를 Swagger](https://westus.cris.ai/swagger/ui/index)합니다.*

> [!NOTE]
> 텍스트 음성 변환 끝점 5 초 당 25에 대 한 요청을 제한 하는 제한을 구현 합니다. 제한이 발생 하는 경우 메시지 헤더를 통해 알림을 받게 됩니다.

## <a name="get-started-with-text-to-speech"></a>텍스트 읽어주기 시작

10 분 이내에 코드를 실행 하는 경우 제공 하도록 설계 되었습니다 빠른 시작을 제공 합니다. 이 테이블에는 언어에서 구성 하는 텍스트 음성 변환 빠른 시작의 목록을 포함 합니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Node.JS](quickstart-nodejs-text-to-speech.md) | Linux, macOS, 창 | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Python](quickstart-python-text-to-speech.md) | Linux, macOS, 창 | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |

## <a name="sample-code"></a>샘플 코드

텍스트 음성 변환에 대 한 예제 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플에는 가장 인기 있는 프로그래밍 언어로 텍스트 음성 변환 변환을 다룹니다.

* [텍스트 음성 변환 샘플 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [체험 Speech Services 구독 받기](get-started.md)
* [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)
