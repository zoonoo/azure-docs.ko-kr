---
title: 비동기 작업의 상태
description: Azure에서 비동기 작업을 추적하는 방법에 대해 설명합니다. 장기 실행 작업의 상태를 가져오는 데 사용하는 값을 보여줍니다.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: f5823bc8f6c0d5b334a638d4ad350eb2f9ef2b28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932746"
---
# <a name="track-asynchronous-azure-operations"></a>Azure 비동기 작업 추적

일부 Azure REST 작업은 작업을 신속하게 완료할 수 없기 때문에 비동기적으로 실행합니다. 이 문서에서는 응답에서 반환되는 값을 통해 비동기 작업의 상태를 추적하는 방법을 설명합니다.  

## <a name="status-codes-for-asynchronous-operations"></a>비동기 작업의 상태 코드

비동기 작업은 처음에 다음 중 하나의 HTTP 상태 코드를 반환합니다.

* 201(만들어짐)
* 202(수락됨)

작업이 성공적으로 완료되면 다음 중 하나를 반환합니다.

* 200(확인)
* 204(내용 없음)

[REST API 설명서](/rest/api/azure/)를 참조하여 실행 중인 작업에 대한 응답을 볼 수 있습니다.

201 또는 202 응답 코드를 가져온 후에는 작업의 상태를 모니터링할 준비가 된 것입니다.

## <a name="url-to-monitor-status"></a>상태를 모니터링할 URL

비동기 작업의 상태를 모니터링 하는 방법에는 두 가지가 있습니다. 원래 요청에서 반환된 헤더 값을 검사하여 올바른 방법을 결정합니다. 먼저 다음을 찾습니다.

* `Azure-AsyncOperation` - 작업의 진행 상태를 확인하는 URL입니다. 작업이 이 값을 반환하는 경우 작업의 상태를 추적하는 데 사용합니다.
* `Retry-After` - 비동기 작업의 상태를 확인하기 전에 대기할 시간(초)입니다.

`Azure-AsyncOperation`가 헤더 값 중 하나가 아닌 경우 다음을 찾습니다.

* `Location` - 작업이 완료된 시점을 결정하는 URL입니다. Azure-AsyncOperation이 반환되지 않은 경우 이 값만 사용합니다.
* `Retry-After` - 비동기 작업의 상태를 확인하기 전에 대기할 시간(초)입니다.

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation 요청 및 응답

`Azure-AsyncOperation` 헤더 값의 URL이 있는 경우 해당 URL에 GET 요청을 보냅니다. `Retry-After`의 값을 사용하여 상태 확인 빈도를 예약합니다. 작업 상태를 나타내는 응답 개체를 가져옵니다. `Location` URL로 작업 상태를 확인할 때 다른 응답이 반환됩니다. 위치 URL의 응답에 대한 자세한 내용은 [스토리지 계정 만들기(위치 및 재시도 후 202)](#create-storage-account-202-with-location-and-retry-after)를 참조하세요.

응답 속성은 다를 수 있지만 항상 비동기 작업의 상태를 포함합니다.

```json
{
    "status": "{status-value}"
}
```

다음 예제에서는 작업에서 반환될 수 있는 다른 값을 보여줍니다.

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}",
    "status" : "Succeeded | Failed | Canceled | {resource provider values}",
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : {
        "code": "{error code}",  
        "message": "{error description}"
    }
}
```

상태가 실패함 또는 취소됨인 경우 오류 개체가 반환됩니다. 다른 모든 값은 선택 사항입니다. 수신하는 응답은 예제와 다를 수 있습니다.

## <a name="provisioningstate-values"></a>provisioningState 값

리소스 만들기, 업데이트 또는 삭제(PUT, PATCH, DELETE)하는 작업은 일반적으로 `provisioningState` 값을 반환합니다. 작업이 완료되면 다음 세 가지 값 중 하나가 반환됩니다.

* 성공
* 실패
* 취소됨

다른 값은 모두 작업이 계속 실행 중임을 나타냅니다. 리소스 공급자는 해당 상태를 표시하는 사용자 지정된 값을 반환할 수 있습니다. 예를 들어 요청을 받고 실행 중인 경우 **수락됨** 을 받을 수 있습니다.

## <a name="example-requests-and-responses"></a>예제 요청 및 응답

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>가상 머신 시작(Azure-AsyncOperation에서 202)

이 예제에서는 [가상 머신의 시작 작업](/rest/api/compute/virtualmachines/start) 상태를 확인하는 방법을 보여줍니다. 초기 요청은 다음 형식으로 되어 있습니다.

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

상태 코드 202를 반환합니다. 헤더 값에서 다음이 표시됩니다.

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

비동기 작업의 상태를 확인하려면 다른 요청을 해당 URL로 전송합니다.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

응답 본문은 작업의 상태를 포함합니다.

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>리소스 배포(Azure-AsyncOperation에서 201)

이 예제에서는 [Azure에 리소스를 배포하는 배포 작업](/rest/api/resources/resources/deployments/createorupdate)의 상태를 확인하는 방법을 보여줍니다. 초기 요청은 다음 형식으로 되어 있습니다.

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

상태 코드 201을 반환합니다. 응답의 본문에는 다음이 포함됩니다.

```json
"provisioningState":"Accepted",
```

헤더 값에서 다음이 표시됩니다.

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

비동기 작업의 상태를 확인하려면 다른 요청을 해당 URL로 전송합니다.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

응답 본문은 작업의 상태를 포함합니다.

```json
{
    "status": "Running"
}
```

배포를 마치면 응답에는 다음이 포함됩니다.

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>스토리지 계정 만들기(위치 및 Retry-After에서 202)

이 예제에서는 [스토리지 계정의 만들기 작업](/rest/api/storagerp/storageaccounts/create) 상태를 확인하는 방법을 보여줍니다. 초기 요청은 다음 형식으로 되어 있습니다.

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

그리고 요청 본문은 스토리지 계정의 속성을 포함합니다.

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

상태 코드 202를 반환합니다. 헤더 값에서 다음 두 개의 값을 확인할 수 있습니다.

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Retry-After에서 지정된 시간(초) 동안 대기한 후에 해당 URL에 다른 요청을 전송하여 비동기 작업의 상태를 확인합니다.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

요청을 계속 실행하는 경우 상태 코드 202를 받게 됩니다. 요청이 완료된 경우 사용자는 상태 코드 200를 수신하고 응답의 본문에는 생성된 스토리지 계정의 속성이 포함됩니다.

## <a name="next-steps"></a>다음 단계

* 각 REST 작업에 대한 설명서는 [REST API 설명서](/rest/api/azure/)를 참조하세요.
* Resource Manager REST API를 통한 템플릿 배포 방법에 대한 정보는 [Resource Manager 템플릿과 Resource Manager REST API로 리소스 배포](../templates/deploy-rest.md)를 참조하세요.