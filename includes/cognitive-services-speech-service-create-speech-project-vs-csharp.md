---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145493"
---
1. Visual Studio 2017을 시작합니다.

1. Visual Studio의 메뉴 모음에서 **도구 > 도구 가져오기**를 선택하고 **.NET 데스크톱 개발** 워크로드를 사용할 수 있는지 확인합니다. 워크로드가 설치되지 않은 경우 확인란을 선택한 다음, **수정**을 클릭하여 설치를 시작합니다. 다운로드 및 설치하는 데 몇 분 정도 걸릴 수 있습니다.

   **.NET 데스크톱 개발** 옆의 확인란을 선택하면 지금 대화 상자를 닫을 수 있습니다.

   ![.NET 데스크톱 개발 사용](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 다음으로, 프로젝트를 만들어 보겠습니다. 메뉴 모음에서 **파일 > 새로 만들기 > 프로젝트**를 차례로 선택합니다. 대화 상자가 표시되면 왼쪽 패널에서 **설치됨 > Visual C# > Windows 데스크톱** 섹션을 차례로 펼치고, **콘솔 앱(.NET Framework)** 을 선택합니다. 이 프로젝트의 이름을 *helloworld*로 지정합니다.

    ![Visual C# 콘솔 앱(.NET Framework) 만들기](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Visual C# 콘솔 앱(.NET Framework) 만들기")

1. 프로젝트가 설정되었으므로 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 코드에서 참조해야 합니다. 솔루션 탐색기를 찾아 helloworld를 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **NuGet 패키지 관리...** 를 선택합니다.

   ![솔루션에 대한 NuGet 패키지 관리를 마우스 오른쪽 단추로 클릭](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "솔루션에 대한 NuGet 패키지 관리")

1. NuGet 패키지 관리자의 오른쪽 위 모서리에서 **패키지 원본** 드롭다운을 찾아 **nuget.org**가 선택되어 있는지 확인합니다. 그런 다음, **찾아보기**를 선택하고, `Microsoft.CognitiveServices.Speech` 패키지를 검색하여 안정적인 최신 버전을 설치합니다.

   ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "NuGet 패키지 설치")

1. 설치를 시작하려면 모든 계약 및 라이선스를 수락합니다.

   ![라이선스에 동의](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "라이선스에 동의")

    패키지를 설치한 후에 패키지 관리자 콘솔에서 확인 메시지가 나타납니다.

1. 다음 단계에서는 콘솔 애플리케이션을 빌드 및 실행하는 데 사용하는 컴퓨터의 아키텍처와 일치하는 플랫폼 구성을 만듭니다. 메뉴 모음에서 **빌드** > **구성 관리자...** 를 차례로 선택합니다.

    ![구성 관리자 시작](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "구성 관리자 시작")

1. **구성 관리자** 대화 상자에서 **Active 솔루션 플랫폼** 드롭다운 목록을 찾고, **새로 만들기**를 선택합니다.

    ![구성 관리자 창 아래에서 새 플랫폼 추가](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "구성 관리자 창 아래에서 새 플랫폼 추가")

1. 64비트 Windows를 실행하는 경우 **새 플랫폼을 입력하거나 선택하세요**라는 메시지가 표시되면 `x64`를 선택합니다. 32비트 Windows를 실행하는 경우 `x86`을 선택합니다. 작업을 완료하면 **확인**을 클릭합니다.

    ![64비트 Windows에서 "x64"라는 새 플랫폼 추가](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64 플랫폼 추가")
