---
title: Azure Monitor API 사용 중지
description: 이전 버전의 OperationalInsights 리소스 공급자 API를 사용 중지 하는 방법을 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 708901dbc37daa075cf38d3f9ef046ae658ea979
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744891"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API 버전 사용 중지
Microsoft는 최신/지원 되는 버전으로 원활 하 게 전환할 수 있도록 API를 사용 중지 하기 전에 최소 12 개월 전에 알림을 제공 합니다. **OperationalInsights** 리소스 공급자 api에 대 한 새로운 버전 (2020-08-01)을 출시 했으며 2023 년 10 월 31 일에 이전 API 버전을 사용 중지 합니다. 새 기능 및 최적화는 현재 API에만 추가 되므로 최대한 일찍 최신 API 버전으로 업그레이드 해야 합니다.

2023 년 10 월 31 일 이후에는 Azure Monitor 이전 Api 버전 2020-08-01을 더 이상 지원 하지 않습니다. 업그레이드 하지 않으려는 경우 이전 버전에서 보낸 요청은 2023 년 10 월 31 일까 지 Azure Monitor 서비스에서 계속 제공 됩니다.

사용 하는 구성 방법에 따라 다음 예제에 따라 REST 요청 및 리소스 관리자 템플릿에서 새 버전을 업데이트 해야 합니다.


## <a name="rest"></a>REST
REST API 요청은 요청 URL의 API 버전을 사용 합니다. 다음 예제와 같이 해당 버전을 최신 버전으로 바꿉니다.

```rest
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
```

## <a name="azure-resource-manager"></a>Azure 리소스 관리자
리소스 관리자 템플릿에서는 리소스의 **apiVersion** 속성에 있는 API 버전을 사용 합니다. 다음 예제와 같이 해당 버전을 최신 버전으로 바꿉니다.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-08-01",
            "location": "westus",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "retentionInDays": 30,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
  }
}
```


## <a name="next-steps"></a>다음 단계

- [OPERATIONALINSIGHTS API에 대 한 참조](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions)를 참조 하세요.
