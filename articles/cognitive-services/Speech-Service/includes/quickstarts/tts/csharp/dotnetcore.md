---
title: '빠른 시작: 음성 합성, C#(.NET Core) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Windows의 .NET Core에서 C#으로 음성을 합성하는 방법을 알아봅니다.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 670d32a93df9e2a0363163079b50c7306f81975e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500469"
---
> [!NOTE]
> .NET Core는 [.NET 표준](https://docs.microsoft.com/dotnet/standard/net-standard) 사양을 구현하는 플랫폼 간 오픈 소스 .NET 플랫폼입니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>샘플 코드 추가

1. `Program.cs`를 열고 모든 코드를 다음으로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. 동일한 파일에서 `YourSubscriptionKey` 문자열을 구독 키로 바꿉니다.

1. 또한 `YourServiceRegion` 문자열을 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다(예: 평가판 구독의 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "성공한 빌드")

1. 애플리케이션을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "앱을 디버깅하기 시작")

1. 콘솔 창이 나타나고 텍스트를 입력라는 메시지가 표시됩니다. 몇 가지 단어나 문장을 입력합니다. 입력한 텍스트는 Speech Services로 전송되고 음성으로 합성되어 스피커로 재생됩니다.

    ![합성에 성공한 후 콘솔 출력 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "합성에 성공한 후의 콘솔 출력")

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>참고 항목

- [사용자 지정 음성 만들기](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [사용자 지정 음성 녹음 샘플](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
