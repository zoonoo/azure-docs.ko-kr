---
title: Bing 음성에서 음성 서비스로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 기존 Bing Speech 구독에서 Azure Cognitive 서비스에서 음성 서비스로 마이그레이션하는 방법을 알아봅니다.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 7b78bdb070cdf1364fe7fbdc75f175be7ce145ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656461"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing 음성에서 음성 서비스로 마이그레이션

이 문서를 사용하여 Bing 음성 API에서 음성 서비스로 응용 프로그램을 마이그레이션합니다.

이 문서에서는 Bing 음성 API와 음성 서비스 간의 차이점을 간략하게 설명하고 응용 프로그램을 마이그레이션하기 위한 전략을 제안합니다. Bing Speech API 구독 키는 음성 서비스와 함께 작동하지 않습니다. 새 음성 서비스 구독이 필요합니다.

단일 음성 서비스 구독 키는 다음 기능에 대한 액세스 권한을 부여합니다. 각 기능은 별도로 측정되므로 사용하는 기능에 대해서만 요금이 청구됩니다.

* [음성 텍스트 변환](speech-to-text.md)
* [음성 텍스트 변환 사용자 지정](https://cris.ai)
* [텍스트 음성 변환](text-to-speech.md)
* [사용자 지정 텍스트 음성 변환](how-to-customize-voice-font.md)
* [음성 번역](speech-translation.md)([텍스트 번역](../translator/translator-info-overview.md) 제외)

[Speech SDK](speech-sdk.md) 는 기능 측면에서 Bing Speech 클라이언트 라이브러리를 대신하지만 다른 API를 사용합니다.

## <a name="comparison-of-features"></a>기능 비교

음성 서비스는 Bing 스피치와 크게 유사하며 다음과 같은 차이점이 있습니다.

| 기능 | Bing Speech | Speech Service | 세부 정보 |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | 음성 서비스는 Windows 10, 범용 Windows 플랫폼(UWP) 및 .NET 표준 2.0을 지원합니다. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | 음성 서비스는 윈도우와 리눅스를 지원합니다. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | 음성 서비스는 안드로이드 및 음성 장치를 지원합니다. |
| 연속 음성 인식 | 10분 | 무제한(SDK 사용) | Bing 음성 및 음성 서비스 WebSockets 프로토콜은 통화당 최대 10분까지 지원합니다. 그러나 Speech SDK는 시간 초과 또는 연결 해제 시 자동으로 다시 연결합니다. |
| 부분 또는 중간 결과 | :heavy_check_mark: | :heavy_check_mark: | WebSocket 프로토콜 또는 SDK 사용. |
| Custom Speech 모델 | :heavy_check_mark: | :heavy_check_mark: | Bing Speech에는 별도의 Custom Speech 구독이 필요합니다. |
| 사용자 지정 음성 글꼴 | :heavy_check_mark: | :heavy_check_mark: | Bing Speech에는 별도의 Custom Voice 구독이 필요합니다. |
| 24kHz 음성 | :heavy_minus_sign: | :heavy_check_mark: |
| 음성 의도 인식 | 별도 LUIS API 호출이 필요합니다. | 통합형(SDK 사용) | 음성 서비스에서 LUIS 키를 사용할 수 있습니다. |
| 간단한 의도 인식 | :heavy_minus_sign: | :heavy_check_mark: |
| 긴 오디오 파일의 일괄 처리 기록 | :heavy_minus_sign: | :heavy_check_mark: |
| 인식 모드 | 엔드포인트 URI 통해 수동 | 자동 | 음성 서비스에서 인식 모드를 사용할 수 없습니다. |
| 엔드포인트 위치 | Global | 지역 | 지역별 엔드포인트가 대기 시간을 향상시킵니다. |
| REST API | :heavy_check_mark: | :heavy_check_mark: | 음성 서비스 REST API는 Bing 음성(다른 끝점)과 호환됩니다. REST API는 텍스트 음성 변환 및 제한된 음성 텍스트 변환 기능을 지원합니다. |
| WebSocket 프로토콜 | :heavy_check_mark: | :heavy_check_mark: | 음성 서비스 WebSockets API는 Bing 음성(다른 끝점)과 호환됩니다. 코드를 단순화하기 위해 가능한 경우 Speech SDK로 마이그레이션합니다. |
| 서비스 간 API 호출 | :heavy_check_mark: | :heavy_minus_sign: | C# 서비스 라이브러리를 통해 Bing Speech에 제공됩니다. |
| 오픈 소스 SDK | :heavy_check_mark: | :heavy_minus_sign: |

음성 서비스는 트랜잭션 기반 모델이 아닌 시간 기반 가격 책정 모델을 사용합니다. 자세한 내용은 [음성 서비스 가격을](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 참조하십시오.

## <a name="migration-strategies"></a>마이그레이션 전략

사용자 또는 조직에 Bing Speech API를 사용하는 개발 또는 프로덕션 응용 프로그램이 있는 경우 가능한 한 빨리 음성 서비스를 사용하도록 업데이트해야 합니다. 사용 가능한 SDK, 코드 샘플 및 자습서에 대한 [음성 서비스 설명서를](index.yml) 참조하십시오.

음성 서비스 [REST API는](rest-apis.md) Bing 음성 API와 호환됩니다. 현재 Bing 음성 REST API를 사용하는 경우 REST 끝점만 변경하고 음성 서비스 구독 키로 전환하면 됩니다.

음성 서비스 WebSockets 프로토콜은 Bing Speech에서 사용하는 프로토콜과도 호환됩니다. 새로 개발할 때에는 Websocket보다 Speech SDK를 사용하는 것이 좋습니다. 기존 코드를 SDK로 마이그레이션하는 것도 좋은 생각입니다. 그러나 REST API와 마찬가지로, WebSocket을 통해 Bing Speech를 사용하는 기존 코드에서는 엔드포인트와 업데이트된 키만 변경하면 됩니다.

특정 프로그래밍 언어에 대해 Bing Speech 클라이언트 라이브러리를 사용하는 경우 API가 다르기 때문에 [Speech SDK](speech-sdk.md)에서 사용 중인 애플리케이션으로 변경해야 합니다. Speech SDK는 새 기능에 액세스하면서 코드를 좀 더 간단히 만들어줄 수 있습니다. 스피치 SDK는 다양한 프로그래밍 언어로 제공됩니다. 모든 플랫폼의 API는 비슷하며, 다중 플랫폼 개발을 용이하게 수행할 수 있습니다.

음성 서비스는 전역 끝점을 제공하지 않습니다. 애플리케이션이 모든 해당 트래픽에 대해 단일 지역별 엔드포인트를 사용할 때 효율적으로 작동하는지를 확인합니다. 그렇지 않을 경우 지리적 위치를 사용하여 가장 효율적인 엔드포인트를 확인합니다. 사용하는 각 지역에서 별도의 음성 서비스 구독이 필요합니다.

애플리케이션에서 수명이 긴 연결을 사용하고 제공되는 SDK를 사용할 수 없는 경우 WebSocket 연결을 사용하면 됩니다. 적절한 시간에 다시 연결하여 10분 시간 제한을 관리합니다.

Speech Devices SDK를 시작하려면

1. [Speech SDK](speech-sdk.md)를 다운로드합니다.
1. 음성 서비스 [빠른 시작 가이드](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) 및 [자습서를](how-to-recognize-intents-from-speech-csharp.md)통해 작업 . 또한 [코드 샘플](samples.md)을 살펴보고 새 API를 경험합니다.
1. 음성 서비스를 사용하도록 응용 프로그램을 업데이트합니다.

## <a name="support"></a>지원

Bing Speech 고객은 [지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 개설하여 고객 지원 서비스에 문의해야 합니다. 지원을 받으려면 [기술 지원 플랜](https://azure.microsoft.com/support/plans/)이 필요한 경우에도 Microsoft로 연락 주시면 됩니다.

음성 서비스, SDK 및 API 지원의 경우 음성 서비스 [지원 페이지를](support.md)방문하십시오.

## <a name="next-steps"></a>다음 단계

* [무료 음성 서비스를 사용해보십시오.](get-started.md)
* [빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 인식](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>참고 항목
* [음성 서비스 릴리스 노트](releasenotes.md)
* [음성 서비스란?](overview.md)
* [음성 서비스 및 음성 SDK 문서](speech-sdk.md#get-the-speech-sdk)
