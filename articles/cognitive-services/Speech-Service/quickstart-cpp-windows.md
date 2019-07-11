---
title: '빠른 시작: 음성 인식, C++(Windows) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Windows Desktop에서 C++로 음성을 인식하는 방법을 알아봅니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: d63d1c96077642e660e2272cbd8c2ee1250b1471
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606485"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows의 C++에서 음성 인식

빠른 시작은 [텍스트 음성 변환](quickstart-text-to-speech-cpp-windows.md) 및 [음성 번역](quickstart-translate-speech-cpp-windows.md)에도 사용할 수 있습니다.

원하는 경우 다음과 같은 다른 프로그래밍 언어 및/또는 환경을 선택하세요.<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Windows용 C++ 콘솔 애플리케이션을 만듭니다. Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 실시간으로 PC 마이크의 음성을 텍스트로 변환할 수 있습니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017(모든 버전)로 빌드되었습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Speech Services 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [Speech Services를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. 원본 파일 *helloworld.cpp*를 엽니다. 다음을 사용하여 아래 코드를 초기 include 문(`#include "stdafx.h"` 또는 `#include "pch.h"`)으로 바꿉니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 동일한 파일에서 `YourSubscriptionKey` 문자열을 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 오류 없이 컴파일됩니다.

   ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-cpp-windows-06-build.png)

1. 애플리케이션을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

   ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 콘솔 창이 나타나면 무엇이든 말해보라는 메시지가 표시됩니다. 영어 구 또는 문장을 말씀하세요. 음성은 Speech Services로 전송되어 텍스트로 변환되고, 동일한 창에 표시됩니다.

   ![인식에 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 C++ 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
