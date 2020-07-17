---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806489"
---
## <a name="run-the-speech-cli"></a>Speech CLI 실행

이제 Speech CLI를 실행하여 음성을 두 가지 다른 언어로된 텍스트로 변환할 준비가 되었습니다.

명령줄에서 Speech CLI 이진 파일이 포함된 디렉터리로 변경하고 다음을 입력합니다.

```bash
spx translate --microphone --target de-DE --target es-MX
```

Speech CLI는 자연어 구어체 영어를 독일어 및 (멕시코) 스페인어로 출력된 텍스트로 변환합니다.
도구를 중지하려면 ENTER를 누릅니다.

> [!NOTE]
> Speech CLI의 기본값은 영어입니다. [음성 텍스트 변환 테이블](../../../../language-support.md)에서 다른 언어를 선택할 수 있습니다.
> 예를 들어 일본어 음성을 인식하도록 `--source ja-JP`를 추가합니다.
