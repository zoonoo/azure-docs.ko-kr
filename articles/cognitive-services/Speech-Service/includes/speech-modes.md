---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81421820"
---
## <a name="speech-modes"></a>음성 모드

**대화형**
- 명령 및 제어 시나리오를 위한 것입니다.
- 의 조각화 시간 제한 값이 X입니다.
- 인식 된 utterance의 끝에서 서비스는 해당 요청 ID에서 오디오 처리를 중지 하 고 차례로 종료 합니다. 연결이 닫혀 있지 않습니다.
- 최대 인식 제한은 .20s입니다.
- 호출에 대 한 일반적인 요청은 `RecognizeOnceAsync` 입니다.

**대화창**
- 인식를 실행 하는 데 더 오래 걸립니다.
- 의 조각화 시간 제한 값은 Y입니다. (Y! = X)
- 는 길이 발언를 끝내 지 않고 여러 전체를 처리 합니다.
- 는 너무 많은 소리를 사용 하 게 됩니다.
- 필요에 따라 새 요청 ID로 계속 진행 하 고 오디오를 재생 합니다.
- 이 서비스는 음성 인식 10 분 후에 강제로 연결을 끊습니다.
- 참조는 승인 되지 않은 오디오를 다시 연결 하 고 재생 합니다.
- 를 사용 하 여 호출 `StartContinuousRecognition` 됩니다.

**받아쓰기**
- 사용자가 문장 부호를 지정 하 여 지정할 수 있습니다.
- `EnableDictation` `SpeechConfig` 인식을 시작 하는 API 호출에 관계 없이 개체에를 지정 하 여 참조에서 호출 됩니다.
- 1 개의<sup>st</sup> 파티 클러스터는 `speech.fragment` 중간 결과의 메시지를 반환 하 고, 3 개의<sup>rd</sup> 파티 반환 메시지를 반환 합니다 `speech.hypothesis` .