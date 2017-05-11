---
title: "Azure Functions에서 OpenAPI 메타데이터 시작 | Microsoft Docs"
description: "Azure Functions에서 OpenAPI 지원 시작"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 9fd5b63259c7771d8b1e346ba3a6c10a4399532a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/01/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>함수 앱에서 OpenAPI 2.0(Swagger) 만들기(미리 보기)

이 문서는 Azure Functions에서 호스팅되는 간단한 API에 대해 OpenAPI 정의를 만드는 단계별 프로세스를 안내합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>OpenAPI(Swagger)란?
[Swagger 메타데이터](http://swagger.io/)는 API의 기능 및 작동 모드를 정의하고 기타 다양한 소프트웨어에서 REST API를 호스팅하는 함수를 사용할 수 있도록 하는 파일입니다. PowerApps, [API 앱](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), [Postman](https://www.getpostman.com/docs/importing_swagger) 등의 타사 개발자 도구와 [기타 다양한 패키지](http://swagger.io/tools/)에서 Swagger 정의로 API를 사용할 수 있습니다.

## <a name="prepare-function"></a>간단한 API가 포함된 함수 만들기
  OpenAPI 정의를 만들기 위해 간단한 API가 포함된 함수를 만들어야 합니다. 이미 함수 앱에서 API를 호스팅하고 있는 경우 바로 다음 섹션으로 건너뛸 수 있습니다.
1. 새로운 함수 앱 만들기
  1. [Azure Portal](https://portal.azure.com) > `+ New` > “함수 앱” 검색
1. 사용자의 새 함수 앱 내에서 새 HTTP 트리거 함수 만들기
  1. 함수는 매우 간단한 REST API를 정의하는 코드로 미리 채워져 있습니다.
  1. 함수에 쿼리 매개 변수로 전달되거나 본문에 있는 모든 문자열은 “Hello {input}”로 반환됩니다
1. 새 HTTP 트리거 함수의 `Integrate` 탭으로 이동
  1. `Allowed HTTP methods`를 `Selected methods`로 전환
  1. `Selected HTTP methods`에서 POST를 제외한 모든 동사를 선택 취소합니다
    ![선택된 HTTP 메서드](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. 이 단계는 나중에 API 정의를 간소화합니다.

## <a name="enable"></a>API 정의 지원을 사용하도록 설정
1. `your function name` > `API Definition (preview)`로 이동합니다.
1. `API Definition Source`를 `Function`로 설정합니다
  1. 이 단계에서는 함수 앱의 도메인에서 OpenAPI 파일을 호스팅하는 끝점, [OpenAPI 편집기](http://editor.swagger.io)의 인라인 복사, 빠른 시작 정의 생성기를 포함하여 함수 앱의 OpenAPI 옵션을 사용하도록 설정합니다.
![사용 가능한 정의](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>템플릿에서 API 정의 만들기
1. `Generate API Definition template`을 클릭합니다.
  1. 이 단계에서는 함수 앱에서 HTTP 트리거 함수가 있는지 검색하고 functions.json에서 해당 정보를 사용하여 OpenAPI 문서를 생성합니다.
2. `paths: /api/yourfunctionroute post:`에 작업 개체 추가
  1. 빠른 시작 OpenAPI 문서는 전체 OpenAPI 문서의 요약본입니다. 여기서 작업 개체, 응답 템플릿 등 더 많은 메타데이터가 전체 OpenApI 정의여야 합니다.
  1. 아래의 샘플 작업은 생산/소비 섹션, 매개 변수 개체, 응답 개체가 채워져 있습니다.
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary는 논리 앱, 흐름, PowerApps에 표시 이름을 제공합니다.
>
> 자세히 알아보려면 [PowerApps에 대한 Swagger 정의 사용자 지정](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)을 확인하세요.

3. 변경 사항을 저장하려면 `save`를 클릭합니다 ![템플릿 정의 추가](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>API 정의 사용
1. 원시 OpenAPI 문서를 보려면 `API definition URL`을 복사한 다음 새 브라우저 탭에 붙여 넣습니다.
1. OpenAPI 문서를 도구로 가져와 해당 URL을 사용하는 테스트 및 통합에 사용할 수 있습니다.
  1. 대부분의 Azure 리소스는 함수 응용 프로그램 설정에 저장된 API 정의 URL을 사용하여 OpenAPI 문서를 자동으로 가져올 수 있습니다. 해당 URL은 `Function` API 정의 원본의 일부로 업데이트됩니다.


## <a name="test-definition"></a>Swagger UI를 사용하여 API 정의 테스트
임베디드 API 정의 편집기의 UI 보기에 테스트 도구가 포함되어 있스니다. 각자의 API 키를 추가한 다음 각 메서드 아래에 있는 `Try this operation` 단추를 사용합니다. 이 도구는 API 정의를 사용하여 요청 형식을 지정합니다. 성공적 응답은 정의가 올바르다는 것을 나타냅니다.

### <a name="steps"></a>단계:

1. 함수 API 키 복사
  1. API 키는 `function name` > `Keys` > `Function Keys`
  ![기능 키](./media/functions-api-definition-getting-started/functionkey.png) 아래 HTTP 트리거 함수에서 찾을 수 있습니다.
1. `API Definition (preview)` 페이지로 이동합니다.
  1. `Authenticate`를 클릭하고 맨 위 보안 개체에 함수 API 키를 추가합니다.
  ![OpenAPI 키](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. `/api/yourfunctionroute` > `POST`를 선택합니다.
1. `Try it out`을 클릭한 다음 테스트할 이름을 입력합니다.
1. `Pretty`
![API 정의 테스트](./media/functions-api-definition-getting-started/definitionTest.png)에서 SUCCESS 결과를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Functions 개요의 OpenAPI 정의](functions-api-definition.md)
  * OpenAPI 지원에 대한 자세한 내용은 전체 설명서를 참조하세요.
* [Azure Functions 개발자 참조](functions-reference.md)  
  * 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.
* [Azure Functions GitHub 리포지토리](https://github.com/Azure/Azure-Functions/)
  * API 정의 지원 미리 보기에 대한 의견을 보내려면 Functions GitHub를 확인하세요! 업데이트 정보를 확인하려는 항목에 대해 GitHub 문제를 만드세요.

