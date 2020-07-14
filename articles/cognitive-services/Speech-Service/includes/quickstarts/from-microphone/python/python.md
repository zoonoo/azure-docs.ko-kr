---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85839074"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 수행합니다.

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.

## <a name="source-code"></a>소스 코드

*quickstart.py*라는 파일을 만들고 다음 Python 코드를 붙여넣습니다.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>코드 설명

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>앱 빌드 및 실행

이제 Speech Service를 사용하여 음성 인식을 테스트할 준비가 되었습니다. 

macOS에서 이를 실행하고 마이크를 사용하는 첫 번째 Python 앱인 경우 터미널에 마이크에 대한 액세스 권한을 부여해야 합니다. **시스템 설정**을 열고 **보안 및 개인 정보**를 선택합니다. 그런 다음, **개인 정보**를 선택하고 목록에서 **마이크**를 찾습니다. 마지막으로 **터미널**을 선택하고 저장합니다. 

1. **앱 시작** - 명령줄에서 다음을 입력합니다.
    ```bash
    python quickstart.py
    ```
2. **인식 시작** - 영어로 문구를 말하라는 메시지가 표시됩니다. 음성은 Speech Service로 전송되어 텍스트로 변환되고 콘솔에 렌더링됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
