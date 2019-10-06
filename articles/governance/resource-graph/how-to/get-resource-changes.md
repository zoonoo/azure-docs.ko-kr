---
title: 리소스 변경 내용 가져오기
description: 리소스가 변경 된 시기를 찾는 방법을 이해 하 고 변경 된 속성의 목록을 가져옵니다.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980315"
---
# <a name="get-resource-changes"></a>리소스 변경 내용 가져오기

리소스는 매일 사용, 재구성 및 심지어 재배포 하는 과정을 통해 변경 됩니다.
변경 내용은 개별 또는 자동화 된 프로세스에서 가져올 수 있습니다. 대부분의 변경은 의도적 이지만 때로는 그렇지 않습니다. 최근 14 일간의 변경 기록으로 Azure 리소스 그래프를 통해 다음 작업을 수행할 수 있습니다.

- Azure Resource Manager 속성에서 변경이 탐지된 시기를 확인합니다.
- 해당 변경 이벤트의 일부로 변경된 속성을 확인합니다.

변경 검색 및 세부 정보는 다음 예제 시나리오에서 유용 합니다.

- 인시던트 관리를 통해 _잠재적_ 으로 관련 된 변경 사항을 파악 합니다. 특정 시간 창에서 변경 이벤트를 쿼리하고 변경 정보를 평가 합니다.
- CMDB 라고 하는 구성 관리 데이터베이스를 최신 상태로 유지 예약 된 빈도로 모든 리소스와 전체 속성 집합을 새로 고치는 대신 변경 된 내용만 가져옵니다.
- 리소스가 준수 상태를 변경 했을 때 변경 되었을 수 있는 다른 속성을 이해 합니다. 이러한 추가 속성을 평가 하면 Azure Policy 정의를 통해 관리 해야 할 수 있는 다른 속성에 대 한 정보를 제공할 수 있습니다.

이 문서에서는 리소스 그래프의 SDK를 통해이 정보를 수집 하는 방법을 보여 줍니다. Azure Portal에서이 정보를 보려면 Azure Policy의 [변경 기록](../../policy/how-to/determine-non-compliance.md#change-history-preview) 또는 Azure 활동 로그 [변경 기록](../../../azure-monitor/platform/activity-log-view.md#azure-portal)을 참조 하세요.

> [!NOTE]
> 리소스 그래프의 변경 정보는 리소스 관리자 속성에 대 한 것입니다. 가상 컴퓨터 내의 변경 내용 추적에 대 한 자세한 내용은 Azure Automation의 [변경 내용 추적](../../../automation/automation-change-tracking.md) 또는 [vm에 대 한 Azure Policy 게스트 구성](../../policy/concepts/guest-configuration.md)을 참조 하세요.

> [!IMPORTANT]
> Azure 리소스 그래프의 변경 기록은 공개 미리 보기로 제공 됩니다.

## <a name="find-when-changes-were-detected"></a>변경 내용이 검색 된 시간 찾기

리소스에서 변경 된 내용을 확인 하는 첫 번째 단계는 시간 창 내에서 해당 리소스와 관련 된 변경 이벤트를 찾는 것입니다. 이 단계는 **resourceChanges** REST 끝점을 통해 수행 됩니다.

**ResourceChanges** 끝점에는 요청 본문에 두 개의 매개 변수가 필요 합니다.

- **resourceId**: 변경 내용을 찾을 Azure 리소스입니다.
- **interval**: **줄루어 표준 시간대 (Z)** 를 사용 하 여 변경 이벤트를 확인 하는 경우 _시작_ 및 _종료_ 날짜가 포함 된 속성입니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

위의 요청 본문을 사용 하 여 **resourceChanges** 에 대 한 REST API URI는 다음과 같습니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

응답은 다음 예제와 유사 합니다.

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

**ResourceId** 에 대해 검색 된 각 변경 이벤트에는 해당 리소스에 고유한 **changeid** 가 있습니다. **Changeid** 문자열은 경우에 따라 다른 속성을 포함할 수 있지만 고유 하 게 보장 됩니다. 변경 레코드에는 전후 스냅숏이 만들어진 시간이 포함 됩니다.
변경 이벤트는이 기간 동안 특정 시점에 발생 합니다.

## <a name="see-what-properties-changed"></a>변경 된 속성 확인

**ResourceChanges** 끝점에서 **changeid** 를 사용 하 여 **resourceChangeDetails** REST 끝점을 사용 하 여 변경 이벤트의 세부 정보를 가져옵니다.

**ResourceChangeDetails** 끝점에는 요청 본문에 두 개의 매개 변수가 필요 합니다.

- **resourceId**: 변경 내용을 찾을 Azure 리소스입니다.
- **changeId**: **ResourceChanges**에서 수집 된 **resourceId** 에 대 한 고유 변경 이벤트입니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

위의 요청 본문을 사용 하 여 **resourceChangeDetails** 에 대 한 REST API URI는 다음과 같습니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

응답은 다음 예제와 유사 합니다.

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

**beforeSnapshot** 및 **aftersnapshot** 은 각각 스냅숏을 만든 시간 및 속성을 제공 합니다. 이러한 스냅숏 간의 특정 지점에서 변경이 발생 했습니다. 위의 예제를 살펴보면 변경 된 속성이 **supportsHttpsTrafficOnly**된 것을 볼 수 있습니다.

결과를 프로그래밍 방식으로 비교 하려면 각 스냅숏의 **콘텐츠** 부분을 비교 하 여 차이를 확인 합니다. 전체 스냅숏을 비교 하는 경우 **타임 스탬프** 는 항상 예상 대로 차이를 표시 합니다.

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 중인 언어를 참조 하세요.
- [고급 쿼리에서](../samples/advanced.md)고급 사용을 참조 하세요.
- 리소스를 [탐색](../concepts/explore-resources.md)하는 방법을 알아봅니다.