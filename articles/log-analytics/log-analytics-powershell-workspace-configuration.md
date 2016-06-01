<properties
	pageTitle="PowerShell을 사용하여 Log Analytics 작업 영역 만들기 및 구성 | Microsoft Azure"
	description="Log Analytics는 온-프레미스 또는 클라우드 인프라의 서버에서 데이터를 사용합니다. Azure 진단에 의해 생성된 경우에 Azure 저장소에서 컴퓨터 데이터를 수집할 수 있습니다."
	services="log-analytics"
	documentationCenter=""
	authors="richrundmsft"
	manager="jochan"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="powershell"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="richrund"/>

# PowerShell을 사용하여 Log Analytics 관리

[Log Analytics PowerShell Cmdlet](http://msdn.microsoft.com/library/mt188224.aspx)을 사용하여 명령줄에서 또는 스크립트의 일부로 Log Analytics의 다양한 기능을 수행할 수 있습니다. PowerShell을 사용하여 수행할 수 있는 작업의 예:

+ 작업 영역 만들기
+ 솔루션 추가 또는 제거
+ 저장된 검색 가져오기 및 내보내기
+ Azure 가상 컴퓨터에 Log Analytics 에이전트 추가
+ Azure 진단을 사용하여 수집된 데이터를 인덱싱하도록 Log Analytics 구성

이 문서에서는 PowerShell에서 수행할 수 있는 몇 가지 기능을 보여 주는 두 가지 코드 샘플을 제공합니다. 다른 기능에 대해서는 [Log Analytics PowerShell Cmdlet 참조](http://msdn.microsoft.com/library/mt188224.aspx)를 참조할 수 있습니다.

> [AZURE.NOTE] Log Analytics의 이전 이름은 Cmdlet에 사용된 이름인 Operational Insights였습니다.

## 필수 조건

PowerShell을 Log Analytics 작업 영역과 함께 사용하려면 다음을 갖추어야 합니다.

+ Azure 구독, 그리고 
+ Azure 구독에 연결된 Azure Log Analytics 작업 영역.

OMS 작업 영역을 만들었지만 아직 Azure 구독에 연결하지 않은 경우 Azure 포털, OMS 포털에서 또는 Get-AzureRMOperationalInsightsLinkTargets 및 New-AzureRMOperationalInsightsWorkspace Cmdlet을 사용하여 링크를 만들 수 있습니다.

## Log Analytics 작업 영역을 만들고 솔루션 및 저장된 검색을 추가합니다.

다음 스크립트 샘플에서는 다음 작업의 방법을 보여 줍니다.

1.	작업 영역 만들기
2.	사용 가능한 솔루션 나열
3.	작업 영역에 솔루션 추가
4.	저장된 검색 가져오기
5.	저장된 검색 내보내기

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

```

## Azure 진단을 인덱싱하도록 Log Analytics 구성 

웹 및 작업자 역할, 서비스 패브릭 클러스터, 네트워크 보안 그룹, 키 자격 증명 모음 및 응용 프로그램 게이트웨이를 비롯한 Azure 리소스에 대한 에이전트 없는 모니터링을 위해서는 리소스는 먼저 Azure 진단을 저장소 계정에 쓸 수 있도록 설정한 다음 Log Analytics를 저장소 계정에서 로그를 수집하도록 구성할 수 있습니다.

또한 PowerShell을 사용하여 Azure 구독 한 개에 Log Analytics 작업 영역을 구성하고 다른 Azure 구독에서 로그를 수집할 수도 있습니다.

아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

1.	Log Analytics가 다음 출처에서 오는 데이터를 인덱싱하는 기존 저장소 계정 및 위치 나열
2.	저장소 계정에서 읽을 새 구성 만들기
3.	새로 만든 구성을 추가 위치에서 오는 데이터를 인덱싱하도록 업데이트
4.	새로 만든 구성 삭제

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## 다음 단계

- Log Analytics의 구성을 위한 PowerShell 사용에 대한 자세한 내용은 [Log Analytics PowerShell Cmdlet 검토](http://msdn.microsoft.com/library/mt188224.aspx)를 참조하세요.

<!---HONumber=AcomDC_0518_2016-->