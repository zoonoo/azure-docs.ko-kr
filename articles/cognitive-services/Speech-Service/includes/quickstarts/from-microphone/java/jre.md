---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: c2b844b3b1aa7a848e2668b3dca5a1cac5b5baaf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400670"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.

## <a name="source-code"></a>소스 코드

Java 프로젝트에 새로운 빈 클래스를 추가하려면 **파일** > **새로 만들기** > **클래스**를 선택합니다. **새 Java 클래스** 창에서, **패키지** 필드에 **speechsdk.quickstart**를 입력하고, **이름** 필드에 **기본**을 입력합니다.

![새 Java 클래스 창의 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

*Main.java* 파일의 콘텐츠를 다음 코드 조각으로 바꿉니다.

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>코드 설명

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>앱 빌드 및 실행

<kbd>F11</kbd>을 누르거나 **실행** > **디버그**를 선택합니다.
마이크에서 나오는 다음 15초 음성 입력이 인식되어 콘솔 창에 기록됩니다.

![인식에 성공한 후의 콘솔 출력 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

