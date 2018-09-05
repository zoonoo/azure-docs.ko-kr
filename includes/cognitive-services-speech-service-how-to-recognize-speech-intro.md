---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43129350"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)는 전체 기능과 함께 응용 프로그램에서 **음성 텍스트 변환**을 사용하는 가장 간단한 방법을 제공합니다.

1. 음성 팩터리를 만들고 Speech Service 구독 키(또는 권한 부여 토큰)와 [지역](~/articles/cognitive-services/speech-service/regions.md)을 매개 변수로 제공합니다. 사용자 지정 엔드포인트를 제공하여 비표준 서비스 엔드포인트를 지정할 수도 있습니다.

1. 음성 팩터리에서 음성 인식기를 가져옵니다. 입력 언어와 출력 형식을 구성할 수 있습니다. 인식기는 장치의 기본 마이크, 오디오 스트림 또는 파일의 오디오를 사용할 수 있습니다.

1. 필요한 경우, 비동기 작업에 대한 이벤트를 연결합니다. 중간 및 최종 결과가 있으면 인식기가 이벤트 처리기를 호출합니다. 그렇지 않을 경우 응용 프로그램은 최종 전사 결과만 받습니다.

1. 인식을 시작합니다. 명령 또는 쿼리 인식과 같은 1단계 인식의 경우 `RecognizeAsync()` 메서드를 사용합니다. 이 메서드는 처음 인식된 발언을 반환합니다. 전사와 같은 장기 실행 인식의 경우 `StartContinuousRecognitionAsync()` 메서드를 사용합니다. 비동기 인식 결과 대한 이벤트를 연결합니다.

Speech SDK를 사용하는 음성 인식 시나리오는 다음 코드 조각을 참조하십시오.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
