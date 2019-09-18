---
title: '빠른 시작: 음성 인식, C++(Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Windows Desktop에서 C++로 음성을 인식하는 방법을 알아봅니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: a9c43e1d27a396a2c3e9123ce5ce538296c6870c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381852"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows의 C++에서 음성 인식

빠른 시작은 [음성 합성](quickstart-text-to-speech-cpp-windows.md) 및 [음성 번역](quickstart-translate-speech-cpp-windows.md)에도 사용할 수 있습니다.

원하는 경우 다른 프로그래밍 언어 및 환경을 선택합니다.<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Windows용 C++ 콘솔 애플리케이션을 만듭니다. Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 실시간으로 PC의 마이크로 음성을 텍스트로 변환할 수 있습니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2019(모든 버전)로 빌드되었습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Speech Services 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [Speech Services를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. 원본 파일 **helloworld.cpp**를 엽니다.

1. 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 동일한 파일에서 `YourSubscriptionKey` 문자열을 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**을 선택하거나 **F5**를 눌러 **helloworld**애플리케이션을 시작합니다.

1. 영어 구 또는 문장을 말씀하세요. 애플리케이션은 음성을 Speech Services로 전송하고, 텍스트로 바꿔서 표시하기 위해 애플리케이션으로 다시 보냅니다.

   ![성공한 인식 이후 콘솔 출력](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 C++ 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [Custom Speech 모델 학습](how-to-custom-speech-train-model.md)
