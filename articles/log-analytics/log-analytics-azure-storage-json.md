---
title: "Log Analytics를 사용하여 Azure 진단 로그 분석 | Microsoft Docs"
description: "Log Analytics는 JSON 형식의 Blob Storage에 Azure 진단 로그를 기록하는 Azure 서비스에서 로그를 읽어올 수 있습니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: adf2f366-ea98-4250-ae66-6d2cfce5b4f9
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: d0b98fe9c3ec685e95b5a8cf96afe1fb72c659f2
ms.openlocfilehash: 76dfc064d4c50f291e48ce35435229da1323a520


---
# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Log Analytics를 사용하여 Azure 진단 로그 분석 
Log Analytics는 JSON 형식의 Blob Storage에 [Azure 진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)를 기록하는 다음 Azure 서비스에 대해 로그를 읽을 수 있습니다.

* 키 자격 증명 모음(Preview)
* 응용 프로그램 게이트웨이(Preview)
* 네트워크 보안 그룹(Preview)

> [!NOTE]
> 로그를 수집하는 이 방법은 더 이상 사용되지 않습니다. 위의 서비스에 대한 로그 수집은 [Log Analytics에 직접 Azure 진단](log-analytics-azure-storage.md)을 사용합니다. Key Vault 분석 및 Azure 네트워크 분석 관리 솔루션이 Azure 진단에서 직접 수집된 로그를 지원하도록 업데이트되면 이 설명서는 삭제됩니다.
>
>

다음 섹션에서는 PowerShell을 사용하여 다음 작업을 수행하는 과정을 안내합니다.

* 각 리소스에 대한 저장소에서 로그를 수집하도록 Log Analytics 구성  
* Azure 서비스에 대한 Log Analytics 솔루션을 사용하도록 설정

Log Analytics에서 이러한 리소스에 대한 데이터를 수집하려면 Azure 진단을 사용하도록 설정되어 있어야 합니다. `Set-AzureRmDiagnosticSetting` cmdlet을 사용하여 로깅을 사용하도록 설정합니다.

진단 로깅을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [키 자격 증명 모음](../key-vault/key-vault-logging.md)
* [응용 프로그램 게이트웨이](../application-gateway/application-gateway-diagnostics.md)
* [네트워크 보안 그룹](../virtual-network/virtual-network-nsg-manage-log.md)

이 문서에는 다음에 대한 세부 정보도 포함되어 있습니다.

* 데이터 수집 문제 해결
* 데이터 수집 중지

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Azure 진단 로그를 수집하도록 Log Analytics 구성
이 서비스에 대한 로그 수집과 로그를 시각화하는 솔루션 사용은 PowerShell 스크립트를 통해 수행됩니다.

아래 예제에서는 모든 지원되는 리소스에 대해 로깅을 사용하도록 설정합니다.

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


일부 리소스의 경우 [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)에서 설명한 절차를 통해 Azure Portal에서 앞의 구성을 수행할 수 있습니다.

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Azure 진단 로그를 수집하도록 Log Analytics 구성
Log Analytics 구성을 지원하기 위해 다음 두 개의 cmdlet을 내보내는 PowerShell 스크립트 모듈을 제공했습니다.

1. `Add-AzureDiagnosticsToLogAnalyticsUI`에 입력하라는 메시지가 표시되며 간단한 구성을 설정할 수 있습니다.
2. `Add-AzureDiagnosticsToLogAnalytics` 는 모니터링할 리소스를 입력으로 가져오고 Log Analytics를 구성합니다.  

### <a name="pre-requisites"></a>필수 구성 요소
1. 버전 1.0.8 이상의 Operational Insights cmdlet이 있는 Azure PowerShell
   * [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)
   * 사용 중인 cmdlet 버전(`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `)을 확인합니다. 
2. 모니터링할 Azure 리소스에 대해 진단 로깅이 구성됩니다. 진단을 사용하도록 설정하는 방법은 `Set-AzureRmDiagnosticSetting`을 사용하거나 [Log Analytics를 사용하여 Azure Storage 계정에서 데이터 수집](log-analytics-azure-storage.md)을 참조하세요.
3. [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS) 작업 영역  
4. AzureDiagnosticsAndLogAnalytics PowerShell 모듈
   * PowerShell 갤러리에서 [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) 모듈을 다운로드합니다.

### <a name="option-1-run-the-interactive-configuration-scripts"></a>옵션 1: 대화형 구성 스크립트 실행
PowerShell을 열고 실행합니다.

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

사용 가능한 선택 항목 목록이 표시되며 원하는 옵션을 선택하라는 메시지가 표시됩니다.
다음 각 항목에 대해 옵션을 선택해야 합니다.

* 로그를 수집할 리소스 유형(Azure 서비스)
* 로그를 수집할 리소스 인스턴스
* 데이터를 수집할 Log Analytics 작업 영역

이 스크립트를 실행하면 새 진단 데이터가 저장소에 기록되고 약 30분 후에 Log Analytics에서 레코드가 표시됩니다. 이 시간 후에 레코드를 사용할 수 없으면 아래의 문제 해결 섹션을 참조하세요.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>옵션 2: 리소스 목록 작성 및 구성 cmdlet에 전달
Azure 진단이 설정된 리소스 목록을 작성하고 해당 리소스를 구성 cmdlet에 전달할 수 있습니다.

`Get-Help Add-AzureDiagnosticsToLogAnalytics`를 실행하여 cmdlet에 대한 추가 정보를 확인할 수 있습니다.

OMS에 대한 자세한 내용은 [Log Analytics PowerShell Cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)을 참조하세요.

> [!NOTE]
> 리소스와 작업 영역이 다른 Azure 구독에 있는 경우 필요에 따라 `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`을 사용하여 구독 사이를 전환합니다.
>
>

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
이 스크립트를 실행하면 새 진단 데이터가 저장소에 기록되고 약 30분 후에 Log Analytics에서 레코드가 표시됩니다. 이 시간 후에 레코드를 사용할 수 없으면 아래의 문제 해결 섹션을 참조하세요.  

> [!NOTE]
> 이 구성은 Azure Portal에서 표시되지 않습니다. `Get-AzureRmOperationalInsightsStorageInsight` cmdlet을 사용하여 구성을 확인할 수 있습니다.  
>
>

## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Log Analytics의 Azure 진단 로그 수집 중지
리소스에 대해 Log Analytics 구성을 삭제하려면 `Remove-AzureRmOperationalInsightsStorageInsight` cmdlet를 사용합니다.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Azure 진단 로그에 대한 구성 문제 해결
*문제*

클래식 저장소에 로깅하는 리소스를 구성할 때 다음 오류가 표시됩니다.

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*해결 방법*

`Add-AzureAccount`을 통해 클래식 배포 모델에 대한 API 로그인

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Azure 진단 로그에 대한 데이터 수집 문제 해결
Log Analytics에서 Azure 리소스에 대한 데이터가 표시되지 않으면 다음 문제 해결 단계를 사용할 수 있습니다.

* 저장소 계정으로의 데이터 흐름 확인
* 서비스에 대한 Log Analytics 솔루션을 사용하도록 설정되었는지 확인
* 저장소에서 읽도록 Log Analytics가 구성되어 있는지 확인
* 저장소 계정 키 업데이트

### <a name="verify-data-is-flowing-to-the-storage-account"></a>데이터가 저장소 계정으로 흐르는지 확인
Azure Storage 탐색을 제공하는 도구(예: Visual Studio)나 PowerShell을 사용하여 저장소 계정으로 데이터가 흘러가는지 확인할 수 있습니다.

리소스가 로깅하도록 구성된 저장소 계정을 찾으려면 다음 PowerShell을 사용합니다.

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Azure 진단에 사용되는 저장소 컨테이너의 이름이 다음 형식입니다.  

`insights-logs-<Category>`

저장소 계정의 내용을 보는 방법에 대한 자세한 내용은 [저장소 cmdlet을 사용하여 컨테이너에서 최신 데이터 확인](../storage/storage-powershell-guide-full.md) 을 참조하세요.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>서비스에 대한 Log Analytics 솔루션을 사용하도록 설정되었는지 확인
`Add-AzureDiagnosticsToLogAnalyticsUI`를 사용하는 경우 올바른 Log Analytics 솔루션이 자동으로 활성화됩니다.

솔루션이 사용되도록 설정되어 있는지 확인하려면 다음 PowerShell을 실행합니다.

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

이 솔루션을 사용하도록 설정되어 있지 않으면 다음 PowerShell을 사용하여 사용하도록 설정할 수 있습니다.

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

각 리소스 유형에 대해 사용하도록 설정할 솔루션의 이름을 찾으려면 다음 PowerShell을 사용합니다(이 변수는 모듈을 가져오면 사용 가능함).

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>저장소에서 읽도록 Log Analytics가 구성되어 있는지 확인
다른 Azure Resources를 추가하는 경우 해당 항목에 대해 진단 로깅을 활성화하고 Log Analytics를 구성해야 합니다.
Log Analytics가 해당 로그를 수집하도록 구성된 리소스 및 저장소 계정을 확인하려면 다음 PowerShell을 사용합니다.

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>저장소 키 업데이트
저장소 계정에 대한 키를 변경하는 경우 Log Analytics 구성도 새 키로 업데이트해야 합니다.
다음 PowerShell을 사용하여 Log Analytics 구성을 새 키로 업데이트할 수 있습니다.

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

저장소 정보 이름을 찾으려면 앞의 예제와 같이 `Get-AzureRmOperationalInsightsStorageInsight` cmdlet을 사용합니다.

## <a name="next-steps"></a>다음 단계
* [이벤트에 대해 IIS 및 테이블 저장소에 Blob Storage를 사용하여](log-analytics-azure-storage-iis-table.md) Table Storage에 진단을 기록하는 Azure 서비스나 Blob Storage에 기록된 IIS 로그에 대해 로그를 읽을 수 있습니다.
* [솔루션을 사용하도록 설정](log-analytics-add-solutions.md) 하여 데이터에 대한 정보를 제공합니다.
* [검색 쿼리를 사용](log-analytics-log-searches.md) 하여 데이터를 분석합니다.



<!--HONumber=Dec16_HO3-->


