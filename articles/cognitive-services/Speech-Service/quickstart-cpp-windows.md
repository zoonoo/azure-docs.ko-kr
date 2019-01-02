---
title: '빠른 시작: 음성 인식, C++(Windows) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Speech Service SDK를 사용하여 Windows 데스크톱에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 454b84f4ba5e492f61daf67c980b204c82a2090d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075190"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows의 C++에서 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Windows용 C++ 콘솔 애플리케이션을 만듭니다. Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 실시간으로 PC 마이크의 음성을 텍스트로 변환할 수 있습니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017(모든 버전)로 빌드되었습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 음성 서비스 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [음성 서비스를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio 2017을 시작합니다.

1. **C++를 사용한 데스크톱 개발** 워크로드를 사용할 수 있도록 합니다. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 엽니다. 이 워크로드를 이미 사용하는 경우 다음 단계를 건너뜁니다.

    ![Visual Studio 워크로드 탭의 스크린샷](media/sdk/vs-enable-cpp-workload.png)

    그렇지 않으면 **C++를 사용한 데스크톱 개발** 옆에 있는 확인란을 선택합니다.

1. **NuGet 패키지 관리자** 구성 요소를 사용할 수 있도록 합니다. 아직 사용하도록 설정되지 않은 경우 Visual Studio 설치 관리자 대화 상자의 **개별 구성 요소** 탭으로 전환하고, **NuGet 패키지 관리자**를 선택합니다.

      ![Visual Studio 개별 구성 요소 탭의 스크린샷](media/sdk/vs-enable-nuget-package-manager.png)

1. C++ 워크로드 또는 NuGet을 사용하도록 설정해야 하는 경우 대화 상자의 오른쪽 아래 모서리에서 **수정**을 선택합니다. 새로운 기능을 설치하려면 잠시 시간이 걸립니다. 두 기능을 모두 이미 사용하도록 설정한 경우 대신 대화 상자를 닫습니다.

1. 새로운 Visual C++ Windows 데스크톱 창 콘솔 애플리케이션을 만듭니다. 먼저, 메뉴에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** > **Visual C++** > **Windows Desktop**을 확장합니다. 그런 다음, **Windows 콘솔 애플리케이션 만들기**를 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![새 프로젝트 대화 상자 스크린샷](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. 64비트 Windows를 실행하는 경우 Visual Studio 도구 모음에서 드롭다운 메뉴를 사용하여 빌드 플랫폼을 `x64`로 전환할 수 있습니다. (64비트 버전의 Windows에서는 32비트 애플리케이션을 실행할 수 있으므로 요구 사항은 아닙니다.)

    ![x64 옵션을 강조 표시한 Visual Studio 도구 모음 스크린샷](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션 옵션의 NuGet 패키지 관리를 강조 표시한 솔루션 탐색기 스크린샷](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 오른쪽 위 모서리의 **패키지 원본** 필드에서 **nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

    ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Cognitive Services 음성 SDK의 현재 버전은 `1.1.0`입니다.

1. NuGet 패키지를 설치하려면 표시된 라이선스에 동의합니다.

    ![라이선스 동의 대화 상자 스크린샷](media/sdk/qs-cpp-windows-05-nuget-license.png)

패키지를 설치한 후에 패키지 관리자 콘솔에서 확인 메시지가 나타납니다.

## <a name="add-sample-code"></a>샘플 코드 추가

1. 원본 파일 *helloworld.cpp*를 엽니다. 다음을 사용하여 아래 코드를 초기 include 문(`#include "stdafx.h"` 또는 `#include "pch.h"`)으로 바꿉니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 동일한 파일에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 오류 없이 컴파일됩니다.

   ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-cpp-windows-06-build.png)

1. 애플리케이션을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

   ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 콘솔 창이 나타나면 무엇이든 말해보라는 메시지가 표시됩니다. 영어 구 또는 문장을 말씀하세요. 음성은 음성 서비스로 전송되어 텍스트로 변환되고, 동일한 창에 표시됩니다.

   ![인식에 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/cpp-windows` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [C++용 Speech SDK를 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
