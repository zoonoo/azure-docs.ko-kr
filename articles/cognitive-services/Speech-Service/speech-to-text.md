---
title: 음성을 텍스트로 변환 정보 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech-to-Text API는 오디오 스트림을 앱에서 표시하거나 입력으로 작동할 수 있는 텍스트로 기록합니다. 서비스는 SDK 및 RESTful 엔드포인트를 통해 사용할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5012245a79295f1e05079f6c0a368ac832b8974a
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978581"
---
# <a name="about-the-speech-to-text-api"></a>Speech to Text API 정보

**Speech to Text**는 오디오 스트림을 애플리케이션에서 사용자에게 표시하거나 명령 입력으로 작동할 수 있는 텍스트로 *기록*합니다. 이 API는 SDK 클라이언트 라이브러리(지원되는 플랫폼 및 언어용) 또는 REST API와 함께 사용할 수 있습니다.

**Speech to Text** API는 다음과 같은 기능을 제공합니다.

- Cortana, Office 및 기타 Microsoft 제품에서 사용하는 것과 동일한 Microsoft의 고급 음성 인식 기술

- 실시간 연속 인식. **Speech to Text**를 사용하여 오디오를 실시간으로 텍스트로 기록할 수 있습니다. 또한 지금까지 인식된 단어의 중간 결과도 수신할 수 있습니다. 이 서비스는 음성의 끝을 자동으로 인식합니다. 사용자는 대문자 표시 및 문장 부호, 욕설 마스킹 및 역 텍스트 정규화와 같은 추가 형식 지정 옵션을 선택할 수도 있습니다.

- 어휘 및 표시 양식으로 모두 반환됩니다(어휘 결과는 예제 또는 API에서 DetailedSpeechRecognitionResult 참조).

- 여러 음성 언어 및 언어 지원. 각 인식 모드에서 지원되는 언어의 전체 목록은 [지원되는 언어](language-support.md#speech-to-text)를 참조하세요.

- 사용자 지정된 언어 및 음향 모델: 애플리케이션을 사용자의 전문 영역 어휘, 말하는 환경 및 말하는 방식에 맞게 조정할 수 있습니다.

- 자연어 이해 LUIS([Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/))와 통합하여 음성에서 의도 및 엔터티를 파생할 수 있습니다. 사용자는 앱 어휘를 알 필요가 없지만 원하는 내용을 자신만의 단어로 설명할 수 있습니다.

- 신뢰성 점수는 음성 구성 개체 (SpeechConfig.OutputFormat 속성)에 대한 자세한 출력을 지정 하는 경우 서비스에서 다시 반환됩니다. 그러면 결과에 대해 Best() 메서드를 사용하거나 서비스에서 반환된 JSON에서 직접 점수를 가져올 수 있습니다(result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)와 같은 것).

## <a name="api-capabilities"></a>API 기능

**음성 텍스트 변환** API의 일부 기능(특히 사용자 지정 기능)은 REST를 통해 사용할 수 있습니다. 다음 표에서는 API에 액세스하는 각 방법의 기능을 요약해서 설명합니다. 기능 및 API의 세부 정보에 대한 전체 목록은 [Swagger 참조](https://westus.cris.ai/swagger/ui/index)를 참조하세요.

| 사용 사례 | REST (영문) | SDK |
|-----|-----|-----|----|
| 명령과 같은 짧은 발언을 기록합니다(길이 15초 미만). 임시 결과는 없음 | 예 | 예 |
| 좀 더 긴 발언을 기록합니다(15초 초과). | 아니요 | 예 |
| 선택적 중간 결과를 포함하여 스트리밍 오디오 기록 | 아니요 | 예 |
| LUIS를 통해 화자의 의도 이해 | 아니요\* | 예 |
| 정확도 테스트 만들기 | 예 | 아니요 |
| 모델 적응에 대한 데이터 세트 업로드 | 예 | 아니요 |
| 음성 모델 만들기 및 관리 | 예 | 아니요 |
| 모델 배포 만들기 및 관리 | 예 | 아니요 |
| 구독 관리 | 예 | 아니요 |
| 모델 배포 만들기 및 관리 | 예 | 아니요 |
| 모델 배포 만들기 및 관리 | 예 | 아니요 |

> [!NOTE]
> REST API는 API 요청 수를 5초당 25개로 제한하는 제한을 구현합니다. 메시지 헤더를 통해 이 제한을 알 수 있습니다.

\* *LUIS 의도 및 엔터티는 별도의 LUIS 구독을 사용하여 파생시킬 수 있습니다. 이 구독에서 SDK는 LUIS를 호출하고, 음성 기록 뿐만 아니라 엔터티 및 의도를 제공할 수 있습니다. REST API를 사용하면 LUIS 구독을 통해 LUIS를 직접 호출하고 의도 및 엔터티를 파생할 수 있습니다.*

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [빠른 시작: C#에서 음성 인식](quickstart-csharp-dotnet-windows.md)
* [C#에서 음성 의도를 인식하는 방법 알아보기](how-to-recognize-intents-from-speech-csharp.md)
