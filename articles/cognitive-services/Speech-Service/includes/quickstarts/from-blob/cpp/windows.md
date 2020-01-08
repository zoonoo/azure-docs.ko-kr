---
title: '빠른 시작: Blob 스토리지에 저장된 음성 인식, C++ - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b7ce80baa090ed87722397384b8e75c134eb26da
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467636"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [Azure Blob에 원본 파일 업로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Visual Studio에서 프로젝트 열기

첫 번째 단계로 Visual Studio에서 프로젝트를 열어야 합니다.

1. Visual Studio 2019를 시작합니다.
2. 프로젝트를 로드하고 `helloworld.cpp`를 엽니다.

## <a name="add-a-references"></a>참조 추가

코드 개발 속도를 높이기 위해 다음과 같은 몇 가지 외부 구성 요소를 사용합니다.
* [CPP Rest SDK](https://github.com/microsoft/cpprestsdk) REST 서비스에 대한 REST 호출을 위한 클라이언트 라이브러리입니다.
* [nlohmann/json](https://github.com/nlohmann/json) 편리한 JSON 구문 분석/직렬화/역직렬화 라이브러리입니다.

둘 다 [vcpkg](https://github.com/Microsoft/vcpkg/)를 사용하여 설치할 수 있습니다.

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]
(`YourSubscriptionKey`, `YourServiceRegion`및 `YourFileUrl` 값을 고유한 값으로 바꿔야 합니다.)

## <a name="json-wrappers"></a>JSON 래퍼

REST API는 JSON 형식으로 요청을 가져오고 JSON으로 결과를 반환하므로 문자열만 사용하여 상호 작용할 수 있지만 권장되지는 않습니다.
요청 및 응답을 보다 쉽게 관리할 수 있도록 JSON을 직렬화/역직렬화하는 데 사용할 클래스와 nlohmann/json 지원을 위한 메서드를 몇 가지 선언합니다.

계속해서 `recognizeSpeech` 앞에 해당 선언을 배치합니다.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Http 클라이언트 만들기 및 구성
가장 먼저 필요한 것은 올바른 기본 URL과 인증 집합이 있는 Http 클라이언트입니다.
이 코드를 `recognizeSpeech`에 삽입

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>전사 요청 생성
다음으로, 전사 요청을 생성합니다. `recognizeSpeech`에 이 코드 추가

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>요청을 보내고 상태 확인
이제 Speech Service에 요청을 게시하고 초기 응답 코드를 확인합니다. 이 응답 코드는 서비스에서 요청을 받았는지 여부를 표시합니다. 서비스는 전사 상태를 저장할 위치에 해당하는 Url을 응답 헤더에 반환합니다.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>전사를 완료할 때까지 대기
서비스는 전사를 비동기식으로 처리하므로 상태를 폴링해야 하는 경우가 있습니다. 5초마다 확인합니다.

요청을 게시할 때 가져온 Url에서 콘텐츠를 검색하여 상태를 확인할 수 있습니다. 콘텐츠를 다시 가져오는 경우 쉽게 상호 작용할 수 있도록 도우미 클래스 중 하나로 역직렬화합니다.

다음은 성공적인 완료를 제외한 모든 상태에 대한 상태 표시가 있는 폴링 코드입니다. 다음에 수행하겠습니다.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>전사 결과 표시
서비스가 전사를 성공적으로 완료하면 결과가 상태 응답에서 가져올 수 있는 다른 Url에 저장됩니다.

해당 URL의 콘텐츠를 다운로드하고, JSON을 역직렬화하고, 결과를 반복하면서 표시 텍스트를 출력하겠습니다.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>코드 확인
이 시점에서 코드는 다음과 같습니다. (이 버전에 몇 가지 주석을 추가함)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 인식을 테스트할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
