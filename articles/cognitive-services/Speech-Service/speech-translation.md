---
title: 음성 서비스를 사용한 음성 변환
titleSuffix: Azure Cognitive Services
description: Speech service를 사용 하면 응용 프로그램, 도구 및 장치에 음성의 종단 간 실시간 다중 언어 번역을 추가할 수 있습니다. 같은 API를 음성 대 음성 및 음성 대 텍스트 번역 모두에 사용될 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f2f0b277fb9dc6270e9015b5bf3205cc8aceaa0a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371242"
---
# <a name="what-is-speech-translation"></a>음성 번역이란?

음성 서비스에서 음성 번역을 사용 하면 오디오 스트림의 실시간 다중 언어 음성-음성-음성-텍스트 변환이 가능 합니다. Speech SDK를 사용하여 응용 프로그램, 도구 및 장치가 제공된 오디오에 대한 원본 기록 및 변환 출력에 액세스할 수 있습니다. 중간 기록 및 번역 결과는 음성이 감지되면 반환되며, 최종 결과는 합성된 음성으로 변환될 수 있습니다.

Microsoft의 번역 엔진은 두 가지 방법을 통해 제공됩니다: 통계 기계 번역(SMT) 및 신경 기계 번역(NMT). SMT는 단어 몇 개의 컨텍스트에서 최상의 가능한 번역을 추정하기 위해 고급 통계 분석을 사용 합니다. NMT의 경우 신경망이 사용되며, 이는 문장의 전체 컨텍스트를 사용하여 단어를 번역함으로써 보다 정확하고, 자연스럽게 들리는 번역을 제공합니다.

현재 Microsoft는 대부분 인기 있는 언어로의 번역에 NMT를 사용합니다. [음성 대 음성 번역에 사용할 수 있는 모든 언어](language-support.md#speech-translation)는 NMT를 통해 지원됩니다. 음성 대 텍스트 번역은 언어 쌍에 따라 SMT 또는 NMT를 사용할 수 있습니다. 대상 언어가 NMT에서 지원되는 경우 전체 번역은 NMT를 기반으로 합니다. 대상 언어가 NMT에서 지원되지 않는 경우, 번역은 NMT 및 SMT를 혼합하며, 두 언어 사이의 "피벗"으로 영어를 사용합니다.

## <a name="core-features"></a>핵심 기능

다음은 Speech SDK 및 REST Api를 통해 사용 가능한 기능입니다:

| 사용 사례 | SDK) | REST (영문) |
|----------|-----|------|
| 인식 결과를 사용하여 음성-텍스트를 번역합니다. | yes | 예 |
| 음성-음성 변환. | yes | 예 |
| 중간 인식 및 번역 결과. | yes | 예 |

## <a name="get-started-with-speech-translation"></a>음성 번역 시작하기

10분 이내에 코드를 실행하도록 디자인된 빠른 시작을 제공합니다. 이 테이블은 언어별로 조직된 음성 번역 빠른 시작의 목록을 포함합니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [찾아보기](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [찾아보기](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>예제 코드

Speech SDK에 대한 예제 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 지속적인 그리고 일회성의 인식/번역, 사용자 지정 모델을 사용하는 작업과 같은 일반적인 시나리오를 다룹니다.

* [음성 텍스트 및 번역 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>마이그레이션 가이드

응용 프로그램, 도구 또는 제품이 [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)를 사용 하는 경우 음성 서비스로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.

* [Translator Speech API에서 Speech service로 마이그레이션](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성 텍스트](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [음성 SDK 가져오기](speech-sdk.md)
