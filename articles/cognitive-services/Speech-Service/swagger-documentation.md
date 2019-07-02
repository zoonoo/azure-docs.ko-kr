---
title: Swagger 설명서 - Speech Services
titleSuffix: Azure Cognitive Services
description: Swagger 설명서는 다양한 프로그래밍 언어의 SDK를 자동으로 생성하는 데 사용할 수 있습니다. Swagger는 Microsoft 서비스의 모든 작업을 지원
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 6cf3ab6480900aa763598120e6ff7e308f5044e1
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743213"
---
# <a name="swagger-documentation"></a>Swagger 설명서

Speech Services는 데이터를 가져오고, 모델을 만들고, 모델 정확도를 테스트하고, 사용자 지정 엔드포인트를 만들고, 일괄 처리 음성 텍스트를 큐에 추가하고, 구독을 관리하는 데 사용되는 몇 가지 REST API와 상호 작용하는 Swagger 사양을 제공합니다. 사용자 지정 음성 포털을 통해 제공되는 대부분의 작업은 이러한 API를 사용하여 프로그래밍 방식으로 완료할 수 있습니다. 

> [!NOTE]
> 음성을 텍스트로 변환 및 텍스트를 음성으로 변환 작업은 Swagger 사양에 문서화된 REST API를 통해 지원됩니다.

## <a name="generating-code-from-the-swagger-specification"></a>Swagger 사양으로 코드 생성

[Swagger 사양](https://cris.ai/swagger/ui/index)에는 다양한 경로를 신속하게 테스트할 수 있는 옵션이 포함되어 있습니다. 그러나 때로는 모든 경로에 대한 코드를 생성하고, 향후 솔루션 기반으로 사용할 수 있는 호출의 단일 라이브러리를 만드는 것이 바람직한 경우가 있습니다. Python 라이브러리를 생성하는 프로세스를 살펴보겠습니다.

Swagger를 Speech Service 구독과 동일한 Azure 지역으로 설정해야 합니다. 자신의 Azure 지역은 Azure Portal의 해당 Speech Services 리소스 아래에서 확인할 수 있습니다. 지원되는 지역의 전체 목록은 [Azure 지역](regions.md)을 참조하세요.

1. [https://editor.swagger.io](https://editor.swagger.io ) 으로 이동합니다.
2. **파일**을 클릭한 다음, **가져오기**를 클릭합니다.
3. Speech Services 구독의 Azure 지역을 포함한 Swagger URL `https://<your-region>.cris.ai/docs/v2.0/swagger`를 입력합니다.
4. **클라이언트 생성**을 클릭하고 Python을 선택합니다.
5. 클라이언트 라이브러리를 저장합니다.

생성한 Python 라이브러리를 [GitHub의 Speech Services 샘플](https://aka.ms/csspeech/samples)에 사용할 수 있습니다.

## <a name="reference-docs"></a>참조 문서

* [REST(Swagger): 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)
* [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)

## <a name="next-steps"></a>다음 단계

* [GitHub의 Speech Services 샘플](https://aka.ms/csspeech/samples)
* [무료로 Speech Service 구독 키 받기](get-started.md)
