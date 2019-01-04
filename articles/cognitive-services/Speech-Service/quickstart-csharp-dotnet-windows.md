---
title: '빠른 시작: 음성 인식, .NET Framework(Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드에 따라 Windows용 .NET Framework 및 Speech SDK를 사용하여 음성을 텍스트로 변환하는 콘솔 애플리케이션을 만들 수 있습니다. 작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: wolfma
ms.openlocfilehash: 171d6c6a972d6fa3eb2e96ceacb75dd2fce1eb92
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074898"
---
# <a name="quickstart-recognize-and-transcribe-speech-using-the-speech-sdk-and-net-framework-windows"></a>빠른 시작: Speech SDK 및 .NET Framework(Windows)를 사용하여 음성 인식 및 기록

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 가이드에 따라 Windows용 .NET Framework 및 Speech SDK를 사용하여 음성을 텍스트로 변환하는 콘솔 애플리케이션을 만들 수 있습니다. 작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.

이 빠른 시작에는 Microsoft Speech가 지원되는 [Azure Cognitive Services 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 필요합니다. 계정이 없는 경우 [평가판](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 프로젝트를 완료하려면 다음이 필요합니다.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service에 대한 구독 키
* 컴퓨터의 마이크에 대한 액세스

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. `Program.cs`를 열고 자동으로 생성된 코드를 이 샘플로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 Speech Service 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [Azure 지역](regions.md)으로 바꿉니다. 예를 들어 평가판을 사용하는 경우 Azure 지역은 `westus`입니다.

1. 프로젝트 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-dotnet-windows-08-build.png "성공적인 빌드")

1. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 눌러 애플리케이션을 시작합니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "앱 디버깅 시작")

1. 말을 하라는 콘솔 창이 나타납니다. 영어로 아무 말이나 합니다. 음성은 Speech Service로 전송되어 실시간으로 텍스트로 기록됩니다. 결과는 콘솔에 출력됩니다.

    ![인식이 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "인식이 성공한 후의 콘솔 출력")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
코드는 `quickstart/csharp-dotnet-windows` 폴더에 제공됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [C#용 Speech SDK를 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
