---
title: Azure Monitor API 사용 중지
description: 이전 버전의 OperationalInsights 리소스 공급자 API를 사용 중지 하는 방법을 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 03e534564b4a32f112c2aaff44a187b10eb2a537
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723115"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API 버전 사용 중지
Microsoft는 최신/지원 되는 버전으로 원활 하 게 전환할 수 있도록 API를 사용 중지 하기 전에 최소 12 개월 전에 알림을 제공 합니다. **OperationalInsights** 리소스 공급자 api에 대 한 새로운 버전 (2020-08-01)을 출시 했으며 2024 년 2 월 29 일에 이전 API 버전을 사용 중지 합니다.

이제 버전 2020-08-01을 사용 하 여 [전용 클러스터](./logs-dedicated-clusters.md), [고객 관리 키](../logs/customer-managed-keys.md), [개인 링크](./private-link-security.md) 및 [데이터 내보내기와](./logs-data-export.md)같은 새로운 기능의 이점을 얻을 수 있습니다. 또한 새로운 기능 및 최적화는 현재 API에만 추가 됩니다.

2 월 29 일 이후 2024 Azure Monitor는 더 이상 2020-08-01 보다 이전 Api 버전을 지원 하지 않습니다. 업그레이드 하지 않으려는 경우 이전 버전에서 보낸 요청은 2024 년 2 월 29 일까 지 Azure Monitor 서비스에서 계속 제공 됩니다.

## <a name="migration-steps"></a>마이그레이션 단계
사용 하는 구성 방법에 따라 **REST** 요청 및 **템플릿 리소스 관리자** 새 버전을 업데이트 해야 합니다. API 버전을 업데이트 하려면 아래 예제를 따르세요.

1. REST API 요청은 요청 URL의 API 버전을 사용 합니다. 다음 예제와 같이 해당 버전을 최신 버전 (2020-08-01)으로 바꿉니다.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager 템플릿에서는 리소스의 **apiVersion** 속성에 있는 API 버전을 사용 합니다. 다음 예제와 같이 해당 버전을 최신 버전 (2020-08-01)으로 바꿉니다.


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


### <a name="more-information"></a>자세한 정보
질문이 있는 경우 [기술 커뮤니티 전문가]( https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)로부터 답변을 받으세요. 지원 계획이 있고 기술 도움말이 필요한 경우 [지원 요청]( https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 만듭니다. 
1.  *문제 유형* 에서 **기술** 을 선택 합니다. 
2.  *구독* 아래에서 구독을 선택합니다. 
3.  *서비스* 에서 **내 서비스** 를 선택 하 고 **Log Analytics** 를 선택 합니다. 
4.  *요약* 아래에서 문제에 대 한 설명을 입력 합니다. 
5.  *문제 유형* 에서 **Log Analytics 작업 영역 관리** 를 선택 합니다.  
6.  *문제 하위 형식* 에서 **ARM 템플릿, PowerShell 및 CLI** 를 선택 합니다. 

## <a name="next-steps"></a>다음 단계

- [OperationalInsights 작업 영역 API에 대 한 참조](/rest/api/loganalytics/workspaces)를 참조 하세요.