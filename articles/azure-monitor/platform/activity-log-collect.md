---
title: Azure Monitor에서 Azure 활동 로그 수집 및 분석
description: Azure Monitor 로그에서 Azure 활동 로그를 수집 하 고 모니터링 솔루션을 사용 하 여 모든 Azure 구독에서 Azure 활동 로그를 분석 하 고 검색 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382865"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Azure Monitor에서 Azure 활동 로그 수집 및 분석
[Azure 활동 로그](platform-logs-overview.md) 는 azure에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 하는 [플랫폼 로그](platform-logs-overview.md) 입니다. Azure Portal에서 활동 로그를 볼 수 있지만, Azure Monitor의 추가 기능을 사용할 수 있도록 Log Analytics 작업 영역에 보내도록 구성 해야 합니다. 이 문서에서는이 구성을 수행 하는 방법과 활동 로그를 Azure storage 및 event hubs에 보내는 방법에 대해 설명 합니다.

Log Analytics 작업 영역에서 활동 로그를 수집 하면 다음과 같은 이점이 있습니다.

- Log Analytics 작업 영역에 저장 된 활동 로그 데이터에 대 한 데이터 수집 또는 데이터 보존 요금이 없습니다.
- 활동 로그 데이터를 Azure Monitor에 의해 수집 된 다른 모니터링 데이터와 상호 연결 합니다.
- 로그 쿼리를 사용 하 여 복잡 한 분석을 수행 하 고 활동 로그 항목에 대 한 심층 통찰력을 얻습니다.
- 활동 항목이 있는 로그 경고를 사용 하 여 보다 복잡 한 경고 논리를 허용 합니다.
- 활동 로그 항목을 90 일 보다 오래 저장 합니다.
- 여러 Azure 구독 및 테 넌 트의 로그 항목을 분석을 위해 하나의 위치로 통합 합니다.

> [!IMPORTANT]
> 테 넌 트 간에 로그를 수집 하려면 [Azure Lighthouse](/azure/lighthouse)가 필요 합니다.

## <a name="collecting-activity-log"></a>활동 로그 수집
활동 로그는 [Azure Portal에서 볼](activity-log-view.md)수 있도록 자동으로 수집 됩니다. Log Analytics 작업 영역에서 수집 하거나 Azure storage 또는 event hubs를 전송 하려면 [진단 설정](diagnostic-settings.md)을 만듭니다. 이는 리소스 로그에 사용 되는 것과 동일한 방법으로 모든 [플랫폼 로그](platform-logs-overview.md)를 일관 되 게 유지 합니다.  

활동 로그에 대 한 진단 설정을 만들려면 Azure Monitor의 **활동 로그** 메뉴에서 **진단 설정** 을 선택 합니다. 설정을 만드는 방법에 대 한 자세한 내용은 [진단 설정 만들기를 참조 하 여 Azure에서 플랫폼 로그 및 메트릭을 수집](diagnostic-settings.md) 합니다. 필터링 할 수 있는 범주에 대 한 설명은 [활동 로그의 범주](activity-log-view.md#categories-in-the-activity-log) 를 참조 하세요. 레거시 설정이 있는 경우 진단 설정을 만들기 전에 사용 하지 않도록 설정 해야 합니다. 둘 다 사용 하도록 설정 하면 중복 된 데이터가 생성 될 수 있습니다.

![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> 현재 Azure Portal 및 리소스 관리자 템플릿을 사용 하 여 구독 수준 진단 설정만 만들 수 있습니다. 


## <a name="legacy-settings"></a>레거시 설정 
진단 설정은 활동 로그를 다른 대상으로 전송 하는 기본 방법 이지만 진단 설정으로 대체 하도록 선택 하지 않은 경우에는 레거시 방법이 계속 작동 합니다. 진단 설정은 레거시 방법에 비해 다음과 같은 장점이 있으며 구성을 업데이트 하는 것이 좋습니다.

- 모든 플랫폼 로그를 수집 하기 위한 일관 된 방법입니다.
- 여러 구독 및 테 넌 트에 걸쳐 활동 로그를 수집 합니다.
- 컬렉션을 필터링 하 여 특정 범주에 대 한 로그만 수집 합니다.
- 모든 활동 로그 범주를 수집 합니다. 일부 범주는 레거시 메서드를 사용 하 여 수집 되지 않습니다.
- 로그 수집에 대 한 대기 시간을 단축 합니다. 진단 설정에 약 1 분만 추가 되는 동안 이전 메서드에는 약 15 분의 대기 시간이 있습니다.



### <a name="log-profiles"></a>로그 프로필
로그 프로필은 활동 로그를 Azure storage 또는 event hubs로 전송 하는 레거시 방법입니다. 다음 절차를 사용 하 여 로그 프로필 작업을 계속 하거나 진단 설정으로 마이그레이션하기 위한 준비에서 로그 프로필을 사용 하지 않도록 설정할 수 있습니다.

1. Azure Portal **Azure Monitor** 메뉴에서 **활동 로그**를 선택 합니다.
3. **진단 설정**을 클릭 합니다.

   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 레거시 환경에 대해 자주색 배너를 클릭 합니다.

    ![레거시 환경](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
활동 로그를 Log Analytics 작업 영역에 수집 하는 레거시 방법은 작업 영역 구성의 로그에 연결 하는 것입니다. 

1. Azure Portal **Log Analytics 작업 영역** 메뉴에서 작업 영역을 선택 하 여 활동 로그를 수집 합니다.
1. 작업 영역 메뉴의 **작업 영역 데이터 원본** 섹션에서 **Azure 활동 로그**를 선택 합니다.
1. 연결 하려는 구독을 클릭 합니다.

    ![작업 영역](media/activity-log-collect/workspaces.png)

1. **연결** 을 클릭 하 여 구독의 활동 로그를 선택한 작업 영역에 연결 합니다. 구독이 다른 작업 영역에 이미 연결 되어 있는 경우 먼저 **연결 끊기** 를 클릭 하 여 연결을 끊습니다.

    ![작업 영역 연결](media/activity-log-collect/connect-workspace.png)


설정을 사용 하지 않도록 설정 하려면 동일한 절차를 수행 하 고 **연결 끊기** 를 클릭 하 여 작업 영역에서 구독을 제거 합니다.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Log Analytics 작업 영역에서 활동 로그 분석
활동 로그를 Log Analytics 작업 영역에 연결 하면 [로그 쿼리](../log-query/log-query-overview.md)를 사용 하 여 검색할 수 있는 *azureactivity* 라는 테이블에 항목이 작업 영역에 기록 됩니다. 이 테이블의 구조는 [로그 항목의 범주](activity-log-view.md#categories-in-the-activity-log)에 따라 달라 집니다. 각 범주에 대 한 설명은 [Azure 활동 로그 이벤트 스키마](activity-log-schema.md) 를 참조 하세요.


### <a name="data-structure-changes"></a>데이터 구조 변경
진단 설정은 *azureactivity* 테이블의 구조를 변경 하 여 활동 로그를 수집 하는 데 사용 되는 레거시 방법과 동일한 데이터를 수집 합니다.

다음 표의 열은 업데이트 된 스키마에서 더 이상 사용 되지 않습니다. 이러한 작업은 여전히 *azureactivity* 에 있지만 데이터는 없습니다. 이러한 열에 대 한 대체는 새로운 것은 아니지만 사용 되지 않는 열과 동일한 데이터를 포함 합니다. 서로 다른 형식 이므로이를 사용 하는 로그 쿼리를 수정 해야 할 수도 있습니다. 

| 사용 되지 않는 열 | 대체 열 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus 상태 | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> 경우에 따라 이러한 열의 값은 모두 대문자 일 수 있습니다. 이러한 열을 포함 하는 쿼리가 있는 경우 [= ~ 연산자](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) 를 사용 하 여 대/소문자를 구분 하지 않는 비교를 수행 해야 합니다.

다음 열이 업데이트 된 스키마의 *Azureactivity* 에 추가 되었습니다.

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>활동 로그 분석 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션은 곧 사용 되지 않으며, Log Analytics 작업 영역에서 업데이트 된 스키마를 사용 하는 통합 문서로 대체 됩니다. 이미 사용 하도록 설정한 경우에도 솔루션을 사용할 수 있지만 레거시 설정을 사용 하 여 활동 로그를 수집 하는 경우에만 사용할 수 있습니다. 



### <a name="use-the-solution"></a>솔루션 사용
모니터링 솔루션은 Azure Portal의 **모니터** 메뉴에서 액세스할 수 있습니다. **Insights** 섹션에서 **자세히** 를 선택 하 여 솔루션 타일이 있는 **개요** 페이지를 엽니다. **Azure 활동 로그** 타일에는 작업 영역의 **azureactivity** 레코드 수 수가 표시 됩니다.

![Azure 활동 로그 타일](media/collect-activity-logs/azure-activity-logs-tile.png)


Azure **활동 로그** 타일을 클릭 하 여 **azure 활동 로그** 보기를 엽니다. 뷰에는 다음 표의 시각화 파트가 포함 되어 있습니다. 각 파트에는 지정 된 시간 범위에 대 한 해당 부분의 기준과 일치 하는 항목이 최대 10 개까지 나열 됩니다. 파트 아래쪽의 **모두 보기** 를 클릭 하 여 일치 하는 모든 레코드를 반환 하는 로그 쿼리를 실행할 수 있습니다.

![Azure 활동 로그 대시보드](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>새 구독에 대해 솔루션을 사용 하도록 설정
Azure Portal를 사용 하 여 구독에 활동 로그 분석 솔루션을 더 이상 추가할 수 없습니다. 리소스 관리자 템플릿을 사용 하 여 다음 절차를 사용 하 여 추가할 수 있습니다. 

1. 다음 json을 *Activitylogtemplate*. json 이라는 파일에 복사 합니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. 다음 PowerShell 명령을 사용 하 여 템플릿을 배포 합니다.

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>다음 단계

- [활동 로그](platform-logs-overview.md)에 대해 자세히 알아보세요.
- [Azure Monitor data platform](data-platform.md)에 대해 자세히 알아보세요.
- [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 활동 로그에서 자세한 정보를 볼 수 있습니다.
