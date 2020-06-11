---
title: Azure Automation 업데이트 관리 로그 쿼리
description: 이 문서에서는 Log Analytics 작업 영역에서 업데이트 관리에 대한 로그를 쿼리하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b40357e71275d835a200f3bc08c618b6713001d8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830772"
---
# <a name="query-update-management-logs"></a>업데이트 관리 로그 쿼리

업데이트 관리 배포 중에 제공되는 세부 정보 외에도 Log Analytics 작업 영역에 저장된 로그를 검색할 수 있습니다. Automation 계정에서 로그를 검색하려면 **업데이트 관리**를 선택하고 배포와 연결된 Log Analytics 작업 영역을 엽니다.

또한 로그 쿼리를 사용자 지정하거나 다른 클라이언트에서 사용할 수도 있습니다. [Log Analytics 검색 API 설명서](https://dev.loganalytics.io/)를 참조하세요.

## <a name="query-update-records"></a>업데이트 레코드 쿼리

업데이트 관리는 Windows 및 Linux VM에 대한 레코드 및 로그 검색 결과에 표시되는 데이터 형식을 수집합니다. 다음 섹션에서는 이러한 레코드에 대해 설명합니다.

### <a name="query-required-updates"></a>필요한 업데이트 쿼리

컴퓨터에 필요한 업데이트를 나타내는 `RequiredUpdate` 형식의 레코드가 만들어집니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | Description | 
|----------|-------------|
| Computer | 보고 컴퓨터의 정규화된 도메인 이름입니다. |
| KBID | Windows 업데이트에 대한 기술 자료 문서 ID입니다. |
| ManagementGroupName | Operations Manager 관리 그룹 또는 Log Analytics 작업 영역의 이름입니다. | 
| Product | 업데이트가 적용되는 제품입니다. | 
| PublishDate | 업데이트를 Windows Update에서 다운로드하여 설치할 준비가 된 날짜입니다. |
| 서버 | | 
| SourceHealthServiceId | Log Analytics Windows 에이전트 ID를 나타내는 고유 식별자입니다. |
| SourceSystem | *OperationsManager* | 
| TenantId | Azure Active Directory의 조직 인스턴스를 나타내는 고유 식별자입니다. | 
| TimeGenerated | 레코드가 생성된 날짜 및 시간입니다. | 
| Type | *Update* | 
| UpdateClassification | 적용할 수 있는 업데이트 유형을 나타냅니다. Windows의 경우:<br> *중요 업데이트*<br> *보안 업데이트*<br> *업데이트 롤업*<br> *기능 팩*<br> *서비스 팩*<br> *정의 업데이트*<br> *Tools*<br> *업데이트*. Linux의 경우:<br> *중요 업데이트 및 보안 업데이트*<br> *기타* |
| UpdateSeverity | 취약성에 대한 심각도입니다. 값은 다음과 같습니다.<br> *심각*<br> *중요*<br> *보통*<br> *낮음* |
| UpdateTitle | 업데이트의 제목입니다.|

### <a name="query-update-record"></a>업데이트 레코드 쿼리

컴퓨터의 설치 상태와 사용 가능한 업데이트를 나타내는 `Update` 형식의 레코드가 만들어집니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | Description | 
|----------|-------------|
| ApprovalSource | Windows 운영 체제에만 적용됩니다. 레코드의 승인 원본입니다. 값은 Microsoft Update입니다. |
| 승인됨 | 레코드가 승인되면 True이고, 그렇지 않으면 False입니다. |
| 분류 | 승인 분류입니다. 값은 Updates입니다. |
| Computer | 보고 컴퓨터의 정규화된 도메인 이름입니다. |
| ComputerEnvironment | 환경입니다. 가능한 값은 Azure 또는 Non-Azure입니다. |
| MSRCBulletinID | 보안 공지 ID 번호입니다. | 
| MSRCSeverity | 취약성에 대한 심각도입니다. 값은 다음과 같습니다.<br> 위험<br> 중요<br> 보통<br> 낮음 |  
| KBID | Windows 업데이트에 대한 기술 자료 문서 ID입니다. |
| ManagementGroupName | Operations Manager 관리 그룹 또는 Log Analytics 작업 영역의 이름입니다. |
| UpdateID | 소프트웨어 업데이트의 고유 식별자입니다. |
| RevisionNumber | 업데이트의 특정 수정 버전에 대한 수정 번호입니다. |
| 옵션 | 레코드가 선택적 요소 레코드이면 True이고, 그렇지 않으면 False입니다. | 
| RebootBehavior | 업데이트를 설치/제거한 후의 재부팅 동작입니다. |
| _ResourceId | 레코드와 연결된 리소스의 고유 식별자입니다. |
| Type | 레코드 종류. 값은 Update입니다. |
| VMUUID | 가상 컴퓨터에 대한 고유 식별자입니다. |
| MG | 관리 그룹 또는 Log Analytics 작업 영역에 대한 고유 식별자입니다. | 
| TenantId | 조직의 Azure Active Directory 인스턴스를 나타내는 고유 식별자입니다. | 
| SourceSystem | 레코드의 원본 시스템입니다. 값이 `OperationsManager`입니다. | 
| TimeGenerated | 레코드 생성 날짜와 시간입니다. | 
| SourceComputerId | 원본 컴퓨터를 나타내는 고유 식별자입니다. | 
| 제목 | 업데이트의 제목입니다. |
| PublishedDate(UTC) | 업데이트를 Windows Update에서 다운로드하여 설치할 준비가 된 날짜입니다.  |
| UpdateState | 업데이트의 현재 상태입니다. | 
| Product | 업데이트가 적용되는 제품입니다. |
| SubscriptionId | Azure 구독의 고유 식별자입니다. | 
| ResourceGroup | 리소스가 속한 리소스 그룹의 이름입니다. | 
| ResourceProvider | 리소스 공급자입니다. | 
| 리소스 | 리소스의 이름입니다. | 
| ResourceType | 리소스 종류입니다. | 

### <a name="query-update-agent-record"></a>업데이트 에이전트 레코드 쿼리

컴퓨터의 업데이트 에이전트에 대한 세부 정보를 제공하는 `UpdateAgent` 형식의 레코드가 만들어집니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | Description | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | 보고 컴퓨터의 정규화된 도메인 이름입니다. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Operations Manager 관리 그룹 또는 Log Analytics 작업 영역의 이름입니다. |
| OSVersion | 운영 체제의 버전입니다. |
| 서버 | |
| SourceHealthServiceId | Log Analytics Windows 에이전트 ID를 나타내는 고유 식별자입니다. |
| SourceSystem | 레코드의 원본 시스템입니다. 값이 `OperationsManager`입니다. | 
| TenantId | 조직의 Azure Active Directory 인스턴스를 나타내는 고유 식별자입니다. |
| TimeGenerated | 레코드 생성 날짜와 시간입니다. |
| Type | 레코드 종류. 값은 Update입니다. | 
| WindowsUpdateAgentVersion | Windows Update 에이전트의 버전입니다. |
| WSUSServer | Windows Update 에이전트에 문제가 있는 경우 문제 해결을 지원합니다. |

### <a name="query-update-deployment-status-record"></a>업데이트 배포 상태 레코드 쿼리

컴퓨터에서 예약된 배포의 업데이트 배포 상태를 제공하는 `UpdateRunProgress` 형식의 레코드가 만들어집니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | Description | 
|----------|-------------|
| Computer | 보고 컴퓨터의 정규화된 도메인 이름입니다. |
| ComputerEnvironment | 환경입니다. 값은 Azure 또는 Non-Azure입니다. | 
| CorrelationId | 업데이트에 대해 실행되는 Runbook 작업의 고유 식별자입니다. |
| EndTime | 동기화 프로세스가 종료된 시간입니다. | 
| ErrorResult | 업데이트 설치에 실패하는 경우 생성되는 Windows Update 오류 코드입니다. | 
| InstallationStatus | 클라이언트 컴퓨터의 가능한 업데이트 설치 상태입니다.<br> `NotStarted` - 작업이 아직 트리거되지 않았습니다.<br> `FailedToStart` -컴퓨터에서 작업을 시작할 수 없습니다.<br> `Failed` - 작업이 시작되었지만 예외로 인해 실패했습니다.<br> `InProgress` - 작업이 진행 중입니다.<br> `MaintenanceWindowExceeded` - 실행이 남아 있지만 유지 관리 기간 간격에 도달한 경우입니다.<br> `Succeeded` - 작업이 성공했습니다.<br> `InstallFailed` - 업데이트를 설치하지 못했습니다.<br> `NotIncluded`<br> `Excluded` |
| KBID | Windows 업데이트에 대한 기술 자료 문서 ID입니다. | 
| ManagementGroupName | Operations Manager 관리 그룹 또는 Log Analytics 작업 영역의 이름입니다. |
| OSType | 운영 체제 유형입니다. 값은 Windows 또는 Linux입니다. | 
| Product | 업데이트가 적용되는 제품입니다. |
| 리소스 | 리소스의 이름입니다. | 
| ResourceId | 레코드와 연결된 리소스의 고유 식별자입니다. |
| ResourceProvider | 리소스 공급자입니다. | 
| ResourceType | 리소스 종류. | 
| SourceComputerId | 원본 컴퓨터를 나타내는 고유 식별자입니다. | 
| SourceSystem | 레코드의 원본 시스템입니다. 값이 `OperationsManager`입니다. |
| StartTime | 업데이트를 설치하도록 예약된 시간입니다. |
| SubscriptionId | Azure 구독의 고유 식별자입니다. | 
| SucceededOnRetry | 첫 번째 시도에서 업데이트 실행이 실패하고 현재 작업은 재시도임을 나타내는 값입니다. |
| TimeGenerated | 레코드 생성 날짜와 시간입니다. |
| 제목 | 업데이트의 제목입니다. |
| Type | 업데이트의 유형입니다. 값이 `UpdateRunProgress`입니다. |
| UpdateId | 소프트웨어 업데이트의 고유 식별자입니다. |
| VMUUID | 가상 컴퓨터에 대한 고유 식별자입니다. |
| ResourceId | 레코드와 연결된 리소스의 고유 식별자입니다. |

### <a name="query-update-summary-record"></a>업데이트 요약 레코드 쿼리

컴퓨터별 업데이트 요약을 제공하는 `UpdateSummary` 형식의 레코드가 만들어집니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | Description | 
|----------|-------------|
| Computer | 보고 컴퓨터의 정규화된 도메인 이름입니다. |
| ComputerEnvironment | 환경입니다. 값은 Azure 또는 Non-Azure입니다. | 
| CriticalUpdatesMissing | 누락된 적용 가능한 중요 업데이트의 수입니다. | 
| ManagementGroupName | Operations Manager 관리 그룹 또는 Log Analytics 작업 영역의 이름입니다. |
| NETRuntimeVersion | Windows 컴퓨터에 설치된 .NET Framework 버전입니다. |
| OldestMissingSecurityUpdateBucket | 가장 오래된 누락 보안 버킷에 대한 지정자입니다. 값은 다음과 같습니다.<br> 최근: 값이 30일 이내인 경우<br> 30일 전<br> 60일 전<br> 90일 전<br> 120일 전<br> 150일 전<br> 180일 전<br> 오래됨: 값이 180일보다 큰 경우 | 
| OldestMissingSecurityUpdateInDays | 설치되지 않은 것으로 검색된 적용 가능한 가장 오래된 업데이트의 총 일수입니다. |
| OsVersion | 운영 체제의 버전입니다. |
| OtherUpdatesMissing | 검색된 누락 업데이트의 수입니다. |
| 리소스 | 레코드에 대한 리소스의 이름입니다. | 
| ResourceGroup | 리소스가 포함된 리소스 그룹의 이름입니다. |
| ResourceId | 레코드와 연결된 리소스의 고유 식별자입니다. |
| ResourceProvider | 리소스 공급자입니다. |
| ResourceType | 리소스 종류. |
| RestartPending | 다시 시작이 보류 중인 경우 True이고, 그렇지 않으면 False입니다. |
| SecurityUpdatesMissing | 적용 가능한 누락된 보안 업데이트 수입니다.| 
| SourceComputerId | 가상 컴퓨터에 대한 고유 식별자입니다. |
| SourceSystem | 레코드의 원본 시스템입니다. 값이 `OpsManager`입니다. | 
| SubscriptionId | Azure 구독의 고유 식별자입니다. |
| TimeGenerated | 레코드 생성 날짜와 시간입니다. |
| TotalUpdatesMissing | 적용 가능한 누락된 업데이트 총 수입니다. | 
| Type | 레코드 종류. 값이 `UpdateSummary`입니다. |
| VMUUID | 가상 컴퓨터에 대한 고유 식별자입니다. |
| WindowsUpdateAgentVersion | Windows Update 에이전트의 버전입니다. |
| WindowsUpdateSetting | Windows Update 에이전트의 상태입니다. 가능한 값은 다음과 같습니다.<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | Windows Update 에이전트에 문제가 있는 경우 문제 해결을 지원합니다. |
| _ResourceId | 레코드와 연결된 리소스의 고유 식별자입니다. |

## <a name="sample-queries"></a>샘플 쿼리

다음 섹션에서는 업데이트 관리를 위해 수집되는 업데이트 레코드에 대한 샘플 로그 쿼리를 제공합니다.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>업데이트 관리에 비 Azure 컴퓨터를 사용할 수 있는지 확인

직접 연결된 컴퓨터가 Azure Monitor 로그와 통신하고 있는지 확인하려면 다음 로그 검색 중 하나를 실행합니다.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows 컴퓨터에서 다음 정보를 검토하여 에이전트가 Azure Monitor 로그에 연결되었는지 확인할 수 있습니다.

1. 제어판에서 **Microsoft Monitoring Agent**를 엽니다. **Azure Log Analytics** 탭에서 에이전트가 다음 메시지를 표시합니다. **Microsoft Monitoring Agent가 Microsoft Log Analytics 서비스에 성공적으로 연결되었습니다.**
2. Windows 이벤트 로그를 엽니다. **애플리케이션 및 서비스 로그\Operations Manager**로 이동한 후, 원본 **서비스 커넥터**에서 이벤트 ID 3000 및 이벤트 ID 5002를 검색합니다. 이러한 이벤트는 컴퓨터가 Log Analytics 작업 영역에 등록되었으며 구성을 수신하고 있음을 나타냅니다.

에이전트가 Azure Monitor 로그와 통신할 수 없고 방화벽 또는 프록시 서버를 통해 인터넷과 통신하도록 구성된 경우 방화벽 또는 프록시 서버가 올바르게 구성되었는지 확인합니다. 방화벽 또는 프록시 서버가 올바르게 구성되어 있는지 확인하는 방법을 알아보려면 [Windows 에이전트에 대한 네트워크 구성](../azure-monitor/platform/agent-windows.md) 또는 [Linux 에이전트에 대한 네트워크 구성](../log-analytics/log-analytics-agent-linux.md)을 참조하세요.

> [!NOTE]
> Linux 시스템에서 프록시 또는 Log Analytics 게이트웨이와 통신하도록 구성되고 업데이트 관리를 사용하도록 설정하는 경우, 다음 명령을 수행하여 omiuser 그룹 읽기 권한을 파일에 부여하도록 `proxy.conf` 권한을 업데이트합니다.
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

평가가 수행된 후 새로 추가된 Linux 에이전트의 상태가 **업데이트됨**으로 표시됩니다. 이 프로세스는 최대 6시간까지 걸릴 수 있습니다.

Operations Manager 관리 그룹이 Azure Monitor 로그와 통신하는지 확인하려면 [Azure Monitor 로그와 Operations Manager 통합 유효성 검사](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)를 참조하세요.

### <a name="single-azure-vm-assessment-queries-windows"></a>단일 Azure VM 평가 쿼리(Windows)

VMUUID 값을 쿼리 중인 가상 머신의 VM GUID로 바꿉니다. Azure Monitor 로그에서 다음 쿼리를 실행하여 사용해야 하는 VMUUID를 찾을 수 있습니다. `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>누락 업데이트 요약

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>누락 업데이트 목록

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>단일 Azure VM 평가 쿼리(Linux)

일부 Linux 배포판의 경우 Azure Resource Manager에서 가져온 VMUUID 값과 Azure Monitor 로그에 저장된 값이 [엔디언](https://en.wikipedia.org/wiki/Endianness)과 일치하지 않습니다. 다음 쿼리는 엔디언의 일치 여부를 확인합니다. 결과를 적절히 반환하려면 VMUUID 값을 GUID의 big-endian 및 little-endian 형식으로 바꿉니다. Azure Monitor 로그에서 다음 쿼리를 실행하여 사용해야 하는 VMUUID를 찾을 수 있습니다. `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>누락 업데이트 요약

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>누락 업데이트 목록

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>다중 VM 평가 쿼리

#### <a name="computers-summary"></a>컴퓨터 요약

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>누락 업데이트 요약

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>컴퓨터 목록

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>누락 업데이트 목록

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>다음 단계

* Azure Monitor 로그에 대한 자세한 내용은 [Azure Monitor 로그](../log-analytics/log-analytics-log-searches.md)를 참조하세요.
* 경고에 대한 도움말은 [경고 구성](automation-tutorial-update-management.md#configure-alerts)을 참조하세요.
