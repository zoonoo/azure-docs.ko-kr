---
title: '빠른 시작: .NET Framework(Windows) 플랫폼 설정용 Speech SDK - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 통해 Speech Services SDK를 사용하여 Windows의 .NET Framework에서 C#용 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 2caa5124144146e2286b102bf6ded9c5d120f904
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500355"
---
이 가이드에서는 .NET Framework(Windows)용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio 프로젝트를 만들고  Speech SDK 설치

코드에서 참조할 수 있도록 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치해야 합니다. 이렇게 하려면 먼저 **helloworld** 프로젝트를 만들어야 할 수 있습니다. **.NET 데스크톱 개발** 워크로드를 사용할 수 있는 프로젝트가 이미 있는 경우 해당 프로젝트를 사용하고 [NuGet 패키지 관리자를 사용하여 Speech SDK 설치](#use-nuget-package-manager-to-install-the-speech-sdk)로 건너뛸 수 있습니다.

### <a name="create-helloworld-project"></a>helloworld 프로젝트 만들기

1. Visual Studio 2019를 엽니다.

1. [시작] 창에서 **새 프로젝트 만들기**를 선택합니다. 

1. **새 프로젝트 만들기** 창에서 **콘솔 앱(.NET Framework)** 을 선택한 다음, **다음**을 선택합니다.

1. **새 프로젝트 구성** 창에서 **프로젝트 이름**에 *helloworld*를 입력하고 **위치**에서 디렉터리 경로를 선택하거나 만든 다음, **만들기**를 선택합니다.

1. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 열고 **수정** 대화 상자를 표시합니다.

1. **.NET 데스크톱 개발** 워크로드를 사용할 수 있는지 확인합니다. 워크로드가 설치되지 않은 경우 옆에 있는 확인란을 선택한 다음, **수정**을 선택하여 설치를 시작합니다. 다운로드 및 설치하는 데 몇 분 정도 걸릴 수 있습니다.

   이미 **.NET 데스크톱 개발** 옆의 확인란이 선택되어 있는 경우 **닫기**를 선택하여 대화 상자를 닫을 수 있습니다.

   ![.NET 데스크톱 개발 사용](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual Studio 설치 관리자를 닫습니다.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>NuGet 패키지 관리자를 사용하여 Speech SDK 설치

1. 솔루션 탐색기에서 **helloworld** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택하여 NuGet 패키지 관리자를 표시합니다.

   ![NuGet 패키지 관리자](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 오른쪽 위 모서리에서 **패키지 원본** 드롭다운 상자를 찾아서 **`nuget.org`** 를 선택합니다.

1. 왼쪽 위 모서리에서 **찾아보기**를 선택합니다.

1. 검색 상자에 *Microsoft.CognitiveServices.Speech*를 입력하고 **Enter**를 선택합니다.

1. 검색 결과에서 **Microsoft.CognitiveServices.Speech** 패키지를 선택한 다음, **설치**를 선택하여 안정적인 최신 버전을 설치합니다.

   ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. 설치를 시작하려면 모든 계약 및 라이선스를 수락합니다.

   패키지를 설치하면 **패키지 관리자 콘솔** 창에 확인 메시지가 나타납니다.

이제 아래의 [다음 단계](#next-steps)로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
