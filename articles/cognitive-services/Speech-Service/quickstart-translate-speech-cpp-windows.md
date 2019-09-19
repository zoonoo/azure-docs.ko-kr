---
title: '빠른 시작: 음성 변환, C++(Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용자 음성을 캡처하고, 다른 언어로 번역하고, 명령줄에 텍스트를 출력하는 C++ 애플리케이션을 만듭니다. 이 가이드는 Windows 사용자를 위해 설계되었습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382665"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows의 C++에서 음성 번역

빠른 시작은 [음성 인식](quickstart-cpp-windows.md) 및 [음성 합성](quickstart-text-to-speech-cpp-windows.md)에도 사용할 수 있습니다.

이 빠른 시작에서는 컴퓨터의 마이크에서 사용자 음성을 캡처하고, 음성을 번역하고, 번역된 텍스트를 실시간으로 명령줄에 전사하는 C++ 애플리케이션을 만듭니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2019(모든 버전)로 빌드되었습니다.

음성 변환에 사용할 수 있는 언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Speech Services 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [Speech Services를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. 원본 파일 **helloworld.cpp**를 엽니다.

1. 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. 동일한 파일에서 `YourSubscriptionKey` 문자열을 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**을 선택하거나, **F5** 키를 눌러 **helloworld** 애플리케이션을 시작합니다.

1. 영어 구 또는 문장을 말씀하세요. 애플리케이션이 음성 서비스로 음성을 전송하고, 여기서 텍스트(이 예제에서는 프랑스어 및 독일어)로 번역하고 전사합니다. 그런 다음, 음성 서비스가 텍스트를 표시하기 위해 애플리케이션으로 다시 보냅니다.

   ![음성 번역에 성공한 후의 콘솔 출력](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽거나 번역된 텍스트를 합성된 음성으로 변환하는 방법 등의 추가 샘플은 GitHub에서 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 C++ 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [Custom Speech 모델 학습](how-to-custom-speech-train-model.md)
