---
title: Bing Speech에서 Speech Service로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 개발자 관점에서 Bing Speech와 Speech Service 간의 차이점을 알아보고 Speech Service를 사용하도록 응용 프로그램을 마이그레이션합니다.
services: cognitive-services
author: wsturman
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: baf9b9cd9b3f57c1d708dd404d59c036df6c169f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466650"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing Speech에서 Speech Service로 마이그레이션

이 문서를 사용하여 Bing Speech API에서 Speech Service로 응용 프로그램을 마이그레이션합니다.

이 문서에서는 Bing Speech API와 Speech Service 간의 차이점을 간략하게 설명하고 응용 프로그램을 마이그레이션하기 위한 전략을 제안합니다. Bing Speech API 구독 키는 Speech Service에서 수락되지 않으므로 새 Speech Service 구독이 필요합니다.

단일 Speech Service 구독 키는 다음 기능에 대한 액세스 권한을 부여합니다. 각 기능은 별도로 측정되므로 사용하는 기능에 대해서만 요금이 청구됩니다.

* [Speech-to-text](speech-to-text.md)
* [사용자 지정 음성 텍스트 변환](https://cris.ai)
* [Text-to-speech](text-to-speech.md)
* [사용자 지정 텍스트 음성 변환](how-to-customize-voice-font.md)
* [음성 번역](speech-translation.md)([텍스트 번역](../translator/translator-info-overview.md) 제외)

[Speech SDK](speech-sdk.md) 는 기능 측면에서 Bing Speech 클라이언트 라이브러리를 대신하지만 다른 API를 사용합니다.

## <a name="comparison-of-features"></a>기능 비교

Speech Service는 다음 차이점을 제외하고 Bing Speech와 기능, 플랫폼 및 프로그래밍 언어 측면에서 대체적으로 비슷합니다.

기능 | Bing Speech | Speech Service | 세부 정보
-|-|-|-
C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Speech Service는 Windows와 Linux를 지원합니다.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service는 Android와 음성 장치를 지원합니다.
C# SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service는 Windows 10, UWP 및 .NET Standard 2.0을 지원합니다.
연속 음성 인식 | 10분 | 무제한(SDK 사용) | Bing Speech와 Speech Service WebSocket 프로토콜은 호출당 최대 10분을 지원합니다. 그러나 Speech SDK는 시간 초과 또는 연결 해제 시 자동으로 다시 연결합니다.
부분 또는 중간 결과 | :heavy_check_mark: | :heavy_check_mark: | WebSocket 프로토콜 또는 SDK 사용
Custom Speech 모델 | :heavy_check_mark: | :heavy_check_mark: | Bing Speech에는 별도의 Custom Speech 구독이 필요합니다.
사용자 지정 음성 글꼴 | :heavy_check_mark: | :heavy_check_mark: | Bing Speech에는 별도의 Custom Voice 구독이 필요합니다.
24kHz 음성 | :heavy_minus_sign: | :heavy_check_mark: 
음성 의도 인식 | 별도 LUIS API 호출이 필요합니다. | 통합형(SDK 사용) |  Speech Service에서 LUIS 키를 사용할 수 있습니다.
간단한 의도 인식 | :heavy_minus_sign: | :heavy_check_mark: 
긴 오디오 파일의 일괄 처리 기록 | :heavy_minus_sign: | :heavy_check_mark:
인식 모드 | 엔드포인트 URI 통해 수동 | 자동 | 인식 모드를 Speech Service에서 사용할 수 없습니다.
엔드포인트 위치 | 전역 | 지역 | 지역별 엔드포인트가 대기 시간을 향상시킵니다. 전역 엔드포인트는 Speech Service에서 고려 중입니다.
REST API | :heavy_check_mark: | :heavy_check_mark: | Speech Service REST API는 Bing Speech(다른 엔드포인트)와 호환됩니다. REST API는 텍스트 음성 변환 및 제한된 음성 텍스트 변환 기능을 지원합니다.
WebSocket 프로토콜 | :heavy_check_mark: | :heavy_check_mark: | Speech Service WebSockets API는 Bing Speech(다른 엔드포인트)와 호환됩니다. 코드를 단순화하기 위해 가능한 경우 Speech SDK로 마이그레이션합니다.
서비스 간 API 호출 | :heavy_check_mark: | :heavy_minus_sign: | C# 서비스 라이브러리를 통해 Bing Speech에 제공됩니다. 
오픈 소스 SDK | :heavy_check_mark: | :heavy_minus_sign: |

Speech Service는 트랜잭션 기반 모델 대신 시간 기반 가격 책정 모델을 사용합니다. 자세한 내용은 [Speech Service 가격](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

## <a name="migration-strategies"></a>마이그레이션 전략

사용자 또는 조직의 개발 또는 프로덕션 환경에 Bing Speech API를 사용하는 응용 프로그램이 있는 경우 최대한 빨리 Speech Service를 사용하도록 업데이트해야 합니다. 사용 가능한 SDK, 코드 샘플 및 자습서에 대해서는 [Speech Service](index.yml) 설명서를 참조하세요.

Speech Service [REST API](rest-apis.md)는 Bing Speech API와 호환됩니다. 현재 Bing Speech REST API를 사용 중인 경우 REST 엔드포인트만 변경하고 Speech Service 구독 키로 전환해야 합니다.

또한 Speech Service WebSocket 프로토콜도 Bing Speech에서 사용되는 프로토콜과 호환됩니다. 새 개발은 WebSocket을 사용하지 않고 Speech Service SDK를 타기팅하는 것이 좋으며, 기존 코드도 SDK로 마이그레이션하는 것이 좋습니다. 그러나 REST API와 마찬가지로, WebSocket을 통해 Bing Speech를 사용하는 기존 코드에서는 엔드포인트와 업데이트된 키만 변경하면 됩니다.

특정 프로그래밍 언어에 대해 Bing Speech 클라이언트 라이브러리를 사용하는 경우 API가 다르기 때문에 [Speech SDK](speech-sdk.md)에서 사용 중인 응용 프로그램으로 변경해야 합니다. Speech SDK는 새 기능에 액세스하면서 코드를 좀 더 간단히 만들어줄 수 있습니다.

현재, Speech SDK는 C#(Windows 10, UWP, .NET Standard), Java(Android 및 사용자 지정 장치), Objective C(iOS), C++(Windows 및 Linux), JavaScript를 지원합니다. 모든 플랫폼의 API는 비슷하며, 다중 플랫폼 개발을 용이하게 수행할 수 있습니다.

Speech Service는 현재 전역 엔드포인트를 제공하지 않습니다. 응용 프로그램모든 트래픽에 대해 단일 지역별 엔드포인트를 사용하여 효율적으로 작동하는지를 확인해야 합니다. 그렇지 않을 경우 지리적 위치를 사용하여 가장 효율적인 엔드포인트를 확인합니다. 사용하는 각 지역에 별도 Speech Service 구독이 필요합니다.

응용 프로그램 수명이 긴 연결을 사용하며 사용 가능한 SDK를 사용할 수 없는 경우 WebSocket 연결을 사용하고, 적절한 시간에 다시 연결하여 10분간의 시간 제한을 관리할 수 있습니다.

Speech Devices SDK를 시작하려면

1. [Speech SDK](speech-sdk.md)를 다운로드합니다.
1. Speech Service [빠른 시작 가이드](quickstart-csharp-dotnet-windows.md), [자습서](how-to-recognize-intents-from-speech-csharp.md)를 통해 작업하고 [코드 샘플](samples.md)을 확인하여 새 API를 사용해봅니다.
1. Speech Service 및 API를 사용하도록 응용 프로그램을 업데이트합니다.

## <a name="support"></a>지원

Bing Speech 고객은 [지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 개설하여 고객 지원 서비스에 문의해야 합니다. 지원을 받기 위해 [기술 지원 요금제](https://azure.microsoft.com/support/plans/)가 필요한 경우에도 저희에게 연락하세요.

Speech Service, SDK 및 API 지원이 필요한 경우, Speech Service [지원 페이지](support.md)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Speech Service 체험해 보기](get-started.md)
* [빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 인식](quickstart-csharp-uwp.md)

## <a name="see-also"></a>참고 항목
* [Speech Service 릴리스 정보](releasenotes.md)
* [Speech Service란?](overview.md)
* [Speech Service 및 SDK 설명서](speech-sdk.md#get-the-sdk)
