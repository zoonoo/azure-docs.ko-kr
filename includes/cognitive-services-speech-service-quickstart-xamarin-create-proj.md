---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837376"
---
Xamarin을 사용한 플랫폼 간 Mobile App .NET 개발을 위한 Visual Studio 프로젝트를 만들려면 Visual Studio 개발 옵션을 설정하고, 프로젝트를 만들고, 대상 아키텍처를 선택하고, Speech SDK를 설치해야 합니다.

### <a name="set-up-visual-studio-development-options"></a>Visual Studio 개발 옵션 설정

시작하려면 Visual Studio에서 .NET을 사용한 플랫폼 간 모바일 개발용으로 올바르게 설정되었는지 확인합니다.

1. Visual Studio 2019를 엽니다.

1. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 열고 **수정** 대화 상자를 봅니다.

   ![워크로드 탭, 수정 대화 상자, Visual Studio 설치 관리자](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. **워크로드** 탭의 **Windows** 아래에서 **.NET을 사용한 모바일 개발** 워크로드를 찾습니다. 해당 작업 옆의 확인란이 이미 선택되어 있으면 **수정** 대화 상자를 닫고 5단계로 이동합니다.

1. **.NET을 사용한 모바일 개발** 확인란을 선택하고 **수정**을 선택한 다음, **시작하기 전에** 대화 상자에서 **계속**을 선택하여 .NET 워크로드로 모바일 개발을 설치합니다. 새로운 기능을 설치하는 데 시간이 걸릴 수 있습니다.

1. Visual Studio 설치 관리자를 닫습니다.

### <a name="create-the-project"></a>프로젝트 만들기

1. Visual Studio 메뉴 모음에서 **파일** > **새로 만들기** > **프로젝트**를 선택하여 **새 프로젝트 만들기** 창을 표시합니다.

   ![새 프로젝트 만들기 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. **모바일 앱(Xamarin Forms)** 을 찾아서 선택합니다.

1. **다음**을 선택하여 **새 프로젝트 구성** 화면을 표시합니다. 

   ![새 프로젝트 구성 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. **프로젝트 이름**에서 `helloworld`를 입력합니다.

1. **위치**에서 프로젝트를 저장할 폴더로 이동하여 선택하거나 새로 만듭니다.

1. **만들기**를 선택하여 **새 모바일 앱 Xamarin Forms Project** 창으로 이동합니다.

   ![새 유니버설 Windows 플랫폼 프로젝트 대화 상자 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. **Blank** 템플릿 선택

1. **플랫폼**에서 **Android**, **iOS** 및 **Windows(UWP)** 의 확인란을 선택합니다.

1. **확인**을 선택합니다. 새 프로젝트가 만들어지고 **솔루션 탐색기** 창에 표시되는 Visual Studio IDE로 돌아갑니다.

   ![helloworld 프로젝트 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

이제 대상 플랫폼 아키텍처 및 시작 프로젝트를 선택합니다. Visual Studio 도구 모음에서 **솔루션 플랫폼** 드롭다운 상자를 찾습니다. (표시되지 않는 경우 **보기** > **도구 모음** > **표준**을 선택하여 **솔루션 플랫폼**을 포함하는 도구 모음을 표시합니다.) 64비트 Windows를 실행하는 경우 드롭다운 상자에서 **x64**를 선택합니다. 64비트 Windows는 32비트 애플리케이션을 실행할 수도 있으므로 원한다면 **x86**을 선택할 수 있습니다. **시작 프로젝트** 드롭다운 상자의 경우 helloworld.UWP(유니버설 Windows)를 설정합니다.

### <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

[Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고, 프로젝트에서 Speech SDK를 참조합니다.

1. **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고, **솔루션에 대한 NuGet 패키지 관리**를 선택하여 **NuGet - 솔루션** 창으로 이동합니다.

1. **찾아보기**를 선택합니다.

   ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. **패키지 원본**에서 **nuget.org**를 선택합니다.

1. **검색** 상자에 `Microsoft.CognitiveServices.Speech`를 입력한 다음, 검색 결과에 표시된 후에 해당 패키지를 선택합니다.

   ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > 참고: Microsoft.CognitiveServices.Speech nuget 내의 iOS 라이브러리에는 bitcode가 활성화되어 있지 않습니다. 애플리케이션에 bitcode를 사용 라이브러리가 필요한 경우 특별히 iOS 프로젝트에 Microsoft.CognitiveServices.Speech.Xamarin.iOS nuget을 사용하세요.

1. 검색 결과 옆에 있는 패키지 상태 창에서 모든 프로젝트를 선택합니다. **helloworld**, **helloworld.Android**, **helloworld.iOS** 및 **helloworld.UWP**.

1. **설치**를 선택합니다.

1. **변경 내용 미리 보기** 대화 상자에서 **확인**을 선택합니다.

1. **라이선스 승인** 대화 상자에서 라이선스를 확인한 다음, **동의함**을 선택하고 모든 프로젝트에 음성 SDK 패키지 참조를 설치합니다. 설치가 성공적으로 완료되면 helloworld.iOS에 대해 다음과 같은 경고가 표시될 수 있습니다. 이는 알려진 문제이며 앱 기능에 영향을 주지 않아야 합니다.

> "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a" 참조를 확인할 수 없습니다. 코드에 이 참조가 필요한 경우 컴파일 오류가 발생할 수 있습니다.
