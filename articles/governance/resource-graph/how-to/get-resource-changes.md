---
title: 리소스 변경 내용 가져오기
description: 리소스가 변경된 시기를 찾고 변경된 속성의 목록을 가져오고 차이를 평가하는 방법을 이해합니다.
ms.date: 05/20/2020
ms.topic: how-to
ms.openlocfilehash: 9e233ebbdd1d70d483af44eacf12cc924deaafac
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167165"
---
# <a name="get-resource-changes"></a>리소스 변경 내용 가져오기

리소스는 매일 사용, 재구성 및 심지어 재배포하는 과정을 통해 변경됩니다.
변경 내용은 개별 또는 자동화된 프로세스에서 가져올 수 있습니다. 대부분의 변경은 의도적이지만 때로는 그렇지 않습니다. 최근 14일 간의 변경 기록으로 Azure Resource Graph를 통해 다음 작업을 수행할 수 있습니다.

- Azure Resource Manager 속성에서 변경 내용이 검색된 경우 찾기
- 각 리소스 변경에 대해 속성 변경 세부 정보 확인
- 검색된 변경 전후에 리소스의 전체 비교 확인

변경 검색 및 세부 정보는 다음 예제 시나리오에서 유용합니다.

- 인시던트를 관리하는 동안 _잠재적으로_ 관련 변경 사항 이해 특정 시간 창에서 변경 이벤트를 쿼리하고 변경 세부 정보 평가
- CMDB라고 하는 구성 관리 데이터베이스를 최신 상태로 유지 예약된 빈도로 모든 리소스와 전체 속성 세트를 새로 고치는 대신 변경된 내용만 가져옵니다.
- 리소스가 준수 상태를 변경했을 때 변경되었을 수 있는 다른 속성을 이해합니다. 이러한 추가 속성을 평가하면 Azure Policy 정의를 통해 관리해야 할 수 있는 다른 속성에 대한 정보를 제공할 수 있습니다.

이 문서에서는 Resource Graph의 SDK를 통해 이 정보를 수집하는 방법을 보여 줍니다. Azure Portal에서 이 정보를 보려면 Azure Policy의 [변경 기록](../../policy/how-to/determine-non-compliance.md#change-history) 또는 Azure 활동 로그 [변경 기록](../../../azure-monitor/platform/activity-log-view.md#azure-portal)을 참조하세요. 인프라 계층에서 애플리케이션 배포로 애플리케이션 변경에 대한 자세한 내용은 Azure Monitor에서 [애플리케이션 변경 분석(미리 보기) 사용](../../../azure-monitor/app/change-analysis.md)을 참조하세요.

> [!NOTE]
> Resource Graph의 변경 정보는 Resource Manager 속성에 대한 것입니다. 가상 머신 내의 변경 내용 추적은 Azure Automation의 [변경 내용 추적](../../../automation/automation-change-tracking.md) 또는 Azure Policy의 [VM에 대한 게스트 구성](../../policy/concepts/guest-configuration.md)을 참조하세요.

> [!IMPORTANT]
> Azure Resource Graph의 변경 기록은 공개 미리 보기로 제공됩니다.

## <a name="find-detected-change-events-and-view-change-details"></a>검색된 변경 이벤트를 찾고 변경 세부 정보 보기

리소스에서 변경된 내용을 확인하는 첫 번째 단계는 시간 창 내에서 해당 리소스와 관련된 변경 이벤트를 찾는 것입니다. 각 변경 이벤트에는 리소스에서 변경된 내용에 대한 세부 정보도 포함됩니다. 이 단계는 **resourceChanges** REST 엔드포인트를 통해 수행됩니다.

**resourceChanges** 엔드포인트는 요청 본문에서 다음 매개 변수를 허용합니다.

- **resourceId** \[필수\]: 변경 내용을 찾을 Azure 리소스입니다.
- **interval** \[필수\]: **Zulu Time Zone(Z)** 을 사용하여 변경 이벤트를 확인하는 시기에 대한 _시작_ 및 _종료_ 날짜가 포함된 속성입니다.
- **fetchPropertyChanges**(선택 사항): 응답 개체에 속성 변경 내용이 포함되어 있는지를 설정하는 부울 속성입니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

위의 요청 본문을 사용하여 **resourceChanges**에 대한 REST API URI는 다음과 같습니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

응답은 다음 예제와 유사합니다.

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

**resourceId**에 대해 검색된 각 변경 이벤트에는 다음 속성이 있습니다.

- **changeId** - 이 값은 해당 리소스에 대해 고유합니다. **changeId** 문자열에는 다른 속성이 포함될 수 있지만 고유하게 보장됩니다.
- **beforeSnapshot** - 변경 내용이 검색되기 전에 수행된 리소스 스냅샷의 **snapshotId** 및 **타임스탬프**를 포함합니다.
- **afterSnapshot** - 변경 내용이 검색된 후에 수행된 리소스 스냅샷의 **snapshotId** 및 **타임스탬프**를 포함합니다.
- **changeType** - **beforeSnapshot**과 **afterSnapshot** 간에 전체 변경 레코드에 대해 검색된 변경 형식을 설명합니다. 값은 다음과 같습니다. _만들기_, _업데이트_ 및 _삭제_ **propertyChanges** 속성 배열은 **changeType**이 _업데이트_인 경우에만 포함됩니다.
- **propertyChanges** - 이 속성 배열은 **beforeSnapshot**과 **afterSnapshot** 간에 업데이트된 모든 리소스 속성에 대해 자세히 설명합니다.
  - **propertyName** - 변경된 리소스 속성의 이름입니다.
  - **changeCategory** - 변경을 수행한 내용에 대해 설명합니다. 값은 다음과 같습니다. _시스템_ 및 _사용자_
  - **changeType** - 개별 리소스 속성에 대해 검색된 변경 형식을 설명합니다.
    값은 다음과 같습니다. _삽입_, _업데이트_, _제거_
  - **beforeValue** - **beforeSnapshot**에서 리소스 속성의 값입니다. **changeType**이 _삽입_인 경우에는 표시되지 않습니다.
  - **afterValue** - **afterSnapshot**에서 리소스 속성의 값입니다. **changeType**이 _제거_인 경우에는 표시되지 않습니다.

## <a name="compare-resource-changes"></a>리소스 변경 내용 비교

**resourceChanges** 엔드포인트에서 **changeId**와 함께 **resourceChangeDetails** REST 엔드포인트를 사용하여 변경된 리소스의 이전 및 이후 스냅샷을 가져옵니다.

**resourceChangeDetails** 엔드포인트에는 요청 본문에 두 개의 매개 변수가 필요합니다.

- **resourceId**: 변경 내용을 비교할 Azure 리소스입니다.
- **changeId**: **resourceChanges**에서 수집된 **resourceId**에 대한 고유 변경 이벤트입니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

위의 요청 본문을 사용하여 **resourceChangeDetails**에 대한 REST API URI는 다음과 같습니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

응답은 다음 예제와 유사합니다.

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** 및 **afterSnapshot**은 각각 스냅샷을 만든 시간 및 해당 시점의 속성을 제공합니다. 이러한 스냅샷 간의 특정 지점에서 변경이 발생했습니다. 위의 예제를 살펴보면 변경된 속성이 **supportsHttpsTrafficOnly**인 것을 볼 수 있습니다.

결과를 비교하려면 **resourceChanges**에서 **changes** 속성을 사용하거나 **resourceChangeDetails**에서 각 스냅샷의 **콘텐츠** 부분을 평가하여 차이점을 확인합니다. 스냅샷을 비교하는 경우 **타임스탬프**는 항상 예상됨에도 불구하고 차이점으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어를 확인합니다.
- [고급 쿼리](../samples/advanced.md)의 고급 사용법을 확인합니다.
- [리소스 검색](../concepts/explore-resources.md) 방법에 대해 자세히 알아보기
- 높은 빈도로 쿼리를 사용하는 방법에 대한 지침은 [제한된 요청에 대한 지침](../concepts/guidance-for-throttled-requests.md)을 참조하세요.
