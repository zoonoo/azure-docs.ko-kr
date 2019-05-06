---
title: 음성-텍스트 Azure 음성 서비스를 사용 하 여
titleSuffix: Azure Cognitive Services
description: 를 표시 하 고 명령 입력으로 작업을 수행 하는 음성-텍스트에서 Azure 라고도 음성-텍스트를 음성 응용 프로그램, 도구 또는 장치에서 사용할 수 있는 텍스트로 오디오 스트림의 수 있도록 실시간 기록 서비스입니다. 이 서비스는 Microsoft Cortana 및 Office 제품에 대 한 사용 및 텍스트 음성 변환 번역와 원활 하 게 작동 하는 동일한 인식 기술을 통해 구현 됩니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 6b144f126e097a8db9fbbf29e47162fd6a1c2fbe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916827"
---
# <a name="what-is-speech-to-text"></a>음성-텍스트 란?

를 표시 하 고 명령 입력으로 작업을 수행 하는 음성-텍스트에서 Azure 라고도 음성-텍스트를 음성 응용 프로그램, 도구 또는 장치에서 사용할 수 있는 텍스트로 오디오 스트림의 수 있도록 실시간 기록 서비스입니다. 이 서비스는 Microsoft Cortana 및 Office 제품에 대 한 사용 및 텍스트 음성 변환 번역와 원활 하 게 작동 하는 동일한 인식 기술을 통해 구현 됩니다.  사용 가능한 음성-텍스트 언어의 전체 목록을 참조 하세요 [지원 되는 언어](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)합니다.

기본적으로 음성-텍스트를 범용 언어 모델을 사용합니다. 이 Microsoft에서 소유한 데이터를 사용 하 여 학습 된 모델과 클라우드에 배포 합니다. 적합 한 대화형 및 받아쓰기 시나리오입니다. 고유한 환경에서 인식 및 기록을 위해 음성 텍스트 변환을 사용하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습하여 주변 소음 또는 산업용 어휘를 처리할 수 있습니다. 

마이크에서 오디오 캡처 하 또는 스트림에서 읽은 다음 Speech SDK 및 REST Api를 사용 하 여 저장소에서 오디오 파일에 액세스할 쉽게 합니다. Speech SDK는 음성 인식을 위한 WAV/PCM 16 비트, 16 kHz, 단일 채널 오디오를 지원합니다. 추가 오디오 형식을 사용 하 여 지원 되는 [음성-텍스트 REST 끝점](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) 또는 [batch 기록 서비스](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)합니다.

## <a name="core-features"></a>핵심 기능

다음은 Speech SDK 및 REST Api를 통해 사용 가능한 기능:

| 사용 사례 | SDK) | REST (영문) |
|----------|-----|------|
| 짧은 길이 발언 기록 (< 15 초)입니다. 만 마지막 기록 결과 지원합니다. | 예 | 예 |
| 스트리밍 오디오 및 비트 긴 길이 발언 연속 기록 (> 15 초)입니다. 중간 및 최종 기록 결과 지원합니다. | 예 | 아닙니다. |
| 인 텐트와 인식 결과에서 파생 [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)합니다. | 예 | 아니요\* |
| 오디오 파일의 대사를 비동기적으로 일괄 처리 합니다. | 아닙니다. | 예\** |
| 음성 모델을 만들고 설정 합니다. | 아닙니다. | 예\** |
| 만들고 사용자 지정 모델 배포를 관리 합니다. | 아닙니다. | 예\** |
| 사용자 지정 모델 및 기본 모델의 정확도 측정 하는 정확도 테스트를 만듭니다. | 아닙니다. | 예\** |
| 구독을 관리 합니다. | 아닙니다. | 예\** |

\* *LUIS 의도 및 엔터티는 별도의 LUIS 구독을 사용하여 파생시킬 수 있습니다. 이 구독을 사용 하 여 SDK LUIS를 호출 하 고 엔터티 및 의도 결과 제공할 수 있습니다. REST API를 사용하면 LUIS 구독을 통해 LUIS를 직접 호출하고 의도 및 엔터티를 파생할 수 있습니다.*

\** *이러한 서비스 cris.ai 끝점을 사용 하 여 사용할 수 있습니다. 참조 [참조를 Swagger](https://westus.cris.ai/swagger/ui/index)합니다.*

## <a name="get-started-with-speech-to-text"></a>음성-텍스트를 사용 하 여 시작

각각이 10분 이내에 코드를 실행할 수 있게 디자인된 가장 인기 있는 프로그래밍 언어로 빠른 시작을 제공합니다. 이 테이블에는 언어를 이끄는 Speech SDK 빠른 시작의 전체 목록을 포함 합니다.

| 빠른 시작 | 플랫폼 | API 참조 |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript, Browser](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | 브라우저, Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tr)  |

음성-텍스트 REST 서비스를 사용 하는 것을 선호 하는 경우 참조 [REST Api](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)합니다.

## <a name="tutorials-and-sample-code"></a>자습서 및 샘플 코드

Speech Services를 사용해본 경우 Speech SDK 및 LUIS를 사용하여 음성에서 의도를 인식하는 방법을 설명하는 자습서를 사용해 보세요.

* [자습서: Speech SDK 및 LUIS, C#을 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)

Speech SDK에 대 한 예제 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다.

* [음성-텍스트 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>사용자 지정

배포 음성 서비스를 사용한 유니버설 모델 외에도 사용자 지정 어쿠스틱, 언어 및 발음 모델 특정 하 게 만들 하는 환경에 수 있습니다. 사용자 지정 옵션의 목록을 다음과 같습니다.

| 모델 | 설명 |
|-------|-------------|
| [음향 모델](how-to-customize-acoustic-models.md) | 사용자 지정 어쿠스틱 모델을 만드는 응용 프로그램, 도구 또는 장치 처럼 사용 되는 특정 환경에서는 자동차 또는 특정 기록 조건 사용 하 여 팩터리 하는 경우 도움이 됩니다. 액센트가 있는 음성, 특정 배경 소음, 레코딩에 특정 마이크 사용 등을 예로 들 수 있습니다. |
| [언어 모델](how-to-customize-language-model.md) | 산업별 어휘 및 의료 용어 등 IT 전문 용어 문법의 대사를 개선 하기 위해 사용자 지정 언어 모델을 만듭니다. |
| [발음 모델](how-to-customize-pronunciation.md) | 사용자 지정 발음 모델을 사용하여 사용자가 발음 유형과 단어 또는 용어의 표시를 정의할 수 있습니다. 제품 이름 또는 머리글자어와 같은 사용자 지정된 용어를 처리하는 데 유용합니다. 발음 파일만 있으면 시작 가능 - 간단한 .txt 파일 |

> [!NOTE]
> 언어/로캘에서 마다 다른 사용자 지정 옵션 (참조 [지원 되는 언어](supported-languages.md)).

## <a name="migration-guides"></a>마이그레이션 가이드

> [!WARNING]
> Bing Speech는 2019 년 10 월 15 일에 폐기 될 예정입니다.

응용 프로그램, 도구 또는 제품을 사용 하는 Bing Speech Api 또는 사용자 지정 음성 음성 서비스를 마이그레이션할 수 있도록 가이드를 만들었습니다.

* [Bing Speech에서 Speech Services로 마이그레이션](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [사용자 지정 음성에서 Speech Services로 마이그레이션](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
