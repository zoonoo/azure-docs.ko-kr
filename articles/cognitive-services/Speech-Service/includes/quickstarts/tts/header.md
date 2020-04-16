---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400422"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 텍스트를 합성된 음성으로 변환합니다. 텍스트 음성 변환 서비스는 [텍스트 음성 변환 언어 지원](../../../language-support.md#text-to-speech)에 따라 합성된 음성을 위한 다양한 옵션을 제공합니다. 몇 가지 필수 구성 요소를 충족한 후 합성된 음성을 기본 스피커로 렌더링하는 작업은 다음 네 개의 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 `SpeechConfig` 개체를 만듭니다.
> * 위의 `SpeechConfig` 개체를 사용하여 `SpeechSynthesizer` 개체를 만듭니다.
> * `SpeechSynthesizer` 개체를 사용하여 텍스트를 말합니다.
> * 오류에 대해 반환된 `SpeechSynthesisResult`를 확인합니다.
