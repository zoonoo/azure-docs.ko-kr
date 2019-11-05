---
title: 음성 서비스를 사용한 음성 변환
titleSuffix: Azure Cognitive Services
description: Speech Service를 사용 하면 응용 프로그램, 도구 및 장치에 음성의 종단 간 실시간 다중 언어 번역을 추가할 수 있습니다. 같은 API를 음성 대 음성 및 음성 대 텍스트 번역 모두에 사용될 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 17ad32cba18915bf7f83163cd876686d42323750
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468751"
---
# <a name="what-is-speech-translation"></a>음성 번역 이란?

Azure Speech Services에서 음성 번역을 사용 하면 실시간 다중 언어 음성-음성-음성-음성-텍스트 음성 변환 및 오디오 스트림의 음성 변환 기능을 사용할 수 있습니다. 음성 SDK를 사용 하 여 응용 프로그램, 도구 및 장치는 제공 된 오디오에 대 한 소스에 액세스할 수 있습니다. 음성이 감지 되 면 임시 기록 및 번역 결과가 반환 되 고 결승전 결과가 합성 음성으로 변환 될 수 있습니다.

Microsoft의 변환 엔진은 SMT (통계적 기계 번역) 및 NMT (신경망 변환)의 두 가지 방법으로 구동 됩니다. SMT는 고급 통계 분석을 사용 하 여 몇 가지 단어의 컨텍스트에서 가능한 최상의 번역을 추정 합니다. NMT를 사용 하면 전체 문장 컨텍스트를 사용 하 여 단어를 번역 하 여 보다 정확 하 고 자연 스러운 변환을 제공 하는 신경망을 사용 합니다.

현재 Microsoft는 가장 인기 있는 언어로 번역 하는 데 NMT를 사용 합니다. [음성 대 음성 번역에 사용할 수 있는 모든 언어](language-support.md#speech-translation)는 NMT를 통해 지원됩니다. 음성 대 텍스트 번역은 언어 쌍에 따라 SMT 또는 NMT를 사용할 수 있습니다. NMT에서 대상 언어를 지 원하는 경우 전체 번역이 NMT로 구동 됩니다. NMT에서 대상 언어를 지원 하지 않는 경우 변환은 두 언어 간의 "피벗"로 영어를 사용 하는 NMT 및 SMT의 하이브리드입니다.

## <a name="core-features"></a>핵심 기능

음성 SDK 및 REST Api를 통해 사용할 수 있는 기능은 다음과 같습니다.

| 사용 사례 | SDK) | REST |
|----------|-----|------|
| 인식 결과를 사용 하 여 음성 텍스트 변환 | 예 | 아니요 |
| 음성-음성 변환. | 예 | 아니요 |
| 중간 인식 및 번역 결과. | 예 | 아니요 |

## <a name="get-started-with-speech-translation"></a>음성 번역 시작

10 분 이내에 코드를 실행 하도록 설계 된 빠른 시작을 제공 합니다. 이 표에는 언어별로 구성 된 음성 번역 빠른 시작 목록이 포함 되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>샘플 코드

이 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일이 나 스트림에서 오디오 읽기, 연속 및 단일 샷 인식/번역, 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다.

* [음성 텍스트 및 번역 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>마이그레이션 가이드

응용 프로그램, 도구 또는 제품이 [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)를 사용 하는 경우 음성 서비스로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.

* [Translator Speech API에서 음성 서비스로 마이그레이션](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성 텍스트](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [음성 SDK 가져오기](speech-sdk.md)
