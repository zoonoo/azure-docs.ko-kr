---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454464"
---
1. Visual Studio 2017을 시작합니다.
 
1. **.NET 데스크톱 환경** 워크로드를 사용할 수 있는지 확인합니다. Visual Studio 메뉴 모음에서 **도구** \> **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 엽니다. 이 워크로드를 이미 사용하도록 설정한 경우 대화 상자를 닫습니다. 

    그렇지 않으면 **.NET 데스크톱 개발** 옆에 있는 확인란을 선택한 다음, 대화 상자의 오른쪽 아래 모서리에 있는 **수정** 단추를 클릭합니다. 새로운 기능을 설치하려면 잠시 시간이 걸립니다.

    ![.NET 데스크톱 개발 사용](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 새 Visual C# 콘솔 앱을 만듭니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** \> **Visual C#** \> **Windows 데스크톱**을 확장한 다음, **콘솔 앱(.NET Framework)** 을 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![Visual C# 콘솔 앱(.NET Framework) 만들기](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Visual C# 콘솔 앱(.NET Framework) 만들기")

1. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션에 대한 NuGet 패키지 관리를 마우스 오른쪽 단추로 클릭](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "솔루션에 대한 NuGet 패키지 관리")

1. 오른쪽 위 모서리의 **패키지 원본** 필드에서 **nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색하고, **helloworld** 프로젝트에 설치합니다.

    ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "NuGet 패키지 설치")

1. NuGet 패키지를 설치하려면 표시된 라이선스에 동의합니다.

    ![라이선스에 동의](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "라이선스에 동의")

    패키지를 설치한 후에 패키지 관리자 콘솔에서 확인 메시지가 나타납니다.

1. Configuration Manager를 통해 PC 아키텍처와 일치하는 플랫폼 구성을 만듭니다. **빌드** > **구성 관리자**를 선택합니다.

    ![구성 관리자 시작](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "구성 관리자 시작")

1. **구성 관리자** 대화 상자에서 새 플랫폼을 추가합니다. **활성 솔루션 플랫폼** 드롭다운 목록에서 **새로 만들기**를 선택합니다.

    ![구성 관리자 창 아래에서 새 플랫폼 추가](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "구성 관리자 창 아래에서 새 플랫폼 추가")

1. 64비트 Windows를 실행하는 경우 `x64`라는 새 플랫폼 구성을 만듭니다. 32비트 Windows를 실행하는 경우 `x86`라는 새 플랫폼 구성을 만듭니다.

    ![64비트 Windows에서 "x64"라는 새 플랫폼 추가](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64 플랫폼 추가")


