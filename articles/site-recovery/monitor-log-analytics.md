---
title: Azure 모니터 로그를 사용 하 고 Azure 사이트 복구 모니터링
description: Azure 모니터 로그(로그 분석)를 사용하여 Azure 사이트 복구를 모니터링하는 방법 알아보기
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133424"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 Site Recovery 모니터링

이 문서에서는 Azure [사이트 복구,](site-recovery-overview.md)Azure 모니터 로그 및 [로그 분석을](../azure-monitor/log-query/log-query-overview.md)사용하여 복제된 컴퓨터를 모니터링하는 방법에 대해 [설명합니다.](../azure-monitor/platform/data-platform-logs.md)

Azure Monitor Logs는 다른 모니터링 데이터와 함께 활동 및 진단 로그를 수집하는 로그 데이터 플랫폼을 제공합니다. Azure Monitor Logs 내에서 로그 분석을 사용하여 로그 쿼리를 작성 및 테스트하고 로그 데이터를 대화형으로 분석합니다. 로그 결과를 시각화및 쿼리하고 모니터링된 데이터를 기반으로 작업을 수행하도록 경고를 구성할 수 있습니다.

사이트 복구의 경우 Azure 모니터 로그를 사용하여 다음을 수행할 수 있습니다.

- **사이트 복구 상태 및 상태를 모니터링합니다.** 예를 들어 복제 상태, 테스트 장애 조치 상태, 사이트 복구 이벤트, 보호된 컴퓨터에 대한 RPO(복구 지점 목표) 및 디스크/데이터 변경 률을 모니터링할 수 있습니다.
- **사이트 복구에 대한 경고를 설정합니다.** 예를 들어 컴퓨터 상태, 테스트 장애 조치 상태 또는 사이트 복구 작업 상태에 대한 경고를 구성할 수 있습니다.

사이트 복구와 함께 Azure 모니터 로그를 사용 하 여 **Azure에서 Azure** 복제및 **VM웨어 VM/물리적 서버에서 Azure 복제에** 대해 지원됩니다.

> [!NOTE]
> 이탈 데이터 로그를 얻고 VMware 및 실제 컴퓨터에 대한 속도 로그를 업로드하려면 프로세스 서버에 Microsoft 모니터링 에이전트를 설치해야 합니다. 이 에이전트는 복제 컴퓨터의 로그를 작업 공간으로 보냅니다. 이 기능은 9.30 이동성 에이전트 버전 이후에서만 사용할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

다음 항목이 필요합니다.

- 복구 서비스 자격 증명 모음에서 보호된 하나 이상의 컴퓨터입니다.
- 사이트 복구 로그를 저장하는 로그 분석 작업 영역입니다. 작업 영역 [설정에 대해 자세히 알아보세요.](../azure-monitor/learn/quick-create-workspace.md)
- 로그 애널리틱스에서 로그 쿼리를 작성, 실행 및 분석하는 방법에 대한 기본적인 이해입니다. [자세히 알아봅니다](../azure-monitor/log-query/get-started-portal.md).

시작하기 전에 [일반적인 모니터링 질문을](monitoring-common-questions.md) 검토하는 것이 좋습니다.

## <a name="configure-site-recovery-to-send-logs"></a>로그를 보낼 사이트 복구 구성

1. 볼트에서 진단 **설정** > **추가 진단 설정 추가를**클릭합니다.

    ![진단 로깅 선택](./media/monitoring-log-analytics/add-diagnostic.png)

2. **진단 설정에서**이름을 지정하고 **로그 분석으로 보내기 확인란을**선택합니다.
3. Azure 모니터 로그 구독 및 로그 분석 작업 영역을 선택합니다.
4. 토글에서 **Azure 진단을 선택합니다.**
5. 로그 목록에서 **접두사 AzureSiteRecovery**를 사용 하 고 모든 로그를 선택 합니다. 그런 다음 **확인**을 클릭합니다.

    ![작업 영역 선택](./media/monitoring-log-analytics/select-workspace.png)

사이트 복구 로그는 선택한 작업 영역에서**테이블(AzureDiagnostics)에**피드를 시작합니다.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>프로세스 서버에서 Microsoft 모니터링 에이전트를 구성하여 변동 및 업로드 율 로그를 보냅니다.

온-프레미스에서 VMware/물리적 컴퓨터에 대한 데이터 이탈률 정보 및 소스 데이터 업로드 속도 정보를 캡처할 수 있습니다. 이를 사용하려면 프로세스 서버에 Microsoft 모니터링 에이전트를 설치해야 합니다.

1. 로그 분석 작업 영역으로 이동하여 **고급 설정을**클릭합니다.
2. 연결된 **소스** 페이지를 클릭하고 **Windows 서버를 추가로 선택합니다.**
3. 프로세스 서버에서 Windows 에이전트(64비트)를 다운로드합니다. 
4. [작업 영역 ID 및 키 얻기](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [TLS 1.2를 사용하도록 에이전트 구성](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. 가져온 작업 영역 ID와 키를 제공하여 [에이전트 설치를 완료합니다.](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)
7. 설치가 완료되면 로그 분석 작업 영역으로 이동하여 **고급 설정을**클릭합니다. **데이터** 페이지로 이동하여 **Windows 성능 카운터를 클릭합니다.** 
8. **'+'를** 클릭하여 샘플 간격이 300초인 다음 두 개의 카운터를 추가합니다.

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

이탈 및 업로드 속도 데이터는 작업 영역에 공급하기 시작합니다.


## <a name="query-the-logs---examples"></a>로그 쿼리 - 예제

[Kusto 쿼리 언어로](../azure-monitor/log-query/get-started-queries.md)작성된 로그 쿼리를 사용하여 로그에서 데이터를 검색합니다. 이 섹션에서는 사이트 복구 모니터링에 사용할 수 있는 일반적인 쿼리의 몇 가지 예를 제공합니다.

> [!NOTE]
> 일부 예제는 **replicationProviderName_s A2A로** **설정합니다.** 이렇게 하면 사이트 복구를 사용하여 보조 Azure 지역으로 복제되는 Azure VM을 검색합니다. 이 예제에서는 사이트 복구를 사용하여 Azure에 복제된 온-프레미스 VM 또는 물리적 서버를 검색하려는 경우 **A2A를** **InMageAzureV2로**바꿀 수 있습니다.


### <a name="query-replication-health"></a>쿼리 복제 상태

이 쿼리는 보호된 모든 Azure VM의 현재 복제 상태에 대한 원형 차트를 정상, 경고 또는 중요의 세 가지 상태로 구분합니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>쿼리 이동성 서비스 버전

이 쿼리는 사이트 복구와 함께 복제된 Azure VM에 대한 원형 차트를 플로팅하며, 실행 중인 Mobility 에이전트 버전으로 세분화됩니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>RPO 쿼리 시간

이 쿼리는 RPO(복구 지점 목표)별로 세분화된 사이트 복구와 함께 복제된 Azure VM의 막대 차트를 플로팅합니다.

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

### <a name="query-site-recovery-jobs"></a>쿼리 사이트 복구 작업

이 쿼리는 지난 72시간 동안 트리거된 모든 사이트 복구 작업(모든 재해 복구 시나리오)과 완료 상태를 검색합니다.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>사이트 복구 이벤트 쿼리

이 쿼리는 심각도와 함께 지난 72시간 동안 발생한 모든 사이트 복구 이벤트(모든 재해 복구 시나리오)를 검색합니다. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>쿼리 테스트 장애 조치 상태(원형 차트)

이 쿼리는 사이트 복구와 함께 복제된 Azure VM의 테스트 장애 조치 상태에 대한 원형 차트를 플로팅합니다.

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

### <a name="query-test-failover-state-table"></a>쿼리 테스트 장애 조치 상태(테이블)

이 쿼리는 사이트 복구와 함께 복제된 Azure VM의 테스트 장애 조치 상태에 대한 테이블을 플로팅합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>쿼리 컴퓨터 RPO

이 쿼리는 지난 72시간 동안 특정 Azure VM(ContosoVM123)의 RPO를 추적하는 추세 그래프를 플로팅합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![쿼리 컴퓨터 RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Azure VM에 대한 데이터 변경률(이탈) 및 업로드 속도 쿼리

이 쿼리는 데이터 변경 속도(초당 바이트 쓰기) 및 데이터 업로드 속도를 나타내는 특정 Azure VM(ContosoVM123)에 대한 추세 그래프를 플로팅합니다. 

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

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>VMware 또는 실제 컴퓨터에 대한 데이터 변경률(변동) 및 업로드 속도 쿼리

> [!Note]
> 이러한 로그를 가져오도록 프로세스 서버에서 모니터링 에이전트를 설정해야 합니다. [단계를 참조하여 모니터링 에이전트를 구성합니다.](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)

이 쿼리는 데이터 변경 률(초당 바이트 쓰기) 및 데이터 업로드 속도를 나타내는 복제된 항목 **win-9r7sfh9qlru의**특정 디스크 **디스크0에** 대한 추세 그래프를 플로팅합니다. 복구 서비스 자격 증명 모음에서 복제된 항목의 디스크 블레이드에서 디스크 이름을 찾을 수 **있습니다.** 쿼리에 사용할 인스턴스 이름은 이 예제와 같이 _및 디스크 이름이 뒤에 있는 컴퓨터의 DNS 이름입니다.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
프로세스 서버는 5분마다 이 데이터를 로그 분석 작업 영역으로 푸시합니다. 이러한 데이터 포인트는 5분 동안 계산된 평균을 나타냅니다.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>쿼리 재해 복구 요약(Azure에서 Azure로)

이 쿼리는 보조 Azure 지역으로 복제된 Azure VM에 대한 요약 테이블을 플로팅합니다.  VM 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치 상태, 이동성 에이전트 버전, 활성 복제 오류 및 원본 위치를 표시합니다.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>쿼리 재해 복구 요약(VMware/물리적 서버)

이 쿼리는 Azure에 복제된 VM웨어 VM 및 실제 서버에 대한 요약 테이블을 플로팅합니다.  컴퓨터 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치 상태, 이동성 에이전트 버전, 활성 복제 오류 및 관련 프로세스 서버가 표시됩니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>경고 설정 - 예제

Azure 모니터 데이터를 기반으로 사이트 복구 경고를 설정할 수 있습니다. 로그 경고 설정에 대해 [자세히 알아보세요.](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) 

> [!NOTE]
> 일부 예제는 **replicationProviderName_s A2A로** **설정합니다.** 이렇게 하면 보조 Azure 지역으로 복제되는 Azure VM에 대한 경고가 설정됩니다. 이러한 예에서 Azure에 복제된 온-프레미스 VM웨어 VM 또는 물리적 서버에 대한 경고를 설정하려는 경우 **A2A를** **InMageAzureV2로** 바꿀 수 있습니다.

### <a name="multiple-machines-in-a-critical-state"></a>임계 상태의 여러 컴퓨터

20개 이상의 복제된 Azure VM이 위험 상태로 전환되면 경고를 설정합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
경고의 경우 **임계값을** 20으로 설정합니다.

### <a name="single-machine-in-a-critical-state"></a>임계 상태의 단일 컴퓨터

복제된 특정 Azure VM이 위험 상태로 전환되는 경우 경고를 설정합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고의 경우 **임계값을** 1로 설정합니다.

### <a name="multiple-machines-exceed-rpo"></a>여러 컴퓨터가 RPO를 초과합니다.

20개 이상의 Azure VM에 대한 RPO가 30분을 초과하는 경우 경고를 설정합니다.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
경고의 경우 **임계값을** 20으로 설정합니다.

### <a name="single-machine-exceeds-rpo"></a>단일 컴퓨터가 RPO를 초과합니다.

단일 Azure VM에 대한 RPO가 30분을 초과하는 경우 경고를 설정합니다.

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
경고의 경우 **임계값을** 1로 설정합니다.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>여러 컴퓨터에 대한 테스트 장애 조치(failover)가 90일을 초과했습니다.

20개 이상의 VM에 대해 마지막으로 성공한 테스트 장애 조치(failover)가 90일을 초과하는 경우 경고를 설정합니다. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고의 경우 **임계값을** 20으로 설정합니다.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>단일 컴퓨터에 대한 테스트 장애 조치(failover)가 90일을 초과했습니다.

특정 VM에 대한 마지막 테스트 장애 조치(failover)가 90일 이상 전에 실패한 경우 경고를 설정합니다.
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
경고의 경우 **임계값을** 1로 설정합니다.

### <a name="site-recovery-job-fails"></a>사이트 복구 작업이 실패했습니다.

마지막 날 동안 모든 사이트 복구 시나리오에 대해 사이트 복구 작업(이 경우 다시 보호 작업)이 실패하면 경고를 설정합니다. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

경고의 경우 **임계값을** 1로 설정하고 **기간을** 1440분으로 설정하여 마지막 날의 오류를 확인합니다.

## <a name="next-steps"></a>다음 단계

붙장된 사이트 복구 [모니터링에 대해 자세히 알아봅니다.](site-recovery-monitor-and-troubleshoot.md)
