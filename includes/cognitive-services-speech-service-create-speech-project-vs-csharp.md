---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362564"
---
1. Visual Studio 2019를 엽니다.

1. [시작] 창에서 **새 프로젝트 만들기**를 선택합니다. 

1. **콘솔 앱(.NET Framework)** 을 선택하고 **다음**을 선택합니다.

1. **프로젝트 이름**에 `helloworld`를 입력하고 **만들기**를 선택합니다.

1. Visual Studio의 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하고, **.NET 데스크톱 개발** 워크로드를 사용할 수 있는지 확인합니다. 워크로드가 설치되지 않은 경우 확인란을 선택한 다음, **수정**을 선택하여 설치를 시작합니다. 다운로드 및 설치하는 데 몇 분 정도 걸릴 수 있습니다.

   **.NET 데스크톱 개발** 옆의 확인란을 선택하면 지금 대화 상자를 닫을 수 있습니다.

   ![.NET 데스크톱 개발 사용](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

다음 단계는 코드에서 참조할 수 있도록 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하는 것입니다.

1. 솔루션 탐색기에서 `helloworld`를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택하여 NuGet 패키지 관리자를 표시합니다.

   ![NuGet 패키지 관리자](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 오른쪽 위 모서리에서 **패키지 원본** 드롭다운 상자를 찾아서 **nuget.org**를 선택합니다.

1. 왼쪽 위 모서리에서 **찾아보기**를 선택합니다.

1. 검색 상자에 `Microsoft.CognitiveServices.Speech` 패키지를 입력하고 Enter 키를 누릅니다.

1. `Microsoft.CognitiveServices.Speech`를 선택한 다음, **설치**를 선택하여 안정적인 최신 버전을 설치합니다.

   ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. 설치를 시작하려면 모든 계약 및 라이선스를 수락합니다.

   패키지를 설치하면 **패키지 관리자 콘솔** 창에 확인 메시지가 나타납니다.

이제 콘솔 애플리케이션을 빌드하여 실행하려면 컴퓨터의 아키텍처와 일치하는 플랫폼 구성을 만듭니다.

1. 메뉴 모음에서 **빌드** > **구성 관리자**를 선택합니다. **구성 관리자** 대화 상자가 나타납니다.

   ![구성 관리자 대화 상자](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **활성 솔루션 플랫폼** 드롭다운 상자에서 **새로 만들기**를 선택합니다. **새 솔루션 플랫폼** 대화 상자가 나타납니다.

1. **새 플랫폼 입력 또는 선택** 드롭다운 상자에서 다음을 수행합니다.
   - 64비트 Windows를 실행하는 경우 **x64**를 선택합니다.
   - 32비트 Windows를 실행하는 경우 **x86**을 선택합니다.

1. **확인**, **닫기**를 차례로 선택합니다.
