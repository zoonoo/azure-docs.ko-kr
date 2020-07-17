---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806270"
---
## <a name="find-a-file-that-contains-speech"></a>음성이 포함된 파일 찾기

Speech CLI는 다양한 파일 형식과 자연어로 음성을 인식할 수 있습니다. 이 빠른 시작에서는 영어 음성이 포함된 WAV 파일(16kHz 또는 8kHz, 16비트 및 모노 PCM)을 사용할 수 있습니다.

1. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a>를 다운로드합니다.
2. `whatstheweatherlike.wav` 파일을 Speech CLI 이진 파일과 동일한 디렉터리에 복사합니다.

## <a name="run-the-speech-cli"></a>Speech CLI 실행

이제 Speech CLI를 실행하여 사운드 파일에 있는 음석을 인식할 준비가 되었습니다.

명령줄에서 Speech CLI 이진 파일이 포함된 디렉터리로 변경하고 다음을 입력합니다.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Speech CLI의 기본값은 영어입니다. [음성 텍스트 변환 테이블](../../../../language-support.md)에서 다른 언어를 선택할 수 있습니다.
> 예를 들어 독일어 음성을 인식하도록 `--source de-DE`를 추가합니다.

Speech CLI는 화면에 음성의 텍스트 전사를 표시합니다. 그러면 음성 CLI가 닫힙니다.
