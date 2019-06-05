---
title: Translator Speech API에서 Speech Service로 마이그레이션
titleSuffix: Azure Cognitive Services
description: Translator Speech API에서 음성 서비스에 응용 프로그램을 마이그레이션하는 방법에 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 1ed494cea1ccf8845a25a3ab49d3194cc6a55509
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "65785671"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Translator Speech API에서 Speech Service로 마이그레이션

이 문서를 사용하여 Microsoft Translator Speech API에서 [Speech Service](index.yml)로 애플리케이션을 마이그레이션합니다. 이 가이드에서는 Translator Speech API와 Speech Service 간의 차이점을 간략하게 설명하고 애플리케이션을 마이그레이션하기 위한 전략을 제안합니다.

> [!NOTE]
> Translator Speech API 구독 키는 Speech Service에서 허용되지 않습니다. 새 음성 서비스 구독을 만드는 해야 합니다.

## <a name="comparison-of-features"></a>기능 비교

| 기능                                           | Translator Speech API                                  | Speech Services | 세부 정보                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 텍스트로 번역                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 음성으로 번역                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 전역 엔드포인트                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 음성 서비스 전역 엔드포인트를 제공 하지 않습니다. 전역 엔드포인트는 트래픽을 가장 가까운 지역 엔드포인트로 자동으로 전송하여 애플리케이션의 대기 시간을 줄일 수 있습니다.                                                    |
| 지역별 엔드포인트                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 연결 시간 제한                             | 90분                                               | SDK를 사용할 경우 무제한 WebSocket 연결을 사용할 경우 10분                                                                                                                                                                                                                                                                                   |
| 헤더의 인증 키                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 단일 요청을 통해 여러 언어 번역 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK 사용 가능                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 참조 된 [음성 서비스 설명서](index.yml) 사용할 수 있는 Sdk에 대 한 합니다.                                                                                                                                                    |
| WebSocket 연결                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Languages API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 음성 서비스는 같은 범위에 설명 된 언어를 지원 합니다 [Translator API 언어 참조](../translator-speech/languages-reference.md) 문서. |
| 욕설 필터 및 표식                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM을 입력으로 사용                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 다른 파일 형식을 입력으로 사용                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 부분 결과                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 타이밍 정보                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 상관관계 ID                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Custom Speech 모델                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Speech Services는 조직의 고유한 어휘로 음성 인식 기능을 사용자 지정할 수 있도록 하는 사용자 지정 음성 모델을 제공 합니다.                                                                                                                                           |
| 사용자 지정 번역 모델                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Microsoft Text Translation API를 구독하면 [Custom Translator](https://www.microsoft.com/translator/business/customization/)를 사용하여 보다 정확한 번역을 위해 자체 데이터를 사용할 수 있습니다.                                                 |

## <a name="migration-strategies"></a>마이그레이션 전략

사용자 또는 조직의 개발 또는 프로덕션 환경에 Translator Speech API를 사용하는 애플리케이션이 있는 경우 Speech Service를 사용하도록 업데이트해야 합니다. 사용 가능한 SDK, 코드 샘플 및 자습서에 대해서는 [Speech Service](index.yml) 설명서를 참조하세요. 마이그레이션하는 경우 다음을 고려합니다.

* 음성 서비스 전역 엔드포인트를 제공 하지 않습니다. 애플리케이션이 모든 해당 트래픽에 대해 단일 지역별 엔드포인트를 사용할 때 효율적으로 작동하는지를 확인합니다. 그렇지 않을 경우 지리적 위치를 사용하여 가장 효율적인 엔드포인트를 확인합니다.

* 애플리케이션이 수명이 긴 연결을 사용하고 사용 가능한 SDK를 사용할 수 없는 경우 WebSocket 연결을 사용할 수 있습니다. 적절한 시간에 다시 연결하여 10분 시간 제한을 관리합니다.

* 애플리케이션이 Translator Text API 및 Translator Speech API를 사용하여 사용자 지정 번역 모델을 사용하도록 설정하는 경우 Speech Service를 사용하여 범주 ID를 직접 추가할 수 있습니다.

* Translator Speech API와 달리 음성 서비스는 단일 요청에서 여러 언어로 번역을 완료할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [음성 서비스를 무료로 체험](get-started.md)
* [빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 인식](quickstart-csharp-uwp.md)

## <a name="see-also"></a>참고 항목

* [Speech Service란](overview.md)
* [음성 서비스 및 Speech SDK 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
