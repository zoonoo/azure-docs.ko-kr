---
title: Azure Monitor 로그를 사용 하 여 Azure Site Recovery 모니터링 (Log Analytics)
description: Azure Monitor 로그 (Log Analytics)를 사용 하 여 Azure Site Recovery를 모니터링 하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: 4eb88658437d3b29cc55d24bb83f73b660daea43
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718484"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 Site Recovery 모니터링

이 문서에서는 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md)및 [Log Analytics](../azure-monitor/log-query/log-query-overview.md)를 사용 하 여 Azure [Site Recovery](site-recovery-overview.md)에서 복제 된 컴퓨터를 모니터링 하는 방법을 설명 합니다.

Azure Monitor 로그는 다른 모니터링 데이터와 함께 작업 및 진단 로그를 수집 하는 로그 데이터 플랫폼을 제공 합니다. Azure Monitor 로그 내에서 Log Analytics를 사용 하 여 로그 쿼리를 작성 및 테스트 하 고 로그 데이터를 대화형으로 분석할 수 있습니다. 로그 결과를 시각화 하 고 쿼리할 수 있으며 모니터링 되는 데이터에 따라 동작을 수행 하도록 경고를 구성할 수 있습니다.

Site Recovery의 경우 로그를 Azure Monitor 하 여 다음 작업을 수행할 수 있습니다.

- **Site Recovery 상태 및 상태를 모니터링**합니다. 예를 들어, 복제 상태, 테스트 장애 조치 상태, Site Recovery 이벤트, 보호 된 컴퓨터에 대 한 Rpo (복구 지점 목표) 및 디스크/데이터 변경 비율을 모니터링할 수 있습니다.
- **Site Recovery에 대 한 경고를 설정**합니다. 예를 들어 컴퓨터 상태, 테스트 장애 조치 (failover) 상태 또는 Site Recovery 작업 상태에 대 한 경고를 구성할 수 있습니다.

Site Recovery에서 Azure Monitor 로그 사용은 azure에서 Azure로 복제, VMware v m/물리적 서버에서 Azure로 복제에 대해 지원 됩니다.
## <a name="before-you-start"></a>시작하기 전에

필요한 사항은 다음과 같습니다.

- Recovery Services 자격 증명 모음에 보호 된 컴퓨터가 하나 이상 있습니다.
- Site Recovery 로그를 저장할 Log Analytics 작업 영역입니다. 작업 영역을 설정 하는 [방법에 대해 알아봅니다](../azure-monitor/learn/quick-create-workspace.md) .
- Log Analytics에서 로그 쿼리를 작성, 실행 및 분석 하는 방법에 대 한 기본적인 이해 [자세히 알아보기](../azure-monitor/log-query/get-started-portal.md).

시작 하기 전에 [일반적인 모니터링 질문](monitoring-common-questions.md) 을 검토 하는 것이 좋습니다.

## <a name="configure-site-recovery-to-send-logs"></a>로그를 보내도록 Site Recovery 구성

1. 자격 증명 모음에서 **진단 설정 진단 설정** > **추가**를 클릭 합니다.

    ![진단 로깅 선택](./media/monitoring-log-analytics/add-diagnostic.png)

2. **진단 설정**에서 로그 작업의 이름을 지정 하 고 **Log Analytics 보내기를**선택 합니다.
3. Azure Monitor 로그 구독 및 Log Analytics 작업 영역을 선택 합니다.
4. 로그 목록에서 접두사가 **AzureSiteRecovery**인 모든 로그를 선택 합니다. 그런 다음 **확인**을 클릭합니다.

    ![작업 영역 선택](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery 로그는 선택한 작업 영역에서 테이블 (**Azurediagnostics**)에 피드를 시작 합니다.


## <a name="query-the-logs---examples"></a>로그 쿼리-예제

[Kusto 쿼리 언어로](../azure-monitor/log-query/get-started-queries.md)작성 된 로그 쿼리를 사용 하 여 로그에서 데이터를 검색 합니다. 이 섹션에서는 Site Recovery 모니터링에 사용할 수 있는 일반적인 쿼리의 몇 가지 예를 제공 합니다.

> [!NOTE]
> 일부 예제에서는 **replicationProviderName_s** 를 **A2A**로 설정 합니다. Site Recovery를 사용 하 여 보조 Azure 지역에 복제 되는 Azure Vm을 검색 합니다. 이 예제에서는 Site Recovery를 사용 하 여 Azure에 복제 된 온-프레미스 VMware Vm 또는 물리적 서버를 검색 하려는 경우 **A2A** 를 **InMageAzureV2**로 바꿀 수 있습니다.


### <a name="query-replication-health"></a>복제 상태 쿼리

이 쿼리는 모든 보호 된 Azure Vm의 현재 복제 상태에 대 한 원형 차트를 플롯 하 여 세 가지 상태로 세분화 합니다. 정상, 경고 또는 위험입니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>쿼리 모바일 서비스 버전

이 쿼리는 Site Recovery으로 복제 된 Azure Vm에 대 한 원형 차트를 그립니다 .이 차트는 실행 중인 모바일 에이전트의 버전으로 분류 됩니다.

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

이 쿼리는 RPO (복구 지점 목표)에 따라 분할 된 Site Recovery으로 복제 된 Azure Vm의 가로 막대형 차트를 그립니다. 15 분 미만, 30 분 이상 15-30 분

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

![RPO 쿼리](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Site Recovery 작업 쿼리

이 쿼리는 지난 72 시간 동안 트리거된 모든 Site Recovery 작업 (모든 재해 복구 시나리오의 경우) 및 완료 상태를 검색 합니다.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>쿼리 Site Recovery 이벤트

이 쿼리는 지난 72 시간 동안 발생 한 모든 Site Recovery 이벤트 (모든 재해 복구 시나리오의 경우)를 해당 심각도와 함께 검색 합니다. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>테스트 장애 조치 상태 쿼리 (원형 차트)

이 쿼리는 Site Recovery로 복제 된 Azure Vm의 테스트 장애 조치 (failover) 상태에 대 한 원형 차트를 그립니다.

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

### <a name="query-test-failover-state-table"></a>테스트 장애 조치 (failover) 상태 쿼리 (테이블)

이 쿼리는 Site Recovery로 복제 된 Azure Vm의 테스트 장애 조치 (failover) 상태에 대 한 테이블을 플롯 합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>컴퓨터 RPO 쿼리

이 쿼리는 지난 72 시간 동안 특정 Azure VM (ContosoVM123)의 RPO를 추적 하는 추세 그래프를 그립니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![컴퓨터 RPO 쿼리](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-for-a-vm"></a>VM에 대 한 쿼리 데이터 변경 률 (변동)

이 쿼리는 데이터 변경 률 (초당 쓰기 바이트) 및 데이터 업로드 율을 추적 하는 특정 Azure VM (ContosoVM123)에 대 한 추세 그래프를 그립니다. 이 정보는 보조 Azure 지역에 복제 된 Azure Vm에만 사용할 수 있습니다.

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
![쿼리 데이터 변경](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>재해 복구 요약 쿼리 (Azure에서 Azure로)

이 쿼리는 보조 Azure 지역에 복제 된 Azure Vm에 대 한 요약 테이블을 플롯 합니다.  VM 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치 상태, 모바일 에이전트 버전, 모든 활성 복제 오류 및 원본 위치를 표시 합니다.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>재해 복구 요약 쿼리 (VMware/물리적 서버)

이 쿼리는 Azure에 복제 된 VMware Vm 및 물리적 서버에 대 한 요약 테이블을 플롯 합니다.  컴퓨터 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치 상태, 모바일 에이전트 버전, 모든 활성 복제 오류 및 관련 프로세스 서버를 표시 합니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>경고 설정-예제

Azure Monitor 데이터를 기반으로 하 Site Recovery 경고를 설정할 수 있습니다. 로그 경고 설정에 [대해 자세히 알아보세요](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) . 

> [!NOTE]
> 일부 예제에서는 **replicationProviderName_s** 를 **A2A**로 설정 합니다. 이렇게 하면 보조 Azure 지역에 복제 되는 Azure Vm에 대 한 경고가 설정 됩니다. 이 예에서는 Azure로 복제 된 온-프레미스 VMware Vm 또는 물리적 서버에 대 한 경고를 설정 하려는 경우 **A2A** 를 **InMageAzureV2** 로 바꿀 수 있습니다.

### <a name="multiple-machines-in-a-critical-state"></a>위험 상태의 여러 컴퓨터

20 개가 넘는 복제 된 Azure Vm이 위험 상태로 전환 되는 경우 경고를 설정 합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
경고에 대해 **임계값** 을 20으로 설정 합니다.

### <a name="single-machine-in-a-critical-state"></a>위험 상태의 단일 컴퓨터

특정 복제 된 Azure VM이 위험 상태가 되는 경우 경고를 설정 합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에 대해 **임계값** 을 1로 설정 합니다.

### <a name="multiple-machines-exceed-rpo"></a>여러 컴퓨터가 RPO를 초과 합니다.

20 개 이상의 Azure Vm에 대 한 RPO가 30 분을 초과 하는 경우 경고를 설정 합니다.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
경고에 대해 **임계값** 을 20으로 설정 합니다.

### <a name="single-machine-exceeds-rpo"></a>단일 컴퓨터가 RPO를 초과 합니다.

단일 Azure VM에 대 한 RPO가 30 분을 초과 하는 경우 경고를 설정 합니다.

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
경고에 대해 **임계값** 을 1로 설정 합니다.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>여러 컴퓨터의 테스트 장애 조치 (failover)가 90 일을 초과 함

20 개 이상의 Vm에 대해 마지막으로 성공한 테스트 장애 조치 (failover)가 90 일을 초과 하는 경우 경고를 설정 합니다. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에 대해 **임계값** 을 20으로 설정 합니다.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>단일 컴퓨터의 테스트 장애 조치 (failover)가 90 일을 초과 함

특정 VM에 대해 마지막으로 성공한 테스트 장애 조치 (failover)가 90 일 이전인 경우 경고를 설정 합니다.
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
경고에 대해 **임계값** 을 1로 설정 합니다.

### <a name="site-recovery-job-fails"></a>Site Recovery 작업이 실패 합니다.

Site Recovery 작업 (이 경우 다시 보호 작업)이 지난 하루 동안 Site Recovery 시나리오에 대해 실패 하는 경우 경고를 설정 합니다. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

경고의 경우 **임계값** 을 1로 설정 하 고 **Period** 를 1440 분으로 설정 하 여 마지막 날의 실패를 확인 합니다.

## <a name="next-steps"></a>다음 단계

기본 제공 Site Recovery 모니터링 [에 대해 알아봅니다](site-recovery-monitor-and-troubleshoot.md) .
