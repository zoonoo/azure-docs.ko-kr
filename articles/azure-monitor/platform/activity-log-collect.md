---
title: Azure 모니터에서 Azure 활동 로그 수집 및 분석
description: Azure 모니터 로그에 있는 Azure 활동 로그를 수집하고 모니터링 솔루션을 사용하여 모든 Azure 구독에서 Azure 활동 로그를 분석하고 검색합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382865"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Azure 모니터에서 Azure 활동 로그 수집 및 분석
[Azure 활동 로그는](platform-logs-overview.md) Azure에서 발생한 구독 수준 이벤트에 대한 통찰력을 제공하는 [플랫폼 로그입니다.](platform-logs-overview.md) Azure 포털에서 활동 로그를 볼 수 있지만 Azure Monitor의 추가 기능을 사용하도록 Log Analytics 작업 영역으로 보내도록 구성해야 합니다. 이 문서에서는 이 구성을 수행하는 방법과 활동 로그를 Azure 저장소 및 이벤트 허브로 보내는 방법에 대해 설명합니다.

로그 분석 작업 영역에서 활동 로그를 수집하면 다음과 같은 이점이 있습니다.

- Log Analytics 작업 영역에 저장된 활동 로그 데이터에 대한 데이터 수집 또는 데이터 보존 요금이 없습니다.
- 활동 로그 데이터를 Azure Monitor에서 수집한 다른 모니터링 데이터와 상호 연관시다.
- 로그 쿼리를 사용하여 복잡한 분석을 수행하고 활동 로그 항목에 대한 심층적인 통찰력을 얻을 수 있습니다.
- 활동 항목과 함께 로그 경고를 사용하여 보다 복잡한 경고 논리를 사용할 수 있습니다.
- 90일 이상 활동 로그 항목을 저장합니다.
- 여러 Azure 구독 및 테넌트에서 로그 항목을 함께 분석할 수 있는 한 위치로 통합합니다.

> [!IMPORTANT]
> 테넌자 간에 로그를 수집하려면 [Azure 등대가](/azure/lighthouse)필요합니다.

## <a name="collecting-activity-log"></a>활동 로그 수집
활동 로그는 [Azure 포털에서 볼](activity-log-view.md)수 있도록 자동으로 수집됩니다. Log Analytics 작업 영역에서 수집하거나 Azure 저장소 또는 이벤트 허브를 보내려면 [진단 설정을](diagnostic-settings.md)만듭니다. 이는 리소스 로그가 사용하는 것과 동일한 방법으로 모든 플랫폼 로그에 대해 [일관되게 유지됩니다.](platform-logs-overview.md)  

활동 로그에 대한 진단 설정을 만들려면 Azure 모니터의 **활동 로그** 메뉴에서 **진단 설정을** 선택합니다. 설정 만들기에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기를](diagnostic-settings.md) 참조하십시오. 필터링할 수 있는 범주에 대한 설명은 [활동 로그에서 범주를](activity-log-view.md#categories-in-the-activity-log) 참조하세요. 레거시 설정이 있는 경우 진단 설정을 만들기 전에 설정하지 않도록 설정해야 합니다. 둘 다 사용하도록 설정하면 중복 데이터가 발생할 수 있습니다.

![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> 현재 Azure 포털 및 리소스 관리자 템플릿을 사용하여 구독 수준 진단 설정만 만들 수 있습니다. 


## <a name="legacy-settings"></a>레거시 설정 
진단 설정은 활동 로그를 다른 대상으로 보내는 기본 방법이지만 진단 설정으로 대체하도록 선택하지 않으면 레거시 메서드가 계속 작동합니다. 진단 설정에는 레거시 메서드에 비해 다음과 같은 장점이 있으므로 구성을 업데이트하는 것이 좋습니다.

- 모든 플랫폼 로그를 수집하는 일관된 방법.
- 여러 구독 및 테넌트와 의 활동 로그를 수집합니다.
- 특정 범주에 대한 로그만 수집하도록 컬렉션을 필터링합니다.
- 모든 활동 로그 범주를 수집합니다. 일부 범주는 레거시 메서드를 사용하여 수집되지 않습니다.
- 로그 생성을 위한 더 빠른 대기 시간. 이전 방법은 약 15분 의 대기 시간을 가지며 진단 설정은 약 1분만 추가합니다.



### <a name="log-profiles"></a>로그 프로필
로그 프로필은 활동 로그를 Azure 저장소 또는 이벤트 허브로 보내는 레거시 방법입니다. 다음 절차를 사용하여 로그 프로필 작업을 계속하거나 진단 설정으로 마이그레이션할 준비를 비활성화합니다.

1. Azure 포털의 **Azure 모니터** 메뉴에서 **활동 로그를**선택합니다.
3. **진단 설정을**클릭합니다.

   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 레거시 환경을 보려면 보라색 배너를 클릭합니다.

    ![레거시 경험](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
활동 로그를 Log Analytics 작업 영역에 수집하는 레거시 방법은 작업 영역 구성의 로그를 연결하는 것입니다. 

1. Azure 포털의 **로그 분석 작업 영역** 메뉴에서 작업 영역을 선택하여 활동 로그를 수집합니다.
1. 작업 **영역** 의 메뉴의 작업 영역 데이터 원본 섹션에서 Azure **활동 로그를**선택합니다.
1. 연결할 구독을 클릭합니다.

    ![작업 영역](media/activity-log-collect/workspaces.png)

1. **연결을** 클릭하여 구독의 활동 로그를 선택한 작업 영역에 연결합니다. 구독이 이미 다른 작업 영역에 연결되어 있는 경우 먼저 **연결을** 끊고 연결을 끊습니다.

    ![작업 영역 연결](media/activity-log-collect/connect-workspace.png)


설정을 사용하지 않도록 설정하려면 동일한 절차를 수행하고 **연결 끊기를** 클릭하여 작업 영역에서 구독을 제거합니다.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>로그 분석 작업 영역에서 활동 로그 분석
활동 로그를 로그 분석 작업 영역에 연결하면 [로그 쿼리로](../log-query/log-query-overview.md)검색할 수 있는 *AzureActivity라는* 테이블에 항목이 작업 영역에 기록됩니다. 이 테이블의 구조는 [로그 항목의 범주에](activity-log-view.md#categories-in-the-activity-log)따라 다릅니다. 각 범주에 대한 설명은 [Azure 활동 로그 이벤트 스키마를](activity-log-schema.md) 참조하십시오.


### <a name="data-structure-changes"></a>데이터 구조 변경
진단 설정은 AzureActivity 테이블의 구조에 대한 일부 변경 사항과 함께 활동 로그를 수집하는 데 사용되는 레거시 방법과 동일한 데이터를 *수집합니다.*

다음 표의 열은 업데이트된 스키마에서 더 이상 사용되지 않습니다. *AzureActivity에는* 여전히 존재하지만 데이터가 없습니다. 이러한 열의 대체는 새 열이 아니지만 더 이상 사용되지 않는 열과 동일한 데이터를 포함합니다. 다른 형식이므로 이를 사용하는 로그 쿼리를 수정해야 할 수 있습니다. 

| 더 이상 사용되지 않습니다. | 대체 열 |
|:---|:---|
| 활동 상태    | ActivityStatusValue    |
| 활동하위 상태 | 활동하위 상태값 |
| OperationName     | 작업 이름값     |
| ResourceProvider  | 리소스 공급자값  |

> [!IMPORTANT]
> 경우에 따라 이러한 열의 값은 모든 대문자에 있을 수 있습니다. 이러한 열이 포함된 쿼리가 있는 경우 [=~ 연산자(=~ 연산자)를](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) 사용하여 대/소문자를 구분하지 않는 비교를 수행해야 합니다.

다음 열이 업데이트된 스키마에서 *AzureActivity에* 추가되었습니다.

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>활동 로그 분석 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션은 곧 더 이상 사용되지 않으며 Log Analytics 작업 영역에서 업데이트된 스키마를 사용하여 통합 문서로 대체됩니다. 이미 활성화된 경우 솔루션을 계속 사용할 수 있지만 레거시 설정을 사용하여 활동 로그를 수집하는 경우에만 사용할 수 있습니다. 



### <a name="use-the-solution"></a>솔루션 사용
모니터링 솔루션은 Azure 포털의 **모니터** 메뉴에서 액세스됩니다. **인사이트** 섹션에서 **자세히 선택하여** 솔루션 타일로 개요 페이지를 **엽니다.** **Azure 활동 로그** 타일에는 작업 영역에 **AzureActivity** 레코드 수가 표시됩니다.

![Azure 활동 로그 타일](media/collect-activity-logs/azure-activity-logs-tile.png)


Azure **활동 로그** 타일을 클릭하여 Azure 활동 로그 보기를 **엽니다.** 뷰에는 다음 표의 시각화 부분이 포함됩니다. 각 부품에는 지정된 시간 범위에 대한 해당 부품의 조건과 일치하는 최대 10개의 항목이 나열됩니다. 부품 하단의 **모든 참조를** 클릭하여 일치하는 모든 레코드를 반환하는 로그 쿼리를 실행할 수 있습니다.

![Azure 활동 로그 대시보드](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>새 구독에 대한 솔루션 사용
곧 Azure 포털을 사용하여 구독에 활동 로그 분석 솔루션을 추가할 수 없게 됩니다. 리소스 관리자 템플릿을 사용하여 다음 절차를 사용하여 추가할 수 있습니다. 

1. 다음 json을 *ActivityLogTemplate*.json이라는 파일에 복사합니다.

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

2. 다음 PowerShell 명령을 사용하여 템플릿을 배포합니다.

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>다음 단계

- [활동 로그에](platform-logs-overview.md)대해 자세히 알아보세요.
- Azure 모니터 [데이터 플랫폼에](data-platform.md)대해 자세히 알아봅니다.
- [로그 쿼리를](../log-query/log-query-overview.md) 사용하여 활동 로그에서 자세한 정보를 볼 수 있습니다.
