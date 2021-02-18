---
title: Swagger 설명서-음성 서비스
titleSuffix: Azure Cognitive Services
description: Swagger 설명서는 다양한 프로그래밍 언어의 SDK를 자동으로 생성하는 데 사용할 수 있습니다. Swagger는 Microsoft 서비스의 모든 작업을 지원
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636271"
---
# <a name="swagger-documentation"></a>Swagger 설명서

Speech service는 데이터를 가져오고, 모델을 만들고, 모델 정확도를 테스트 하 고, 사용자 지정 끝점을 만들고, 일괄 처리를 큐에 대기 하 고, 구독을 관리 하는 데 사용 되는 REST Api와 상호 작용 하기 위한 Swagger 사양을 제공 합니다. [음성 스튜디오의 Custom Speech 영역을](https://aka.ms/customspeech) 통해 사용할 수 있는 대부분의 작업은 이러한 api를 사용 하 여 프로그래밍 방식으로 완료할 수 있습니다.

> [!NOTE]
> Speech service에는 [음성 텍스트](rest-speech-to-text.md) 및 [텍스트 음성 변환](rest-text-to-speech.md)에 사용할 수 있는 여러 REST api가 있습니다.  
>
> 그러나 Swagger 사양에는 [음성 텍스트 REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 및 v2.0만 설명 되어 있습니다. 다른 모든 Speech Service REST Api에 대 한 정보는 이전 단락에서 참조 된 문서를 참조 하세요.

## <a name="generating-code-from-the-swagger-specification"></a>Swagger 사양으로 코드 생성

[Swagger 사양](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)에는 다양한 경로를 신속하게 테스트할 수 있는 옵션이 포함되어 있습니다. 그러나 때로는 모든 경로에 대한 코드를 생성하고, 향후 솔루션 기반으로 사용할 수 있는 호출의 단일 라이브러리를 만드는 것이 바람직한 경우가 있습니다. Python 라이브러리를 생성하는 프로세스를 살펴보겠습니다.

Swagger를 음성 리소스의 지역으로 설정 해야 합니다. Azure Portal에서 음성 리소스 설정의 **개요** 부분에 있는 지역을 확인할 수 있습니다. 지원 되는 지역에 대 한 전체 목록은 [여기](regions.md#speech-to-text)에서 사용할 수 있습니다.

1. 브라우저에서 해당 [지역의](regions.md#speech-to-text)Swagger 사양으로 이동 합니다.  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. 해당 페이지에서 **API 정의** 를 클릭 하 고 **Swagger** 를 클릭 합니다. 표시 되는 페이지의 URL을 복사 합니다.
1. 새 브라우저에서로 이동 합니다. [https://editor.swagger.io](https://editor.swagger.io)
1. **파일**, **url 가져오기** 를 차례로 클릭 하 고 url을 붙여 넣은 다음 **확인** 을 클릭 합니다.
1. **클라이언트 생성** 을 클릭 하 고 **python** 을 선택 합니다. 클라이언트 라이브러리는 파일의 컴퓨터에 다운로드 합니다 `.zip` .
1. 다운로드에서 모든 항목을 추출 합니다. `tar -xf`를 사용 하 여 모든 항목을 추출할 수 있습니다.
1. 추출 된 모듈을 Python 환경에 설치 합니다.  
      `pip install path/to/package/python-client`
1. 설치 된 패키지의 이름은 `swagger_client` 입니다. 설치가 정상적으로 작동 하는지 확인 합니다.  
       `python -c "import swagger_client"`

[GitHub에서 음성 서비스 샘플](https://aka.ms/csspeech/samples)로 생성 한 Python 라이브러리를 사용할 수 있습니다.

## <a name="reference-documents"></a>참조 문서

* [Swagger: 음성 텍스트 REST API v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Speech-to-Text REST API](rest-speech-to-text.md)
* [Text-to-Speech REST API](rest-text-to-speech.md)

## <a name="next-steps"></a>다음 단계

* [GitHub의 음성 서비스 샘플](https://aka.ms/csspeech/samples).
* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
