---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: af61d975b540787b9b54d9fdea66773c10aeb6ac
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145454"
---
1. Visual Studio 2017을 시작합니다.

1. **C++를 사용한 데스크톱 개발** 워크로드를 사용할 수 있도록 합니다. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 엽니다. 이 워크로드를 이미 사용하는 경우 다음 단계를 건너뜁니다.

    ![Visual Studio 워크로드 탭의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    그렇지 않으면 **C++를 사용한 데스크톱 개발** 옆에 있는 확인란을 선택합니다.

1. **NuGet 패키지 관리자** 구성 요소를 사용할 수 있도록 합니다. 아직 사용하도록 설정되지 않은 경우 Visual Studio 설치 관리자 대화 상자의 **개별 구성 요소** 탭으로 전환하고, **NuGet 패키지 관리자**를 선택합니다.

      ![Visual Studio 개별 구성 요소 탭의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. C++ 워크로드 또는 NuGet을 사용하도록 설정해야 하는 경우 대화 상자의 오른쪽 아래 모서리에서 **수정**을 선택합니다. 새로운 기능을 설치하려면 잠시 시간이 걸립니다. 두 기능을 모두 이미 사용하도록 설정한 경우 대신 대화 상자를 닫습니다.

1. 새로운 Visual C++ Windows 데스크톱 창 콘솔 애플리케이션을 만듭니다. 먼저, 메뉴에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** > **Visual C++** > **Windows Desktop**을 확장합니다. 그런 다음, **Windows 콘솔 애플리케이션 만들기**를 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![새 프로젝트 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. 64비트 Windows를 실행하는 경우 Visual Studio 도구 모음에서 드롭다운 메뉴를 사용하여 빌드 플랫폼을 `x64`로 전환할 수 있습니다. (64비트 버전의 Windows에서는 32비트 애플리케이션을 실행할 수 있으므로 요구 사항은 아닙니다.)

    ![x64 옵션을 강조 표시한 Visual Studio 도구 모음 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션 옵션의 NuGet 패키지 관리를 강조 표시한 솔루션 탐색기 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 오른쪽 위 모서리의 **패키지 원본** 필드에서 **nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

    ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Cognitive Services 음성 SDK의 현재 버전은 `1.5.0`입니다.

1. NuGet 패키지를 설치하려면 표시된 라이선스에 동의합니다.

    ![라이선스 동의 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

패키지를 설치한 후에 패키지 관리자 콘솔에서 확인 메시지가 나타납니다.
