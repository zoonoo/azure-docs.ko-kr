---
title: '빠른 시작: Blob 스토리지에 저장된 음성 인식, Java - Speech Service'
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
ms.openlocfilehash: e110c87835cdf517bdd54adda0ef6d9168a44d52
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467425"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [Azure Blob에 원본 파일 업로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-eclipse"></a>Eclipse에서 프로젝트 열기

첫 번째 단계로 Eclipse에서 프로젝트를 열어야 합니다.

1. Eclipse 시작
2. 프로젝트를 로드하고 `Main.java`를 엽니다.

## <a name="add-a-reference-to-gson"></a>Gson에 대한 참조 추가
이 빠른 시작에서는 외부 JSON 직렬 변환기/역직렬 변환기를 사용합니다. Java의 경우 [Gson](https://github.com/google/gson)을 선택했습니다.

pom.xml을 열고 다음 참조를 추가합니다. [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(`YourSubscriptionKey`, `YourServiceRegion`및 `YourFileUrl` 값을 고유한 값으로 바꿔야 합니다.)

## <a name="json-wrappers"></a>JSON 래퍼

REST API는 JSON 형식으로 요청을 가져오고 JSON으로 결과를 반환하므로 문자열만 사용하여 상호 작용할 수 있지만 권장되지는 않습니다.
요청 및 응답을 보다 쉽게 관리할 수 있도록 JSON을 직렬화/역직렬화하는 데 사용할 몇 가지 클래스를 선언합니다.

계속해서 `Main` 앞에 선언을 배치합니다.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Http 클라이언트 만들기 및 구성
가장 먼저 필요한 것은 올바른 기본 URL과 인증 집합이 있는 Http 클라이언트입니다.
이 코드를 `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]에 삽입

## <a name="generate-a-transcription-request"></a>전사 요청 생성
다음으로, 전사 요청을 생성합니다. `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]에 이 코드 추가

## <a name="send-the-request-and-check-its-status"></a>요청을 보내고 상태 확인
이제 Speech Service에 요청을 게시하고 초기 응답 코드를 확인합니다. 이 응답 코드는 서비스에서 요청을 받았는지 여부를 표시합니다. 서비스는 전사 상태를 저장할 위치에 해당하는 Url을 응답 헤더에 반환합니다.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>전사를 완료할 때까지 대기
서비스는 전사를 비동기식으로 처리하므로 상태를 폴링해야 하는 경우가 있습니다. 5초마다 확인합니다.

요청을 게시할 때 가져온 Url에서 콘텐츠를 검색하여 상태를 확인할 수 있습니다. 콘텐츠를 다시 가져오는 경우 쉽게 상호 작용할 수 있도록 도우미 클래스 중 하나로 역직렬화합니다.

다음은 성공적인 완료를 제외한 모든 상태에 대한 상태 표시가 있는 폴링 코드입니다. 다음에 수행하겠습니다.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>전사 결과 표시
서비스가 전사를 성공적으로 완료하면 결과가 상태 응답에서 가져올 수 있는 다른 Url에 저장됩니다.

해당 URL의 콘텐츠를 다운로드하고, JSON을 역직렬화하고, 결과를 반복하면서 표시 텍스트를 출력하겠습니다.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>코드 확인
이 시점에서 코드는 다음과 같습니다. (이 버전에 일부 주석을 추가했습니다.) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 인식을 테스트할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
