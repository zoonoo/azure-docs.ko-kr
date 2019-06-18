---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482433"
---
1. Visual Studio 2019를 시작합니다.

1. **.NET 플랫폼 간 개발** 워크로드를 사용할 수 있는지 확인합니다. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 엽니다. 이 워크로드를 이미 사용하도록 설정한 경우 대화 상자를 닫습니다.

   ![워크로드 탭이 강조 표시된 Visual Studio 설치 관리자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   그렇지 않으면 **.NET Core 플랫폼 간 개발** 옆의 상자를 선택하고 대화 상자의 오른쪽 아래 모서리에서 **수정**을 선택합니다. 새로운 기능을 설치하려면 잠시 시간이 걸립니다.

1. 새로운 Visual C# .NET Core 콘솔 앱을 만듭니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** > **Visual C#**  >  **.NET Core**를 확장합니다. 그런 다음, **콘솔 앱(.NET Core)** 을 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

   ![새 프로젝트 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# 콘솔 앱 만들기(.NET Core)")

1. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

   ![솔루션의 NuGet 패키지 관리를 강조 표시한 솔루션 탐색기 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "솔루션의 NuGet 패키지 관리")

1. 오른쪽 위 모서리의 **패키지 원본** 필드에서 **nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

   ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet 패키지 설치")

1. NuGet 패키지를 설치하려면 표시된 라이선스에 동의합니다.

   ![라이선스 동의 대화 상자 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "라이선스에 동의")

패키지를 설치한 후에 패키지 관리자 콘솔에서 확인 메시지가 나타납니다.
