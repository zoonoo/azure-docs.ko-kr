---
title: Azure API Management 일시 삭제 (미리 보기) | Microsoft Docs
description: 일시 삭제를 사용 하면 삭제 된 API Management 인스턴스를 복구할 수 있습니다.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: fca98414a87f3b8a4f3c0969a28ee95c7ed47dc3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501575"
---
# <a name="api-management-soft-delete-preview"></a>API Management 일시 삭제 (미리 보기)

API Management 일시 삭제 (미리 보기)를 사용 하 여 최근에 삭제 된 API Management (APIM) 인스턴스를 복구 및 복원할 수 있습니다.

> [!IMPORTANT]
> 이상 API 버전을 사용 하 여 삭제 된 API Management 인스턴스만 `2020-01-01-preview` 일시 삭제 되 고이 문서에 설명 된 단계를 사용 하 여 복구할 수 있습니다. 이전 API 버전을 사용 하 여 삭제 된 APIM 인스턴스는 계속 하드 삭제 됩니다. 현재 Azure PowerShell 및 Azure CLI는 버전을 사용 하지 않으며 `2020-06-01-preview` 하드 삭제 동작도 발생 합니다.

## <a name="supporting-interfaces"></a>인터페이스 지원

일시 삭제 기능은 [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore)를 통해 사용할 수 있습니다.

> [!TIP]
> Azure REST Api를 호출 하기 위한 팁과 도구는 [azure REST API 참조](/rest/api/azure/) 를 참조 하세요.

| 작업(Operation) | Description | API Management 네임 스페이스 | 최소 API 버전 |
|--|--|--|--|
| [만들기 또는 업데이트](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | API Management 서비스를 만들거나 업데이트 합니다.  | API Management 서비스 | 모두 |
| 속성을 true로 설정 하 여 [만들기 또는 업데이트](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) `restore` **true** | Undeletes 서비스가 이전에 일시 삭제 된 경우에는 API Management 합니다. 을 `restore` 지정 하 고로 설정 하면 `true` 다른 모든 속성은 무시 됩니다.  | API Management 서비스 |  2020-06-01-미리 보기 |
| [삭제](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | 기존 API Management 서비스를 삭제 합니다. | API Management 서비스 | 2020-01-01-미리 보기|
| [이름으로 가져오기](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | 이름으로 일시 삭제 된 Api Management 서비스를 가져옵니다. | 삭제 된 서비스 | 2020-06-01-미리 보기 |
| [구독 별로 나열](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | 지정 된 구독에 대 한 삭제 취소에 사용할 수 있는 모든 일시 삭제 된 서비스를 나열 합니다. | 삭제 된 서비스 | 2020-06-01-미리 보기
| [제거](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | API Management 서비스를 제거 합니다. 삭제를 취소 하는 옵션을 사용 하지 않고 삭제 합니다. | 삭제 된 서비스 | 2020-06-01-미리 보기

## <a name="soft-delete-behavior"></a>일시 삭제 동작

모든 API 버전을 사용 하 여 API Management 인스턴스를 만들 수 있지만 이상 버전을 사용 하 여 `2020-01-01-preview` APIM 인스턴스를 일시 삭제 하 고 복구 하는 옵션을 제공 해야 합니다.

API Management 인스턴스를 삭제할 때 서비스는 삭제 된 상태에 존재 하 여 APIM 작업에 액세스할 수 없게 됩니다. 이 상태에서 APIM 인스턴스는 나열, 복구 또는 제거 (영구적으로 삭제) 할 수 있습니다.

동시에 Azure는 미리 결정 된 (48 시간) 보존 간격 후에 실행 하기 위해 APIM 인스턴스에 해당 하는 기본 데이터의 삭제를 예약 합니다. 인스턴스에 해당 하는 DNS 레코드는 보존 간격이 지속 되는 동안에도 유지 됩니다. 보존 기간이 경과 될 때까지 일시 삭제 된 API Management 인스턴스의 이름을 다시 사용할 수 없습니다.

APIM 인스턴스가 48 시간 이내에 복구 되지 않는 경우 하드 삭제 됩니다 (복구할 수 없음). APIM 인스턴스를 [제거](#purge-a-soft-deleted-apim-instance) (영구적으로 삭제) 하도록 선택할 수도 있습니다. forgoing은 일시 삭제 보존 기간입니다.

## <a name="list-deleted-apim-instances"></a>삭제 된 APIM 인스턴스 나열

삭제 된 서비스 [이름으로 가져오기](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) 또는 [구독 별로 나열](/deletedservices/listbysubscription) 작업을 사용 하 여 일시 삭제 된 apim 인스턴스를 복원 (삭제 취소) 할 수 있는지 확인할 수 있습니다.

### <a name="get-a-soft-deleted-instance-by-name"></a>이름으로 일시 삭제 된 인스턴스 가져오기

[이름으로 가져오기](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) 작업을 사용 하 여 `{subscriptionId}` , `{location}` 및를 `{serviceName}` Azure 구독, 리소스 위치 및 API Management 인스턴스 이름으로 대체 API Management 합니다.

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

삭제 취소에 사용할 수 있는 경우 Azure는 및를 표시 하는 APIM 인스턴스의 레코드를 반환 합니다 `deletionDate` `scheduledPurgeDate` . 예를 들면 다음과 같습니다.

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>지정 된 구독에 대해 일시 삭제 된 모든 인스턴스를 나열 합니다.

[구독으로 API Management 목록](/deletedservices/listbysubscription) 사용 작업을 사용 하 여 `{subscriptionId}` 구독 ID로 대체 합니다.

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

그러면 지정 된 구독에서 삭제 취소에 사용할 수 있는 모든 일시 삭제 된 서비스가 나열 `deletionDate` 되며 `scheduledPurgeDate` 각각에 대해 및를 표시 합니다.

## <a name="recover-a-deleted-apim-instance"></a>삭제 된 APIM 인스턴스 복구

API Management [만들기 또는 업데이트](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) 작업을 사용 하 여 `{subscriptionId}` `{resourceGroup}` `{apimServiceName}` Azure 구독, 리소스 그룹 이름 및 API Management 이름으로, 및를 대체 합니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . `restore`요청 본문에서 속성을로 설정 합니다 `true` . 이 플래그를 지정 하 고를 *true* 로 설정 하면 다른 모든 속성이 무시 됩니다. 예를 들어:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>일시 삭제 된 APIM 인스턴스 제거

API Management [제거](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) 작업을 사용 하 여 `{subscriptionId}` , `{location}` 및를 `{serviceName}` Azure 구독, 리소스 위치 및 API Management 이름으로 대체 합니다.

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

그러면 Azure에서 API Management 인스턴스가 영구적으로 삭제 됩니다.

## <a name="next-steps"></a>다음 단계

장기 API Management 백업 및 복구 옵션에 대해 알아봅니다.

- [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](api-management-howto-disaster-recovery-backup-restore.md)