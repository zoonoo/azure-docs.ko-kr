---
title: Azure Digital Twins Swagger를 사용하는 방법 이해 | Microsoft Docs
description: Azure Digital Twins Swagger를 사용하는 방법
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: adgera
ms.openlocfilehash: dbadc90a206937d4c9f1d7b75a872d93b1a8a587
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323992"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Azure Digital Twins Swagger를 사용하는 방법

프로비전된 Azure Digital Twins 인스턴스에는 각각 자동으로 생성된 고유한 Swagger 참조 설명서가 포함됩니다.

[Swagger](https://swagger.io/)(또는 [OpenAPI](https://www.openapis.org/))는 복잡한 API 정보를 대화형 및 언어 중립적 참조 리소스로 통합합니다. 특히, Swagger는 API에 대한 작업을 수행하는 데 사용할 JSON 페이로드, HTTP 메서드 및 특정 엔드포인트에 대한 중요 참고 자료를 제공합니다.

## <a name="swagger-summary"></a>Swagger 요약

Swagger는 다음을 비롯한 API의 대화형 요약을 제공합니다.

* API 및 개체 모델 정보
* 필수 요청 페이로드, 헤더, 매개 변수, 컨텍스트 경로 및 HTTP 메서드를 지정하는 REST API 엔드포인트
* API 기능 테스트
* HTTP 응답의 유효성을 검사하고 확인하는 예제 응답 정보
* 오류 코드 정보

따라서 Swagger는 관리 API에 대해 수행된 개발 및 테스트 호출을 보조하는 편리한 도구입니다.

> [!TIP]
> 참조로 Swagger 미리 보기는 API 기능 집합을 설명하기 위해 제공됩니다.
> [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)에서 호스팅됩니다.

다음에서 생성된 고유한 관리 API Swagger 설명서에 액세스할 수 있습니다.

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| 사용자 지정 특성 이름 | 다음 항목으로 교체 |
| --- | --- |
| `yourInstanceName` | Azure Digital Twins 인스턴스의 이름 |
| `yourLocation` | 인스턴스를 호스팅하는 서버 지역 |

## <a name="reference-material"></a>참조 자료

자동으로 생성된 참조 자료는 중요 개념 및 개체 모델을 설명합니다.

간결한 요약에서는 API를 설명합니다.

![Swagger 상위][1]

핵심 API 개체 모델도 나열됩니다.

![Swagger 모델][2]

키 특성의 자세한 요약은 각 나열된 개체 모델을 클릭하면 됩니다.

![Swagger 모델][3]

생성된 Swagger 개체 모델은 사용 가능한 모든 Azure Digital Twins [개체 및 API](./concepts-objectmodel-spatialgraph.md)를 보는 데 편리합니다. 개발자가 Azure Digital Twins에서 솔루션을 빌드할 때 사용할 훌륭한 리소스입니다.

## <a name="endpoint-summary"></a>엔드포인트 요약

또한 Swagger는 API를 구성하는 모든 엔드포인트의 철저한 개요를 제공합니다.

나열된 각 엔드포인트에는 다음과 같은 필수 요청 정보도 포함됩니다.

* 필수 매개 변수
* 필수 매개 변수 데이터 형식
* 리소스에 액세스하는 HTTP 메서드

![Swagger 엔드포인트][4]

자세한 개요를 보려면 각 리소스를 클릭하면 됩니다.

## <a name="using-swagger-to-test-endpoints"></a>Swagger를 사용하여 엔드포인트 테스트

Swagger의 강력한 기능 중 하나는 문서 UI 통해 직접 API 엔드포인트를 **사용** 또는 테스트할 수 있는 기능입니다.

특정 엔드포인트를 클릭하면 **사용해 보기** 단추가 표시됩니다.

![Swagger 사용][5]

해당 섹션을 확장하면 각 필수 및 선택적 매개 변수에 대한 입력 필드가 표시됩니다. 값을 적절하게 입력하고 **실행**을 클릭합니다.

![Swagger 사용됨][6]

테스트를 실행하면 응답 데이터의 유효성을 검사할 수 있습니다.

## <a name="swagger-response-data"></a>Swagger 응답 데이터

나열된 각 엔드포인트에는 개발 및 테스트의 유효성을 검사하는 응답 본문 데이터도 포함됩니다. 이러한 예제에는 성공적인 HTTP 요청에 대한 원하는 상태 코드 및 JSON이 포함됩니다.

![Swagger 응답][7]

예제에는 실패한 테스트를 디버그하거나 개선하는 오류 코드도 포함됩니다.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 권한 부여

OAuth 2.0으로 보호되는 API 리소스에 대한 요청을 대화형으로 테스트하려면 [공식 설명서](https://swagger.io/docs/specification/authentication/oauth2/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 Azure Digital Twins 개체 모델 및 공간 인텔리전스 그래프에 대해 알아보려면 [이 문서](./concepts-objectmodel-spatialgraph.md)를 참고하세요.

관리 API를 사용하여 인증하는 방법을 알아보려면 [API를 사용하여 인증](./security-authenticating-apis.md)을 참고하세요.

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
