---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: fe2978e176b986164ebb01fddbd29481f8a117bd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145416"
---
1. Visual Studio 2017을 시작합니다.

1. **유니버설 Windows 플랫폼 개발** 워크로드를 사용할 수 있는지 확인합니다. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 엽니다. 이 워크로드를 이미 사용하도록 설정한 경우 대화 상자를 닫습니다.

    ![워크로드 탭이 강조 표시된 Visual Studio 설치 관리자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    그렇지 않으면 **.NET 플랫폼 간 개발** 옆의 상자를 선택하고 대화 상자 오른쪽 아래 모서리의 **수정**을 선택합니다. 새로운 기능을 설치하려면 잠시 시간이 걸립니다.

1. 빈 Visual C# 유니버설 Windows 앱을 만듭니다. 먼저, 메뉴에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** > **Visual C#**  > **Windows Universal**을 확장합니다. 그런 다음, **비어 있는 앱(유니버설 Windows)** 을 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![새 프로젝트 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speech SDK를 사용하려면 애플리케이션을 Windows 10 Fall Creators Update 이상용으로 빌드해야 합니다. 팝업되는 **새 유니버설 Windows 플랫폼 프로젝트** 창에서 **Windows 10 Fall Creators Update(10.0; Build 16299)** 를 **최소 버전**으로 선택합니다. **대상 버전** 상자에서 이 이상 버전을 선택한 다음, **확인**을 클릭합니다.

    ![새 유니버설 Windows 플랫폼 프로젝트 창의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 64비트 Windows를 실행하는 경우 Visual Studio 도구 모음에서 드롭다운 메뉴를 사용하여 빌드 플랫폼을 `x64`로 전환할 수 있습니다. 64비트 Windows는 32비트 애플리케이션을 실행할 수 있으므로 원한다면 `x86`으로 설정한 상태로 둘 수 있습니다.

   ![x64를 강조 표시한 Visual Studio 도구 모음 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Speech SDK는 Intel 호환 프로세서만 지원합니다. ARM은 현재 지원되지 않습니다.

1. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션 옵션의 NuGet 패키지 관리를 강조 표시한 솔루션 탐색기 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 오른쪽 위 모서리의 **패키지 원본** 필드에서 **nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

    ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "NuGet 패키지 설치")

1. NuGet 패키지를 설치하려면 표시된 라이선스에 동의합니다.

    ![라이선스 동의 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "라이선스에 동의")

1. 패키지 관리자 콘솔에 다음 출력 줄이 표시됩니다.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.5.0' to helloworld
   ```

1. 애플리케이션은 음성 입력에 마이크를 사용하므로 **마이크** 기능을 프로젝트에 추가합니다. 솔루션 탐색기에서 **Package.appxmanifest**를 두 번 클릭하여 애플리케이션 매니페스트를 편집합니다. 그런 다음, **기능** 탭으로 전환하고 **마이크** 기능에 대한 확인란을 선택한 후 변경 내용을 저장합니다.

   ![기능 및 마이크가 강조 표시된 Visual Studio 애플리케이션 매니페스트의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
