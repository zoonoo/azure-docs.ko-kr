---
title: Azure Cognitive Services 음성 시나리오
description: 시나리오
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: v-jerkin
ms.openlocfilehash: 98e79112ba70ff578975cea9d872b4fcc41512bc
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42093806"
---
# <a name="speech-scenarios"></a>음성 시나리오

음성 기술로 지원될 수 있는 많은 시나리오가 있습니다. 이 설명서에서는 가장 일반적인 몇 가지 기술을 분석하고 관련 기능을 설명하려고 합니다. 대부분의 콘텐츠에서 이러한 시나리오는 [SDK](speech-sdk.md)를 통해 지원됩니다.

이 페이지에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 음성 트리거 앱 만들기
> * 호출 센터 음성 통화 기록
> * 음성 봇

## <a name="voice-triggered-apps"></a>음성 트리거 앱

많은 사용자가 해당 애플리케이션에서 음성 입력을 지원하려고 합니다. 음성 입력은 앱을 유연하게 만들거나, 핸즈프리를 지원하거나(예: 자동차), 뉴스나 날씨 정보를 묻는 것과 같은 다양한 작업을 빠르게 처리하는 유용한 방법입니다. 

### <a name="voice-triggered-apps-with-baseline-models"></a>기준 모델을 사용하는 음성 트리거 앱

해당 앱을 배경 소음이 과도하지 않은 환경에서 일반 대중이 사용할 예정이라면 이 작업을 수행하는 가장 쉽고 가장 빠른 방법은 [Speech SDK](speech-sdk.md)를 다운로드하고 관련 [샘플](quickstart-csharp-dotnet-windows.md)을 따르도록 하는 것입니다. [Azure 구독 키](https://azure.microsoft.com/try/cognitive-services/)로 지원되는 SDK를 사용하여 개발자는 Cortana 및 Skype를 지원하는 기준 음성 인식 모델에 오디오를 업로드할 수 있습니다. 이 모델은 최첨단 기술을 갖추고 있으며 앞에서 언급한 제품에서 사용됩니다. 또한 몇 분 내에 실행할 수 있습니다.

### <a name="voice-triggered-apps-with-custom-models"></a>사용자 지정 모델을 사용하는 음성 트리거 앱

앱이 특정 영역(예: 화학, 생물학 또는 특별한 식이 요구)을 다룰 경우 [언어 모델](how-to-customize-language-model.md)을 적응시킬 수 있습니다. 언어 모델을 적응시키면 앱에서 가장 일반적으로 사용되는 구 및 단어가 디코더에 학습됩니다. 디코더는 기준 모델보다는 특정 영역에 대한 사용자 지정 언어 모델을 사용해서 음성 입력을 보다 정확히 기록할 수 있습니다. 마찬가지로 앱이 사용될 위치의 배경 소음이 두드러질 경우 음향 모델을 적응시킬 수 있습니다. [언어 적응](how-to-customize-language-model.md) 및 [음향 적응](how-to-customize-acoustic-models.md)이 유용한 정보를 제공하는 다른 사례에 대한 설명서를 찾아보고 [적응 포털](https://customspeech.ai)을 방문하여 모델 만들기 환경을 간단히 시작하세요. 기준 모델과 비슷하게, 사용자 지정 모델도 [Speech SDK](speech-sdk.md)를 통해 호출되며 관련된 [샘플](quickstart-csharp-dotnet-windows.md)을 따릅니다.

## <a name="transcribe-call-center-audio-calls"></a>호출 센터 음성 통화 기록

호출 센터는 많은 양의 오디오를 누적합니다. 해당 오디오 파일 내에는 기록을 통해 얻을 수 있는 값이 숨겨져 있습니다. 호출 기간, 감정, 고객의 만족도, 호출을 통해 호출자가 얻은 일반적인 가치는 호출 기록을 획득하여 알아낼 수 있습니다.

가장 좋은 시작점은 관련된 [샘플](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)이 있는 [Batch Transcription API](batch-transcription.md)입니다.

먼저 [Azure 구독 키](https://azure.microsoft.com/try/cognitive-services/)를 가져온 후 [Batch 대본 API](batch-transcription.md) 설명서를 참조해야 합니다.

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>기준 모델을 사용하여 호출 센터 음성 통화 기록

결정해야 하는 사항은 내부 기준 모델을 사용해서 기록을 수행할지, 언어 또는 음향 모델 중 하나 또는 둘 다를 적응시킬지입니다. 기준 모델을 사용하려면 API에 API 키만 필요합니다. 내부적으로 API는 데이터에 대한 최상의 모델을 호출하고 적응합니다.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>사용자 지정 모델을 사용하여 호출 센터 음성 통화 기록

사용자 지정 모델을 사용하려는 경우 API 키와 함께 해당 모델의 ID도 필요합니다. 모델 ID는 [적응 포털](https://customspeech.ai)에서 가져옵니다. 이 ID는 끝점 세부 정보 보기에서 찾을 수 있는 끝점 ID가 아니라 해당 모델의 세부 정보를 클릭하여 검색할 수 있는 모델 ID입니다.

## <a name="voice-bots"></a>음성 봇

개발자는 음성 출력을 사용하여 응용 프로그램을 지원할 수 있습니다. Speech Service는 많은 수의 [언어](supported-languages.md)에 대해 음성을 합성할 수 있으며, 앱에 액세스하여 해당 기능을 추가하기 위한 [끝점](rest-apis.md)을 제공합니다.

또한 해당 봇에 더 많은 개성과 고유성을 추가하려는 사용자를 위해, 개발자들은 Speech Service에서 고유한 음성 글꼴을 사용자 지정할 수 있습니다. 음성 인식 모델을 사용자 지정하는 것과 비슷하게, 음성 글꼴에도 사용자 데이터가 필요합니다. 개발자는 [음성 적응 포털](https://customspeech.ai)에 해당 데이터를 업로드하고 봇을 위한 고유한 음성 브랜드를 빌드하기 시작합니다. 세부 사항은 [FAQ](faq-text-to-speech.md) 페이지와 [여기](how-to-text-to-speech.md)에 설명되어 있습니다. 

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [Speech SDK 시작](speech-sdk.md)
