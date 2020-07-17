---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "71327046"
---
Windows 개발에 대한 Visual Studio 프로젝트를 만들려면 .NET 데스크톱 개발용으로 Visual Studio를 설정하고, Speech SDK를 설치하고 대상 아키텍처를 선택해야 합니다.

### <a name="create-the-project-and-add-the-workload"></a>프로젝트 만들기 및 워크로드 추가

시작하려면 Visual Studio에서 프로젝트를 만들고 Visual Studio가 .NET 데스크톱 개발용으로 설정되어 있는지 확인합니다.

1. Visual Studio 2019를 엽니다.

1. 시작 창에서 **새 프로젝트 만들기**를 선택합니다. 

1. **새 프로젝트 만들기** 창에서 **콘솔 앱(.NET Framework)** 을 선택한 다음, **다음**을 선택합니다.

1. **새 프로젝트 구성** 창에서 **프로젝트 이름**에 *helloworld*를 입력하고 **위치**에서 디렉터리 경로를 선택하거나 만든 다음, **만들기**를 선택합니다.

1. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택 하 여 Visual Studio 설치 관리자 열고 **수정** 대화 상자를 표시 합니다.

1. **.NET 데스크톱 개발** 워크로드를 사용할 수 있는지 확인합니다. 워크로드가 설치되지 않은 경우 옆에 있는 확인란을 선택한 다음, **수정**을 선택하여 설치를 시작합니다. 다운로드 및 설치하는 데 몇 분 정도 걸릴 수 있습니다.

   이미 **.NET 데스크톱 개발** 옆의 확인란이 선택되어 있는 경우 **닫기**를 선택하여 대화 상자를 닫을 수 있습니다.

   ![.NET 데스크톱 개발 사용](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual Studio 설치 관리자를 닫습니다.

### <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

다음 단계는 코드에서 참조할 수 있도록 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하는 것입니다.

1. 솔루션 탐색기에서 **helloworld** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택하여 NuGet 패키지 관리자를 표시합니다.

   ![NuGet 패키지 관리자](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 오른쪽 위 모서리에서 **패키지 원본** 드롭다운 상자를 찾아서 **nuget.org**를 선택합니다.

1. 왼쪽 위 모서리에서 **찾아보기**를 선택합니다.

1. 검색 상자에 *Microsoft.CognitiveServices.Speech*를 입력하고 **Enter**를 선택합니다.

1. 검색 결과에서 **Microsoft.CognitiveServices.Speech** 패키지를 선택한 다음, **설치**를 선택하여 안정적인 최신 버전을 설치합니다.

   ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. 설치를 시작하려면 모든 계약 및 라이선스를 수락합니다.

   패키지를 설치하면 **패키지 관리자 콘솔** 창에 확인 메시지가 나타납니다.

### <a name="choose-the-target-architecture"></a>대상 아키텍처 선택

이제 콘솔 애플리케이션을 빌드하여 실행하려면 컴퓨터의 아키텍처와 일치하는 플랫폼 구성을 만듭니다.

1. 메뉴 모음에서 **빌드** > **Configuration Manager**를 선택 합니다. **구성 관리자** 대화 상자가 나타납니다.

   ![구성 관리자 대화 상자](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **활성 솔루션 플랫폼** 드롭다운 상자에서 **새로 만들기**를 선택합니다. **새 솔루션 플랫폼** 대화 상자가 나타납니다.

1. **새 플랫폼 입력 또는 선택** 드롭다운 상자에서 다음을 수행합니다.
   - 64비트 Windows를 실행하는 경우 **x64**를 선택합니다.
   - 32비트 Windows를 실행하는 경우 **x86**을 선택합니다.

1. **확인**, **닫기**를 차례로 선택합니다.
