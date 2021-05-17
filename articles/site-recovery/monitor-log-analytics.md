---
title: Azure Monitor 로그를 사용하여 Azure Site Recovery 모니터링
description: Azure Monitor 로그(Log Analytics)를 사용하여 Azure Site Recovery를 모니터링하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 308e1bcf042feb15179d32844d8c569af6166619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571684"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 Site Recovery 모니터링

이 문서에서는 [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md)와 [Log Analytics](../azure-monitor/logs/log-query-overview.md)를 사용하여 Azure [Site Recovery](site-recovery-overview.md)가 복제한 컴퓨터를 모니터링하는 방법을 설명합니다.

Azure Monitor 로그는 활동 및 리소스 로그와 함께 기타 모니터링 데이터를 수집하는 로그 데이터 플랫폼을 제공합니다. Azure Monitor 로그 내에서 Log Analytics를 사용하여 로그 쿼리를 작성 및 테스트하고, 로그 데이터를 대화형으로 분석할 수 있습니다. 로그 결과를 시각화하고 쿼리할 수 있으며, 모니터링되는 데이터에 따라 작업을 수행하도록 경고를 구성할 수 있습니다.

Site Recovery의 경우 Azure Monitor 로그를 사용하여 다음 작업을 수행할 수 있습니다.

- **Site Recovery 상태를 모니터링** 합니다. 예를 들어 복제 상태, 테스트 장애 조치(failover) 상태, Site Recovery 이벤트, 보호된 컴퓨터 RPO(복구 지점 목표) 및 디스크/데이터 변경 비율을 모니터링할 수 있습니다.
- **Site Recovery를 위한 경고를 설정** 합니다. 예를 들어 컴퓨터 상태, 테스트 장애 조치(failover) 상태 또는 Site Recovery 작업 상태에 대한 경고를 구성할 수 있습니다.

**Azure에서 Azure로의** 복제 및 **VMware VM/물리적 서버에서 Azure로의** 복제에서 Azure Monitor 로그를 Site Recovery에 사용할 수 있습니다.

> [!NOTE]
> VMware 및 물리적 컴퓨터의 변동 데이터 로그 및 업로드 속도 로그를 가져오려면 프로세스 서버에 Microsoft 모니터링 에이전트를 설치해야 합니다. 이 에이전트는 복제하는 컴퓨터의 로그를 작업 영역으로 보냅니다. 이 기능은 9.30 모바일 에이전트 버전 이상에서만 사용할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

다음 항목이 필요합니다.

- Recovery Services 자격 증명 모음에서 보호되는 하나 이상의 컴퓨터.
- Site Recovery 로그를 저장할 Log Analytics 작업 영역. 작업 영역을 설정하는 방법에 대해 [알아보세요](../azure-monitor/logs/quick-create-workspace.md).
- Log Analytics에서 로그 쿼리를 작성, 실행, 분석하는 방법에 대한 기본적인 이해. [자세히 알아봅니다](../azure-monitor/logs/log-analytics-tutorial.md).

시작하기 전에 [일반적인 모니터링 질문](monitoring-common-questions.md)을 검토하는 것이 좋습니다.

## <a name="configure-site-recovery-to-send-logs"></a>로그를 보내도록 Site Recovery 구성

1. 자격 증명 모음에서 **진단 설정** > **진단 설정 추가** 를 클릭합니다.

    ![진단 설정 추가 옵션을 보여 주는 스크린샷.](./media/monitoring-log-analytics/add-diagnostic.png)

2. **진단 설정** 에서 이름을 지정하고 **Log Analytics로 보내기** 상자를 선택합니다.
3. Azure Monitor 로그 구독과 Log Analytics 작업 영역을 선택합니다.
4. 토글에서 **Azure Diagnostics** 를 선택합니다.
5. 로그 목록에서 접두사가 **AzureSiteRecovery** 인 모든 로그를 선택합니다. 그런 후 **OK** 를 클릭합니다.

    ![Diagnostics 설정 페이지의 스크린샷.](./media/monitoring-log-analytics/select-workspace.png)

선택한 작업 영역의 **AzureDiagnostics** 테이블에 Site Recovery 로그가 피드되기 시작합니다.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>변동률 및 업로드 속도 로그를 보내도록 프로세스 서버에서 Microsoft 모니터링 에이전트 구성

온-프레미스에서 VMware/물리적 컴퓨터의 데이터 변동률 정보 및 원본 데이터 업로드 속도 정보를 캡처할 수 있습니다. 이를 사용하려면 프로세스 서버에 Microsoft 모니터링 에이전트를 설치해야 합니다.

1. Log Analytics로 이동하여 **고급 설정** 을 클릭합니다.
2. **연결된 원본** 페이지를 클릭하고 **Windows 서버** 를 추가로 선택합니다.
3. 프로세스 서버에서 Windows 에이전트(64비트)를 다운로드합니다. 
4. [작업 영역 ID 및 키 가져오기](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)
5. [TLS 1.2를 사용하도록 에이전트 구성](../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12)
6. 가져온 작업 영역 ID 및 키를 제공하여 [에이전트 설치를 완료](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)합니다.
7. 설치가 완료되면 Log Analytics 작업 영역으로 이동한 다음 **고급 설정** 을 클릭합니다. **데이터** 페이지로 이동하여 **Windows 성능 카운터** 를 추가로 클릭합니다. 
8. **' + '** 를 클릭하여 샘플 간격이 300초인 다음 두 카운터를 추가합니다.

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

변동률 및 업로드 속도 데이터가 작업 영역으로 피드되기 시작합니다.


## <a name="query-the-logs---examples"></a>로그 쿼리 - 예제

[Kusto 쿼리 언어](../azure-monitor/logs/get-started-queries.md)로 작성된 로그 쿼리를 사용하여 로그에서 데이터를 검색합니다. 이 섹션에서는 Site Recovery 모니터링에 사용할 수 있는 일반적인 쿼리의 몇 가지 예제를 제공합니다.

> [!NOTE]
> 일부 예제에서는 **A2A** 로 설정된 **replicationProviderName_s** 를 사용합니다. 이 쿼리는 Site Recovery를 사용하여 보조 Azure 지역에 복제되는 Azure VM을 검색합니다. 이 예제에서 Site Recovery를 사용하여 Azure에 복제되는 온-프레미스 VMware VM 또는 물리적 서버를 검색하려면 **A2A** 를 **InMageAzureV2** 로 바꾸면 됩니다.


### <a name="query-replication-health"></a>복제 상태 쿼리

이 쿼리는 모든 보호된 Azure VM의 현재 복제 상태를 표준, 경고 또는 위험의 세 가지 상태로 나눈 원형 차트를 그립니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>모바일 서비스 버전 쿼리

이 쿼리는 Site Recovery를 사용하여 복제된 Azure VM을 VM이 실행 중인 모바일 에이전트의 버전으로 나눈 원형 차트를 그립니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>RPO 시간 쿼리

이 쿼리는 Site Recovery를 사용하여 복제된 Azure VM을 RPO(복구 지점 목표)에 따라 15분 미만, 15~30분, 30분 이상으로 나눈 막대형 차트를 그립니다.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Site Recovery를 사용하여 복제된 Azure VM의 막대형 차트를 보여 주는 스크린샷.](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Site Recovery 작업 쿼리

이 쿼리는 지난 72시간 동안 트리거된 모든 Site Recovery 작업(모든 재해 복구 시나리오) 및 완료 상태를 검색합니다.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Site Recovery 이벤트 쿼리

이 쿼리는 지난 72시간 동안 발생한 모든 Site Recovery 이벤트(모든 재해 복구 시나리오)를 해당 심각도와 함께 검색합니다. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>테스트 장애 조치(failover) 상태 쿼리(원형 차트)

이 쿼리는 Site Recovery를 사용하여 복제된 Azure VM의 테스트 장애 조치(failover) 상태에 대한 원형 차트를 그립니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>테스트 장애 조치(failover) 상태 쿼리(테이블)

이 쿼리는 Site Recovery를 사용하여 복제된 Azure VM의 테스트 장애 조치(failover) 상태에 대한 테이블을 그립니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>컴퓨터 RPO 쿼리

이 쿼리는 지난 72시간 동안 특정 Azure VM(ContosoVM123)의 RPO를 추적하는 추세 그래프를 그립니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![특정 Azure VM의 RPO를 추적하는 추세 그래프의 스크린샷.](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Azure VM의 데이터 변경률(변동) 및 업로드 속도 쿼리

이 쿼리는 특정 Azure VM(ContosoVM123)의 데이터 변경률(초당 쓰기 바이트) 및 데이터 업로드 속도를 나타내는 추세 그래프를 그립니다. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![특정 Azure VM의 추세 그래프 스크린샷.](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>VMware 또는 물리적 컴퓨터의 데이터 변경률(변동) 및 업로드 속도 쿼리

> [!Note]
> 프로세스 서버에서 이러한 로그를 가져오도록 모니터링 에이전트를 설정했는지 확인합니다. [모니터링 에이전트 구성 단계](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)를 참조하세요.

이 쿼리는 복제된 항목 **win-9r7sfh9qlru** 의 특정 디스크 **disk0** 의 데이터 변경률(초당 쓰기 바이트 수)과 데이터 업로드 속도를 나타내는 추세 그래프를 그립니다. 복구 서비스 자격 증명 모음의 복제된 항목 **디스크** 블레이드에서 디스크 이름을 찾을 수 있습니다. 쿼리에 사용할 인스턴스 이름은 다음 예제와 같이 _ 및 디스크 이름이 뒤에 오는 컴퓨터의 DNS 이름입니다.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
프로세스 서버는 이 데이터를 5분마다 Log Analytics 작업 영역에 밀어넣습니다. 이러한 데이터 요소는 5분 동안 계산된 평균을 나타냅니다.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>재해 복구 요약 쿼리(Azure에서 Azure로)

이 쿼리는 보조 Azure 지역에 복제된 Azure VM의 요약 테이블을 그립니다.  VM 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치(failover) 상태, 모바일 에이전트 버전, 모든 활성 복제 오류 및 원본 위치가 표시됩니다.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>재해 복구 요약 쿼리(VMware/물리적 서버)

이 쿼리는 Azure에 복제된 VMware VM 및 물리적 서버의 요약 테이블을 그립니다.  컴퓨터 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치(failover) 상태, 모바일 에이전트 버전, 활성 복제 오류 및 관련 프로세스 서버가 표시됩니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>경고 설정 - 예제

Azure Monitor 데이터를 기반으로 Site Recovery 경고를 설정할 수 있습니다. 로그 경고 설정에 대해 [자세히 알아보세요](../azure-monitor/alerts/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal). 

> [!NOTE]
> 일부 예제에서는 **A2A** 로 설정된 **replicationProviderName_s** 를 사용합니다. 이렇게 하면 보조 Azure 지역에 복제되는 Azure VM에 대한 경고가 설정됩니다. 이 예제에서 Azure에 복제되는 온-프레미스 VMware VM 또는 물리적 서버에 대한 경고를 설정하려면 **A2A** 를 **InMageAzureV2** 로 바꾸면 됩니다.

### <a name="multiple-machines-in-a-critical-state"></a>위험 상태의 여러 컴퓨터

20개가 넘는 복제된 Azure VM이 위험 상태로 전환되는 경우 경고를 설정합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
경고에서 **임계값** 을 20으로 설정합니다.

### <a name="single-machine-in-a-critical-state"></a>위험 상태의 단일 컴퓨터

복제된 특정 Azure VM이 위험 상태가 되는 경우 경고를 설정합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에서 **임계값** 을 1로 설정합니다.

### <a name="multiple-machines-exceed-rpo"></a>여러 컴퓨터가 RPO 초과

20개 이상의 Azure VM의 RPO가 30분을 초과하는 경우 경고를 설정합니다.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
경고에서 **임계값** 을 20으로 설정합니다.

### <a name="single-machine-exceeds-rpo"></a>단일 컴퓨터가 RPO 초과

단일 Azure VM의 RPO가 30분을 초과하는 경우 경고를 설정합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
경고에서 **임계값** 을 1로 설정합니다.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>여러 컴퓨터의 테스트 장애 조치(failover)가 90일 초과

20개 이상의 VM에서 마지막으로 성공한 테스트 장애 조치(failover)가 90일을 초과하는 경우 경고를 설정합니다. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에서 **임계값** 을 20으로 설정합니다.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>단일 컴퓨터의 테스트 장애 조치(failover)가 90일 초과

특정 VM의 마지막으로 성공한 테스트 장애 조치(failover)가 90일 이상 지난 경우 경고를 설정합니다.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에서 **임계값** 을 1로 설정합니다.

### <a name="site-recovery-job-fails"></a>Site Recovery 작업 실패

Site Recovery 작업(이 경우 다시 보호 작업)이 지난 하루 동안 모든 Site Recovery 시나리오에서 실패하는 경우 경고를 설정합니다. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

경고에서 **임계값** 을 1로 설정하고 **기간** 을 1,440분으로 설정하여 지난 하루의 실패를 확인합니다.

## <a name="next-steps"></a>다음 단계

기본 제공 Site Recovery 모니터링에 대해 [알아봅니다](site-recovery-monitor-and-troubleshoot.md).