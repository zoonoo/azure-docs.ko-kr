---
title: 텍스트 음성 변환 정보 - Speech Service
titleSuffix: Azure Cognitive Services
description: Text to Speech API는 45개를 초과하는 언어와 로캘로 된 75개를 초과하는 음성을 제공합니다. 표준 음성 글꼴을 사용하려면 Speech Service를 호출할 때 음성 이름을 몇 가지 다른 매개 변수로 지정하면 됩니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b413bd65582dceadd9aab912694c3b560070c4f3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251557"
---
# <a name="about-the-text-to-speech-api"></a>Text-to-Speech API 정보

TTS(**Text-to-Speech**) API는 입력 텍스트를 자연스러운 발음으로 변환합니다(*음성 합성*이라고도 함).

음성을 생성하기 위해 애플리케이션은 Text-to-Speech API에 HTTP POST 요청을 보냅니다. 여기에서 텍스트는 인간의 음성으로 합성된 후 오디오 파일로 반환됩니다. 다양한 음성 및 언어가 지원됩니다.

음성 합성이 채택되는 시나리오는 다음과 같습니다.

* *접근성 개선:* **텍스트 음성 변환** 기술을 사용하여 콘텐츠 소유자와 게시자는 사람들이 해당 콘텐츠와 상호 작용하는 다양한 방식에 대응합니다. 시각 장애가 있거나 난독증이 있는 사람들은 콘텐츠를 들을 수 있다는 사실에 고마움을 느낍니다. 또한 음성 출력은 사람들이 출퇴근하거나 운동을 하는 동안 모바일 디바이스에서 신문이나 블로그 같은 텍스트 콘텐츠를 보다 쉽게 즐길 수 있도록 해줍니다.

* *멀티태스킹 시나리오에 대응:* **텍스트 음성 변환**은 사람들이 운전 중이거나 편안하게 읽을 수 없는 환경에 있을 때 중요한 정보를 빠르고 편안하게 습득할 수 있도록 합니다. 이러한 분야에서 일반적인 응용 사례가 바로 네비게이션입니다.

* *여러 모드를 사용하여 학습 향상:* 다양한 사람이 다양한 방식으로 가장 잘 학습합니다. 온라인 학습 전문가에 따르면 음성과 텍스트를 함께 제공할 경우 정보를 보다 쉽게 학습하고 지속할 수 있다고 합니다.

* *직관적인 봇 또는 도우미 제공:* 말하는 능력은 인텔리전트 챗봇이나 가상 도우미에게 필수적인 부분으로 통합될 수 있습니다. 점점 더 많은 회사들이 고객에게 참여형 서비스 환경을 제공하기 위해 챗봇을 개발하고 있습니다. 음성은 봇 응답을 음성(예: 전화)으로 받을 수 있도록 하여 한 차원 높아진 서비스를 가능하게 합니다.

## <a name="voice-support"></a>음성 지원

Microsoft **Text-to-Speech** 서비스는 45개 이상의 언어 및 로캘로 75가지 이상의 음성을 제공합니다. 이러한 표준 "음성 글꼴"을 사용하려는 경우 서비스의 REST API를 호출할 때 몇 개의 매개 변수를 사용해서 음성 이름을 지정하기만 하면 됩니다. 지원되는 언어, 로캘 및 음성에 대한 자세한 내용은 [지원되는 언어](language-support.md#text-to-speech)를 참조하세요.

> [!IMPORTANT]
> 비용은 표준, 사용자 지정 및 인공신경망 음성별로 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

### <a name="neural-voices"></a>인공신경망 음성

인공신경망 텍스트 음성 변환을 사용하여 챗봇 및 가상 도우미와의 상호 작용에 더 자연스럽게 참여하도록 만들고, 전자책 같은 디지털 텍스트를 오디오북으로 변환하고, 자동차 내부 내비게이션 시스템을 향상시킬 수 있습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 AI 시스템과 상호 작용할 때 인공신경망 TTS는 수신 피로도를 현저히 줄일 수 있습니다. 인공신경망 음성에 대한 자세한 내용은 [지원되는 언어](language-support.md#text-to-speech)를 참조하세요.

### <a name="custom-voices"></a>사용자 지정 음성

텍스트 음성 변환 사용자 지정을 사용하면 인식 가능한, 독특한 브랜드의 음성인 *음성 글꼴*을 만들 수 있습니다. 음성 글꼴을 만들려면 스튜디오에서 녹음한 후 관련 스크립트를 학습 데이터로서 업로드합니다. 그러면 이 서비스는 녹음한 음성에 맞게 튜닝된 고유한 음성 모델을 만듭니다. 이 음성 글꼴을 사용하여 음성을 합성할 수 있습니다. 자세한 내용은 [사용자 지정 음성 글꼴](how-to-customize-voice-font.md)을 참조하세요.

## <a name="api-capabilities"></a>API 기능

**Text-to-Speech** API의 많은 기능, 특히 사용자 지정 관련 기능은 REST를 통해 사용할 수 있습니다. 다음 표에서는 API에 액세스하는 각 방법의 기능을 요약해서 설명합니다. 기능 및 API의 세부 정보에 대한 전체 목록은 [Swagger 참조](https://westus.cris.ai/swagger/ui/index)를 참조하세요.

| 사용 사례 | REST (영문) | SDK |
|-----|-----|-----|----|
| 모델 적응을 위한 데이터 세트 업로드 | 예 | 아니요 |
| 음성 글꼴 모델 만들기 및 관리 | 예 | 아니요 |
| 음성 글꼴 배포 만들기 및 관리 | 예 | 아니요 |
| 음성 글꼴 테스트 만들기 및 관리| 예 | 아니요 |
| 구독 관리 | 예 | 아니요 |

> [!NOTE]
> API는 API 요청을 5초당 25개로 한정하는 제한을 구현합니다. 메시지 헤더에서 이 제한을 알려줍니다.

## <a name="next-steps"></a>다음 단계

* [체험 Speech Services 구독 받기](https://azure.microsoft.com/try/cognitive-services/)
* [빠른 시작: 텍스트 음성 변환, Python](quickstart-python-text-to-speech.md)
* [빠른 시작: 텍스트 음성 변환, .NET Core](quickstart-dotnet-text-to-speech.md)
* [REST API 참조](rest-apis.md)
