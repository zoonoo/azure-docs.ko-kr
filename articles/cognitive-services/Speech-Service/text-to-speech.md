---
title: Azure 음성 서비스를 사용 하 여 텍스트 음성 변환
titleSuffix: Azure Cognitive Services
description: Azure Speech Services에서 text to speech는 서비스에 응용 프로그램, 도구 또는 장치 자연 스러운 인간과 유사한 합성 된 음성 텍스트 변환입니다. 표준 및 신경망 음성에서 선택 하거나 사용자 고유의 사용자 지정 음성을 고유한 제품 또는 브랜드를 만듭니다. 75 표준 음성 45 개 언어 및 로캘을 제공 되며 5 신경망 음성 4 언어 및 로캘을 사용할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2c5c2bc2b8dd1930efef9833bd442fcad5566e2a
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578787"
---
# <a name="what-is-text-to-speech"></a>텍스트 음성 변환 이란?

Azure Speech Services에서 text to speech는 서비스에 응용 프로그램, 도구 또는 장치 자연 스러운 인간과 유사한 합성 된 음성 텍스트 변환입니다. 표준 및 신경망 음성에서 선택 하거나 사용자 고유의 사용자 지정 음성을 고유한 제품 또는 브랜드를 만듭니다. 75 표준 음성 45 개 언어 및 로캘을 제공 되며 5 신경망 음성 4 언어 및 로캘을 사용할 수 있습니다. 전체 목록을 참조 하세요 [지원 되는 언어](language-support.md#text-to-speech)합니다.

텍스트 음성 변환 기술 콘텐츠 작성자가 사용자에 게 다양 한 방법으로 조작할 수 있습니다. Text to speech 음성으로 콘텐츠와 상호 작용 하는 옵션이 사용자에 게 제공 하 여 접근성을 향상할 수 있습니다. 든 사용자가 시각 장애가, 학습 장애가 운전 하면서 탐색 정보가 필요 text to speech 기존 환경을 개선할 수 있습니다. Text to speech 음성 봇 및 가상 도우미에 대 한 유용한 추가 기능을 이기도합니다.

### <a name="standard-voices"></a>표준 음성

표준 음성 통계 파라메트릭 합성 및/또는 연결 합성 기술을 사용 하 여 생성 됩니다. 이러한 음성 항상 알 이며 매우 자연 스러운 생각 합니다. 다양 한 음성 옵션을 사용 하 여 45 개 언어에서 사용 하도록 응용 프로그램을 쉽게 사용할 수 있습니다. 이러한 음성 약어, 머리글자어 확장, 날짜/시간 해석을, polyphones, 등에 대 한 지원을 비롯 하 여 정확도 높은 발음을 제공 합니다. 사용자가 콘텐츠를 사용 하 여 음성으로 상호 작용할 수 있도록 하 여 응용 프로그램 및 서비스에 대 한 내게 필요한 옵션 개선 하기 위해 표준 음성을 사용 합니다.

### <a name="neural-voices"></a>인공신경망 음성

신경망 음성 스트레스 및 음성된 언어 및 컴퓨터 음성에 음성의 단위를 취합 억양의 패턴 일치에서 기존 텍스트 음성 변환 시스템의 한계를 극복 하기 위해 심층 신경망을 사용 합니다. 표준 텍스트 음성 변환 prosody 음향 예측 단계 독립적인 모델에 의해 제어 되는 별도 언어 분석을 분류 합니다. muffled buzzy 음성 합성 될 수 있습니다. 우리의 신경망 기능이 않습니다 prosody 예측 및 음성 합성 동시에 더 유연 하 고 자연 발음이 음성 결과입니다.

인공신경망 음성을 사용하여 챗봇 및 가상 도우미와의 상호 작용에 더 자연스럽게 참여하도록 만들고, 전자책 같은 디지털 텍스트를 오디오북으로 변환하고, 자동차 내부 내비게이션 시스템을 향상시킬 수 있습니다. 인간과 유사한 자연 스러운 prosody와 단어 지우기 할 신경망 음성을 크게 줄일 수신 피로 AI 시스템과 상호 작용 하는 경우. 인공신경망 음성에 대한 자세한 내용은 [지원되는 언어](language-support.md#text-to-speech)를 참조하세요.

신경망 음성의 이점에 대 한 자세한 내용은 참조 하세요 [Microsoft의 새 신경망 텍스트 음성 변환 서비스는 다른 사람들 처럼 말하는 컴퓨터](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/)합니다.

### <a name="custom-voices"></a>사용자 지정 음성

음성 사용자 지정 브랜드에 대 한 인식할 수 있는 한 종류 음성을 만들 수 있습니다. 사용자 지정 음성 글꼴을 만들려면 studio 기록을 확인 하 고 학습 데이터와 관련된 스크립트를 업로드 합니다. 그러면 이 서비스는 녹음한 음성에 맞게 튜닝된 고유한 음성 모델을 만듭니다. 음성 합성에이 사용자 지정 음성 글꼴을 사용할 수 있습니다. 자세한 내용은 [사용자 지정 음성](how-to-customize-voice-font.md)합니다.

## <a name="core-features"></a>핵심 기능

이 표에서 텍스트 음성 변환에 대 한 핵심 기능을 보여 줍니다.

| 사용 사례 | SDK) | REST (영문) |
|----------|-----|------|
| 텍스트를 음성으로 변환 합니다. | 예 | 예 |
| 음성 조정에 대 한 데이터 집합을 업로드 합니다. | 아닙니다. | 예\* |
| 음성 글꼴 모델을 만들고 설정 합니다. | 아닙니다. | 예\* |
| 만들고 음성 글꼴 배포를 관리 합니다. | 아닙니다. | 예\* |
| 음성 글꼴 테스트를 만들고 설정 합니다. | 아닙니다. | 예\* |
| 구독을 관리 합니다. | 아닙니다. | 예\* |

\* *이러한 서비스 cris.ai 끝점을 사용 하 여 사용할 수 있습니다. 참조 [참조를 Swagger](https://westus.cris.ai/swagger/ui/index)합니다. 이러한 사용자 지정 음성 교육 및 관리 Api는 5 초 당 25로 제한 요청 음성 합성 API 자체는 제한을 구현 하는 동안 허용은 최고 초당 200 요청 제한을 구현 합니다. 제한이 발생 하는 경우 메시지 헤더를 통해 알림을 받게 됩니다.*

## <a name="get-started-with-text-to-speech"></a>텍스트 읽어주기 시작

10 분 이내에 코드를 실행 하는 경우 제공 하도록 설계 되었습니다 빠른 시작을 제공 합니다. 이 테이블에는 언어에서 구성 하는 텍스트 음성 변환 빠른 시작의 목록을 포함 합니다.

### <a name="sdk-quickstarts"></a>SDK 빠른 시작

| 빠른 시작 (SDK) | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>REST 빠른 시작

| 빠른 시작 (REST) | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.JS](quickstart-nodejs-text-to-speech.md) | Linux, macOS, 창 | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Linux, macOS, 창 | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>샘플 코드

텍스트 음성 변환에 대 한 예제 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플에는 가장 인기 있는 프로그래밍 언어로 텍스트 음성 변환 변환을 다룹니다.

* [텍스트 음성 변환 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [체험 Speech Services 구독 받기](get-started.md)
* [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)
