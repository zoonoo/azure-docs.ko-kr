---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421820"
---
## <a name="speech-modes"></a>음성 모드

**대화형**
- 명령 및 제어 시나리오를 위한 것입니다.
- Has a segmentation time out value of X.
- 인식된 발언이 끝나면 서비스는 해당 요청 ID에서 오디오 처리를 중지하고 턴을 종료합니다. 연결이 닫히지 않았습니다.
- 최대 인식 한도는 20대입니다.
- 호출하는 일반적인 탄소 호출은 `RecognizeOnceAsync`.

**대화**
- 더 긴 실행 인식을 위한 것입니다.
- Y.의 분할 시간 시간 시간 부족 값(Y!= X)이 있습니다.
- 턴을 종료하지 않고 여러 개의 완전한 발언을 처리합니다.
- 너무 많은 침묵에 대한 차례를 종료합니다.
- 카본은 필요에 따라 새로운 요청 ID와 재생 오디오를 계속합니다.
- 음성 인식 10분 후에 서비스가 강제로 연결이 끊어집니다.
- 카본은 승인되지 않은 오디오를 다시 연결하고 재생합니다.
- `StartContinuousRecognition`와 탄소에서 호출 .

**받아쓰기**
- 사용자가 구두점을 말하여 지정할 수 있습니다.
- 인식을 시작하는 API 호출에 `SpeechConfig` 관계없이 개체를 지정하여 `EnableDictation` Carbon에서 호출되었습니다.
- 1st<sup>st</sup> 파티 클러스터는 중간 결과,<sup>3rd</sup> `speech.hypothesis` 파티 반환 메시지에 대한 메시지를 반환합니다. `speech.fragment`