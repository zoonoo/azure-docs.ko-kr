---
title: '빠른 시작: 첫 번째 REST API 쿼리'
description: 이 빠른 시작에서는 REST API용 Resource Graph 엔드포인트를 호출하고 첫 번째 쿼리를 실행하는 단계를 수행합니다.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: eafbd0b408ab50f901d52e59efcfbd5f89b46fe5
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566081"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>빠른 시작: REST API를 사용하여 첫 번째 Resource Graph 쿼리 실행

REST API와 함께 Azure Resource Graph를 사용하는 첫 번째 단계는 사용 가능한 REST API를 호출하기 위한 도구가 준비되어 있는지 확인하는 것입니다. 그런 다음, 이 빠른 시작에서는 Azure Resource Graph REST API 엔드포인트를 호출하여 쿼리를 실행하고 결과를 검색하는 과정을 안내합니다.

이 과정이 끝나면 REST API 엔드포인트를 호출하기 위한 도구가 준비되어 첫 번째 Resource Graph 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>REST API 사용 시작

REST API에 대해 잘 모르는 경우에는 REST API에 대한 일반적인 이해, 특히 요청 URI 및 요청 본문에 대한 일반적인 이해가 가능하도록 [Azure REST API 참조](/rest/api/azure/)를 검토하는 단계부터 시작합니다. 이 문서에서는 이러한 개념을 사용하여 Azure Resource Graph를 사용하는 작업에 대한 지침을 제공하며 이에 대한 실무 지식이 있다고 가정합니다. [ARMClient](https://github.com/projectkudu/ARMClient) 등과 같은 도구는 인증을 자동으로 처리할 수 있으며 초보자가 사용하면 좋습니다.

Azure Resource Graph 사양은 [Azure Resource Graph REST API](/rest/api/azure-resourcegraph/)를 참조하세요.

### <a name="rest-api-and-powershell"></a>REST API 및 PowerShell

REST API 호출을 만드는 도구가 없는 경우 지침에 대해 PowerShell을 사용하는 것이 좋습니다. 다음 코드 샘플은 Azure로 인증하기 위한 헤더를 가져옵니다. 인증 헤더(다른 이름: **전달자 토큰**)를 생성하고 모든 매개 변수 또는 **요청 본문** 에 연결할 REST API URI를 제공합니다.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

`$restUri` 변수의 `{subscriptionId}`를 대체하여 구독에 대한 정보를 가져옵니다.
`$response` 변수는 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json)과 같은 cmdlet으로 구문 분석할 수 있는 `Invoke-RestMethod` cmdlet의 결과를 보관합니다. REST API 서비스 엔드포인트에 **요청 본문** 이 필요하면 JSON 형식의 변수를 `Invoke-RestMethod`의 `-Body` 매개 변수에 제공합니다.

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

원하는 환경에 REST API 도구가 추가되었으므로, 이제 간단한 Resource Graph 쿼리를 시도해 볼 수 있습니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식** 과 함께 처음 5개 Azure 리소스를 반환합니다.

각 REST API 호출의 요청 본문에는 사용자 고유의 값으로 바꿔야 하는 다음과 같은 변수가 있습니다.

- `{subscriptionID}` - 사용자의 구독 ID로 대체

1. REST API 및 `resources` 엔드포인트를 사용하여 첫 번째 Azure Resource Graph 쿼리를 실행합니다.

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 요청 본문

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 세트가 생성될 수 있습니다.

1. 다음과 같이 **Name** 속성을 기준으로 `order by`하도록 `resouces` 엔드포인트 호출을 업데이트하고 **쿼리** 를 변경합니다.

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 요청 본문

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. 다음과 같이 `resources` 엔드포인트 호출을 업데이트하고 **쿼리** 를 변경하여 먼저 **Name** 속성을 기준으로 `order by`한 다음, 상위 5개 결과로 `limit`합니다.

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 요청 본문

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

Azure Resource Graph에 대한 REST API 호출 추가 예제는 [Azure Resource Graph REST 예제](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

REST API에는 제거할 라이브러리 또는 모듈이 없습니다. _ARMClient_ 또는 _Postman_ 같은 도구를 설치했는데 더 이상 필요 없는 경우 지금 제거해도 됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph REST API 엔드포인트를 호출하고 첫 번째 쿼리를 실행했습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)
