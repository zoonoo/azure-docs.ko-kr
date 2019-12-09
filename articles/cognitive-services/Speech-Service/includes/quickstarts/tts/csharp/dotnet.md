---
title: '빠른 시작: 음성 합성, C#(Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드에 따라 Windows용 .NET Framework 및 Speech SDK를 사용하여 텍스트를 음성으로 변환하는 콘솔 애플리케이션을 만들 수 있습니다. 마치면, 텍스트를 음성으로 합성하여 스피커를 통해 실시간으로 들을 수 있습니다.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 38dac73d253245fce86ac419c7a4eb3a106df3f0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818325"
---
## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>샘플 코드 추가

1. **Program.cs**를 열고 자동으로 생성된 코드를 이 샘플로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 Speech Service 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다. 예를 들어 평가판 구독을 사용하는 경우 지역은 `westus`입니다.

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**(또는 **F5** 선택)을 선택하여 **helloworld** 애플리케이션을 시작합니다.

1. 영어 구 또는 문장을 입력합니다. 애플리케이션은 텍스트를 Speech Service로 전송하고, 여기서 합성된 음성을 스피커에서 재생하기 위해 애플리케이션으로 다시 보냅니다.

   ![음성 합성 사용자 인터페이스](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>참고 항목

- [사용자 지정 음성 만들기](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [사용자 지정 음성 녹음 샘플](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
