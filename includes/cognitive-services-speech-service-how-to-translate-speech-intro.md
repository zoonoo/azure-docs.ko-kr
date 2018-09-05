---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144603"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)는 응용 프로그램에서 **음성 번역**을 사용하는 가장 간단한 방법을 제공합니다.
SDK는 서비스의 전체 기능을 제공합니다. 음성 번역을 수행하기 위한 기본 프로세스에는 다음 단계가 포함됩니다.

1. 음성 팩터리를 만들고 Speech Service 구독 키(또는 권한 부여 토큰)와 [지역](~/articles/cognitive-services/speech-service/regions.md)을 매개 변수로 제공합니다.
   
1. 음성 팩터리에서 번역 인식기를 만듭니다. 원본 및 대상 번역 언어를 구성할 수 있고 텍스트 또는 음성 출력을 원하는지 여부도 지정할 수 있습니다. 사용하는 오디오 소스를 기반으로 다양한 번역 인식기를 선택할 수 있습니다.

1. 필요한 경우, 비동기 작업에 대한 이벤트를 연결합니다. 인식기는 중간 및 최종 결과가 있을 때 이벤트 처리기를 호출하고 선택 사항인 오디오 출력에 대한 합성 이벤트도 호출합니다. 그렇지 않을 경우 응용 프로그램은 최종 전사 결과만 받습니다.

1. 인식을 시작합니다. 1단계 번역에는 처음 인식된 발언을 반환하는 `RecognizeAsync()` 메서드를 사용합니다. 오래 실행되는 번역의 경우 `StartContinuousRecognitionAsync()` 메서드를 사용하고 비동기 인식 결과 대한 이벤트를 연결합니다.

Speech SDK를 사용하는 음성 번역 시나리오는 다음 코드 조각을 참조하십시오.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
