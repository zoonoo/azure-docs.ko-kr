---
title: 음성 텍스트 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 라고도 하는 speech Service의 음성 텍스트 기능을 사용 하면 응용 프로그램, 도구 또는 장치에서 명령 입력으로 사용 하 고, 표시 하 고, 작업을 수행할 수 있는 텍스트로 오디오 스트림의 실시간 기록을 사용할 수 있습니다. 이 서비스는 Microsoft Cortana 및 Office 제품에서 사용하는 것과 동일한 인식 기술로 동작하며, 번역 및 텍스트 음성 변환과 원활하게 작동합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fa80ebea93ae897ba01ff54bdb797ed4194665cd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068853"
---
# <a name="what-is-speech-to-text"></a>음성 텍스트 란 무엇 인가요?

음성 텍스트 라고도 하는 Azure Speech Services의 음성 텍스트를 사용 하면 응용 프로그램, 도구 또는 장치에서 명령 입력으로 사용 하 고 표시 하 고 작업을 수행할 수 있는 텍스트로 오디오 스트림의 실시간 기록을 사용할 수 있습니다. 이 서비스는 Microsoft Cortana 및 Office 제품에서 사용하는 것과 동일한 인식 기술로 동작하며, 번역 및 텍스트 음성 변환과 원활하게 작동합니다.  사용 가능한 음성-텍스트 언어의 전체 목록에 대해서는 [지원되는 언어](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)를 참조합니다.

기본적으로 음성 텍스트 서비스는 유니버설 언어 모델을 사용 합니다. 이 모델은 Microsoft 소유의 데이터를 사용 하 여 학습 되었으며 클라우드에 배포 됩니다. 이는 대화형 및 받아쓰기 시나리오에 적합 합니다. 고유한 환경에서 인식 및 기록을 위해 음성 텍스트 변환을 사용하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습하여 주변 소음 또는 산업용 어휘를 처리할 수 있습니다.

음성 SDK 및 REST Api를 사용 하 여 마이크에서 오디오를 쉽게 캡처하거나, 스트림에서 읽거나, 저장소에서 오디오 파일에 액세스할 수 있습니다. 음성 SDK는 소리 인식에 대해 WAV/PCM 16 비트, 16Khz/8Khz, 단일 채널 오디오를 지원 합니다. 추가 오디오 형식은 [음성-텍스트 REST 끝점](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) 또는 [배치 기록 서비스](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)를 사용 하 여 지원 됩니다.

## <a name="core-features"></a>핵심 기능

다음은 Speech SDK 및 REST Api를 통해 사용 가능한 기능입니다:

| 사용 사례 | SDK | REST(영문) |
|----------|-----|------|
| 높여줄 short 길이 발언 (< 15 초). 는 최종 기록 결과만 지원 합니다. | 예 | 예 |
| 스트리밍 오디오 및 긴 길이 발언 연속 기록 (> 15 초)입니다. 중간 및 최종 기록 결과를 지원 합니다. | 예 | 아니요 |
| [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)를 사용하여 인식 결과를 도출합니다. | 예 | No\* |
| 오디오 파일의 일괄 처리를 비동기적으로 처리 합니다. | 아니요 | 예\** |
| 음성 모델을 만들고 관리합니다. | 아니요 | 예\** |
| 사용자 지정 모델 배포를 만들고 관리합니다. | 아니요 | 예\** |
| 사용자 지정 모델과 기본 모델과 비교하여 정확도를 측정하는 정확도 테스트를 만듭니다. | 아니요 | 예\** |
| 구독을 관리합니다. | 아니요 | 예\** |

\* *LUIS 의도 및 엔터티는 별도의 LUIS 구독을 사용하여 파생시킬 수 있습니다. 이 구독을 사용하여 SDK는 LUIS를 호출하고 엔터티 및 의도 결과를 제공할 수 있습니다. REST API를 사용하면 LUIS 구독을 통해 LUIS를 직접 호출하고 의도 및 엔터티를 파생할 수 있습니다.*

\** *이러한 서비스는 cris.ai 끝점을 사용하여 이용할 수 있습니다.  [Swagger 참조](https://westus.cris.ai/swagger/ui/index)를 확인합니다.*

## <a name="get-started-with-speech-to-text"></a>음성-텍스트 시작하기

각각 10분 이내에 코드를 실행할 수 있게 디자인된 가장 인기 있는 프로그래밍 언어로 빠른 시작을 제공합니다. [이 표에](https://aka.ms/csspeech#5-minute-quickstarts) 는 platfrom.details.heap.alignedallocate 및 language로 구성 된 음성 SDK 퀵 스타트의 전체 목록이 포함 되어 있습니다.  API 참조는 [여기](https://aka.ms/csspeech#reference)에서 찾을 수 있습니다.

음성-텍스트 REST 서비스를 사용하는 것을 선호하는 경우, [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)를 확인합니다.

## <a name="tutorials-and-sample-code"></a>자습서 및 샘플 코드

Speech Services를 사용해본 경우 Speech SDK 및 LUIS를 사용하여 음성에서 의도를 인식하는 방법을 설명하는 자습서를 사용해 보세요.

* [자습서: Speech SDK 및 LUIS, C#을 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)

Speech SDK에 대한 예제 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 단발성 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다.

* [음성 텍스트 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>사용자 지정

음성 서비스에서 사용 하는 표준 기준 모델 외에도 사용 가능한 데이터로 모델을 사용자 지정 하 여 말하는 스타일, 어휘 및 배경 소음과 같은 음성 인식 장벽을 극복할 수 있습니다 [Custom Speech](how-to-custom-speech.md)

> [!NOTE]
> 언어/로캘마다 사용자 지정 옵션이 다릅니다([지원되는 언어](supported-languages.md) 참조).

## <a name="migration-guides"></a>마이그레이션 가이드

> [!WARNING]
> Bing Speech는 2019 년 10 월 15 일에 서비스 해제 됩니다.

응용 프로그램, 도구 또는 제품이 Bing Speech API 또는 Custom Speech를 사용하는 경우, 음성 서비스로 마이그레이션할 수 있도록 가이드를 만들었습니다.

* [Bing Speech에서 Speech Services로 마이그레이션](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Custom Speech에서 Speech Services로 마이그레이션](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](https://aka.ms/csspeech)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [음성 SDK 가져오기](speech-sdk.md)
