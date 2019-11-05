---
title: 텍스트 음성 변환-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스의 텍스트 음성 변환 기능은 응용 프로그램, 도구 또는 장치에서 텍스트를 사용자와 같은 자연 스러운 음성으로 변환 하는 데 사용할 수 있는 기능입니다. 표준 및 신경망에서 선택 하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만드세요. 75 + standard 음성은 45 개 이상의 언어 및 로캘에서 사용할 수 있으며, 5 개의 신경망은 4 개의 언어와 로캘에서 사용할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: d3d4777d54e3ef6b20ab0ac0f0890da958411297
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468670"
---
# <a name="what-is-text-to-speech"></a>텍스트 음성 변환 이란?

Azure Speech Services에서 텍스트 음성 변환은 응용 프로그램, 도구 또는 장치에서 텍스트를 자연 스런 사람이 나 합성 음성으로 변환 하는 데 사용할 수 있는 서비스입니다. 표준 및 신경망에서 선택 하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만드세요. 75 + standard 음성은 45 개 이상의 언어 및 로캘에서 사용할 수 있으며, 5 개의 신경망은 4 개의 언어와 로캘에서 사용할 수 있습니다. 전체 목록은 [지원 되는 언어](language-support.md#text-to-speech)를 참조 하세요.

텍스트 음성 변환 기술은 콘텐츠 작성자가 다양 한 방식으로 사용자와 상호 작용할 수 있도록 합니다. 텍스트 음성 변환 기능을 사용 하면 사용자에 게 content 통화와 상호 작용할 수 있는 옵션을 제공 하 여 내게 필요한 옵션을 향상할 수 있습니다. 사용자에 게 시각적 장애가 있거나, 학습 장애가 발생 하거나, 이동 하는 동안 탐색 정보가 필요한 지 여부에 관계 없이 텍스트를 음성으로 변환 하면 기존 환경을 개선할 수 있습니다. 텍스트 음성 변환도 음성 봇 및 음성 도우미를 위한 중요 한 추가 기능입니다.


XML 기반 태그 언어인 SSML (Speech Markup Language)를 활용 하 여 텍스트 음성 변환 서비스를 사용 하는 개발자는 입력 텍스트가 합성 된 음성으로 변환 되는 방식을 지정할 수 있습니다. SSML를 사용 하면 피치, 발음, 말하기 요금, 볼륨 등을 조정할 수 있습니다. 자세한 내용은 [SSML](#speech-synthesis-markup-language-ssml)를 참조 하세요.

### <a name="standard-voices"></a>표준 음성

표준 음성은 통계 패라메트릭 합성 및/또는 연결 합성 기술을 사용 하 여 생성 됩니다. 이러한 음성은 매우 있는 하 고 자연 스러운 소리를 가집니다. 다양 한 음성 옵션을 사용 하 여 응용 프로그램에서 45 개 이상의 언어를 쉽게 사용할 수 있습니다. 이러한 음성은 약어, 머리글자어 확장, 날짜/시간 해석, polyphones 등에 대 한 지원을 포함 하 여 높은 발음 정확도를 제공 합니다. 표준 음성을 사용 하 여 사용자가 콘텐츠 통화 상호 작용할 수 있도록 하 여 응용 프로그램 및 서비스에 대 한 접근성을 향상 하세요.

### <a name="neural-voices"></a>인공신경망 음성

신경망은 심층 신경망을 사용 하 여 음성 언어의 스트레스와 intonation 패턴을 일치 시킬 때 기존 텍스트 음성 변환 시스템의 제한을 극복 하 고 음성 단위를 컴퓨터 음성으로 synthesizing 합니다. 표준 텍스트 음성 변환 기능은 독립적인 모델에 의해 제어 되는 별도의 언어 분석 및 음향 예측 단계로 분류 되어 muffled 음성 합성이 발생할 수 있습니다. Microsoft의 신경망은 prosody 예측 및 음성 합성을 동시에 수행 하므로 더 많은 유체와 자연 스러운 음성이 발생 합니다.

신경망을 사용 하 여 자연 스런 봇 및 음성 도우미와의 상호 작용을 보다 자연스럽 게 만들고 활용할 수 있습니다. 예를 들어 전자 서적에서 오디오 책으로의 디지털 텍스트를 변환 하 고 자동차 탐색 시스템을 향상 시킬 수 있습니다. 인간 같은 자연 스러운 자연 proaaady와 명확한 articulation을 사용 하 여 AI 시스템을 조작할 때 신경망은 듣기 피로를 크게 줄입니다.

신경망은 중립 및 cheerful와 같은 다양 한 스타일을 지원 합니다. 예를 들어 Jessa (en-us) 음성은 웜, 행복 한 대화에 최적화 된를 말할 수 있습니다. [음성 합성 마크업 언어](speech-synthesis-markup.md)를 사용 하 여 톤, 피치, 속도 등의 음성 출력을 조정할 수 있습니다. 사용 가능한 음성의 전체 목록은 [지원 되는 언어](language-support.md#text-to-speech)를 참조 하세요.

신경망의 이점에 대 한 자세한 내용은 [Microsoft의 새로운 신경망 음성 변환 서비스를 사용 하 여 사용자와 같이 컴퓨터를 말할 수 있습니다](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>사용자 지정 음성

음성 사용자 지정을 통해 브랜드에 대해 인식할 수 있는 일종의 음성을 만들 수 있습니다. 사용자 지정 음성 글꼴을 만들려면 스튜디오를 기록 하 고 연결 된 스크립트를 학습 데이터로 업로드 합니다. 그러면 이 서비스는 녹음한 음성에 맞게 튜닝된 고유한 음성 모델을 만듭니다. 이 사용자 지정 음성 글꼴을 사용 하 여 음성을 합성할 수 있습니다. 자세한 내용은 [사용자 지정 음성](how-to-customize-voice-font.md)을 참조 하세요.

## <a name="speech-synthesis-markup-language-ssml"></a>SSML(Speech Synthesis Markup Language)

SSML (Speech 합성 Markup Language)은 텍스트 음성 변환 서비스를 사용 하 여 입력 텍스트가 합성 음성으로 변환 되는 방법을 개발자가 지정할 수 있는 XML 기반 태그 언어입니다. 일반 텍스트와 비교해 서 SSML를 사용 하면 개발자가 피치, 발음, 말하는 요금, 볼륨 및 텍스트 음성 변환의 출력을 세밀 하 게 조정할 수 있습니다. 마침표 뒤에 일시 중지 또는 문장이 끝나는 경우 올바른 intonation를 사용 하는 등의 일반 문장 부호는 자동으로 처리 됩니다.

텍스트 음성 변환 서비스로 전송 되는 모든 텍스트 입력은 SSML로 구성 되어야 합니다. 자세한 내용은 [음성 합성 태그 언어](speech-synthesis-markup.md)를 참조 하세요.

### <a name="pricing-note"></a>가격 정보

텍스트 음성 변환 서비스를 사용 하는 경우 문장 부호를 포함 하 여 음성으로 변환 되는 각 문자에 대 한 요금이 청구 됩니다. SSML 문서 자체에는 요금이 청구 되지 않지만 음소 및 피치와 같이 텍스트를 음성으로 변환 하는 방법을 조정 하는 데 사용 되는 선택적 요소는 청구 가능 문자로 계산 됩니다. 다음은 청구 가능한 항목 목록입니다.

* 요청의 SSML 본문에서 텍스트 음성 변환 서비스에 전달 된 텍스트입니다.
* `<speak>` 및 `<voice>` 태그를 제외 하 고 SSML 형식으로 된 요청 본문의 텍스트 필드 내 모든 태그
* 문자, 문장 부호, 공백, 탭, 태그 및 모든 공백 문자
* 유니코드에 정의된 모든 코드 포인트

자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조 하세요.

> [!IMPORTANT]
> 각 중국어, 일본어 및 한국어 문자는 청구를 위해 두 문자로 계산 됩니다.

## <a name="core-features"></a>핵심 기능

다음 표에서는 텍스트 음성 변환의 핵심 기능을 보여 줍니다.

| 사용 사례 | SDK) | REST |
|----------|-----|------|
| 텍스트를 음성으로 변환 합니다. | 예 | 예 |
| 음성 적응을 위한 데이터 집합을 업로드 합니다. | 아니요 | 예\* |
| 음성 글꼴 모델을 만들고 관리 합니다. | 아니요 | 예\* |
| 음성 글꼴 배포를 만들고 관리 합니다. | 아니요 | 예\* |
| 음성 글꼴 테스트를 만들고 관리 합니다. | 아니요 | 예\* |
| 구독을 관리 합니다. | 아니요 | 예\* |

\* *cris.ai 끝점을 사용 하 여 이러한 서비스를 사용할 수 있습니다. [Swagger 참조](https://westus.cris.ai/swagger/ui/index)를 참조 하세요. 이러한 사용자 지정 음성 학습 및 관리 Api는 요청을 5 초 당 25로 제한 하는 제한을 구현 하는 반면, 음성 합성 API 자체는 초당 200 요청을 허용 하는 조정을 구현 합니다. 제한이 발생 하면 메시지 헤더를 통해 알림이 표시 됩니다.*

## <a name="get-started-with-text-to-speech"></a>텍스트 읽어주기 시작

10 분 이내에 코드를 실행 하도록 설계 된 빠른 시작을 제공 합니다. 이 표에는 언어별로 구성 된 텍스트 음성 변환의 목록이 포함 되어 있습니다.

### <a name="sdk-quickstarts"></a>SDK 퀵 스타트

| 빠른 시작 (SDK) | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=unity) | Windows, Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-macos.md) | macOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-ios.md) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Swift](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-macos.md) | macOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Swift](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-ios.md) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python) | 창, Linux, macOS | [Browse](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>REST 빠른 시작

| 빠른 시작 (REST) | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.JS](quickstart-nodejs-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>샘플 코드

텍스트 음성 변환에 대 한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 가장 인기 있는 프로그래밍 언어의 텍스트 음성 변환 변환을 다룹니다.

* [텍스트 음성 변환 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성 텍스트](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [체험 Speech Services 구독 받기](get-started.md)
* [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)
