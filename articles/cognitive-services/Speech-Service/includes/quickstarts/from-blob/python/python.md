---
title: '빠른 시작: Blob 스토리지에 저장된 음성 인식, C# - Speech Service'
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
ms.openlocfilehash: 2f06d0015bd80b37407df28045d4ced4a128e47e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467540"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [Azure Blob에 원본 파일 업로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md)

## <a name="download-and-install-the-api-client-library"></a>API 클라이언트 라이브러리 다운로드 및 설치

샘플을 실행하려면 [Swagger](https://swagger.io)를 통해 생성된 REST API용 Python 라이브러리를 생성해야 합니다.

설치하려면 다음 단계를 수행하세요.

1. [https://editor.swagger.io](https://editor.swagger.io ) 로 이동합니다.
1. **파일**을 클릭한 다음, **URL 가져오기**를 클릭합니다.
1. Speech Service 구독 지역을 포함한 Swagger URL(예: `https://<your-region>.cris.ai/docs/v2.0/swagger`)을 입력합니다.
1. **클라이언트 생성**을 클릭하고 **Python**을 선택합니다.
1. 클라이언트 라이브러리를 저장합니다.
1. 파일 시스템 어딘가에 다운로드한 python-client-generated.zip의 압축을 풉니다.
1. pip(`pip install path/to/package/python-client`)을 사용하여 압축 해제된 python-client 모듈을 Python 환경에 설치합니다.
1. 설치된 패키지에 `swagger_client`라는 이름이 지정됩니다. `python -c "import swagger_client"` 명령을 사용하여 설치가 정상적으로 완료되었는지 확인할 수 있습니다.

> **참고:** [Swagger 자동 생성의 알려진 버그](https://github.com/swagger-api/swagger-codegen/issues/7541)로 인해 `swagger_client` 패키지를 가져오는 동안 오류가 발생할 수 있습니다.
> 이 문제는 다음 내용이 포함된 줄을
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> `swagger_client/models/model.py` 파일에서 삭제하고 다음 내용이 포함된 줄을
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> 설치된 패키지 내에 있는 `swagger_client/models/inner_error.py` 파일에서 삭제하여 해결할 수 있습니다. 오류 메시지는 설치를 돕기 위해 이러한 파일들이 어디에 있는지 알려줍니다.

## <a name="install-other-dependencies"></a>다른 종속성 설치

이 샘플은 `requests` 라이브러리를 사용합니다. 설치는 다음 명령을 통해 가능합니다.

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(`YourSubscriptionKey`, `YourServiceRegion`및 `YourFileUrl` 값을 고유한 값으로 바꿔야 합니다.)

## <a name="create-and-configure-an-http-client"></a>Http 클라이언트 만들기 및 구성
가장 먼저 필요한 것은 올바른 기본 URL과 인증 집합이 있는 Http 클라이언트입니다.
이 코드를 `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]에 삽입

## <a name="generate-a-transcription-request"></a>전사 요청 생성
다음으로, 전사 요청을 생성합니다. `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]에 이 코드 추가

## <a name="send-the-request-and-check-its-status"></a>요청을 보내고 상태 확인
이제 Speech Service에 요청을 게시하고 초기 응답 코드를 확인합니다. 이 응답 코드는 서비스에서 요청을 받았는지 여부를 표시합니다. 서비스는 전사 상태를 저장할 위치에 해당하는 Url을 응답 헤더에 반환합니다.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>전사를 완료할 때까지 대기
서비스는 전사를 비동기식으로 처리하므로 상태를 폴링해야 하는 경우가 있습니다. 5초마다 확인합니다.

이 Speech Service 리소스가 처리하는 모든 전사를 열거하고 직접 생성한 전사를 찾습니다.

다음은 성공적인 완료를 제외한 모든 상태에 대한 상태 표시가 있는 폴링 코드입니다. 다음에 수행하겠습니다.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>전사 결과 표시
서비스가 전사를 성공적으로 완료하면 결과가 상태 응답에서 가져올 수 있는 다른 Url에 저장됩니다.

여기에 해당 결과 JSON을 가져와 표시합니다.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>코드 확인
이 시점에서 코드는 다음과 같습니다. (이 버전에 몇 가지 주석을 추가함) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 인식을 테스트할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
