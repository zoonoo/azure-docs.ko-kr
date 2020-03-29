---
title: 리소스 변경 내용 가져오기
description: 리소스가 변경된 시기를 찾고 변경한 속성 목록을 얻고 diffs를 평가하는 방법을 이해합니다.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873032"
---
# <a name="get-resource-changes"></a>리소스 변경 내용 가져오기

리소스는 일상적인 사용, 재구성 및 재배포 과정을 통해 변경됩니다.
변화는 개인또는 자동화된 프로세스에 의해 이루어질 수 있습니다. 대부분의 변화는 의도적으로 설계되었지만 때로는 그렇지 않은 경우도 있습니다. 지난 14일간의 변경 기록을 통해 Azure 리소스 그래프를 사용하면 다음을 수행할 수 있습니다.

- Azure 리소스 관리자 속성에서 변경 내용이 검색된 시기 찾기
- 각 리소스 변경에 대해 속성 변경 세부 정보 참조
- 감지된 변경 전후의 리소스 전체 비교 보기

변경 검색 및 세부 정보는 다음 예제 시나리오에서 유용합니다.

- 인시던트 관리 중에 _잠재적으로_ 관련된 변경 사항을 파악할 수 있습니다. 특정 기간 동안 변경 이벤트를 쿼리하고 변경 세부 정보를 평가합니다.
- CMDB로 알려진 구성 관리 데이터베이스를 최신 상태로 유지합니다. 예약된 빈도로 모든 리소스와 전체 속성 집합을 새로 고치는 대신 변경된 내용만 가져옵니다.
- 리소스가 규정 준수 상태를 변경할 때 변경되었을 수 있는 다른 속성을 이해합니다. 이러한 추가 속성의 평가는 Azure Policy 정의를 통해 관리해야 할 수 있는 다른 속성에 대한 통찰력을 제공할 수 있습니다.

이 문서에서는 리소스 그래프의 SDK를 통해 이 정보를 수집하는 방법을 보여 주며 이 정보를 수집합니다. Azure 포털에서 이 정보를 보려면 Azure 정책의 [변경 기록](../../policy/how-to/determine-non-compliance.md#change-history-preview) 또는 Azure 활동 로그 변경 [기록을](../../../azure-monitor/platform/activity-log-view.md#azure-portal)참조하세요.
인프라 계층에서 응용 프로그램 배포에 이르는 응용 프로그램 변경에 대한 자세한 내용은 Azure Monitor의 [응용 프로그램 변경 분석 사용(미리 보기)을](../../../azure-monitor/app/change-analysis.md) 참조하십시오.

> [!NOTE]
> 리소스 그래프의 변경 세부 정보는 리소스 관리자 속성에 대한 것입니다. 가상 시스템 내에서 변경 내용을 추적하려면 Azure Automation의 [변경 내용 추적](../../../automation/automation-change-tracking.md) 또는 [VM에 대한](../../policy/concepts/guest-configuration.md)Azure 정책의 게스트 구성을 참조하십시오.

> [!IMPORTANT]
> Azure 리소스 그래프의 변경 내역은 공개 미리 보기입니다.

## <a name="find-detected-change-events-and-view-change-details"></a>검색된 변경 이벤트 찾기 및 변경 세부 정보 보기

리소스에서 변경된 내용을 확인하는 첫 번째 단계는 시간 내에 해당 리소스와 관련된 변경 이벤트를 찾는 것입니다. 각 변경 이벤트에는 리소스에서 변경된 내용에 대한 세부 정보도 포함됩니다. 이 단계는 리소스를 통해 **수행됩니다REST** 끝점을 변경합니다.

**resourceChanges** 끝점은 요청 본문에서 다음 매개 변수를 허용합니다.

- **resourceId** \[\]필수 : 변경 내용을 찾을 Azure 리소스입니다.
- **interval** \[필요한\]간격 : **Zulu 표준 시간대 (Z)를**사용하여 변경 이벤트를 확인하는 시기에 대한 _시작_ 및 _종료_ 날짜가있는 속성입니다.
- **fetchPropertyChanges(선택** 사항): 응답 개체에 속성 변경 내용이 포함되는 경우 설정하는 부울 속성입니다.

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

위의 요청 본문을 사용하면 **리소스변경에** 대한 REST API URI는 다음과 같이 됩니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

응답은 이 예제와 유사합니다.

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

**resourceId에** 대해 검색된 각 변경 이벤트에는 다음 속성이 있습니다.

- **changeId** - 이 값은 해당 리소스에 고유합니다. **changeId** 문자열에 다른 속성이 포함될 수도 있지만 고유만 보장됩니다.
- **이전스냅샷** - 변경이 감지되기 전에 수행된 리소스 스냅숏의 **스냅샷Id** 및 **타임스탬프를** 포함합니다.
- **스냅샷 -** 변경이 검색된 후 수행된 리소스 스냅숏의 **스냅샷Id** 및 **타임스탬프를** 포함합니다.
- **changeType** - **이전 스냅숏과** **afterSnapshot**사이의 전체 변경 레코드에 대해 검색된 변경 유형을 설명합니다. 값은 다음과 같습니다: _만들기,_ _업데이트_및 _삭제_. **propertyChanges** 속성 배열은 **changeType이** _업데이트인_경우에만 포함됩니다.
- **propertyChanges** - 이 속성 배열은 **이전 스냅샷과** **afterSnapshot**사이에 업데이트된 모든 리소스 속성을 자세히 설명합니다.
  - **propertyName** - 변경된 리소스 속성의 이름입니다.
  - **변경 범주** - 변경한 내용을 설명합니다. 값은 _System_ 다음과 _User_같습니다.
  - **changeType** - 개별 리소스 속성에 대해 검색된 변경 유형을 설명합니다.
    값은 다음과 같습니다: _삽입_, _업데이트_, _제거_.
  - **이전값** - 이전의 리소스 **속성값스냅숏**. **changeType이** _삽입될_때 표시되지 않습니다.
  - **afterValue** - **afterSnapshot**에서 리소스 속성의 값입니다. **changeType이** _제거될_때 표시되지 않습니다.

## <a name="compare-resource-changes"></a>리소스 변경 사항 비교

**리소스에서** 변경 **Id를** **사용하면변경된 리소스의** 이전 및 이후 스냅숏을 가져오기 위해 리소스변경 세부 정보 REST 끝점이 사용됩니다.

**리소스변경세부사항** 끝점에는 요청 본문에 두 개의 매개 변수가 필요합니다.

- **resourceId**: 변경 내용을 비교할 수 있는 Azure 리소스입니다.
- **changeId**: **resourceId에서** 수집된 고유 변경 **이벤트입니다.**

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

위의 요청 본문을 사용하면 리소스에 대한 REST API **URI입니다.자세히 보기 세부 정보:**

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

응답은 이 예제와 유사합니다.

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

**전에스냅샷과** **afterSnapshot** 각각 스냅숏이 생성된 시간과 해당 시점의 속성을 제공합니다. 이러한 스냅숏 간에 변경이 발생했습니다. 위의 예제를 보면 변경된 속성이 **supportsHttpsTrafficOnly**.

결과를 비교하려면 **리소스에서** **변경 속성(변경** 내용)을 사용하거나 **resourceChangeDetails에서** 각 스냅숏의 **콘텐츠** 부분을 평가하여 차이를 확인합니다. 스냅샷을 비교하면 **타임스탬프는** 항상 예상에도 불구하고 차이로 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 되는 언어를 참조 하십시오.
- [고급 쿼리에서](../samples/advanced.md)고급 용도를 참조하십시오.
- [리소스 검색](../concepts/explore-resources.md) 방법에 대해 자세히 알아보기