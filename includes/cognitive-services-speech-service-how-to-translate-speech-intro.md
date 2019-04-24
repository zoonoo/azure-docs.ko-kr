---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 09/24/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: ab4795325f733d15403b53f027daa4fb8c824cc6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531017"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)는 애플리케이션에서 **음성 번역**을 사용하는 가장 간단한 방법을 제공합니다.
SDK는 서비스의 전체 기능을 제공합니다. 음성 번역을 수행하기 위한 기본 프로세스에는 다음 단계가 포함됩니다.

1. 음성 번역 구성을 만들고 Speech Service 구독 키(또는 권한 부여 토큰) 및 [지역](~/articles/cognitive-services/speech-service/regions.md)을 매개 변수로 제공합니다. 필요에 따라 구성을 변경합니다. 예를 들어 원본 및 대상 번역 언어를 구성할 수 있으며 텍스트 또는 음성 출력을 원하는지 여부도 지정할 수 있습니다.

1. 음성 번역 구성에서 번역 인식기를 만듭니다. 기본 마이크 이외의 원본에서 인식하는 경우(예: 오디오 스트림 또는 오디오 파일) 오디오 구성을 제공합니다.

1. 필요한 경우, 비동기 작업에 대한 이벤트를 연결합니다. 인식기는 중간 및 최종 결과가 있을 때 이벤트 처리기를 호출하고 선택 사항인 오디오 출력에 대한 합성 이벤트도 호출합니다. 그렇지 않을 경우 애플리케이션은 최종 전사 결과만 받습니다.

1. 인식을 시작합니다. 1단계 번역에는 처음 인식된 발언을 반환하는 `RecognizeOnceAsync()` 메서드를 사용합니다. 오래 실행되는 번역의 경우 `StartContinuousRecognitionAsync()` 메서드를 사용하고 비동기 인식 결과 대한 이벤트를 연결합니다.

Speech SDK를 사용하는 음성 번역 시나리오는 다음 코드 조각을 참조하십시오.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
