---
title: Azure API Management를 사용하여 함수에 대한 OpenAPI 정의 만들기
description: 다른 앱 및 서비스를 사용하도록 설정하여 Azure에서 함수를 호출할 수 있도록 하는 OpenAPI 정의를 만듭니다.
services: functions
keywords: OpenAPI, Swagger, 클라우드 앱, 클라우드 서비스,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3ad304bc8f038d4009352dae72d70079828c26ba
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141498"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Azure API Management를 사용하여 함수에 대한 OpenAPI 정의 만들기

REST API는 종종 OpenAPI 정의를 사용하여 설명됩니다. 이 정의에는 API에서 사용할 수 있는 작업 및 API에 대한 요청 및 응답 데이터가 구성되는 방식에 대한 정보가 포함됩니다.

이 자습서에서는 풍차의 응급 복구가 비용 효율적인지 여부를 결정하는 함수를 만듭니다. 그런 다음, 해당 함수가 다른 앱 및 서비스에서 호출될 수 있도록 [Azure API Management](../api-management/api-management-key-concepts.md)를 사용하여 함수 앱에 대한 OpenAPI 정의를 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 함수 만들기
> * Azure API Management를 사용하여 OpenAPI 정의 생성
> * 함수를 호출하여 정의 테스트

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 더 쉽게 리소스를 관리, 배포, 크기 조정 및 공유할 수 있습니다.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>함수 만들기

이 자습서에서는 다음과 같은 두 개의 매개 변수를 사용하는 HTTP 트리거 함수를 사용합니다.

* 터빈 복구 예상 시간(시간)
* 터빈 용량(킬로와트) 

그런 후 이 함수는 복구 비용과 터빈이 24시간 후에 가져올 수 있는 수익을 계산합니다. [Azure Portal](https://portal.azure.com)에서 HTTP 트리거 함수를 만들려면 다음을 수행합니다.

1. 함수 앱을 확장한 후 **함수** 옆의 **+** 단추를 선택합니다. **포털 내** > **계속**을 선택합니다.

1. **추가 템플릿...** 을 선택한 다음, **템플릿 마침 및 보기**를 선택합니다.

1. HTTP 트리거를 선택하고, 함수 **이름**에 `TurbineRepair`를 입력하고, **[인증 수준](functions-bindings-http-webhook.md#http-auth)** 에 대해 `Function`을 선택한 다음, **만들기**를 선택합니다.  

    ![OpenAPI에 대한 HTTP 함수 만들기](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. run.csx C# 스크립트 파일 내용을 다음 코드로 바꾼 다음, **저장**을 선택합니다.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    이 함수 코드는 응급 복구가 비용 효율적인지 여부와 터빈이 나타내는 수익 기회 및 터빈 수리 비용을 나타내기 위해 `Yes` 또는 `No` 메시지를 반환합니다.

1. 이 함수를 테스트하려면 오른쪽 끝의 **테스트**를 클릭하여 테스트 탭을 확장합니다. **요청 본문**에 다음 값을 입력한 다음 **실행**을 클릭합니다.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Azure Portal에서 함수 테스트](media/functions-openapi-definition/test-function.png)

    응답의 본문에 다음 값이 반환됩니다.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

이제 응급 복구 작업의 비용 효율성을 결정하는 함수가 만들어졌습니다. 다음으로 함수 앱에 대한 OpenAPI 정의를 생성합니다.

## <a name="generate-the-openapi-definition"></a>OpenAPI 정의 생성

이제 OpenAPI 정의를 생성할 준비가 되었습니다.

1. 함수 앱을 선택한 다음, **플랫폼 기능**, **모든 설정**을 선택합니다.

    ![Azure Portal에서 함수 테스트](media/functions-openapi-definition/select-all-settings-openapi.png)

1. 아래로 스크롤한 다음, **API Management** > **새로 만들기**를 선택하여 새 API Management 인스턴스를 만듭니다.

    ![링크 기능](media/functions-openapi-definition/link-apim-openapi.png)

1. 이미지 아래의 표에 지정된 API Management 설정을 사용합니다.

    ![새 API Management 서비스 만들기](media/functions-openapi-definition/new-apim-service-openapi.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | 전역적으로 고유한 이름 | 함수는 함수 앱의 이름을 기반으로 생성됩니다. |
    | **구독** | 사용자의 구독 | 이 새 리소스가 만들어지는 구독입니다. |  
    | **[리소스 그룹](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 함수 앱과 동일한 리소스로, 사용자에 맞게 설정해야 합니다. |
    | **위치**: | 미국 서부 | 미국 서부 위치 선택 |
    | **조직 이름** | Contoso | 개발자 포털 및 이메일 알림에 사용되는 조직의 이름입니다. |
    | **관리자 전자 메일** | 사용자 이메일 | API Management로부터 시스템 알림을 수신하는 이메일입니다. |
    | **가격 책정 계층** | 소비(미리 보기) | 전체 가격 책정 세부 정보는 [API Management 가격 책정 페이지](https://azure.microsoft.com/pricing/details/api-management/)를 참조하세요. |
    | **Application Insights** | 사용자 인스턴스 | 함수 앱에서 사용되는 동일한 Application Insights를 사용합니다. |

1. **만들기**를 선택하여 API Management 인스턴스를 만듭니다. 몇 분 정도 소요될 수 있습니다.

1. **Application Insights 사용**을 선택하여 함수 애플리케이션과 동일한 위치에 로그를 보낸 다음, 나머지 기본 값을 수락하고 **링크 API**를 선택합니다.

1. **TurbineRepair** 함수가 강조 표시된 상태로 **Azure Functions 가져오기**가 열립니다. **선택**을 선택하여 계속합니다.

    ![Azure Functions를 API Management로 가져오기](media/functions-openapi-definition/import-function-openapi.png)

1. **Function App에서 만들기** 페이지에서 기본값을 수락하고 **만들기**를 선택합니다.

    ![Function App에서 만들기](media/functions-openapi-definition/create-function-openapi.png)

API가 함수에 대해 만들어집니다.

## <a name="test-the-openapi-definition"></a>OpenAPI 정의 테스트

API 정의를 사용하기 전에 작동하는지 확인해야 합니다.

1. 함수의 **테스트** 탭에서 **게시** 작업을 선택합니다.

1. **시간** 및 **용량**에 대한 값을 입력합니다.

```json
{
"hours": "6",
"capacity": "2500"
}
```

1. **보내기**를 클릭한 다음, HTTP 응답을 봅니다.

    ![테스트 함수 API](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 함수 만들기
> * Azure API Management를 사용하여 OpenAPI 정의 생성
> * 함수를 호출하여 정의 테스트

API Management에 대해 자세히 알아보려면 다음 항목으로 이동합니다.

> [!div class="nextstepaction"]
> [API Management](../api-management/api-management-key-concepts.md)
