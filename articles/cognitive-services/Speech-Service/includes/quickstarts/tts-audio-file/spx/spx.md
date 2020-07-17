---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806333"
---
## <a name="run-the-speech-cli"></a>Speech CLI 실행

이제 Speech CLI를 실행하여 텍스트에서 새 오디오 파일로 음성을 합성할 준비가 되었습니다.

명령줄에서 Speech CLI 이진 파일이 포함된 디렉터리로 변경하고 다음을 입력합니다.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Speech CLI는 `greetings.wav` 오디오 파일에 영어로 자연어를 생성합니다.
Windows에서는 `start greetings.wav`를 입력하여 오디오 파일을 재생할 수 있습니다.
