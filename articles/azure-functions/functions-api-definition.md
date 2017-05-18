---
title: "Azure Functions의 OpenAPI 메타데이터 | Microsoft Docs"
description: "Azure Functions에서 OpenAPI 지원 개요"
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: aac362f5123038cc39e0d2b32c10f7747a702cfe
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions에서 OpenAPI 2.0 메타데이터 지원(미리 보기)
이 미리 보기 기능에서는 함수 앱 안에서 OpenAPI 2.0(이전의 Swagger) 정의를 작성하고 함수 앱을 사용하여 해당 파일을 호스팅할 수 있습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>OpenAPI 메타데이터란?
[OpenAPI 메타데이터](http://swagger.io/)는 기타 다양한 소프트웨어에서 REST API를 호스팅하는 함수를 사용할 수 있도록 합니다. [PowerApps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), API 앱 등의 Microsoft 제품부터 [Postman](https://www.getpostman.com/docs/importing_swagger)과 같은 타사 개발자 도구와 [기타 다양한 패키지](http://swagger.io/tools/)에서 이러한 함수를 사용할 수 있습니다.

>[!TIP]
>우선 [초보자를 위한 자습서](./functions-api-definition-getting-started.md)부터 시작한 다음 이 문서로 돌아와 구체적 기능에 대해 알아보는 것이 좋습니다.

## <a name="enable"></a>OpenAPI 정의 사용 지원
* 함수 앱 설정의 `API Definition (preview)` 페이지에서 모든 OpenAPI 설정을 구성할 수 있습니다.
* 호스팅된 OpenAPI 정의와 빠른 시작 정의 생성을 사용하도록 설정하려면 `API definition source`를 `Function`으로 설정합니다.
  * `External URL`을 사용하면 함수가 다른 곳에 호스팅된 OpenAPI 정의를 사용할 수 있습니다.

## <a name="generate-definition"></a>함수 메타데이터에서 Swagger Skeleton 생성
템플릿은 첫 번째 OpenAPI 정의를 작성하는 데 적절한 방법입니다. 정의 템플릿 기능은 각 HTTP 트리거 기능에 대한 function.json의 모든 메타데이터를 사용하여 스파스 OpenAPI 정의를 만듭니다. **[OpenAPI 사양](http://swagger.io/specification/)(예: 요청 및 응답 템플릿)에서 API에 대한 추가 정보를 입력해야 합니다.**

[이 초보자를 위한 자습서에서 단계별 지침을 확인하십시오](./functions-api-definition-getting-started.md)

### <a name="templates"></a>사용 가능한 템플릿

|이름| 설명 |
|:-----|:-----|
|생성된 정의|함수의 기존 데이터에서 유추할 수 있는 최대 정보를 이용한 OpenAPI 정의|

### <a name="quickstart-details"></a>생성된 정의에 포함된 메타데이터

다음 표는 포털 설정과 생성된 해골 Swagger에 매핑된 function.json에서 해당하는 데이터를 나타냅니다.

|Swagger.json|포털 UI|Function.json|
|:----|:-----|:-----|
|[호스트](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*없음*
|[경로](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|바인딩: 경로
|[경로 항목](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|바인딩: 메서드
|[보안](http://swagger.io/specification/#security-scheme-object-112)|구성|*없음*|
|operationID*|경로 + 허용 동사|경로 + 허용 동사|

\*작업 ID는 PowerApps + 흐름과 통합하는 경우에만 필요합니다.
> [!NOTE]
>  x-ms-summary는 논리 앱, 흐름, PowerApps에 표시 이름을 제공합니다.
>
> 자세히 알아보려면 [PowerApps에 대한 Swagger 정의 사용자 지정](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)을 확인하세요.

## <a name="CICD"></a>CI/CD를 사용하여 API 정의 설정

 소스 제어에서 API 정의를 수정할 수 있도록 설정하기 전에 포털에서 호스팅되는 API 정의를 활성화해야 합니다. 아래의 지침을 따르세요.

1. 함수 앱 설정에서 `API Definition (preview)`으로 이동합니다.
  1. `API definition source`를 `Function`으로 설정합니다.
  1. `Generate API definition template`을 클릭한 다음 `Save`를 클릭하여 나중에 수정하기 위한 템플릿 정의를 만듭니다.
  1. `API definition URL` 및 `key`를 기록해 두십시오.
1. [CI/CD를 설정합니다](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. 다음 위치의 소스 제어에서 `swagger.json`을 수정합니다 `\site\wwwroot\.azurefunctions\swagger\swagger.json`

이제 1.3단계에서 기록해 둔 `API definition URL` 및 `key`에서 함수 앱이 리포지토리의 `swagger.json` 변경 사항을 호스팅합니다.

## <a name="next-steps"></a>다음 단계
* [초보자를 위한 자습서](functions-api-definition-getting-started.md)
  * 연습에서 OpenAPI 정의가 어떻게 사용되는지 확인해 보세요!
* [Azure Functions Github 리포지토리](https://github.com/Azure/Azure-Functions/)
  * API 정의 지원 미리 보기에 대한 의견을 보내려면 Functions Github을 확인하세요! 업데이트 정보를 확인하려는 항목에 대해 github 문제를 만드세요.
* [Azure Functions 개발자 참조](functions-reference.md)  
  * 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.

