---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806181"
---
## <a name="enable-microphone"></a>마이크 사용

PC 마이크를 연결하여 켜고 마이크를 사용할 수도 있는 앱을 해제합니다. 일부 컴퓨터에는 기본 제공 마이크가 있지만 다른 컴퓨터에는 Bluetooth 디바이스를 구성해야 합니다.

## <a name="run-the-speech-cli"></a>Speech CLI 실행

이제 음성 CLI를 실행하여 마이크의 음성을 인식할 준비가 되었습니다.

1. **앱 시작** - 명령줄에서 Speech CLI 이진 파일이 포함된 디렉터리로 변경하고 다음을 입력합니다.
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Speech CLI의 기본값은 영어입니다. [음성 텍스트 변환 테이블](../../../../language-support.md)에서 다른 언어를 선택할 수 있습니다.
    > 예를 들어 독일어 음성을 인식하도록 `--source de-DE`를 추가합니다.

2. **인식 시작** - 마이크에 말하세요. 실시간으로 단어를 텍스트로 전사하는 것을 볼 수 있습니다. 음성 CLI는 일정 시간 음소거 후 또는 ctrl-C를 누를 때 중지됩니다.
