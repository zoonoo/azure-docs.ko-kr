---
title: 음성 서비스를 활용한 음성 번역
titleSuffix: Azure Cognitive Services
description: 음성 서비스를 사용하면 애플리케이션, 도구 및 장치에 종단 간 실시간 다국어 음성 번역을 추가할 수 있습니다. 같은 API를 음성 대 음성 및 음성 대 텍스트 번역 모두에 사용될 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052613"
---
# <a name="what-is-speech-translation"></a>음성 번역이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

음성 서비스의 음성 변환을 통해 오디오 스트림의 실시간 다국어 음성 변환 및 음성-텍스트 변환이 가능합니다. Speech SDK를 사용하면 응용 프로그램, 도구 및 장치에서 제공된 오디오에 대한 소스 전사 및 번역 출력에 액세스할 수 있습니다. 중간 전사 및 번역 결과는 음성이 감지되면 반환되며 최종 결과는 합성 된 음성으로 변환 할 수 있습니다.

Microsoft의 번역 엔진은 통계 적 컴퓨터 번역(SMT) 및 신경기계 번역(NMT)의 두 가지 접근 방식에 의해 구동됩니다. SMT는 고급 통계 분석을 사용하여 몇 단어의 컨텍스트를 고려하여 최상의 번역을 추정합니다. NMT를 사용하면 문장의 전체 컨텍스트를 사용하여 단어를 번역하여 보다 정확하고 자연스러운 번역을 제공하는 데 신경망이 사용됩니다.

오늘날 Microsoft는 가장 인기 있는 언어로 번역하기 위해 NMT를 사용합니다. [음성 대 음성 번역에 사용할 수 있는 모든 언어](language-support.md#speech-translation)는 NMT를 통해 지원됩니다. 음성 대 텍스트 번역은 언어 쌍에 따라 SMT 또는 NMT를 사용할 수 있습니다. 대상 언어가 NMT에서 지원되면 전체 번역은 NMT 기반입니다. 대상 언어가 NMT에서 지원되지 않는 경우 번역은 영어를 두 언어 간의 "피벗"으로 사용하는 NMT와 SMT의 하이브리드입니다.

## <a name="core-features"></a>핵심 기능

다음은 음성 SDK 및 REST API를 통해 사용할 수 있는 기능입니다.

| 사용 사례 | SDK) | REST (영문) |
|----------|-----|------|
| 인식 결과와 음성 - 텍스트 번역. | yes | 예 |
| 음성 변환. | yes | 예 |
| 중간 인식 및 번역 결과. | yes | 예 |

## <a name="get-started-with-speech-translation"></a>음성 번역 시작

10분 이내에 코드를 실행할 수 있도록 설계된 퀵스타트를 제공합니다. 이 표에는 언어별로 구성된 음성 번역 빠른 시작 목록이 포함되어 있습니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET 코어](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| [C#, .NET 프레임워크](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [찾아보기](https://aka.ms/csspeech/csharpref) |
| [C + +](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [찾아보기](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [찾아보기](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>예제 코드

음성 SDK에 대한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 단일 샷 인식/번역, 사용자 지정 모델 작업과 같은 일반적인 시나리오를 다룹니다.

* [음성-텍스트 및 번역 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>마이그레이션 가이드

응용 프로그램, 도구 또는 제품이 [번역기 음성 API를](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)사용하는 경우 음성 서비스로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.

* [번역기 음성 API에서 음성 서비스로 마이그레이션](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>참조 문서

* [음성 SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성-텍스트](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 전사 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
