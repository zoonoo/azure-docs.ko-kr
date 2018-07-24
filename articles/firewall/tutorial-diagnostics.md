---
title: 자습서 - Azure Firewall 로그 모니터링
description: 이 자습서에서는 Azure Firewall 로그를 사용하도록 설정하고 관리하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991902"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>자습서: Azure Firewall 로그 모니터링

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure Firewall 문서의 예제에서는 이미 Azure Firewall 공개 미리 보기를 사용하도록 설정했다고 가정합니다. 자세한 내용은 [Azure Firewall 공개 미리 보기 사용하도록 설정](public-preview.md)을 참조합니다.

방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.

이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 로그를 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), 저장소 및 Event Hubs로 보내 Log Analytics 또는 Excel 및 Power BI 같은 다른 도구에서 분석합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal을 통한 로깅 사용
> * PowerShell을 통해 로깅을 사용하도록 설정
> * 활동 로그 보기 및 분석
> * 네트워크 및 응용 프로그램 규칙 로그 보기 및 분석

## <a name="diagnostic-logs"></a>진단 로그

 다음 진단 로그는 Azure Firewall에 사용할 수 있습니다.

* **응용 프로그램 규칙 로그**

   각 Azure Firewall에 대해 사용하도록 설정하는 경우에만 응용 프로그램 규칙 로그를 저장소 계정에 저장하고 Event 허브로 스트리밍하고/또는 Log Analytics로 보냅니다. 허용/거부된 연결에 대한 로그에서 구성된 응용 프로그램 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **네트워크 규칙 로그**

   각 Azure Firewall에 대해 사용하도록 설정하는 경우에만 네트워크 규칙 로그를 저장소 계정에 저장하고 Event 허브로 스트리밍하고/또는 Log Analytics로 보냅니다. 허용/거부된 연결에 대한 로그에서 구성된 네트워크 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

로그 저장에는 세 가지 옵션이 있습니다.

* **Storage 계정** - 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **이벤트 허브** - 다른 SEIM(보안 정보 및 이벤트 관리) 도구와 통합하여 리소스에 대한 알림을 얻을 수 있는 좋은 옵션입니다.
* **Log Analytics** - 일반적으로 응용 프로그램을 실시간 모니터링하거나 추세를 파악하는 데 가장 적합합니다.

## <a name="activity-logs"></a>활동 로그

   활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.

   [Azure 활동 로그](../azure-resource-manager/resource-group-audit.md)(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure Portal을 통한 진단 로깅 사용

진단 로깅을 켜는 이 절차를 완료한 후 데이터가 로그에 표시되는 데 몇 분 정도 걸릴 수 있습니다. 처음에 아무 것도 표시되지 않으면 몇 분 후에 다시 확인합니다.

1. Azure Portal에서 방화벽 리소스 그룹을 열고 방화벽을 클릭합니다.
2. **모니터링**에서 **진단 로그**를 클릭합니다.

   Azure Firewall의 경우 두 개의 서비스 관련 로그를 사용할 수 있습니다.

   * 응용 프로그램 규칙 로그
   * 네트워크 규칙 로그

3. 데이터 수집을 시작하려면 **진단 켜기**를 클릭합니다.
4. **진단 설정** 페이지에서는 진단 로그에 대한 설정을 제공합니다. 
5. 이 예제에서 Log Analytics는 로그를 저장하므로 이름에 **방화벽 로그 분석**을 입력합니다.
6. **Log Analytics에 보내기**를 클릭하여 작업 영역을 구성합니다. 또한 이벤트 허브 및 저장소 계정을 사용하여 진단 로그를 저장할 수도 있습니다.
7. **Log Analytics** 아래에서 **구성**을 클릭합니다.
8. OMS 작업 영역 페이지에서 **새 작업 영역 만들기**를 클릭합니다.
9. **Log analytics 작업 영역** 페이지에서 새 **OMS 작업 영역** 이름에 **firewall-oms**를 입력합니다.
10. 구독을 선택하고, 기존 방화벽 리소스 그룹(**Test-FW-RG**)을 사용하고, 위치에 대해 **미국 동부**를 선택하고, **무료** 가격 책정 계층을 선택합니다.
11. **확인**을 클릭합니다.
   ![구성 프로세스 시작][1]
12. **로그** 아래에서 **AzureFirewallApplicationRule** 및 **AzureFirewallNetworkRule**을 클릭하여 응용 프로그램 및 네트워크 규칙에 대한 로그를 수집합니다.
   ![진단 설정 저장][2]
13. **저장**을 클릭합니다.

## <a name="enable-logging-with-powershell"></a>PowerShell을 통해 로깅을 사용하도록 설정

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 로깅을 사용하도록 설정해야 합니다.

진단 로깅을 활성화하려면 다음 단계를 사용합니다.

1. 로그 데이터를 저장할 저장소 계정의 리소스 ID를 적어 둡니다. 이 값은 */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>* 형식입니다.

   구독의 모든 저장소 계정을 사용할 수 있습니다. Azure Portal을 사용하여 이 정보를 찾을 수 있습니다. 정보는 리소스 **속성** 페이지에 있습니다.

2. 로깅을 사용할 방화벽의 리소스 ID를 적어 둡니다. 이 값은 */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/azureFirewalls/\<Firewall name\>* 의 형식입니다.

   포털을 사용하여 이 정보를 찾을 수 있습니다.

3. 다음 PowerShell cmdlet을 사용하여 진단 로깅을 사용하도록 설정합니다.

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>진단 로그에는 별도의 저장소 계정이 필요하지 않습니다. 액세스 및 성능 로깅에 저장소를 사용할 경우 서비스 요금이 부과됩니다.

## <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법 중 하나를 사용하여 활동 로그 데이터를 확인하고 분석할 수 있습니다.

* **Azure 도구** - Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [Resource Manager의 활동 작업](../azure-resource-manager/resource-group-audit.md) 문서에 자세히 나와 있습니다.
* **Power BI** - [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우 무료로 사용해볼 수 있습니다. [Power BI용 Azure Activity Logs 콘텐츠 팩](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 미리 구성된 대시보드를 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>네트워크 및 응용 프로그램 규칙 로그 보기 및 분석

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)는 카운터 및 이벤트 로그 파일을 수집합니다. 여기에는 로그를 분석하는 시각화 및 강력한 검색 기능이 포함되어 있습니다.

저장소 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

로그를 수집하도록 방화벽을 구성했으므로 데이터를 보려면 Log Anaytics를 탐색할 수 있습니다.

> [!div class="nextstepaction"]
> [Log Analytics의 네트워킹 모니터링 솔루션](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
