---
title: RESTful 엔드포인트 작성
description: 이 자습서에서는 사용자 지정 공급자에 대한 RESTful 엔드포인트를 작성하는 방법을 보여줍니다. 지원되는 RESTful HTTP 메서드에 대한 요청 및 응답을 처리하는 방법을 자세히 설명합니다.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75648730"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>사용자 지정 공급자를 위한 RESTful 엔드포인트 작성

사용자 지정 공급자는 Azure와 엔드포인트 사이의 계약입니다. 사용자 지정 공급자를 사용하면 Azure에서 사용자 지정할 수 있습니다. 이 자습서에서는 사용자 지정 공급자 RESTful 엔드포인트를 작성하는 방법을 보여줍니다. Azure 사용자 지정 공급자에 익숙하지 않은 경우 [사용자 지정 리소스 공급자에 대한 개요](overview.md)를 참조하세요.

> [!NOTE]
> 이 자습서는 [Azure 사용자 지정 공급자를 위한 Azure Functions 설정](./tutorial-custom-providers-function-setup.md) 자습서를 기반으로 합니다. 이 자습서의 일부 단계는 Azure 함수 앱이 사용자 지정 공급자에서 작동하도록 설정된 경우에만 작동합니다.

## <a name="work-with-custom-actions-and-custom-resources"></a>사용자 지정 작업 및 사용자 지정 리소스 사용

이 자습서에서는 사용자 지정 공급자에 대한 RESTful 엔드포인트로 작동하도록 함수 앱을 업데이트합니다. Azure에서 리소스와 작업은 다음과 같은 기본 RESTful 사양 이후에 모델링됩니다.

- **PUT**: 새 리소스 만들기
- **GET(instance)** : 기존 리소스 검색
- **DELETE**: 기존 리소스 삭제
- **POST**: 작업 트리거
- **GET(collection)** : 모든 기존 리소스 나열

 이 자습서에서는 Azure Table 스토리지를 사용합니다. 하지만 아무 데이터베이스 또는 스토리지 서비스를 사용해도 됩니다.

## <a name="partition-custom-resources-in-storage"></a>스토리지의 사용자 지정 리소스 분할

RESTful 서비스를 만들고 있으므로, 만든 리소스를 저장해야 합니다. Azure Table 스토리지의 경우 데이터에 대한 파티션 및 행 키를 생성해야 합니다. 사용자 지정 공급자의 경우 데이터는 사용자 지정 공급자로 분할해야 합니다. 들어오는 요청이 사용자 지정 공급자에게 전달되면 사용자 지정 공급자는 `x-ms-customproviders-requestpath` 헤더를 엔드포인트로 나가는 요청에 추가합니다.

다음 예제에서는 사용자 지정 리소스에 대한 `x-ms-customproviders-requestpath` 헤더를 보여줍니다.

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

예제의 `x-ms-customproviders-requestpath` 헤더를 기준으로, 다음 표에 나와 있는 것처럼 스토리지에 대한 *partitionKey* 및 *rowKey* 매개 변수를 만들 수 있습니다.

매개 변수 | 템플릿 | Description
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | *partitionKey* 매개 변수는 데이터를 분할하는 방법을 지정합니다. 일반적으로 데이터는 사용자 지정 공급자 인스턴스에 의해 분할됩니다.
*rowKey* | `{myResourceType}:{myResourceName}` | *rowKey* 매개 변수는 데이터의 개별 식별자를 지정합니다. 일반적으로 식별자는 리소스의 이름입니다.

사용자 지정 리소스를 모델링하는 새 클래스도 만들어야 합니다. 이 자습서에서는 함수 앱에 다음 **CustomResource** 클래스를 추가합니다.

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource**는 모든 입력 데이터를 수락하는 간단한 제네릭 클래스입니다. 데이터를 저장하는 데 사용되는 **TableEntity**를 기반으로 합니다. **CustomResource** 클래스는 **TableEntity**에서 두 가지 속성 **partitionKey** 및 **rowKey**를 상속합니다.

## <a name="support-custom-provider-restful-methods"></a>사용자 지정 공급자 RESTful 메서드 지원

> [!NOTE]
> 이 자습서의 코드를 직접 복사하지 않는 경우 응답 내용은 `Content-Type` 헤더를 `application/json`으로 설정하는 유효한 JSON이어야 합니다.

데이터 분할을 설정했으므로, 이번에는 사용자 지정 리소스 및 사용자 지정 작업에 대한 기본 CRUD와 트리거 메서드를 만들겠습니다. 사용자 지정 공급자는 프록시 역할을 하기 때문에 RESTful 엔드포인트가 요청 및 응답을 모델링하고 처리해야 합니다. 다음 코드 조각은 기본 RESTful 작업을 처리하는 방법을 보여줍니다.

### <a name="trigger-a-custom-action"></a>사용자 지정 작업 트리거

사용자 지정 공급자의 경우 사용자 지정 작업은 POST 요청을 통해 트리거됩니다. 사용자 지정 작업은 필요에 따라 입력 매개 변수 세트가 포함된 요청 본문을 수락할 수 있습니다. 그런 다음, 작업에서는 작업의 결과와 성공 또는 실패 여부를 알리는 응답을 반환합니다.

함수 앱에 다음 **TriggerCustomAction** 메서드를 추가합니다.

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

**TriggerCustomAction** 메서드는 들어오는 요청을 수락하고, 상태 코드가 있는 응답을 단순히 반환합니다.

### <a name="create-a-custom-resource"></a>사용자 지정 리소스 만들기

사용자 지정 공급자의 경우 사용자 지정 리소스는 PUT 요청을 통해 만들어집니다. 사용자 지정 공급자는 사용자 지정 리소스에 대한 속성 세트가 포함된 JSON 요청 본문을 수락합니다. Azure의 리소스는 RESTful 모델을 따릅니다. 동일한 요청 URL을 사용하여 리소스를 만들고, 검색하고, 삭제할 수 있습니다.

새 리소스를 만드는 다음 **CreateCustomResource** 메서드를 추가합니다.

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

**CreateCustomResource** 메서드는 Azure 관련 필드 **id**, **이름**, **유형**을 포함하도록 들어오는 요청을 업데이트합니다. 이러한 필드는 Azure 전체의 서비스에서 사용되는 최상위 속성입니다. 이러한 필드를 통해 사용자 지정 공급자는 Azure Policy, Azure Resource Manager 템플릿, Azure Activity Logs 등의 다른 서비스와 상호 운용할 수 있습니다.

속성 | 예제 | Description
---|---|---
**name** | {myCustomResourceName} | 사용자 지정 리소스의 이름입니다.
**type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | 리소스 유형 네임스페이스입니다.
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | 리소스 ID입니다.

속성 추가 외에도, JSON 문서를 Azure Table 스토리지에 저장했습니다.

### <a name="retrieve-a-custom-resource"></a>사용자 지정 리소스 검색

사용자 지정 공급자의 경우 GET 요청을 통해 사용자 지정 리소스가 검색됩니다. 사용자 지정 공급자는 JSON 요청 본문을 수락하지 *않습니다*. GET 요청의 경우 엔드포인트에서는 `x-ms-customproviders-requestpath` 헤더를 사용하여 이미 만들어진 리소스를 반환합니다.

기존 리소스를 검색하는 다음 **RetrieveCustomResource** 메서드를 추가합니다.

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Azure에서 리소스는 RESTful 모델을 따릅니다. GET 요청이 수행되면 리소스를 만드는 요청 URL도 리소스를 반환합니다.

### <a name="remove-a-custom-resource"></a>사용자 지정 리소스 제거

사용자 지정 공급자의 경우 사용자 지정 리소스는 DELETE 요청을 통해 제거됩니다. 사용자 지정 공급자는 JSON 요청 본문을 수락하지 *않습니다*. DELETE 요청의 경우 엔드포인트에서는 `x-ms-customproviders-requestpath` 헤더를 사용하여 이미 만들어진 리소스를 삭제합니다.

기존 리소스를 제거하는 다음 **RemoveCustomResource** 메서드를 추가합니다.

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Azure에서 리소스는 RESTful 모델을 따릅니다. DELETE 요청이 수행되면 리소스를 만드는 요청 URL도 리소스를 삭제합니다.

### <a name="list-all-custom-resources"></a>모든 사용자 지정 리소스 나열

사용자 지정 공급자의 경우 컬렉션 GET 요청을 사용하여 기존 사용자 지정 리소스 목록을 열거할 수 있습니다. 사용자 지정 공급자는 JSON 요청 본문을 수락하지 *않습니다*. GET 요청 컬렉션의 경우 엔드포인트에서는 `x-ms-customproviders-requestpath` 헤더를 사용하여 이미 만들어진 리소스를 열거합니다.

기존 리소스를 열거하는 다음 **EnumerateAllCustomResources** 메서드를 추가합니다.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey QueryComparisons.GreaterThan 및 QueryComparisons.LessThan은 문자열에 대한 "startswith" 쿼리를 수행하는 Azure Table 스토리지 구문입니다.

기존 리소스를 모두 나열하려면 리소스가 사용자 지정 공급자 파티션 아래에 있는지 확인하는 Azure Table 스토리지 쿼리를 생성합니다. 이 쿼리는 행 키가 동일한 `{myResourceType}` 값으로 시작하는지 확인합니다.

## <a name="integrate-restful-operations"></a>RESTful 작업 통합

모든 RESTful 메서드가 함수 앱에 추가되면 다양한 REST 요청을 처리하는 함수를 호출하도록 기본 **Run** 메서드를 업데이트합니다.

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

업데이트된 **Run** 메서드에는 Azure Table 스토리지에 대해 추가한 *tableStorage* 입력 바인딩이 포함되어 있습니다. 메서드의 첫 번째 부분은 이제 `x-ms-customproviders-requestpath` 헤더를 읽고, `Microsoft.Azure.Management.ResourceManager.Fluent` 라이브러리를 사용하여 값을 리소스 ID로 구문 분석합니다. `x-ms-customproviders-requestpath` 헤더는 사용자 지정 공급자를 통해 전송되고, 들어오는 요청의 경로를 지정합니다.

구문 분석된 리소스 ID를 사용하면 데이터에 대한 **partitionKey** 및 **rowKey** 값을 생성하여 사용자 지정 리소스를 조회하거나 저장할 수 있습니다.

메서드와 클래스를 추가한 후에는 함수 앱의 **using** 메서드를 업데이트해야 합니다. C# 파일의 맨 위에 다음 코드를 추가합니다.

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

이 자습서를 진행하다가 막히는 경우 [사용자 지정 공급자 C# RESTful 엔드포인트 참조](./reference-custom-providers-csharp-endpoint.md)에서 전체 코드 샘플을 찾을 수 있습니다. 함수 앱을 마쳤으면 함수 앱 URL을 저장합니다. 이후에 나오는 다른 자습서에서 함수 앱을 트리거하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 사용자 지정 공급자 엔드포인트에서 작동하는 RESTful 엔드포인트를 작성했습니다. 사용자 지정 공급자를 만드는 방법을 알아보려면 [자습서: 사용자 지정 공급자 만들기](./tutorial-custom-providers-create.md) 문서로 이동하세요.
