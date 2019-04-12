---
title: PowerShell을 사용하여 Log Analytics 작업 영역 만들기 및 구성 | Microsoft Docs
description: Azure Monitor에서 log Analytics 작업 영역에서 온-프레미스 서버에서 데이터를 저장 또는 클라우드 인프라입니다. Azure 진단에 의해 생성된 경우에 Azure 스토리지에서 머신 데이터를 수집할 수 있습니다.
services: log-analytics
author: richrundmsft
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: richrund
ms.openlocfilehash: 5c348adea0847929b37d1b61f024859b1d634fe7
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903406"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>PowerShell을 사용 하 여 Azure Monitor에서 Log Analytics 작업 영역 관리

사용할 수는 [Log Analytics PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights/) Log Analytics 작업 영역에서 Azure Monitor의 명령줄 또는 스크립트의 일부로 다양 한 기능을 수행할 수 있습니다.  PowerShell을 사용하여 수행할 수 있는 작업의 예:

* 작업 영역 만들기
* 솔루션 추가 또는 제거
* 저장된 검색 가져오기 및 내보내기
* 컴퓨터 그룹 만들기
* Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
* Linux 및 Windows 컴퓨터에서 성능 카운터 수집
* Linux 컴퓨터의 syslog에서 이벤트 수집
* Windows 이벤트 로그에서 이벤트 수집
* 사용자 지정 이벤트 로그 수집
* Azure 가상 머신에 로그 분석 에이전트 추가
* Azure 진단을 사용하여 수집된 데이터를 인덱싱하도록 로그 분석 구성

이 문서에서는 PowerShell에서 수행할 수 있는 몇 가지 기능을 보여 주는 두 가지 코드 샘플을 제공합니다.  다른 기능에 대해서는 [Log Analytics PowerShell Cmdlet 참조](https://docs.microsoft.com/powershell/module/az.operationalinsights/) 를 참조할 수 있습니다.

> [!NOTE]
> Log Analytics는 이전에 Operational Insights라고 했기 때문에 cmdlet에서는 Operational Insights라고 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건
이러한 예제 Az.OperationalInsights 모듈 또는 나중에 버전 1.0.0 사용 하 여 작동합니다.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기 및 구성
다음 스크립트 샘플에서는 다음 작업의 방법을 보여 줍니다.

1. 작업 영역 만들기
2. 사용 가능한 솔루션 나열
3. 작업 영역에 솔루션 추가
4. 저장된 검색 가져오기
5. 저장된 검색 내보내기
6. 컴퓨터 그룹 만들기
7. Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
8. Linux 컴퓨터에서 논리 디스크 성능 카운터 수집(사용된 Inode 비율, 사용 가능한 MB, 사용된 공간 비율, 초당 디스크 전송, 초당 디스크 읽기, 초당 디스크 쓰기)
9. Linux 컴퓨터에서 syslog 이벤트 수집
10. Windows 컴퓨터에서 애플리케이션 이벤트 로그의 오류 및 경고 이벤트 수집
11. Windows 컴퓨터에서 사용 가능한 메모리(MB) 성능 카운터 수집
12. 사용자 지정 로그 수집

```powershell

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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
위의 예에서 regexDelimiter는 줄 바꿈에 대한 “\\n”으로 정의되었습니다. 로그 구분 기호는 타임스탬프일 수도 있습니다.  지원되는 형식은 다음과 같습니다.

| 형식 | Json RegEx 형식에서는 표준 RegEx에서 모든 \에 대해 두 개의 \\를 사용하므로 RegEx 앱에서 테스트하는 경우 \\를 \로 줄입니다. | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\\\d)\|[0-9])/(([0-3]\\\\d)\|(\\\\d))/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\\\s(AM\|PM\|am\|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `((([0-3]\\\\d)\` | `(\\\\d))/(Jan\|Feb\|Mar\|May\|Apr\|Jul\|Jun\|Aug\|Oct\|Sep\|Nov\|Dec\|jan\|feb\|mar\|may\|apr\|jul\|jun\|aug\|oct\|sep\|nov\|dec)/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\` | `([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?\|Feb(?:ruary)?\|Mar(?:ch)?\|Apr(?:il)?\|May\|Jun(?:e)?\|Jul(?:y)?\|Aug(?:ust)?\|Sep(?:tember)?\|Sept\|Oct(?:ober)?\|Nov(?:ember)?\|Dec(?:ember)?)).*?((?:(?:[0-2]?\\\\d{1})\|(?:[3][01]{1})))(?![\\\\d]).*?((?:(?:[1]{1}\\\\d{1}\\\\d{1}\\\\d{1})\|(?:[2]{1}\\\\d{3})))(?![\\\\d]).*?((?:(?:[0-1][0-9])\|(?:[2][0-3])\|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\\\s?(?:am\|AM\|pm\|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]\|[1][0-2])([0-2][0-9]\|[3][0-1])\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]\|[3][0-1])([0][1-9]\|[1][0-2])[0-9]{2}\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s?([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0-1]?[0-9]\|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> MMM 뒤에 두 개의 공백 | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> 여기서 +는 + 또는 - <br> 여기서 zzzz 시간 오프셋 | `(([0-2][1-9]\|[3][0-1])\\\\/(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\/((19\|20)[0-9][0-9]):([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\\\s[\\\\+\|\\\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T는 리터럴 문자 T | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))T((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Azure 진단을 보내도록 Log Analytics를 구성 합니다.
에이전트 없이 Azure 리소스를 모니터링할 경우 리소스가 Azure 진단을 사용할 수 있어야 하며 Log Analytics 작업 영역에 쓰도록 구성되어야 합니다. 이 방법은 작업 영역에 직접 데이터를 보내고 저장소 계정에 쓸 데이터를 필요 하지 않습니다. 지원되는 리소스는 다음과 같습니다.

| 리소스 종류 | 로그 | 메트릭 |
| --- | --- | --- |
| Application Gateway    | 예 | 예 |
| Automation 계정     | 예 | |
| Batch 계정          | 예 | 예 |
| Data Lake Analytics     | 예 | |
| Data Lake Store         | 예 | |
| 탄력적인 SQL 풀        |     | 예 |
| 이벤트 허브 네임스페이스     |     | 예 |
| IoT Hub                |     | 예 |
| Key Vault               | 예 | |
| Load Balancer          | 예 | |
| Logic Apps              | 예 | 예 |
| 네트워크 보안 그룹 | 예 | |
| Azure Cache for Redis             |     | 예 |
| Search 서비스         | 예 | 예 |
| Service Bus 네임스페이스   |     | 예 |
| SQL(v12)               |     | 예 |
| 웹 사이트               |     | 예 |
| 웹 서버 팜        |     | 예 |

사용 가능한 메트릭에 대한 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](../../azure-monitor/platform/metrics-supported.md)을 참조하세요.

사용 가능한 로그에 대한 자세한 내용은 [진단 로그에 지원되는 서비스 및 스키마](../../azure-monitor/platform/diagnostic-logs-schema.md)를 참조하세요.

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

위의 cmdlet을 사용하여 다른 구독에 속하는 리소스에서 로그를 수집할 수도 있습니다. 로그를 만드는 리소스의 ID와 로그가 전송되는 작업 영역의 ID를 둘 다 제공하기 때문에 cmdlet이 구독 간에 작동할 수 있습니다.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Azure 진단 저장소에서 수집 하도록 Log Analytics 작업 영역 구성
클래식 클라우드 서비스 또는 Service Fabric 클러스터의 실행 중인 인스턴스 내에서 로그 데이터를 수집하려면 먼저 Azure Storage에 데이터를 써야 합니다. Log Analytics 작업 영역을 저장소 계정에서 로그를 수집 하도록 구성 됩니다. 지원되는 리소스는 다음과 같습니다.

* 기존 Cloud Services(웹 및 작업자 역할)
* Service Fabric 클러스터

아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

1. 기존 저장소 계정 및 작업 영역 데이터를 인덱싱할 수 있는 위치를 나열 합니다.
2. 저장소 계정에서 읽을 구성 만들기
3. 새로 만든 구성을 추가 위치에서 오는 데이터를 인덱싱하도록 업데이트
4. 새로 만든 구성 삭제

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

위의 스크립트를 사용하여 다른 구독에 속하는 저장소 계정에서 로그를 수집할 수도 있습니다. 스토리지 계정 리소스 ID와 해당 액세스 키를 제공하기 때문에 스크립트가 구독 간에 작동할 수 있습니다. 액세스 키를 변경할 때 저장소 정보가 새 키를 갖도록 업데이트해야 합니다.


## <a name="next-steps"></a>다음 단계
* [Log Analytics PowerShell Cmdlet 검토](https://docs.microsoft.com/powershell/module/az.operationalinsights/) 를 참조하세요.

