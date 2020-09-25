---
title: Azure Firewall 로그 및 메트릭 모니터링
description: 이 문서에서는 Azure 방화벽 로그 및 메트릭을 사용 하도록 설정 하 고 관리 하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/17/2020
ms.author: victorh
ms.openlocfilehash: 784459282007edab599d54edff0d2b38eed07b34
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320645"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Azure Firewall 로그 및 메트릭 모니터링

방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다. 메트릭을 사용하여 포털에서 성능 카운터를 볼 수 있습니다.

이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 로그는 [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md), 저장소 및 Event Hubs 전송 되며 Azure Monitor 로그 나 Excel 및 Power BI 같은 다른 도구에 의해 분석 될 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 azure 방화벽에 사용할 수 있는 진단 로그 및 메트릭에 대 한 개요는 [Azure 방화벽 로그 및 메트릭을](logs-and-metrics.md) 읽어 보아야 합니다.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure Portal을 통한 진단 로깅 사용

진단 로깅을 켜는 이 절차를 완료한 후 데이터가 로그에 표시되는 데 몇 분 정도 걸릴 수 있습니다. 처음에 아무 것도 표시되지 않으면 몇 분 후에 다시 확인합니다.

1. Azure Portal에서 방화벽 리소스 그룹을 열고 방화벽을 선택 합니다.
2. **모니터링** 아래에서 **진단 설정**을 선택합니다.

   Azure 방화벽의 경우 다음과 같은 4 가지 서비스별 로그를 사용할 수 있습니다.

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. **진단 설정 추가**를 선택 합니다. **진단 설정** 페이지에서는 진단 로그에 대한 설정을 제공합니다.
5. 이 예제에서 Azure Monitor 로그가 로그를 저장하므로 이름에 **방화벽 로그 분석**을 입력합니다.
6. **로그**에서 **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog**및 **AzureFirewallDnsProxy** 를 선택 하 여 로그를 수집 합니다.
7. **Log Analytics 보내기를** 선택 하 여 작업 영역을 구성 합니다.
8. 구독을 선택합니다.
9. **저장**을 선택합니다.

## <a name="enable-logging-with-powershell"></a>PowerShell을 통해 로깅을 사용하도록 설정

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 로깅을 사용하도록 설정해야 합니다.

진단 로깅을 활성화하려면 다음 단계를 사용합니다.

1. 로그 데이터를 저장할 스토리지 계정의 리소스 ID를 적어 둡니다. 이 값의 형식은 */Subscriptions/ \<subscriptionId\> /Stggg/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> *입니다.

   구독의 모든 스토리지 계정을 사용할 수 있습니다. Azure Portal을 사용하여 이 정보를 찾을 수 있습니다. 정보는 리소스 **속성** 페이지에 있습니다.

2. 로깅을 사용할 방화벽의 리소스 ID를 적어 둡니다. 이 값의 형식은 */Subscriptions/ \<subscriptionId\> /Stggg/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\> *입니다.

   포털을 사용하여 이 정보를 찾을 수 있습니다.

3. 다음 PowerShell cmdlet을 사용하여 진단 로깅을 사용하도록 설정합니다.

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>진단 로그에는 별도의 스토리지 계정이 필요하지 않습니다. 액세스 및 성능 로깅에 스토리지를 사용할 경우 서비스 요금이 부과됩니다.

## <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법 중 하나를 사용하여 활동 로그 데이터를 확인하고 분석할 수 있습니다.

* **Azure 도구** - Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [Resource Manager의 활동 작업](../azure-resource-manager/management/view-activity-logs.md) 문서에 자세히 나와 있습니다.
* **Power BI**: 아직 [Power BI](https://powerbi.microsoft.com/pricing) 계정이 없으면 무료로 사용해 볼 수 있습니다. [Power BI용 Azure Activity Logs 콘텐츠 팩](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 미리 구성된 대시보드를 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.
* **Azure 센티널**: azure 방화벽 로그를 azure 센티널에 연결 하 여 통합 문서에서 로그 데이터를 보고,이를 사용 하 여 사용자 지정 경고를 만들고,이를 통합 하 여 조사를 개선할 수 있습니다. Azure 센티널의 Azure 방화벽 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다. 자세한 내용은 [Azure 방화벽에서 데이터 연결](../sentinel/connect-azure-firewall.md)을 참조 하세요.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>네트워크 및 애플리케이션 규칙 로그 보기 및 분석

[Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md)는 카운터 및 이벤트 로그 파일을 수집합니다. 여기에는 로그를 분석하는 시각화 및 강력한 검색 기능이 포함되어 있습니다.

Azure 방화벽 로그 분석 샘플 쿼리를 보려면 [Azure 방화벽 로그 분석 샘플](log-analytics-samples.md)을 참조 하세요.

스토리지 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## <a name="view-metrics"></a>메트릭 보기
Azure 방화벽으로 이동 하 고 **모니터링** 에서 **메트릭**을 선택 합니다. 사용 가능한 값을 보려면 **메트릭** 드롭다운 목록을 선택합니다.

## <a name="next-steps"></a>다음 단계

로그를 수집하도록 방화벽을 구성했으므로 Azure Monitor 로그를 살펴보고 데이터를 볼 수 있습니다.

[Azure Monitor 로그의 네트워킹 모니터링 솔루션](../azure-monitor/insights/azure-networking-analytics.md)
