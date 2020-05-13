---
title: Windows 가상 데스크톱 진단 log analytics-Azure
description: Windows 가상 데스크톱 진단 기능을 사용 하 여 log analytics를 사용 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76a5e12eee7a325a73b3c17dba6c775b6984b89a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195917"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>진단 기능에 Log Analytics 사용

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows 가상 데스크톱은 다른 여러 Azure 서비스와 같은 모니터링 및 경고에 [Azure Monitor](../azure-monitor/overview.md) 를 사용 합니다. 이를 통해 관리자는 단일 인터페이스를 통해 문제를 식별할 수 있습니다. 서비스는 사용자 및 관리 작업 모두에 대 한 활동 로그를 만듭니다. 각 활동 로그는 다음과 같은 범주에 속합니다.  

- 관리 작업:
    - Api 또는 PowerShell을 사용 하 여 Windows 가상 데스크톱 개체를 변경 하려는 시도가 성공 했는지 여부를 추적 합니다. 예를 들어 사용자가 PowerShell을 사용 하 여 호스트 풀을 성공적으로 만들 수 있나요?
- 방향 
    - 사용자가 작업 영역을 성공적으로 구독할 수 있나요? 
    - 사용자가 원격 데스크톱 클라이언트에 게시 된 모든 리소스를 볼 수 있나요?
- 연결: 
    - 사용자가 서비스에 대 한 연결을 시작 하 고 완료 하는 경우 
- 호스트 등록: 
    - 세션 호스트가 연결 시 서비스에 성공적으로 등록 되었습니까?
- Errors: 
    - 사용자에 게 특정 작업에 대 한 문제가 발생 하나요? 이 기능은 정보를 활동과 조인 하는 한 활동 데이터를 추적 하는 테이블을 생성할 수 있습니다.
- 검사점  
    - 작업 수명의 특정 단계에 도달 했습니다. 예를 들어, 세션 중에 사용자가 특정 호스트로 부하가 분산 된 다음 연결 중에 사용자가 로그온 했습니다.

진단 역할 서비스 자체는 Windows 가상 데스크톱의 일부 이기 때문에 Windows 가상 데스크톱에 연결 되지 않은 연결은 진단 결과에 표시 되지 않습니다. 사용자에 게 네트워크 연결 문제가 발생 하는 경우 Windows 가상 데스크톱 연결 문제가 발생할 수 있습니다.

Azure Monitor를 사용 하 여 Windows 가상 데스크톱 데이터를 분석 하 고 동일한 도구 내에서 VM (가상 컴퓨터) 성능 카운터를 검토할 수 있습니다. 이 문서에서는 Windows 가상 데스크톱 환경에 대해 진단을 사용 하도록 설정 하는 방법에 대해 자세히 설명 합니다. 

>[!NOTE] 
>Azure에서 Vm을 모니터링 하는 방법에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 azure virtual Machines 모니터링](../azure-monitor/insights/monitor-vm-azure.md)을 참조 하세요. 또한 세션 호스트에서 사용자 환경을 보다 잘 이해 하려면 [성능 카운터 임계값을 검토](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) 해야 합니다.

## <a name="before-you-get-started"></a>시작하기 전에

Log Analytics를 사용 하려면 먼저 작업 영역을 만들어야 합니다. 이렇게 하려면 다음 두 문서 중 하나에 있는 지침을 따르세요.

- Azure Portal를 사용 하는 것을 선호 하 [는 경우 Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)를 참조 하세요.
- PowerShell을 선호 하는 경우 [powershell을 사용 하 여 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace-posh.md)를 참조 하세요.

작업 영역을 만든 후에는 [Azure Monitor에 Windows 컴퓨터 연결](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) 의 지침에 따라 다음 정보를 가져옵니다.

- 작업 영역 ID
- 작업 영역의 기본 키

이 정보는 나중에 설치 프로세스에서 필요 합니다.

Windows 가상 데스크톱 환경을 모니터링 하 고 유지 관리 하는 사용자에 대 한 데이터 액세스를 사용 하도록 설정 하려면 Azure Monitor에 대 한 권한 관리를 검토 해야 합니다. 자세한 내용은 Azure Monitor를 사용 하 여 [역할, 권한 및 보안 시작](../azure-monitor/platform/roles-permissions-security.md)을 참조 하세요. 

## <a name="push-diagnostics-data-to-your-workspace"></a>작업 영역에 진단 데이터 푸시

Windows 가상 데스크톱 개체의 진단 데이터를 작업 영역에 대 한 Log Analytics로 푸시할 수 있습니다. 개체를 처음 만들 때 바로이 기능을 설정할 수 있습니다.

새 개체에 대 한 Log Analytics를 설정 하려면 다음을 수행 합니다.

1. Azure Portal에 로그인 하 고 **Windows 가상 데스크톱**으로 이동 합니다. 

2. 로그 및 이벤트를 캡처할 개체 (예: 호스트 풀, 앱 그룹 또는 작업 영역)로 이동 합니다. 

3. 화면 왼쪽의 메뉴에서 **진단 설정** 을 선택 합니다. 

4. 화면 오른쪽에 표시 되는 메뉴에서 **진단 설정 추가** 를 선택 합니다. 
   
    진단 설정 페이지에 표시 되는 옵션은 편집 하는 개체의 종류에 따라 달라 집니다.

    예를 들어 응용 프로그램 그룹에 대 한 진단을 사용 하도록 설정 하는 경우 검사점, 오류 및 관리를 구성 하는 옵션이 표시 됩니다. 작업 영역의 경우 이러한 범주는 사용자가 앱 목록을 구독할 때 추적할 피드를 구성 합니다. 진단 설정에 대해 자세히 알아보려면 [Azure에서 리소스 로그 및 메트릭을 수집 하는 진단 설정 만들기를](../azure-monitor/platform/diagnostic-settings.md)참조 하세요. 

     >[!IMPORTANT] 
     >모니터링할 각 Azure Resource Manager 개체에 대해 진단을 사용 하도록 설정 해야 합니다. 진단이 사용 하도록 설정 된 후에는 작업에 대 한 데이터를 사용할 수 있습니다. 처음 설정한 후 몇 시간이 걸릴 수 있습니다.  

5. 설정 구성의 이름을 입력 한 다음 **Log Analytics 보내기를**선택 합니다. 사용할 이름에는 공백이 없어야 하며 [Azure 명명 규칙](../azure-resource-manager/management/resource-name-rules.md)을 준수 해야 합니다. 로그의 일부로 Log Analytics에 추가 하려는 모든 옵션 (예: 검사점, 오류, 관리 등)을 선택할 수 있습니다.

6. **저장**을 선택합니다.

>[!NOTE]
>Log Analytics은 [Event Hubs](../event-hubs/event-hubs-about.md) 데이터를 스트리밍하는 옵션을 제공 하 여 저장소 계정에 보관 합니다. 이 기능에 대 한 자세한 내용은 [azure 모니터링 데이터를 이벤트 허브로 스트리밍](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) 및 [저장소 계정에 Azure 리소스 로그 보관](../azure-monitor/platform/resource-logs-collect-storage.md)을 참조 하세요. 

## <a name="how-to-access-log-analytics"></a>Log Analytics에 액세스 하는 방법

Azure Portal 또는 Azure Monitor에서 Log Analytics 작업 영역에 액세스할 수 있습니다.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 Log Analytics 액세스

1. Azure Portal에 로그인합니다.

2. **Log Analytics 작업 영역**을 검색 합니다. 

3. 서비스에서 **Log Analytics 작업 영역**을 선택 합니다. 
   
4. 목록에서 Windows 가상 데스크톱 개체에 대해 구성한 작업 영역을 선택 합니다.

5. 작업 영역에서 **로그**를 선택 합니다. **검색** 함수를 사용 하 여 메뉴 목록을 필터링 할 수 있습니다. 

### <a name="access-log-analytics-on-azure-monitor"></a>Azure Monitor에서 Log Analytics 액세스

1. Azure Portal에 로그인합니다.

2. **모니터**를 검색 하 고 선택 합니다. 

3. **로그**를 선택 합니다.

4. 로깅 페이지의 지침에 따라 쿼리의 범위를 설정 합니다.  

5. 진단을 쿼리할 준비가 되었습니다. 모든 진단 테이블에는 "WVD" 접두사가 있습니다.

## <a name="cadence-for-sending-diagnostic-events"></a>진단 이벤트를 보내기 위한 흐름

완료 되 면 진단 이벤트가 Log Analytics 전송 됩니다.

연결 작업에 대 한 이러한 중간 상태의 보고서만 Log Analytics 합니다.

- 시작 됨: 사용자가 원격 데스크톱 클라이언트에서 앱 또는 데스크톱을 선택 하 고 연결 하는 경우
- 연결 됨: 사용자가 앱 또는 데스크톱이 호스트 되는 VM에 성공적으로 연결 된 경우
- 완료 됨: 사용자 또는 서버에서 활동이 발생 한 세션의 연결을 끊은 경우

## <a name="example-queries"></a>쿼리 예

다음 예제 쿼리는 진단 기능이 시스템에서 가장 자주 수행 되는 작업에 대 한 보고서를 생성 하는 방법을 보여 줍니다.

사용자가 만든 연결 목록을 가져오려면 다음 cmdlet을 실행 합니다.

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

사용자의 피드 활동을 보려면 다음을 수행 합니다.

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

단일 사용자에 대 한 모든 연결을 찾으려면 다음을 수행 합니다. 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

하루에 연결 된 사용자 수를 찾으려면 다음을 수행 합니다.

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

사용자가 세션 기간을 찾으려면:

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

특정 사용자에 대 한 오류를 찾으려면 다음을 수행 합니다.

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

특정 오류가 발생 했는지 여부를 확인 하려면 다음을 수행 합니다.

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

모든 사용자에 게 오류 발생을 찾으려면:

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>문제 해결을 위한 가장 중요 한 테이블은 WVDErrors입니다. 이 쿼리를 사용 하 여 사용자가 앱 또는 데스크톱 목록을 구독할 때 연결 또는 피드와 같은 사용자 활동에 대해 발생 하는 문제를 파악할 수 있습니다. 테이블에는 관리 오류 뿐만 아니라 호스트 등록 문제도 표시 됩니다.
>
>공개 미리 보기 중에 문제를 해결 하는 데 도움이 필요한 경우 도움말 요청에서 해당 오류에 대 한 CorrelationID를 제공 해야 합니다. 또한 서비스 오류 값에 항상 ServiceError = "false"가 표시 되는지 확인 합니다. "False" 값은 끝의 관리 작업으로 문제를 해결할 수 있음을 의미 합니다. ServiceError = "true" 인 경우 문제를 Microsoft로 에스컬레이션 해야 합니다.

## <a name="next-steps"></a>다음 단계 

진단 기능에서 식별할 수 있는 일반적인 오류 시나리오를 검토 하려면 [문제 식별 및 진단](diagnostics-role-service.md#common-error-scenarios)을 참조 하세요.
