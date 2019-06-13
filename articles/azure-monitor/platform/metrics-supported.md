---
title: Azure Monitor에서 지원되는 리소스 종류별 메트릭
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: f3907cbc46bd226ef8e90d2061f2d36b2afabf8a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957161"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor에서 지원되는 메트릭

Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 다음은 현재 Azure Monitor의 메트릭 파이프라인을 통해 사용할 수 있는 모든 메트릭의 전체 목록입니다. 레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다. 아래 목록에는 통합 Azure Monitor 메트릭 파이프라인을 통해 사용할 수 있는 메트릭만 포함됩니다. 이러한 메트릭을 쿼리하고 액세스하려면 [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)을 사용하세요.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|qpu_metric|QPU|카운트|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|ServerResourceType|
|memory_metric|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|ServerResourceType|
|TotalConnectionRequests|총 연결 요청 수|카운트|평균|도착한 총 연결 요청 수입니다.|ServerResourceType|
|SuccessfullConnectionsPerSec|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|ServerResourceType|
|TotalConnectionFailures|총 연결 실패 수|카운트|평균|실패한 총 연결 시도 수입니다.|ServerResourceType|
|CurrentUserSessions|현재 사용자 세션 수|카운트|평균|현재 설정된 사용자 세션 수입니다.|ServerResourceType|
|QueryPoolBusyThreads|쿼리 풀의 사용 중인 스레드|카운트|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolJobQueueLength|명령 풀의 작업 큐 길이|카운트|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolJobQueueLength|처리 풀의 작업 큐 길이|카운트|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|ServerResourceType|
|CurrentConnections|연결: 현재 연결|카운트|평균|현재 설정된 클라이언트 연결 수입니다.|ServerResourceType|
|CleanerCurrentPrice|메모리: 클리너 현재 가격|카운트|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|ServerResourceType|
|CleanerMemoryShrinkable|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|ServerResourceType|
|CleanerMemoryNonshrinkable|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|ServerResourceType|
|MemoryUsage|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 xVelocity 엔진 메모리 제한을 초과하여 xVelocity 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|ServerResourceType|
|MemoryLimitHard|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitHigh|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitLow|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitVertiPaq|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|ServerResourceType|
|할당량|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|ServerResourceType|
|QuotaBlocked|메모리: 차단된 할당량|카운트|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|ServerResourceType|
|VertiPaqNonpaged|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|ServerResourceType|
|VertiPaqPaged|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|ServerResourceType|
|RowsReadPerSec|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|ServerResourceType|
|RowsConvertedPerSec|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|ServerResourceType|
|RowsWrittenPerSec|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|ServerResourceType|
|CommandPoolBusyThreads|스레드: 명령 풀 사용 중인 스레드|카운트|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolIdleThreads|스레드: 명령 풀 유휴 상태 스레드|카운트|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingBusyThreads|스레드: 긴 구문 분석 사용 중인 스레드|카운트|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|LongParsingIdleThreads|스레드: 긴 구문 분석 유휴 상태 스레드|카운트|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingJobQueueLength|스레드: 긴 구문 분석 작업 큐 길이|카운트|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|스레드: 처리 풀 사용 중인 I/O 작업 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|스레드: 처리 풀 사용 중인 비-I/O 스레드|카운트|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|스레드: 처리 중인 풀 I/O 작업 큐 길이|카운트|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|스레드: 처리 풀 유휴 상태 비-I/O 스레드|카운트|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolIdleThreads|스레드: 쿼리 풀 유휴 상태 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|카운트|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ShortParsingBusyThreads|스레드: 짧은 구문 분석 사용 중인 스레드|카운트|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|ShortParsingIdleThreads|스레드: 짧은 구문 분석 유휴 상태 스레드|카운트|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ShortParsingJobQueueLength|스레드: 짧은 구문 분석 작업 큐 길이|카운트|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|ServerResourceType|
|mashup_engine_qpu_metric|M 엔진 QPU|카운트|평균|매시업 엔진 프로세스별 QPU 사용량|ServerResourceType|
|mashup_engine_memory_metric|M 엔진 메모리|바이트|평균|매시업 엔진 프로세스별 메모리 사용량|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalRequests|총 게이트웨이 요청|카운트|합계|게이트웨이 요청 수|위치, 호스트 이름|
|SuccessfulRequests|성공적인 게이트웨이 요청|카운트|합계|성공적인 게이트웨이 요청 수|위치, 호스트 이름|
|UnauthorizedRequests|허가되지 않은 게이트웨이 요청|카운트|합계|허가되지 않은 게이트웨이 요청 수|위치, 호스트 이름|
|FailedRequests|실패한 게이트웨이 요청|카운트|합계|게이트웨이 요청 실패 수|위치, 호스트 이름|
|OtherRequests|기타 게이트웨이 요청|카운트|합계|기타 게이트웨이 요청 수|위치, 호스트 이름|
|기간|게이트웨이 요청의 전체 기간|밀리초|평균|게이트웨이 요청의 전체 기간(밀리초)|위치, 호스트 이름|
|용량|용량|백분율|평균|ApiManagement 서비스에 대한 사용률 메트릭|Location|
|EventHubTotalEvents|총 EventHub 이벤트|카운트|합계|이벤트 허브로 전송 하는 이벤트 수|Location|
|EventHubSuccessfulEvents|성공적인 EventHub 이벤트|카운트|합계|성공한 이벤트 허브 이벤트의 수|Location|
|EventHubTotalFailedEvents|실패 한 이벤트 허브 이벤트|카운트|합계|실패 한 이벤트 허브 이벤트의 수|Location|
|EventHubRejectedEvents|거부 된 이벤트 허브 이벤트|카운트|합계|거부 된 이벤트 허브 이벤트 수 (구성이 잘못 되었습니다. 또는 권한 없음)|Location|
|EventHubThrottledEvents|제한 된 EventHub 이벤트|카운트|합계|제한 된 EventHub 이벤트 수|Location|
|EventHubTimedoutEvents|EventHub 이벤트 시간 초과|카운트|합계|EventHub 이벤트 시간 초과 수|Location|
|EventHubDroppedEvents|삭제 EventHub 이벤트|카운트|합계|이벤트 수에 도달 하는 큐 크기 제한으로 인해 건너뜀|Location|
|EventHubTotalBytesSent|이벤트 허브 이벤트의 크기|바이트|합계|EventHub 이벤트 (바이트)에서의 총 크기|Location|
|요청|요청|카운트|합계|게이트웨이 요청|Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalJob|총 작업 수|카운트|합계|총 작업 수|Runbook, 상태|
|TotalUpdateDeploymentRuns|총 업데이트 배포 실행|카운트|합계|전체 소프트웨어 업데이트 배포 실행|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|총 업데이트 배포 컴퓨터 실행|카운트|합계|소프트웨어 업데이트 배포 실행에서 실행 되는 총 소프트웨어 업데이트 배포 컴퓨터|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CoreCount|전용된 코어 수|카운트|합계|배치 계정의 총 전용 코어 수|차원 없음|
|TotalNodeCount|전용된 노드 수|카운트|합계|배치 계정의 총 전용 노드 수|차원 없음|
|LowPriorityCoreCount|LowPriority 코어 수|카운트|합계|배치 계정의 우선 순위가 낮은 총 코어 수|차원 없음|
|TotalLowPriorityNodeCount|우선 순위가 낮은 노드 수|카운트|합계|배치 계정의 우선 순위가 낮은 총 노드 수|차원 없음|
|CreatingNodeCount|노드 수 만들기|카운트|합계|만든 노드 수|차원 없음|
|StartingNodeCount|시작 노드 수|카운트|합계|시작 노드 수|차원 없음|
|WaitingForStartTaskNodeCount|작업 시작 대기 노드 수|카운트|합계|시작 작업 완료를 기다리는 노드 수|차원 없음|
|StartTaskFailedNodeCount|시작 작업 실패 노드 수|카운트|합계|시작 작업이 실패한 노드 수|차원 없음|
|IdleNodeCount|유휴 상태인 노드 수|카운트|합계|유휴 노드 수|차원 없음|
|OfflineNodeCount|오프라인 노드 수|카운트|합계|오프라인 노드 수|차원 없음|
|RebootingNodeCount|재부팅 노드 수|카운트|합계|다시 부팅하는 노드의 수|차원 없음|
|ReimagingNodeCount|이미지로 다시 설치하는 노드 수|카운트|합계|이미지로 다시 설치하는 노드의 수|차원 없음|
|RunningNodeCount|실행 노드 수|카운트|합계|실행 중인 노드의 수|차원 없음|
|LeavingPoolNodeCount|나가는 풀 노드 수|카운트|합계|풀을 나가는 노드 수|차원 없음|
|UnusableNodeCount|사용 불가 노드 수|카운트|합계|사용할 수 없는 노드 수|차원 없음|
|PreemptedNodeCount|선점된 노드 수|카운트|합계|선점된 노드 수|차원 없음|
|TaskStartEvent|작업 시작 이벤트|카운트|합계|시작된 총 작업 수|차원 없음|
|TaskCompleteEvent|작업 완료 이벤트|카운트|합계|완료된 총 작업 수|차원 없음|
|TaskFailEvent|작업 실패 이벤트|카운트|합계|실패한 상태로 완료된 총 작업 수|차원 없음|
|PoolCreateEvent|풀 만들기 이벤트|카운트|합계|만든 총 풀 수|차원 없음|
|PoolResizeStartEvent|풀 크기 조정 시작 이벤트|카운트|합계|시작된 총 풀 크기 조정 작업 수|차원 없음|
|PoolResizeCompleteEvent|풀 크기 조정 완료 이벤트|카운트|합계|완료된 총 풀 크기 조정 수|차원 없음|
|PoolDeleteStartEvent|풀 삭제 시작 이벤트|카운트|합계|시작된 총 풀 삭제 수|차원 없음|
|PoolDeleteCompleteEvent|풀 삭제 완료 이벤트|카운트|합계|완료된 총 풀 삭제 수|차원 없음|
|JobDeleteCompleteEvent|작업 삭제 완료 이벤트|카운트|합계|정상적으로 삭제된 총 작업 수입니다.|차원 없음|
|JobDeleteStartEvent|작업 삭제 시작 이벤트|카운트|합계|삭제되도록 요청된 총 작업 수입니다.|차원 없음|
|JobDisableCompleteEvent|작업 비활성화 완료 이벤트|카운트|합계|정상적으로 사용하지 않도록 설정된 총 작업 수입니다.|차원 없음|
|JobDisableStartEvent|작업 비활성화 시작 이벤트|카운트|합계|비활성화되도록 요청된 총 작업 수입니다.|차원 없음|
|JobStartEvent|작업 시작 이벤트|카운트|합계|정상적으로 시작된 총 작업 수입니다.|차원 없음|
|JobTerminateCompleteEvent|작업 종료 완료 이벤트|카운트|합계|정상적으로 종료된 총 작업 수입니다.|차원 없음|
|JobTerminateStartEvent|작업 종료 시작 이벤트|카운트|합계|종료되도록 요청된 총 작업 수입니다.|차원 없음|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|connectedclients|연결된 클라이언트|카운트|최대||ShardId|
|totalcommandsprocessed|총 작업|카운트|합계||ShardId|
|cachehits|캐시 적중|카운트|합계||ShardId|
|cachemisses|캐시 누락|카운트|합계||ShardId|
|getcommands|가져오기|카운트|합계||ShardId|
|setcommands|설정|카운트|합계||ShardId|
|operationsPerSecond|초당 작업|카운트|최대||ShardId|
|evictedkeys|제거된 키|카운트|합계||ShardId|
|totalkeys|전체 키|카운트|최대||ShardId|
|expiredkeys|만료된 키|카운트|합계||ShardId|
|usedmemory|사용된 메모리|바이트|최대||ShardId|
|usedmemorypercentage|사용된 메모리 비율|백분율|최대||ShardId|
|usedmemoryRss|사용된 메모리 RSS|바이트|최대||ShardId|
|serverLoad|서버 부하|백분율|최대||ShardId|
|cacheWrite|캐시 쓰기|초당 바이트 수|최대||ShardId|
|cacheRead|캐시 읽기|초당 바이트 수|최대||ShardId|
|percentProcessorTime|CPU|백분율|최대||ShardId|
|cacheLatency|캐시 대기 시간 마이크로초(미리 보기)|카운트|평균||ShardId, SampleType|
|errors|오류|카운트|최대||ShardId, ErrorType|
|connectedclients0|연결된 클라이언트(분할 0)|카운트|최대||차원 없음|
|totalcommandsprocessed0|총 작업(분할 0)|카운트|합계||차원 없음|
|cachehits0|캐시 적중(분할 0)|카운트|합계||차원 없음|
|cachemisses0|캐시 누락(분할 0)|카운트|합계||차원 없음|
|getcommands0|가져오기(분할 0)|카운트|합계||차원 없음|
|setcommands0|설정(분할 0)|카운트|합계||차원 없음|
|operationsPerSecond0|초당 작업(분할 0)|카운트|최대||차원 없음|
|evictedkeys0|제거된 키(분할 0)|카운트|합계||차원 없음|
|totalkeys0|총 키(분할 0)|카운트|최대||차원 없음|
|expiredkeys0|만료된 키(분할 0)|카운트|합계||차원 없음|
|usedmemory0|사용된 메모리(분할 0)|바이트|최대||차원 없음|
|usedmemoryRss0|사용된 메모리 RSS(분할 0)|바이트|최대||차원 없음|
|serverLoad0|서버 부하(분할 0)|백분율|최대||차원 없음|
|cacheWrite0|캐시 쓰기(분할 0)|초당 바이트 수|최대||차원 없음|
|cacheRead0|캐시 읽기(분할 0)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime0|CPU(분할 0)|백분율|최대||차원 없음|
|connectedclients1|연결된 클라이언트(분할 1)|카운트|최대||차원 없음|
|totalcommandsprocessed1|총 작업(분할 1)|카운트|합계||차원 없음|
|cachehits1|캐시 적중(분할 1)|카운트|합계||차원 없음|
|cachemisses1|캐시 누락(분할 1)|카운트|합계||차원 없음|
|getcommands1|가져오기(분할 1)|카운트|합계||차원 없음|
|setcommands1|집합(분할 1)|카운트|합계||차원 없음|
|operationsPerSecond1|초당 작업(분할 1)|카운트|최대||차원 없음|
|evictedkeys1|제거된 키(분할 1)|카운트|합계||차원 없음|
|totalkeys1|총 키(분할 1)|카운트|최대||차원 없음|
|expiredkeys1|만료된 키(분할 1)|카운트|합계||차원 없음|
|usedmemory1|사용된 메모리(분할 1)|바이트|최대||차원 없음|
|usedmemoryRss1|사용된 메모리 RSS(분할 1)|바이트|최대||차원 없음|
|serverLoad1|서버 부하(분할 1)|백분율|최대||차원 없음|
|cacheWrite1|캐시 쓰기(분할 1)|초당 바이트 수|최대||차원 없음|
|cacheRead1|캐시 읽기(분할 1)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime1|CPU(분할 1)|백분율|최대||차원 없음|
|connectedclients2|연결된 클라이언트(분할 2)|카운트|최대||차원 없음|
|totalcommandsprocessed2|총 작업(분할 2)|카운트|합계||차원 없음|
|cachehits2|캐시 적중(분할 2)|카운트|합계||차원 없음|
|cachemisses2|캐시 누락(분할 2)|카운트|합계||차원 없음|
|getcommands2|가져오기(분할 2)|카운트|합계||차원 없음|
|setcommands2|설정(분할 2)|카운트|합계||차원 없음|
|operationsPerSecond2|초당 작업(분할 2)|카운트|최대||차원 없음|
|evictedkeys2|제거된 키(분할 2)|카운트|합계||차원 없음|
|totalkeys2|총 키(분할 2)|카운트|최대||차원 없음|
|expiredkeys2|만료된 키(분할 2)|카운트|합계||차원 없음|
|usedmemory2|사용된 메모리(분할 2)|바이트|최대||차원 없음|
|usedmemoryRss2|사용된 메모리 RSS(분할 2)|바이트|최대||차원 없음|
|serverLoad2|서버 부하(분할 2)|백분율|최대||차원 없음|
|cacheWrite2|캐시 쓰기(분할 2)|초당 바이트 수|최대||차원 없음|
|cacheRead2|캐시 읽기(분할 2)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime2|CPU(분할 2)|백분율|최대||차원 없음|
|connectedclients3|연결된 클라이언트(분할 3)|카운트|최대||차원 없음|
|totalcommandsprocessed3|총 작업(분할 3)|카운트|합계||차원 없음|
|cachehits3|캐시 적중(분할 3)|카운트|합계||차원 없음|
|cachemisses3|캐시 누락(분할 3)|카운트|합계||차원 없음|
|getcommands3|가져오기(분할 3)|카운트|합계||차원 없음|
|setcommands3|설정(분할 3)|카운트|합계||차원 없음|
|operationsPerSecond3|초당 작업(분할 3)|카운트|최대||차원 없음|
|evictedkeys3|제거된 키(분할 3)|카운트|합계||차원 없음|
|totalkeys3|총 키(분할 3)|카운트|최대||차원 없음|
|expiredkeys3|만료된 키(분할 3)|카운트|합계||차원 없음|
|usedmemory3|사용된 메모리(분할 3)|바이트|최대||차원 없음|
|usedmemoryRss3|사용된 메모리 RSS(분할 3)|바이트|최대||차원 없음|
|serverLoad3|서버 부하(분할 3)|백분율|최대||차원 없음|
|cacheWrite3|캐시 쓰기(분할 3)|초당 바이트 수|최대||차원 없음|
|cacheRead3|캐시 읽기(분할 3)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime3|CPU(분할 3)|백분율|최대||차원 없음|
|connectedclients4|연결된 클라이언트(분할 4)|카운트|최대||차원 없음|
|totalcommandsprocessed4|총 작업(분할 4)|카운트|합계||차원 없음|
|cachehits4|캐시 적중(분할 4)|카운트|합계||차원 없음|
|cachemisses4|캐시 누락(분할 4)|카운트|합계||차원 없음|
|getcommands4|가져오기(분할 4)|카운트|합계||차원 없음|
|setcommands4|설정(분할 4)|카운트|합계||차원 없음|
|operationsPerSecond4|초당 작업(분할 4)|카운트|최대||차원 없음|
|evictedkeys4|제거된 키(분할 4)|카운트|합계||차원 없음|
|totalkeys4|총 키(분할 4)|카운트|최대||차원 없음|
|expiredkeys4|만료된 키(분할 4)|카운트|합계||차원 없음|
|usedmemory4|사용된 메모리(분할 4)|바이트|최대||차원 없음|
|usedmemoryRss4|사용된 메모리 RSS(분할 4)|바이트|최대||차원 없음|
|serverLoad4|서버 부하(분할 4)|백분율|최대||차원 없음|
|cacheWrite4|캐시 쓰기(분할 4)|초당 바이트 수|최대||차원 없음|
|cacheRead4|캐시 읽기(분할 4)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime4|CPU(분할 4)|백분율|최대||차원 없음|
|connectedclients5|연결된 클라이언트(분할 5)|카운트|최대||차원 없음|
|totalcommandsprocessed5|총 작업(분할 5)|카운트|합계||차원 없음|
|cachehits5|캐시 적중(분할 5)|카운트|합계||차원 없음|
|cachemisses5|캐시 누락(분할 5)|카운트|합계||차원 없음|
|getcommands5|가져오기(분할 5)|카운트|합계||차원 없음|
|setcommands5|설정(분할 5)|카운트|합계||차원 없음|
|operationsPerSecond5|초당 작업(분할 5)|카운트|최대||차원 없음|
|evictedkeys5|제거된 키(분할 5)|카운트|합계||차원 없음|
|totalkeys5|총 키(분할 5)|카운트|최대||차원 없음|
|expiredkeys5|만료된 키(분할 5)|카운트|합계||차원 없음|
|usedmemory5|사용된 메모리(분할 5)|바이트|최대||차원 없음|
|usedmemoryRss5|사용된 메모리 RSS(분할 5)|바이트|최대||차원 없음|
|serverLoad5|서버 부하(분할 5)|백분율|최대||차원 없음|
|cacheWrite5|캐시 쓰기(분할 5)|초당 바이트 수|최대||차원 없음|
|cacheRead5|캐시 읽기(분할 5)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime5|CPU(분할 5)|백분율|최대||차원 없음|
|connectedclients6|연결된 클라이언트(분할 6)|카운트|최대||차원 없음|
|totalcommandsprocessed6|총 작업(분할 6)|카운트|합계||차원 없음|
|cachehits6|캐시 적중(분할 6)|카운트|합계||차원 없음|
|cachemisses6|캐시 누락(분할 6)|카운트|합계||차원 없음|
|getcommands6|가져오기(분할 6)|카운트|합계||차원 없음|
|setcommands6|설정(분할 6)|카운트|합계||차원 없음|
|operationsPerSecond6|초당 작업(분할 6)|카운트|최대||차원 없음|
|evictedkeys6|제거된 키(분할 6)|카운트|합계||차원 없음|
|totalkeys6|총 키(분할 6)|카운트|최대||차원 없음|
|expiredkeys6|만료된 키(분할 6)|카운트|합계||차원 없음|
|usedmemory6|사용된 메모리(분할 6)|바이트|최대||차원 없음|
|usedmemoryRss6|사용된 메모리 RSS(분할 6)|바이트|최대||차원 없음|
|serverLoad6|서버 부하(분할 6)|백분율|최대||차원 없음|
|cacheWrite6|캐시 쓰기(분할 6)|초당 바이트 수|최대||차원 없음|
|cacheRead6|캐시 읽기(분할 6)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime6|CPU(분할 6)|백분율|최대||차원 없음|
|connectedclients7|연결된 클라이언트(분할 7)|카운트|최대||차원 없음|
|totalcommandsprocessed7|총 작업(분할 7)|카운트|합계||차원 없음|
|cachehits7|캐시 적중(분할 7)|카운트|합계||차원 없음|
|cachemisses7|캐시 누락(분할 7)|카운트|합계||차원 없음|
|getcommands7|가져오기(분할 7)|카운트|합계||차원 없음|
|setcommands7|설정(분할 7)|카운트|합계||차원 없음|
|operationsPerSecond7|초당 작업(분할 7)|카운트|최대||차원 없음|
|evictedkeys7|제거된 키(분할 7)|카운트|합계||차원 없음|
|totalkeys7|총 키(분할 7)|카운트|최대||차원 없음|
|expiredkeys7|만료된 키(분할 7)|카운트|합계||차원 없음|
|usedmemory7|사용된 메모리(분할 7)|바이트|최대||차원 없음|
|usedmemoryRss7|사용된 메모리 RSS(분할 7)|바이트|최대||차원 없음|
|serverLoad7|서버 부하(분할 7)|백분율|최대||차원 없음|
|cacheWrite7|캐시 쓰기(분할 7)|초당 바이트 수|최대||차원 없음|
|cacheRead7|캐시 읽기(분할 7)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime7|CPU(분할 7)|백분율|최대||차원 없음|
|connectedclients8|연결된 클라이언트(분할 8)|카운트|최대||차원 없음|
|totalcommandsprocessed8|총 작업(분할 8)|카운트|합계||차원 없음|
|cachehits8|캐시 적중(분할 8)|카운트|합계||차원 없음|
|cachemisses8|캐시 누락(분할 8)|카운트|합계||차원 없음|
|getcommands8|가져오기(분할 8)|카운트|합계||차원 없음|
|setcommands8|설정(분할 8)|카운트|합계||차원 없음|
|operationsPerSecond8|초당 작업(분할 8)|카운트|최대||차원 없음|
|evictedkeys8|제거된 키(분할 8)|카운트|합계||차원 없음|
|totalkeys8|총 키(분할 8)|카운트|최대||차원 없음|
|expiredkeys8|만료된 키(분할 8)|카운트|합계||차원 없음|
|usedmemory8|사용된 메모리(분할 8)|바이트|최대||차원 없음|
|usedmemoryRss8|사용된 메모리 RSS(분할 8)|바이트|최대||차원 없음|
|serverLoad8|서버 부하(분할 8)|백분율|최대||차원 없음|
|cacheWrite8|캐시 쓰기(분할 8)|초당 바이트 수|최대||차원 없음|
|cacheRead8|캐시 읽기(분할 8)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime8|CPU(분할 8)|백분율|최대||차원 없음|
|connectedclients9|연결된 클라이언트(분할 9)|카운트|최대||차원 없음|
|totalcommandsprocessed9|총 작업(분할 9)|카운트|합계||차원 없음|
|cachehits9|캐시 적중(분할 9)|카운트|합계||차원 없음|
|cachemisses9|캐시 누락(분할 9)|카운트|합계||차원 없음|
|getcommands9|가져오기(분할 9)|카운트|합계||차원 없음|
|setcommands9|설정(분할 9)|개수|합계||차원 없음|
|operationsPerSecond9|초당 작업(분할 9)|카운트|최대||차원 없음|
|evictedkeys9|제거된 키(분할 9)|카운트|합계||차원 없음|
|totalkeys9|총 키(분할 9)|카운트|최대||차원 없음|
|expiredkeys9|만료된 키(분할 9)|카운트|합계||차원 없음|
|usedmemory9|사용된 메모리(분할 9)|바이트|최대||차원 없음|
|usedmemoryRss9|사용된 메모리 RSS(분할 9)|바이트|최대||차원 없음|
|serverLoad9|서버 부하(분할 9)|백분율|최대||차원 없음|
|cacheWrite9|캐시 쓰기(분할 9)|초당 바이트 수|최대||차원 없음|
|cacheRead9|캐시 읽기(분할 9)|초당 바이트 수|최대||차원 없음|
|percentProcessorTime9|CPU(분할 9)|백분율|최대||차원 없음|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|차원 없음|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|차원 없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|차원 없음|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|차원 없음|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|차원 없음|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|RoleInstanceId|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|RoleInstanceId|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|RoleInstanceId|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|RoleInstanceId|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|RoleInstanceId|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|RoleInstanceId|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalCalls|총 호출|카운트|합계|총 호출 수.|ApiName, OperationName, 지역|
|SuccessfulCalls|성공한 호출|카운트|합계|성공한 호출 수입니다.|ApiName, OperationName, 지역|
|TotalErrors|총 오류|카운트|합계|오류 응답(HTTP 응답 코드 4xx 또는 5xx)이 있는 총 호출 수입니다.|ApiName, OperationName, 지역|
|BlockedCalls|차단된 호출|카운트|합계|요금 또는 할당량 한도를 초과한 호출 수입니다.|ApiName, OperationName, 지역|
|ServerErrors|서버 오류|카운트|합계|서비스 내부 오류(HTTP 응답 코드 5xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|ClientErrors|클라이언트 오류|카운트|합계|클라이언트 쪽 오류(HTTP 응답 코드 4xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|DataIn|데이터 입력|바이트|합계|들어오는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|DataOut|데이터 출력|바이트|합계|나가는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|대기 시간|대기 시간|밀리초|평균|대기 시간(밀리초)입니다.|ApiName, OperationName, 지역|
|CharactersTranslated|변환된 문자|카운트|합계|들어오는 텍스트 요청에 있는 문자의 총 수입니다.|ApiName, OperationName, 지역|
|CharactersTrained|학습 하는 문자|카운트|합계|학습 된 문자의 총 수입니다.|ApiName, OperationName, 지역|
|SpeechSessionDuration|음성 세션 기간|초|합계|음성 세션의 총 기간(초)입니다.|ApiName, OperationName, 지역|
|TotalTransactions|총 트랜잭션|카운트|합계|총 트랜잭션 수|차원 없음|
|TotalTokenCalls|총 토큰 호출|카운트|합계|총 토큰 호출 수입니다.|ApiName, OperationName, 지역|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|차원 없음|
|네트워크 인|청구 가능 네트워크 입력|바이트|합계|가상 머신이 모든 네트워크 인터페이스에서 수신한 청구 가능 바이트 수(들어오는 트래픽)|차원 없음|
|네트워크 아웃|청구 가능 네트워크 출력|바이트|합계|가상 머신이 모든 네트워크 인터페이스에서 발신한 청구 가능 바이트 수(나가는 트래픽)|차원 없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 중 디스크에서 읽은 바이트 수|차원 없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 중 디스크에 쓴 바이트 수|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|남은 CPU 크레딧|남은 CPU 크레딧|카운트|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|카운트|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 쓰기|SlotId|
|디스크당 QD|데이터 디스크 QD(사용되지 않음)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 읽은 바이트/초|차원 없음|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에 쓴 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 읽기|차원 없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 쓰기|차원 없음|
|디스크당 OS QD|OS 디스크 QD(사용되지 않음)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|데이터 디스크 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 읽은 바이트/초|LUN|
|데이터 디스크 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에 쓴 바이트/초|LUN|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 쓰기|LUN|
|데이터 디스크 큐 크기|데이터 디스크 큐 크기(미리 보기)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|OS 디스크 읽기 바이트/초|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 읽은 바이트/초|차원 없음|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에 쓴 바이트/초|차원 없음|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 읽기|차원 없음|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 쓰기|차원 없음|
|OS 디스크 큐 크기|OS 디스크 큐 크기(미리 보기)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|인바운드 흐름|인바운드 흐름(미리 보기)|카운트|평균|인바운드 흐름은 인바운드 방향에서 현재 흐름의 수입니다(트래픽이 VM으로 들어감).|차원 없음|
|아웃바운드 흐름|아웃바운드 흐름(미리 보기)|카운트|평균|아웃바운드 흐름은 아웃바운드 방향의 현재 흐름 수입니다(트래픽이 VM에서 나감).|차원 없음|
|인바운드 흐름 최대 생성 속도|인바운드 흐름 최대 생성 속도(미리 보기)|초당 개수|평균|인바운드 흐름(VM으로의 트래픽) 최대 생성 속도|차원 없음|
|아웃바운드 흐름 최대 생성 속도|아웃바운드 흐름 최대 생성 속도(미리 보기)|초당 개수|평균|아웃바운드 흐름(VM으로의 트래픽) 최대 생성 속도|차원 없음|
|프리미엄 데이터 디스크 캐시 읽기 적중|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN|
|프리미엄 데이터 디스크 캐시 읽기 누락|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN|
|프리미엄 OS 디스크 캐시 읽기 적중|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|차원 없음|
|프리미엄 OS 디스크 캐시 읽기 누락|프리미엄 OS 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|차원 없음|
|전체 네트워크 입력|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|전체 네트워크 출력|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|VMName|
|네트워크 인|청구 가능 네트워크 입력|바이트|합계|가상 머신이 모든 네트워크 인터페이스에서 수신한 청구 가능 바이트 수(들어오는 트래픽)|VMName|
|네트워크 아웃|청구 가능 네트워크 출력|바이트|합계|가상 머신이 모든 네트워크 인터페이스에서 발신한 청구 가능 바이트 수(나가는 트래픽)|VMName|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 중 디스크에서 읽은 바이트 수|VMName|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 중 디스크에 쓴 바이트 수|VMName|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|VMName|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|VMName|
|남은 CPU 크레딧|남은 CPU 크레딧|카운트|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|카운트|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 쓰기|SlotId|
|디스크당 QD|데이터 디스크 QD(사용되지 않음)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 읽은 바이트/초|차원 없음|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에 쓴 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 읽기|차원 없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 쓰기|차원 없음|
|디스크당 OS QD|OS 디스크 QD(사용되지 않음)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|데이터 디스크 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 읽은 바이트/초|LUN, VMName|
|데이터 디스크 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에 쓴 바이트/초|LUN, VMName|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 읽기|LUN, VMName|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 쓰기|LUN, VMName|
|데이터 디스크 큐 크기|데이터 디스크 큐 크기(미리 보기)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN, VMName|
|OS 디스크 읽기 바이트/초|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 읽은 바이트/초|VMName|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에 쓴 바이트/초|VMName|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 읽기|VMName|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 쓰기|VMName|
|OS 디스크 큐 크기|OS 디스크 큐 크기(미리 보기)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|VMName|
|인바운드 흐름|인바운드 흐름(미리 보기)|카운트|평균|인바운드 흐름은 인바운드 방향에서 현재 흐름의 수입니다(트래픽이 VM으로 들어감).|VMName|
|아웃바운드 흐름|아웃바운드 흐름(미리 보기)|카운트|평균|아웃바운드 흐름은 아웃바운드 방향의 현재 흐름 수입니다(트래픽이 VM에서 나감).|VMName|
|인바운드 흐름 최대 생성 속도|인바운드 흐름 최대 생성 속도(미리 보기)|초당 개수|평균|인바운드 흐름(VM으로의 트래픽) 최대 생성 속도|VMName|
|아웃바운드 흐름 최대 생성 속도|아웃바운드 흐름 최대 생성 속도(미리 보기)|초당 개수|평균|아웃바운드 흐름(VM으로의 트래픽) 최대 생성 속도|VMName|
|프리미엄 데이터 디스크 캐시 읽기 적중|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN, VMName|
|프리미엄 데이터 디스크 캐시 읽기 누락|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN, VMName|
|프리미엄 OS 디스크 캐시 읽기 적중|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|VMName|
|프리미엄 OS 디스크 캐시 읽기 누락|프리미엄 OS 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|VMName|
|전체 네트워크 입력|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|VMName|
|전체 네트워크 출력|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuUsage|CPU 사용량|카운트|평균|모든 코어의 CPU 사용량(밀리코어)|containerName|
|MemoryUsage|메모리 사용량|바이트|평균|총 메모리 사용량(바이트)|containerName|
|NetworkBytesReceivedPerSecond|초당 수신된 네트워크 바이트|바이트|평균|초당 수신된 네트워크 바이트입니다.|차원 없음|
|NetworkBytesTransmittedPerSecond|초당 전송된 네트워크 바이트|바이트|평균|초당 전송된 네트워크 바이트입니다.|차원 없음|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalPullCount|총 풀 수|카운트|평균|이미지 수가 총에서|차원 없음|
|SuccessfulPullCount|성공적인 끌어오기 개수|카운트|평균|성공적으로 이미지 끌어오기 수|차원 없음|
|TotalPushCount|총 푸시 수|카운트|평균|총에서 푸시 이미지의 수입니다.|차원 없음|
|SuccessfulPushCount|성공한 푸시 수|카운트|평균|푸시 성공적으로 이미지의 수입니다.|차원 없음|
|RunDuration|실행 지속 시간|밀리초|합계|실행 지속 시간 (밀리초)|차원 없음|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|카운트|합계|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|차원 없음|
|kube_node_status_allocatable_memory_bytes|관리 클러스터에서 사용 가능한 총 메모리 양|바이트|합계|관리 클러스터에서 사용 가능한 총 메모리 양|차원 없음|
|kube_pod_status_ready|준비 상태인 Pod 수|카운트|합계|준비 상태인 Pod 수|네임스페이스, Pod|
|kube_node_status_condition|다양한 노드 조건에 대한 상태|카운트|합계|다양한 노드 조건에 대한 상태|조건, 상태, status2 노드|
|kube_pod_status_phase|단계별 Pod 수|카운트|합계|단계별 Pod 수|단계, 네임스페이스, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API 호출 수|카운트|합계||차원 없음|
|DCIMappingImportOperationSuccessfulLines|매핑 가져오기 작업 성공 줄 수|카운트|합계||차원 없음|
|DCIMappingImportOperationFailedLines|매핑 가져오기 작업 실패 줄 수|카운트|합계||차원 없음|
|DCIMappingImportOperationTotalLines|매핑 가져오기 작업 총 줄 수|카운트|합계||차원 없음|
|DCIMappingImportOperationRuntimeInSeconds|매핑 가져오기 작업 런타임(초)|초|합계||차원 없음|
|DCIOutboundProfileExportSucceeded|아웃바운드 프로필 내보내기 성공|카운트|합계||차원 없음|
|DCIOutboundProfileExportFailed|아웃바운드 프로필 내보내기 실패|카운트|합계||차원 없음|
|DCIOutboundProfileExportDuration|아웃바운드 프로필 내보내기 기간|초|합계||차원 없음|
|DCIOutboundKpiExportSucceeded|아웃바운드 Kpi 내보내기 성공|카운트|합계||차원 없음|
|DCIOutboundKpiExportFailed|아웃바운드 Kpi 내보내기 실패|카운트|합계||차원 없음|
|DCIOutboundKpiExportDuration|아웃바운드 Kpi 내보내기 기간|초|합계||차원 없음|
|DCIOutboundKpiExportStarted|아웃바운드 Kpi 내보내기 시작|초|합계||차원 없음|
|DCIOutboundKpiRecordCount|아웃바운드 Kpi 레코드 수|초|합계||차원 없음|
|DCIOutboundProfileExportCount|아웃바운드 프로필 내보내기 수|초|합계||차원 없음|
|DCIOutboundInitialProfileExportFailed|아웃바운드 초기 프로필 내보내기 실패|초|합계||차원 없음|
|DCIOutboundInitialProfileExportSucceeded|아웃바운드 초기 프로필 내보내기 성공|초|합계||차원 없음|
|DCIOutboundInitialKpiExportFailed|아웃바운드 초기 Kpi 내보내기 실패|초|합계||차원 없음|
|DCIOutboundInitialKpiExportSucceeded|아웃바운드 초기 Kpi 내보내기 성공|초|합계||차원 없음|
|DCIOutboundInitialProfileExportDurationInSeconds|아웃바운드 초기 프로필 내보내기 기간(초)|초|합계||차원 없음|
|AdlaJobForStandardKpiFailed|표준 Kpi에 대한 Adla 작업 실패(초)|초|합계||차원 없음|
|AdlaJobForStandardKpiTimeOut|표준 Kpi에 대한 Adla 작업 시간 제한(초)|초|합계||차원 없음|
|AdlaJobForStandardKpiCompleted|표준 Kpi에 대한 Adla 작업 완료(초)|초|합계||차원 없음|
|ImportASAValuesFailed|ASA 값 가져오기 실패 수|카운트|합계||차원 없음|
|ImportASAValuesSucceeded|ASA 값 가져오기 성공 수|카운트|합계||차원 없음|
|DCIProfilesCount|프로필 인스턴스 수|카운트|마지막||차원 없음|
|DCIInteractionsPerMonthCount|개월당 상호 작용|카운트|마지막||차원 없음|
|DCIKpisCount|KPI 수|카운트|마지막||차원 없음|
|DCISegmentsCount|세그먼트 수|카운트|마지막||차원 없음|
|DCIPredictiveMatchPoliciesCount|예측 일치 수|카운트|마지막||차원 없음|
|DCIPredictionsCount|예측 수|카운트|마지막||차원 없음|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|NICReadThroughput|읽기 처리량 (네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대해 보고 기간 장치에서 네트워크 인터페이스의 읽기 처리량입니다.|InstanceName|
|NICWriteThroughput|쓰기 처리량 (네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대해 보고 기간 장치에서 네트워크 인터페이스의 쓰기 처리량입니다.|InstanceName|
|CloudReadThroughputPerShare|클라우드 다운로드 처리량 (공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로 다운로드 처리량입니다.|공유|
|CloudUploadThroughputPerShare|클라우드 업로드 처리량 (공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로 업로드 처리량입니다.|공유|
|BytesUploadedToCloudPerShare|클라우드 바이트 업로드 (공유)|바이트|평균|보고 기간 동안 공유에서 Azure에 업로드 된 바이트의 총 수입니다.|공유|
|TotalCapacity|전체 용량|바이트|평균|전체 용량|차원 없음|
|AvailableCapacity|사용 가능한 용량|바이트|평균|보고 기간 동안 (바이트)는 사용할 수 있는 용량입니다.|차원 없음|
|CloudUploadThroughput|클라우드 업로드 처리량|초당 바이트 수|평균|보고 기간의 Azure에 대한 클라우드 업로드 처리량입니다.|차원 없음|
|CloudReadThroughput|클라우드 다운로드 처리량|초당 바이트 수|평균|보고 기간의 Azure에 대한 클라우드 다운로드 처리량입니다.|차원 없음|
|BytesUploadedToCloud|업로드 된 클라우드 바이트 (장치)|바이트|평균|보고 기간 동안 장치에서 Azure에 업로드 된 바이트의 총 수입니다.|차원 없음|
|HyperVVirtualProcessorUtilization|Edge 컴퓨팅 - CPU 사용률|백분율|평균|CPU 사용률|InstanceName|
|HyperVMemoryUtilization|Edge 컴퓨팅 - 메모리 사용|백분율|평균|사용 중인 RAM 크기|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|FailedRuns|실패한 실행|카운트|합계||pipelineName, activityName|
|SuccessfulRuns|성공한 실행|카운트|합계||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|PipelineFailedRuns|실패한 파이프라인 실행 메트릭|카운트|합계||FailureType, Name|
|PipelineSucceededRuns|성공한 파이프라인 실행 메트릭|카운트|합계||FailureType, Name|
|ActivityFailedRuns|실패한 활동 실행 메트릭|카운트|합계||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|성공한 활동 실행 메트릭|카운트|합계||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|실패한 트리거 실행 메트릭|카운트|합계||Name, FailureType|
|TriggerSucceededRuns|성공한 트리거 실행 메트릭|카운트|합계||Name, FailureType|
|IntegrationRuntimeCpuPercentage|통합 런타임 CPU 사용률|백분율|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|통합 런타임 사용 가능한 메모리|바이트|평균||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|허용 된 최대 엔터티 수|카운트|최대||차원 없음|
|MaxAllowedFactorySizeInGbUnits|허용 되는 최대 팩터리 크기 (GB 단위)|카운트|최대||차원 없음|
|ResourceCount|총 엔터티 수|카운트|최대||차원 없음|
|FactorySizeInGbUnits|총 팩터리 크기 (GB 단위)|카운트|최대||차원 없음|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|JobEndedSuccess|성공한 작업|카운트|합계|성공한 작업의 수입니다.|차원 없음|
|JobEndedFailure|실패한 작업|카운트|합계|실패한 작업 수입니다.|차원 없음|
|JobEndedCancelled|취소 된 작업|카운트|합계|취소 된 작업 수입니다.|차원 없음|
|JobAUEndedSuccess|성공한 AU 시간|초|합계|성공한 작업에 대한 총 AU 시간입니다.|차원 없음|
|JobAUEndedFailure|실패한 AU 시간|초|합계|실패한 작업에 대한 총 AU 시간입니다.|차원 없음|
|JobAUEndedCancelled|취소 된 AU 시간|초|합계|취소 된 작업에 대 한 총 AU 시간입니다.|차원 없음|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TotalStorage|총 저장소|바이트|최대|계정에 저장된 총 데이터 양.|차원 없음|
|DataWritten|기록된 데이터|바이트|합계|계정에 기록된 총 데이터 양.|차원 없음|
|DataRead|데이터 읽기|바이트|합계|계정에서 읽어 온 총 데이터 양.|차원 없음|
|WriteRequests|쓰기 요청|카운트|합계|계정에 데이터 쓰기 요청 수.|차원 없음|
|ReadRequests|읽기 요청|카운트|합계|계정에 데이터 읽기 요청 수.|차원 없음|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|차원 없음|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|차원 없음|
|storage_limit|저장소 제한|바이트|평균|저장소 제한|차원 없음|
|serverlog_storage_percent|서버 로그 저장소 비율|백분율|평균|서버 로그 저장소 비율|차원 없음|
|serverlog_storage_percent|사용된 서버 로그 저장소|바이트|평균|사용된 서버 로그 저장소|차원 없음|
|serverlog_storage_limit|서버 로그 저장소 제한|바이트|평균|서버 로그 저장소 제한|차원 없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|차원 없음|
|connections_failed|실패한 연결|카운트|합계|실패한 연결|차원 없음|
|seconds_behind_master|복제 지연 시간(초)|카운트|평균|복제 지연 시간(초)|차원 없음|
|backup_storage_used|백업 저장소 사용|바이트|평균|백업 저장소 사용|차원 없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|차원 없음|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|차원 없음|
|storage_limit|저장소 제한|바이트|평균|저장소 제한|차원 없음|
|serverlog_storage_percent|서버 로그 저장소 비율|백분율|평균|서버 로그 저장소 비율|차원 없음|
|serverlog_storage_percent|사용된 서버 로그 저장소|바이트|평균|사용된 서버 로그 저장소|차원 없음|
|serverlog_storage_limit|서버 로그 저장소 제한|바이트|평균|서버 로그 저장소 제한|차원 없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|차원 없음|
|connections_failed|실패한 연결|카운트|합계|실패한 연결|차원 없음|
|seconds_behind_master|복제 지연 시간(초)|카운트|평균|복제 지연 시간(초)|차원 없음|
|backup_storage_used|백업 저장소 사용|바이트|평균|백업 저장소 사용|차원 없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|차원 없음|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|차원 없음|
|storage_limit|저장소 제한|바이트|평균|저장소 제한|차원 없음|
|serverlog_storage_percent|서버 로그 저장소 비율|백분율|평균|서버 로그 저장소 비율|차원 없음|
|serverlog_storage_percent|사용된 서버 로그 저장소|바이트|평균|사용된 서버 로그 저장소|차원 없음|
|serverlog_storage_limit|서버 로그 저장소 제한|바이트|평균|서버 로그 저장소 제한|차원 없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|차원 없음|
|connections_failed|실패한 연결|카운트|합계|실패한 연결|차원 없음|
|backup_storage_used|백업 저장소 사용|바이트|평균|백업 저장소 사용|차원 없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|pg_replica_log_delay_in_seconds|복제본 지연 시간|초|최대|복제 지연 시간 (초)|차원 없음|
|pg_replica_log_delay_in_bytes|복제본 간 최대 지연 시간|바이트|최대|지연 복제본 가장 바이트 지연|차원 없음|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|iops|IOPS|카운트|평균|초당 IO 작업|차원 없음|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|차원 없음|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|차원 없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|차원 없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|원격 분석 메시지 보내기 시도|카운트|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.telemetry.ingress.success|보낸 원격 분석 메시지|카운트|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|c2d.commands.egress.complete.success|완료된 명령|카운트|합계|디바이스에서 성공적으로 완료한 클라우드-디바이스 명령 수|차원 없음|
|c2d.commands.egress.abandon.success|중단된 명령|카운트|합계|디바이스에서 중단한 클라우드-디바이스 명령 수|차원 없음|
|c2d.commands.egress.reject.success|거부된 명령|카운트|합계|디바이스에서 거부한 클라우드-디바이스 명령 수|차원 없음|
|devices.totalDevices|총 디바이스(사용되지 않음)|카운트|합계|IoT 허브에 등록된 디바이스 수|차원 없음|
|devices.connectedDevices.allProtocol|연결된 디바이스(사용되지 않음) |카운트|합계|IoT 허브에 연결된 디바이스 수|차원 없음|
|d2c.telemetry.egress.success|라우팅: 배달된 원격 분석 메시지|카운트|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|차원 없음|
|d2c.telemetry.egress.dropped|라우팅: 삭제된 원격 분석 메시지 |카운트|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|차원 없음|
|d2c.telemetry.egress.orphaned|라우팅: 분리된 원격 분석 메시지 |카운트|합계|메시지가 라우팅 규칙(대체 규칙 포함)과 일치하지 않았으므로 IoT Hub 라우팅에 의해 분리된 횟수입니다. |차원 없음|
|d2c.telemetry.egress.invalid|라우팅: 원격 분석 메시지 호환되지 않음|카운트|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. 이 값은 재시도를 포함하지 않습니다.|차원 없음|
|d2c.telemetry.egress.fallback|라우팅: 대체에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.eventHubs|라우팅: 이벤트 허브에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 이벤트 허브 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.latency.eventHubs|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 엔드포인트에 대한 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusQueues|라우팅: Service Bus 큐에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusQueues|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusTopics|라우팅: Service Bus 토픽에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusTopics|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.egress.builtIn.events|라우팅: 메시지/이벤트에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 기본 제공 엔드포인트(메시지/이벤트)에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.latency.builtIn.events|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 엔드포인트(메시지.이벤트)에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.egress.storage|라우팅: 저장소에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 저장소 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.latency.storage|라우팅: 저장소에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 저장소 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.egress.storage.bytes|라우팅: 저장소에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 저장소 엔드포인트에 배달된 데이터 양입니다(바이트).|차원 없음|
|d2c.endpoints.egress.storage.blobs|라우팅: 저장소에 배달된 Blob|카운트|합계|IoT Hub 라우팅에서 저장소 엔드포인트에 Blob을 배달한 횟수입니다.|차원 없음|
|EventGridDeliveries|Event Grid 배달 (미리 보기)|카운트|합계|Event Grid에 이벤트를 내보내는 IoT Hub에 대 한 요청의 수입니다. 이 숫자는 성공한 요청과 실패 한 요청이 포함 됩니다. 결과 차원을 사용 하 여 다른 종류의 응답 수입니다. Where를 확인 하려면 요청에서 제공, EventType 차원을 사용 합니다.|결과 이벤트 유형|
|EventGridLatency|Event Grid 대기 시간 (미리 보기)|밀리초|평균|평균 대기 시간 (밀리초) IoT Hub로 이벤트 수신 및 이벤트 수신을 간에 Event Grid. 이 수에는 모든 이벤트 형식 간의 평균입니다. EventType 차원을 사용 하 여 특정 유형의 이벤트의 대기 시간을 참조 하세요.|이벤트 유형|
|d2c.twin.read.success|디바이스에서의 성공한 쌍 읽기|카운트|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.read.failure|디바이스에서의 실패한 쌍 읽기|카운트|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.read.size|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|d2c.twin.update.success|디바이스에서의 성공한 쌍 업데이트|카운트|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c.twin.update.failure|디바이스에서의 실패한 쌍 업데이트|카운트|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c.twin.update.size|디바이스에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|c2d.methods.success|성공한 직접 메서드 호출|카운트|합계|성공한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.methods.failure|실패한 직접 메서드 호출|카운트|합계|실패한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.methods.requestSize|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.responseSize|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.twin.read.success|백 엔드에서의 성공한 쌍 읽기|카운트|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.read.failure|백 엔드에서의 실패한 쌍 읽기|카운트|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.read.size|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|c2d.twin.update.success|백 엔드에서의 성공한 쌍 업데이트|카운트|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d.twin.update.failure|백 엔드에서의 실패한 쌍 업데이트|카운트|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d.twin.update.size|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|twinQueries.success|성공한 쌍 쿼리|카운트|합계|성공한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries.failure|실패한 쌍 쿼리|카운트|합계|실패한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries.resultSize|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|차원 없음|
|jobs.createTwinUpdateJob.success|쌍 업데이트 작업에 대한 성공한 만들기|카운트|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.failure|쌍 업데이트 작업에 대한 실패한 만들기|카운트|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.success|메서드 호출 작업에 대한 성공한 만들기|카운트|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.failure|실패한 메서드 호출 작업 만들기|카운트|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.listJobs.success|목록 작업에 대한 성공한 호출|카운트|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.listJobs.failure|목록 작업에 대한 실패한 호출|카운트|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.cancelJob.success|성공한 작업 취소|카운트|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.cancelJob.failure|실패한 작업 취소|카운트|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.success|성공한 작업 쿼리|카운트|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.failure|실패한 작업 쿼리|카운트|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.completed|완료된 작업|카운트|합계|완료된 모든 작업의 수입니다.|차원 없음|
|jobs.failed|실패한 작업|카운트|합계|실패한 모든 작업의 수입니다.|차원 없음|
|d2c.telemetry.ingress.sendThrottle|제한 오류 수|카운트|합계|디바이스 처리량 제한으로 인한 제한 오류 수|차원 없음|
|dailyMessageQuotaUsed|사용된 전체 메시지 수|카운트|평균|오늘 사용된 전체 메시지 수입니다. 매일 00:00 UTC에 0으로 다시 설정되는 누적 값입니다.|차원 없음|
|deviceDataUsage|총 장치 데이터 사용|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|totalDeviceCount|총 디바이스(미리 보기)|카운트|평균|IoT 허브에 등록된 디바이스 수|차원 없음|
|connectedDeviceCount|연결된 디바이스(미리 보기)|카운트|평균|IoT 허브에 연결된 디바이스 수|차원 없음|
|구성|구성 메트릭|카운트|합계|구성 작업에 대한 메트릭|차원 없음|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|RegistrationAttempts|등록 시도|카운트|합계|디바이스 등록 시도 수|ProvisioningServiceName, IotHubName, 상태|
|DeviceAssignments|할당된 디바이스|카운트|합계|IoT Hub에 할당된 디바이스 수|ProvisioningServiceName, IotHubName|
|AttestationAttempts|증명 시도|카운트|합계|디바이스 증명 시도 수|ProvisioningServiceName, 상태, 프로토콜|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|AvailableStorage|사용 가능한 스토리지|바이트|합계|5 분 간격 보고 사용 가능한 총 저장소|CollectionName, DatabaseName, 지역|
|CassandraConnectionClosures|Cassandra 연결 차단|카운트|합계|1 분 간격 보고 종결 된 Cassandra 연결 수|ClosureReason 지역|
|CassandraRequestCharges|Cassandra 요청 요금|카운트|합계|Cassandra 요청에 대 한 Ru|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra 요청|카운트|카운트|Cassandra 요청 수|DatabaseName, CollectionName, 지역, OperationType, ResourceType, 오류 코드|
|DataUsage|데이터 사용량|바이트|합계|5 분 간격 보고 된 총 데이터 사용|CollectionName, DatabaseName, 지역|
|DocumentCount|문서 수|카운트|합계|5 분 간격 보고 된 총 문서 수|CollectionName, DatabaseName, 지역|
|DocumentQuota|문서 할당량|바이트|합계|5 분 간격 보고 하는 총 저장소 할당량|CollectionName, DatabaseName, 지역|
|IndexUsage|인덱스 사용량|바이트|합계|5 분 간격 보고 하는 총 인덱스 사용|CollectionName, DatabaseName, 지역|
|MetadataRequests|메타데이터 요청|카운트|카운트|메타데이터 요청 수. Cosmos DB는 컬렉션, 데이터베이스 등과 해당 구성을 무료로 열거할 수 있는 각 계정에 대한 시스템 메타데이터 컬렉션을 유지 관리합니다.|CollectionName, DatabaseName, 지역, StatusCode, |
|MongoRequestCharge|Mongo 요청 요금|카운트|합계|사용된 Mongo 요청 단위|DatabaseName, CollectionName, 지역, CommandName, ErrorCode|
|MongoRequests|Mongo 요청|카운트|카운트|생성된 Mongo 요청 수|DatabaseName, CollectionName, 지역, CommandName, ErrorCode|
|ProvisionedThroughput|프로비전된 처리량|카운트|최대|프로비전된 처리량|DatabaseName, CollectionName|
|ReplicationLatency|P99 복제 대기 시간|밀리초|평균|지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간|SourceRegion, TargetRegion|
|ServiceAvailability|서비스 가용성|백분율|평균|1 시간, 일 또는 월 간격 계정 요청 가용성|차원 없음|
|TotalRequestUnits|총 요청 단위|카운트|합계|사용된 요청 단위|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|총 요청 수|카운트|카운트|요청 수|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|카운트|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishFailCount|실패 한 이벤트를 게시 합니다.|카운트|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|UnmatchedEventCount|일치하지 않는 이벤트|카운트|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|카운트|합계|성공 대기 시간 (밀리초)에 게시|차원 없음|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|MatchedEventCount|일치하는 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 이벤트|차원 없음|
|DeliveryAttemptFailCount|배달 실패 이벤트|카운트|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|Error, ErrorType|
|DeliverySuccessCount|배달된 이벤트|카운트|합계|이 이벤트 구독에 배달된 총 이벤트|차원 없음|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|차원 없음|
|DroppedEventCount|삭제된 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason|
|DeadLetteredCount|배달 못한 편지 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|카운트|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishFailCount|실패된 이벤트|카운트|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|UnmatchedEventCount|일치하지 않는 이벤트|카운트|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|카운트|합계|성공 대기 시간 (밀리초)에 게시|차원 없음|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청|카운트|합계|Microsoft.EventHub에 대한 성공한 요청.|EntityName, |
|ServerErrors|서버 오류.|카운트|합계|Microsoft.EventHub에 대한 서버 오류.|EntityName, |
|UserErrors|사용자 오류.|카운트|합계|Microsoft.EventHub에 대한 사용자 오류.|EntityName, |
|QuotaExceededErrors|할당량 초과 오류.|카운트|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|EntityName, |
|ThrottledRequests|제한된 요청.|카운트|합계|Microsoft.EventHub에 대한 제한된 요청.|EntityName, |
|IncomingRequests|들어오는 요청|카운트|합계|Microsoft.EventHub에 대한 들어오는 요청.|EntityName|
|IncomingMessages|들어오는 메시지|카운트|합계|Microsoft.EventHub에 대한 들어오는 메시지.|EntityName|
|OutgoingMessages|보내는 메시지|카운트|합계|Microsoft.EventHub에 대한 보내는 메시지.|EntityName|
|IncomingBytes|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|EntityName|
|OutgoingBytes|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|EntityName|
|ActiveConnections|ActiveConnections|카운트|평균|Microsoft.EventHub에 대한 총 활성 연결.|차원 없음|
|ConnectionsOpened|열린 연결.|카운트|평균|Microsoft.EventHub에 대한 열린 연결.|EntityName|
|ConnectionsClosed|끊어진 연결.|카운트|평균|Microsoft.EventHub에 대한 끊어진 연결.|EntityName|
|CaptureBacklog|캡처 백로그.|카운트|합계|Microsoft.EventHub에 대한 캡처 백로그.|EntityName|
|CapturedMessages|캡처된 메시지.|카운트|합계|Microsoft.EventHub에 대한 캡처된 메시지.|EntityName|
|CapturedBytes|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|EntityName|
|크기|크기|바이트|평균|EventHub 크기(바이트)|EntityName|
|INREQS|들어오는 요청 (사용 되지 않음)|카운트|합계|총 들어오는 전송 요청 네임 스페이스 (사용 되지 않음)|차원 없음|
|SUCCREQ|성공한 요청 수 (사용 되지 않음)|카운트|합계|네임 스페이스 (사용 되지 않음)에 대 한 총 성공한 요청|차원 없음|
|FAILREQ|실패 한 요청 (사용 되지 않음)|카운트|합계|네임 스페이스 (사용 되지 않음)에 대 한 총 실패 한 요청|차원 없음|
|SVRBSY|서버 작업 중 오류 (사용 되지 않음)|카운트|합계|네임 스페이스 (사용 되지 않음)에 대 한 총 서버 작업 중 오류|차원 없음|
|INTERR|내부 서버 오류 (사용 되지 않음)|카운트|합계|네임 스페이스 (사용 되지 않음)에 대 한 총 내부 서버 오류|차원 없음|
|MISCERR|다른 오류 (사용 되지 않음)|카운트|합계|네임 스페이스 (사용 되지 않음)에 대 한 총 실패 한 요청|차원 없음|
|INMSGS|(사용 되지 않음)는 들어오는 메시지 (사용 되지 않음)|카운트|합계|네임스페이스에 들어오는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 들어오는 메시지 메트릭을 사용 하세요 (사용 되지 않음)|차원 없음|
|EHINMSGS|들어오는 메시지(사용되지 않음)|카운트|합계|네임 스페이스 (사용 되지 않음)에 대 한 총 들어오는 메시지|차원 없음|
|OUTMSGS|(사용 되지 않음)는 나가는 메시지 (사용 되지 않음)|카운트|합계|네임스페이스에 보내는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 보내는 메시지 메트릭을 사용 하세요 (사용 되지 않음)|차원 없음|
|EHOUTMSGS|보내는 메시지(사용되지 않음)|카운트|합계|네임 스페이스 (사용 되지 않음)에 대 한 메시지를 보내는 총|차원 없음|
|EHINMBS|들어오는 바이트 (사용 되지 않음) (사용 되지 않음)|바이트|합계|네임스페이스에 들어오는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 들어오는 바이트 메트릭을 사용 하세요 (사용 되지 않음)|차원 없음|
|EHINBYTES|들어오는 바이트(사용되지 않음)|바이트|합계|(사용 되지 않음) 네임 스페이스에 event Hub 들어오는 메시지 처리량|차원 없음|
|EHOUTMBS|나가는 바이트 (사용 되지 않음) (사용 되지 않음)|바이트|합계|네임스페이스에 보내는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 보내는 바이트 메트릭을 사용 하세요 (사용 되지 않음)|차원 없음|
|EHOUTBYTES|보내는 바이트(사용되지 않음)|바이트|합계|(사용 되지 않음) 네임 스페이스에 event Hub 나가는 메시지 처리량|차원 없음|
|EHABL|보관 백로그 메시지 (사용 되지 않음)|카운트|합계|이벤트 허브 네임 스페이스 (사용 되지 않음)에 대 한 백로그 메시지 보관|차원 없음|
|EHAMSGS|메시지 보관 (사용 되지 않음)|카운트|합계|이벤트 허브에에서 보관 된 메시지 (사용 되지 않음) 네임 스페이스|차원 없음|
|EHAMBS|보관 메시지 처리량 (사용 되지 않음)|바이트|합계|이벤트 허브에서에서 보관 된 메시지 처리량 네임 스페이스 (사용 되지 않음)|차원 없음|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청(미리 보기)|카운트|합계|Microsoft.EventHub에 대한 성공한 요청. (미리 보기)|차원 없음|
|ServerErrors|서버 오류. (미리 보기)|카운트|합계|Microsoft.EventHub에 대한 서버 오류. (미리 보기)|차원 없음|
|UserErrors|사용자 오류. (미리 보기)|카운트|합계|Microsoft.EventHub에 대한 사용자 오류. (미리 보기)|차원 없음|
|QuotaExceededErrors|할당량 초과 오류. (미리 보기)|카운트|합계|Microsoft.EventHub에 대한 할당량 초과 오류. (미리 보기)|차원 없음|
|ThrottledRequests|제한된 요청. (미리 보기)|카운트|합계|Microsoft.EventHub에 대한 제한된 요청. (미리 보기)|차원 없음|
|IncomingRequests|들어오는 요청(미리 보기)|카운트|합계|Microsoft.EventHub에 대한 들어오는 요청. (미리 보기)|차원 없음|
|IncomingMessages|들어오는 메시지(미리 보기)|카운트|합계|Microsoft.EventHub에 대한 들어오는 메시지. (미리 보기)|차원 없음|
|OutgoingMessages|보내는 메시지(미리 보기)|카운트|합계|Microsoft.EventHub에 대한 보내는 메시지. (미리 보기)|차원 없음|
|IncomingBytes|들어오는 바이트 (미리 보기)|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트. (미리 보기)|차원 없음|
|OutgoingBytes|보내는 바이트 (미리 보기)|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트. (미리 보기)|차원 없음|
|ActiveConnections|활성 연결(미리 보기)|카운트|평균|Microsoft.EventHub에 대한 총 활성 연결. (미리 보기)|차원 없음|
|ConnectionsOpened|열린 연결. (미리 보기)|카운트|평균|Microsoft.EventHub에 대한 열린 연결. (미리 보기)|차원 없음|
|ConnectionsClosed|끊어진 연결. (미리 보기)|카운트|평균|Microsoft.EventHub에 대한 끊어진 연결. (미리 보기)|차원 없음|
|CaptureBacklog|캡처 백로그. (미리 보기)|카운트|합계|Microsoft.EventHub에 대한 캡처 백로그. (미리 보기)|차원 없음|
|CapturedMessages|캡처된 메시지. (미리 보기)|카운트|합계|Microsoft.EventHub에 대한 캡처된 메시지. (미리 보기)|차원 없음|
|CapturedBytes|캡처된 바이트. (미리 보기)|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트. (미리 보기)|차원 없음|
|CPU|CPU(미리 보기)|백분율|최대|이벤트 허브 클러스터에 대한 CPU 사용률(백분율)|역할|
|AvailableMemory|사용 가능한 메모리(미리 보기)|카운트|최대|이벤트 허브 클러스터에 대해 사용 가능한 메모리(바이트)|역할|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|GatewayRequests|게이트웨이 요청|카운트|합계|게이트웨이 요청 수|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|분류된 게이트웨이 요청|카운트|합계|범주별 게이트웨이 요청 수(1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|활성 작업자 수|카운트|최대|활성 작업자 수|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ObservedMetricValue|관찰된 메트릭 값|카운트|평균|실행될 때 자동 크기 조정에서 계산된 값|MetricTriggerSource|
|MetricThreshold|메트릭 임계값|카운트|평균|자동 크기 조정이 실행되었을 때 구성된 자동 크기 조정 임계값입니다.|MetricTriggerRule|
|ObservedCapacity|관찰된 용량|카운트|평균|실행될 때 자동 크기 조정을 위해 보고된 용량입니다.|차원 없음|
|ScaleActionsInitiated|시작된 크기 조정 작업|카운트|합계|크기 조정 작업의 방향입니다.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(공개 미리 보기)

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|가용성|백분율|평균|성공적으로 완료 된 가용성 테스트의 백분율|availabilityResult/이름 availabilityResult/위치|
|availabilityResults/count|가용성 테스트|카운트|카운트|가용성 테스트 개수|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|가용성 테스트 기간|밀리초|평균|가용성 테스트 기간|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|페이지 로드 네트워크 연결 시간|밀리초|평균|사용자 요청과 네트워크 연결 사이의 시간입니다. DNS 조회 및 전송 연결을 포함합니다.|차원 없음|
|browserTimings/processingDuration|클라이언트 처리 시간|밀리초|평균|DOM을 로드할 때부터 문서의 마지막 바이트를 받는 사이의 시간입니다. 비동기 요청은 계속 처리 중일 수 있습니다.|차원 없음|
|browserTimings/receiveDuration|응답 수신 시간|밀리초|평균|첫 번째 및 마지막 바이트 사이 또는 연결 끊기까지의 시간입니다.|차원 없음|
|browserTimings/sendDuration|요청 전송 시간|밀리초|평균|네트워크 연결과 첫 번째 바이트 받기 사이의 시간입니다.|차원 없음|
|browserTimings/totalDuration|브라우저 페이지 로드 시간|밀리초|평균|사용자가 요청한 때부터 DOM, 스타일시트, 스크립트 및 이미지가 로드될 때까지 소요된 시간입니다.|차원 없음|
|dependencies/count|종속성 호출|카운트|카운트|외부 리소스에 대해 애플리케이션이 만든 호출 수입니다.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|종속성 기간|밀리초|평균|외부 리소스에 대한 서버 애플리케이션의 호출 기간입니다.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|종속성 호출 실패|카운트|카운트|외부 리소스에 대해 애플리케이션이 만든 실패한 종속성 호출 수입니다.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|페이지 보기|카운트|카운트|페이지 보기 수입니다.|operation/synthetic|
|pageViews/duration|페이지 보기 로드 시간|밀리초|평균|페이지 보기 로드 시간|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP 요청 실행 시간|밀리초|평균|가장 최근 요청의 실행 시간입니다.|cloud/roleInstance|
|performanceCounters/requestsInQueue|애플리케이션 큐의 HTTP 요청|카운트|평균|애플리케이션 요청 큐의 길이입니다.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 요청 속도|초당 개수|평균|ASP.NET에서 애플리케이션에 전송된 모든 요청의 속도(초)입니다.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|예외 속도|초당 개수|평균|.NET 예외 및 .NET 예외로 변환된 관리되지 않는 예외를 포함하여 Windows에 보고된 처리된 예외 및 처리되지 않은 예외 수입니다.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|프로세스 IO 속도|초당 바이트 수|평균|파일, 네트워크 및 디바이스에서 읽고 쓴 초당 총 바이트 수입니다.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU 프로세스|백분율|평균|모든 프로세스 스레드가 명령을 실행하기 위해 프로세서를 사용한 경과된 시간의 백분율입니다. 0~100 사이로 달라질 수 있습니다. 이 메트릭은 w3wp 프로세스만의 성능을 나타냅니다.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|프로세서 시간|백분율|평균|프로세서가 비 유휴 스레드에 소요한 시간의 비율입니다.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|사용 가능한 메모리|바이트|평균|프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리입니다.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|프로세스 프라이빗 바이트|바이트|평균|모니터링되는 애플리케이션의 프로세스에 독점적으로 할당된 메모리입니다.|cloud/roleInstance|
|requests/duration|서버 응답 시간|밀리초|평균|HTTP 요청을 받은 후 응답 전송을 완료한 때까지의 시간입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|서버 요청|카운트|카운트|완료된 HTTP 요청 수입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|실패한 요청|카운트|카운트|실패한 것으로 표시된 HTTP 요청 수입니다. 대부분의 경우 응답 코드가 >= 400이고 401과 같지 않은 요청입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|서버 요청 속도|초당 개수|평균|서버 요청 수 / 초 수.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|예외|카운트|카운트|모든 Catch되지 않은 예외의 결합된 수입니다.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|브라우저 예외|카운트|카운트|브라우저에서 발생한 확인할 수 없는 예외의 개수입니다.|차원 없음|
|exceptions/server|서버 예외|카운트|카운트|서버 애플리케이션에서 발생된 확인할 수 없는 예외의 수입니다.|cloud/roleName, cloud/roleInstance|
|traces/count|추적|카운트|카운트|문서 개수 추적|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ServiceApiHit|Service API 총 방문 횟수|카운트|카운트|Service API의 총 방문 횟수|ActivityType, ActivityName|
|ServiceApiLatency|전체 Service API 대기 시간|밀리초|평균|Service API 요청의 전체 대기 시간|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Service API 총 결과|카운트|카운트|Service API의 총 결과 수|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|캐시 사용률|백분율|평균|클러스터 범위의 사용률 수준|차원 없음|
|QueryDuration|쿼리 기간|밀리초|평균|쿼리 기간(초)|QueryStatus|
|IngestionsLoadFactor|수집 사용률|백분율|평균|클러스터에서 사용되는 수집 슬롯의 비율|차원 없음|
|IsEngineAnsweringQuery|활성 유지|카운트|평균|온전성 검사는 쿼리에 대한 클러스터 응답을 나타냅니다.|차원 없음|
|IngestCommandOriginalSizeInMb|수집 볼륨(MB)|카운트|합계|클러스터에 수집된 데이터의 전체 볼륨(MB)|차원 없음|
|IngestedEventAgeSeconds|수집 대기 시간(초)|초|평균|원본(예: EventHub에 있는 메시지)에서 클러스터로 수집 시간(초)|차원 없음|
|EventRecievedFromEventHub|이벤트 처리됨(Event Hubs)|카운트|합계|이벤트 허브에서 수집하는 경우 클러스터에서 처리된 이벤트 수|차원 없음|
|IngestionResult|수집 결과|카운트|카운트|수집 작업 수|IngestionResultDetails|
|EngineCPU|CPU|백분율|평균|CPU 사용률 수준|차원 없음|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|사용 현황|사용 현황|카운트|카운트|API 호출 수|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|RunsStarted|실행 시작됨|카운트|합계|실행 시작된 워크플로 수|차원 없음|
|RunsCompleted|실행 완료됨|카운트|합계|실행 완료된 워크플로 수|차원 없음|
|RunsSucceeded|실행 성공함|카운트|합계|실행 성공한 워크플로 수|차원 없음|
|RunsFailed|실행 실패함|카운트|합계|실행 실패한 워크플로 수|차원 없음|
|RunsCancelled|실행 취소 됨|카운트|합계|취소 실행 워크플로의 수입니다.|차원 없음|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|차원 없음|
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|차원 없음|
|RunThrottledEvents|실행 제한 이벤트|카운트|합계|워크플로 작업 또는 트리거 제한 이벤트 수|차원 없음|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|차원 없음|
|ActionsStarted|작업 시작됨 |카운트|합계|시작된 워크플로 작업 수|차원 없음|
|ActionsCompleted|작업 완료됨 |카운트|합계|완료된 워크플로 작업 수|차원 없음|
|ActionsSucceeded|작업 성공함 |카운트|합계|성공한 워크플로 작업 수|차원 없음|
|ActionsFailed|작업 실패함|카운트|합계|실패한 워크플로 작업 수|차원 없음|
|ActionsSkipped|작업 생략됨 |카운트|합계|생략한 워크플로 작업 수|차원 없음|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|차원 없음|
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|차원 없음|
|ActionThrottledEvents|작업 제한 이벤트|카운트|합계|워크플로 작업 제한 이벤트 수|차원 없음|
|TriggersStarted|트리거 시작됨 |카운트|합계|시작된 워크플로 트리거 수|차원 없음|
|TriggersCompleted|트리거 완료됨 |카운트|합계|완료된 워크플로 트리거 수|차원 없음|
|TriggersSucceeded|트리거 성공함 |카운트|합계|성공한 워크플로 트리거 수|차원 없음|
|TriggersFailed|트리거 실패함 |카운트|합계|실패한 워크플로 트리거 수|차원 없음|
|TriggersSkipped|트리거 생략함|카운트|합계|생략한 워크플로 트리거 수|차원 없음|
|TriggersFired|트리거 실행됨 |카운트|합계|실행한 워크플로 트리거 수|차원 없음|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|차원 없음|
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|차원 없음|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|차원 없음|
|TriggerThrottledEvents|트리거 제한 이벤트|카운트|합계|워크플로 트리거 제한 이벤트 수|차원 없음|
|BillableActionExecutions|청구 가능한 작업 실행|카운트|합계|청구되는 워크플로 작업 실행 수.|차원 없음|
|BillableTriggerExecutions|청구 가능한 트리거 실행|카운트|합계|청구되는 워크플로 트리거 실행 수.|차원 없음|
|TotalBillableExecutions|총 청구 가능 실행|카운트|합계|청구되는 워크플로 실행 수.|차원 없음|
|BillingUsageNativeOperation|기본 작업 실행에 대한 청구 사용량|카운트|합계|청구되는 기본 작업 실행 수입니다.|차원 없음|
|BillingUsageStandardConnector|표준 커넥터 실행에 대한 청구 사용량|카운트|합계|청구되는 표준 커넥터 실행 수입니다.|차원 없음|
|BillingUsageStorageConsumption|저장소 소비 실행에 대한 청구 사용량|카운트|합계|청구되는 저장소 소비 실행 수입니다.|차원 없음|
|BillingUsageNativeOperation|기본 작업 실행에 대한 청구 사용량|카운트|합계|청구되는 기본 작업 실행 수입니다.|차원 없음|
|BillingUsageStandardConnector|표준 커넥터 실행에 대한 청구 사용량|카운트|합계|청구되는 표준 커넥터 실행 수입니다.|차원 없음|
|BillingUsageStorageConsumption|저장소 소비 실행에 대한 청구 사용량|카운트|합계|청구되는 저장소 소비 실행 수입니다.|차원 없음|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|RunsStarted|실행 시작됨|카운트|합계|실행 시작된 워크플로 수|차원 없음|
|RunsCompleted|실행 완료됨|카운트|합계|실행 완료된 워크플로 수|차원 없음|
|RunsSucceeded|실행 성공함|카운트|합계|실행 성공한 워크플로 수|차원 없음|
|RunsFailed|실행 실패함|카운트|합계|실행 실패한 워크플로 수|차원 없음|
|RunsCancelled|실행 취소 됨|카운트|합계|취소 실행 워크플로의 수입니다.|차원 없음|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|차원 없음|
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|차원 없음|
|RunThrottledEvents|실행 제한 이벤트|카운트|합계|워크플로 작업 또는 트리거 제한 이벤트 수|차원 없음|
|RunStartThrottledEvents|실행 시작 제한 이벤트|카운트|합계|워크플로 실행 시작 제한 이벤트의 수입니다.|차원 없음|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|차원 없음|
|ActionsStarted|작업 시작됨 |카운트|합계|시작된 워크플로 작업 수|차원 없음|
|ActionsCompleted|작업 완료됨 |카운트|합계|완료된 워크플로 작업 수|차원 없음|
|ActionsSucceeded|작업 성공함 |카운트|합계|성공한 워크플로 작업 수|차원 없음|
|ActionsFailed|작업 실패함 |카운트|합계|실패한 워크플로 작업 수|차원 없음|
|ActionsSkipped|작업 생략됨 |카운트|합계|생략한 워크플로 작업 수|차원 없음|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|차원 없음|
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|차원 없음|
|ActionThrottledEvents|작업 제한 이벤트|카운트|합계|워크플로 작업 제한 이벤트 수|차원 없음|
|TriggersStarted|트리거 시작됨 |카운트|합계|시작된 워크플로 트리거 수|차원 없음|
|TriggersCompleted|트리거 완료됨 |카운트|합계|완료된 워크플로 트리거 수|차원 없음|
|TriggersSucceeded|트리거 성공함 |카운트|합계|성공한 워크플로 트리거 수|차원 없음|
|TriggersFailed|트리거 실패함 |카운트|합계|실패한 워크플로 트리거 수|차원 없음|
|TriggersSkipped|트리거 생략함|카운트|합계|생략한 워크플로 트리거 수|차원 없음|
|TriggersFired|트리거 실행됨 |카운트|합계|실행한 워크플로 트리거 수|차원 없음|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|차원 없음|
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|차원 없음|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|차원 없음|
|TriggerThrottledEvents|트리거 제한 이벤트|카운트|합계|워크플로 트리거 제한 이벤트 수|차원 없음|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|통합 서비스 환경의 워크플로 프로세서 사용량|백분율|평균|통합 서비스 환경의 워크플로 프로세서 사용량입니다.|차원 없음|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|통합 서비스 환경의 워크플로 메모리 사용량|백분율|평균|통합 서비스 환경의 워크플로 메모리 사용량입니다.|차원 없음|
|IntegrationServiceEnvironmentConnectorProcessorUsage|통합 서비스 환경의 커넥터 프로세서 사용량|백분율|평균|통합 서비스 환경의 커넥터 프로세서 사용량입니다.|차원 없음|
|IntegrationServiceEnvironmentConnectorMemoryUsage|통합 서비스 환경의 커텍터 메모리 사용량|백분율|평균|통합 서비스 환경의 커넥터 메모리 사용량입니다.|차원 없음|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|완료 된 실행|완료 된 실행|카운트|합계|이 작업 영역에 대해 성공적으로 완료 된 실행의 수|시나리오|
|실행 시작된|실행 시작된|카운트|합계|이 작업 영역에 대 한 시작 실행 수|시나리오|
|실패한 실행|실패한 실행|카운트|합계|이 작업 영역에 대 한 실패 한 실행 수|시나리오|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|사용 현황|사용 현황|카운트|카운트|API 호출 수|ApiCategory, ApiName, ResultType, ResponseCode|
|가용성|가용성|백분율|평균|Api의 가용성|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|AverageOtherLatency|평균 기타 대기 시간|ms/op|평균|작업당 평균 기타 대기 시간(읽기 또는 쓰기가 아님)(밀리초)|차원 없음|
|AverageReadLatency|평균 읽기 대기 시간|ms/op|평균|작업당 평균 읽기 대기 시간(밀리초)|차원 없음|
|AverageTotalLatency|평균 총 대기 시간|ms/op|평균|작업당 평균 총 대기 시간(밀리초)|차원 없음|
|AverageWriteLatency|평균 쓰기 대기 시간|ms/op|평균|작업당 평균 쓰기 대기 시간(밀리초)|차원 없음|
|FilesystemOtherOps|파일 시스템 기타 작업|작업|평균|파일 시스템 기타 작업의 수(읽기 또는 쓰기가 아님)|차원 없음|
|FilesystemReadOps|파일 시스템 읽기 작업|작업|평균|파일 시스템 읽기 작업의 수|차원 없음|
|FilesystemTotalOps|파일 시스템 총 작업|작업|평균|모든 파일 시스템 작업의 합계|차원 없음|
|FilesystemWriteOps|파일 시스템 쓰기 작업|작업|평균|파일 시스템 쓰기 작업의 수|차원 없음|
|IoBytesPerOtherOps|기타 작업당 Io 바이트|bytes/op|평균|기타 작업당 입력/출력 바이트의 수(읽기 또는 쓰기가 아님)|차원 없음|
|IoBytesPerReadOps|읽기 작업당 Io 바이트|bytes/op|평균|읽기 작업당 입력/출력 바이트의 수|차원 없음|
|IoBytesPerTotalOps|모든 작업에서 작업당 Io 바이트|bytes/op|평균|입력/출력 바이트 작업의 합계|차원 없음|
|IoBytesPerWriteOps|쓰기 작업당 Io 바이트|bytes/op|평균|쓰기 작업당 입력/출력 바이트의 수|차원 없음|
|OtherIops|기타 IOPS|operations/second|평균|초당 기타 입력/출력 작업|차원 없음|
|OtherThroughput|기타 처리량|MBps|평균|초당 기타 처리량(읽기 또는 쓰기가 아님)(메가바이트)|차원 없음|
|ReadIops|읽기 IOPS|operations/second|평균|초당 읽기 입력/출력 작업|차원 없음|
|ReadThroughput|읽기 처리량|MBps|평균|초당 읽기 처리량(메가바이트)|차원 없음|
|TotalIops|총 IOPS|operations/second|평균|초당 모든 입력/출력 작업의 합계|차원 없음|
|TotalThroughput|총 처리량|MBps|평균|초당 모든 처리량의 합계(메가바이트)|차원 없음|
|VolumeAllocatedSize|할당된 볼륨 크기|바이트|평균|볼륨의 할당된 크기(실제 사용되는 바이트가 아님)|차원 없음|
|VolumeLogicalSize|볼륨 논리적 크기|바이트|평균|볼륨의 논리적 크기(사용되는 바이트)|차원 없음|
|VolumeSnapshotSize|볼륨 스냅샷 크기|바이트|평균|볼륨의 모든 스냅샷 크기|차원 없음|
|WriteIops|쓰기 IOPS|operations/second|평균|초당 쓰기 입력/출력 작업|차원 없음|
|WriteThroughput|쓰기 처리량|MBps|평균|초당 쓰기 처리량(메가바이트)|차원 없음|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|할당된 볼륨 풀 크기|바이트|평균|풀의 할당된 크기(실제 사용되는 바이트가 아님)|차원 없음|
|VolumePoolAllocatedUsed|할당된 사용되는 볼륨 풀|바이트|평균|풀의 사용되는 할당된 크기|차원 없음|
|VolumePoolTotalLogicalSize|볼륨 풀 총 논리적 크기|바이트|평균|풀에 속한 모든 볼륨의 논리적 크기의 합계|차원 없음|
|VolumePoolTotalSnapshotSize|볼륨 풀 총 스냅샷 크기|바이트|평균|풀의 모든 스냅샷의 합계|차원 없음|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BytesSentRate|보낸 바이트|카운트|합계|네트워크 인터페이스가 보낸 바이트 수|차원 없음|
|BytesReceivedRate|수신된 바이트|카운트|합계|네트워크 인터페이스가 수신한 바이트 수|차원 없음|
|PacketsSentRate|보낸 패킷|카운트|합계|네트워크 인터페이스가 보낸 패킷 수|차원 없음|
|PacketsReceivedRate|수신된 패킷|카운트|합계|네트워크 인터페이스가 수신한 패킷 수|차원 없음|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|VipAvailability|데이터 경로 가용성|카운트|평균|기간당 평균 Load Balancer 데이터 경로 가용성|FrontendIPAddress, FrontendPort|
|DipAvailability|상태 프로브 상태|카운트|평균|기간당 평균 Load Balancer 상태 프로브 상태|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|바이트 수|카운트|합계|기간 내에 전송된 총 바이트 수|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|패킷 수|카운트|합계|기간 내에 전송된 총 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN 수|카운트|합계|기간 내에 전송된 총 SYN 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT 연결 수|카운트|합계|기간 내에 만들어진 새 SNAT 연결의 총 수|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|할당된 SNAT 포트(미리 보기)|카운트|합계|기간 내에 할당된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|사용된 SNAT 포트(미리 보기)|카운트|합계|기간 내에 사용된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QueryVolume|쿼리 볼륨|카운트|합계|DNS 영역에 대해 제공된 쿼리 수|차원 없음|
|RecordSetCount|레코드 집합 수|카운트|최대|DNS 영역의 레코드 집합 수|차원 없음|
|RecordSetCapacityUtilization|레코드 집합 용량 사용률|백분율|최대|DNS 영역에서 사용된 레코드 집합 용량 백분율|차원 없음|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|PacketsInDDoS|인바운드 패킷 DDoS|초당 개수|최대|인바운드 패킷 DDoS|차원 없음|
|PacketsDroppedDDoS|인바운드 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 패킷이 삭제된 DDoS|차원 없음|
|PacketsForwardedDDoS|인바운드 패킷이 전달된 DDoS|초당 개수|최대|인바운드 패킷이 전달된 DDoS|차원 없음|
|TCPPacketsInDDoS|인바운드 TCP 패킷 DDoS|초당 개수|최대|인바운드 TCP 패킷 DDoS|차원 없음|
|TCPPacketsDroppedDDoS|인바운드 TCP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 삭제된 DDoS|차원 없음|
|TCPPacketsForwardedDDoS|인바운드 TCP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 전달된 DDoS|차원 없음|
|UDPPacketsInDDoS|인바운드 UDP 패킷 DDoS|초당 개수|최대|인바운드 UDP 패킷 DDoS|차원 없음|
|UDPPacketsDroppedDDoS|인바운드 UDP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 삭제된 DDoS|차원 없음|
|UDPPacketsForwardedDDoS|인바운드 UDP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 전달된 DDoS|차원 없음|
|BytesInDDoS|인바운드 바이트 DDoS|초당 바이트 수|최대|인바운드 바이트 DDoS|차원 없음|
|BytesDroppedDDoS|인바운드 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 바이트가 삭제된 DDoS|차원 없음|
|BytesForwardedDDoS|인바운드 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 바이트가 전달된 DDoS|차원 없음|
|TCPBytesInDDoS|인바운드 TCP 바이트 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트 DDoS|차원 없음|
|TCPBytesDroppedDDoS|인바운드 TCP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 삭제된 DDoS|차원 없음|
|TCPBytesForwardedDDoS|인바운드 TCP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 전달된 DDoS|차원 없음|
|UDPBytesInDDoS|인바운드 UDP 바이트 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트 DDoS|차원 없음|
|UDPBytesDroppedDDoS|인바운드 UDP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 삭제된 DDoS|차원 없음|
|UDPBytesForwardedDDoS|인바운드 UDP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 전달된 DDoS|차원 없음|
|IfUnderDDoSAttack|DDoS 공격 진행 여부|카운트|최대|DDoS 공격 진행 여부|차원 없음|
|DDoSTriggerTCPPackets|DDoS 완화를 트리거하는 인바운드 TCP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 TCP 패킷|차원 없음|
|DDoSTriggerUDPPackets|DDoS 완화를 트리거하는 인바운드 UDP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 UDP 패킷|차원 없음|
|DDoSTriggerSYNPackets|DDoS 완화를 트리거하는 인바운드 SYN 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 SYN 패킷|차원 없음|
|VipAvailability|데이터 경로 가용성|카운트|평균|기간당 평균 IP 주소 가용성|포트|
|ByteCount|바이트 수|카운트|합계|기간 내에 전송된 총 바이트 수|포트, 방향|
|PacketCount|패킷 수|카운트|합계|기간 내에 전송된 총 패킷 수|포트, 방향|
|SynCount|SYN 수|카운트|합계|기간 내에 전송된 총 SYN 패킷 수|포트, 방향|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ApplicationRuleHit|응용 프로그램 규칙 적중 횟수|카운트|합계|응용 프로그램 규칙 적중 된 횟수|상태, 이유, 프로토콜|
|NetworkRuleHit|네트워크 규칙에 대 한 적중된 횟수|카운트|합계|네트워크 규칙 적중 된 횟수|상태, 이유, 프로토콜|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|처리량|처리량|초당 바이트 수|합계|Application Gateway에서 제공하는 초당 바이트 수|차원 없음|
|UnhealthyHostCount|비정상 호스트 수|카운트|평균|비정상 백 엔드 호스트 수|BackendSettingsPool|
|HealthyHostCount|정상 호스트 수|카운트|평균|정상 백 엔드 호스트 수|BackendSettingsPool|
|TotalRequests|총 요청 수|카운트|합계|Application Gateway가 제공하는 성공한 요청 수|BackendSettingsPool|
|FailedRequests|실패한 요청|카운트|합계|Application Gateway가 제공하는 실패한 요청 수|BackendSettingsPool|
|ResponseStatus|응답 상태|카운트|합계|Application Gateway에서 반환된 HTTP 응답 상태|HttpStatusGroup|
|CurrentConnections|현재 연결|카운트|합계|Application Gateway와 설정된 현재 연결 수|차원 없음|
|CapacityUnits|현재 용량 단위|카운트|평균|사용 된 용량 단위|차원 없음|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|AverageBandwidth|게이트웨이 S2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 사이트 간 대역폭(바이트)|차원 없음|
|P2SBandwidth|게이트웨이 P2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 지점 및 사이트 간 대역폭(바이트)|차원 없음|
|P2SConnectionCount|P2S 연결 수|카운트|최대|게이트웨이의 지점 및 사이트 간 연결 수|프로토콜|
|TunnelAverageBandwidth|터널 대역폭|초당 바이트 수|평균|초당 터널의 평균 대역폭(바이트)|ConnectionName, RemoteIP|
|TunnelEgressBytes|터널 송신 바이트|바이트|합계|터널의 송신 바이트|ConnectionName, RemoteIP|
|TunnelIngressBytes|터널 수신 바이트|바이트|합계|터널의 수신 바이트|ConnectionName, RemoteIP|
|TunnelEgressPackets|터널 송신 패킷|카운트|합계|터널의 송신 패킷 수|ConnectionName, RemoteIP|
|TunnelIngressPackets|터널 수신 패킷|카운트|합계|터널의 수신 패킷 수|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|터널 송신 TS 불일치 패킷 삭제|카운트|합계|터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|터널 수신 TS 불일치 패킷 삭제|카운트|합계|터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|차원 없음|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|차원 없음|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|차원 없음|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|차원 없음|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QpsByEndpoint|반환된 엔드포인트별 쿼리|카운트|합계|Traffic Manager 엔드포인트가 주어진 시간 내에 반환된 횟수|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|엔드포인트별 엔드포인트 상태|카운트|최대|엔드포인트의 프로브 상태가 "사용"이면 1이고 그렇지 않으면 0입니다.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ProbesFailedPercent|실패한 프로브 %|백분율|평균|실패한 연결 모니터링 프로브 %|차원 없음|
|AverageRoundtripMs|평균 왕복 시간(ms)|밀리초|평균|원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 왕복 시간(ms)|차원 없음|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|RequestCount|요청 수|카운트|합계|HTTP/S 프록시에서 제공하는 클라이언트 요청 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|요청 크기|바이트|합계|클라이언트에서 HTTP/S 프록시로 요청으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트로 응답으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|백 엔드 요청 수|카운트|합계|HTTP/S 프록시에서 백 엔드로 전송된 요청 수|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|백 엔드 요청 대기 시간|밀리초|평균|HTTP/S 프록시에서 백 엔드의 마지막 응답 바이트를 받을 때까지 HTTP/S 프록시에서 백 엔드로 요청이 전송될 때 계산된 시간|백 엔드|
|TotalLatency|총 대기 시간|밀리초|평균|클라이언트가 HTTP/S 프록시의 마지막 응답 바이트를 승인할 때까지 클라이언트 요청이 HTTP/S 프록시에서 수신될 때 계산된 시간|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|백 엔드 상태 비율|백분율|평균|HTTP/S 프록시에서 백 엔드로 성공한 상태 프로브의 비율|Backend, BackendPool|
|WebApplicationFirewallRequestCount|웹 애플리케이션 방화벽 요청 수|카운트|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|registration.all|등록 작업|카운트|합계|성공한 모든 등록 작업(만들기 업데이트 쿼리 및 삭제)입니다. |차원 없음|
|registration.create|등록 만들기 작업|카운트|합계|성공한 모든 등록 만들기의 수입니다.|차원 없음|
|registration.update|등록 업데이트 작업|카운트|합계|성공한 모든 등록 업데이트의 수입니다.|차원 없음|
|registration.get|등록 읽기 작업|카운트|합계|성공한 모든 등록 쿼리의 수입니다.|차원 없음|
|registration.delete|등록 삭제 작업|카운트|합계|성공한 모든 등록 삭제의 수입니다.|차원 없음|
|incoming|들어오는 메시지|카운트|합계|성공한 모든 API 호출 전송의 수입니다. |차원 없음|
|incoming.scheduled|전송된 예약된 푸시 알림|카운트|합계|예약 된 푸시 알림 취소|차원 없음|
|incoming.scheduled.cancel|예약 된 푸시 알림 취소|카운트|합계|예약 된 푸시 알림 취소|차원 없음|
|scheduled.pending|보류 중인 예약된 알림|카운트|합계|보류 중인 예약된 알림|차원 없음|
|installation.all|설치 관리 작업|카운트|합계|설치 관리 작업|차원 없음|
|installation.get|설치 작업 가져오기|카운트|합계|설치 작업 가져오기|차원 없음|
|installation.upsert|설치 작업 만들기 또는 업데이트|카운트|합계|설치 작업 만들기 또는 업데이트|차원 없음|
|installation.patch|설치 작업 패치|카운트|합계|설치 작업 패치|차원 없음|
|installation.delete|설치 작업 삭제|카운트|합계|설치 작업 삭제|차원 없음|
|outgoing.allpns.success|성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.allpns.invalidpayload|페이로드 오류|카운트|합계|PNS가 잘못된 페이로드 오류를 반환하기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.pnserror|외부 알림 시스템 오류|카운트|합계|PNS와 통신하는 데 문제(인증 문제 제외)가 있기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.channelerror|채널 오류|카운트|합계|채널이 잘못되었거나 제한 또는 만료된 올바른 앱과 연결되지 않았으므로 실패 한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.badorexpiredchannel|잘못되거나 만료된 채널 오류|카운트|합계|등록의 channel/token/registrationId가 만료되었거나 올바르지 않기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.wns.success|WNS 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.wns.invalidcredentials|WNS 권한 부여 오류(잘못된 자격 증명)|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다. (Windows Live는 자격 증명을 인식하지 못함)|차원 없음|
|outgoing.wns.badchannel|WNS 잘못된 채널 오류|카운트|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(WNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.wns.expiredchannel|WNS 만료된 채널 오류|카운트|합계|ChannelURI가 만료되어 실패한 푸시의 수입니다(WNS 상태: 410 없음).|차원 없음|
|outgoing.wns.throttled|WNS 제한된 알림|카운트|합계|WNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS 상태: 406 승인 금지).|차원 없음|
|outgoing.wns.tokenproviderunreachable|WNS 권한 부여 오류(연결할 수 없음)|카운트|합계|Windows Live에 연결할 수 없습니다.|차원 없음|
|outgoing.wns.invalidtoken|WNS 권한 부여 오류(잘못된 토큰)|카운트|합계|WNS에 제공한 토큰이 잘못되었습니다(WNS 상태: 401 권한 없음).|차원 없음|
|outgoing.wns.wrongtoken|WNS 권한 부여 오류(잘못된 토큰)|카운트|합계|WNS에 제공된 토큰은 유효하지만 다른 애플리케이션에 대해서는 유효하지 않습니다(WNS 상태: 403 사용 권한 없음). 등록의 ChannelURI가 다른 앱에 연결된 경우 이 문제가 발생할 수 있습니다. 클라이언트 앱은 자격 증명이 알림 허브에 있는 동일한 앱과 연결되어 있는지 확인합니다.|차원 없음|
|outgoing.wns.invalidnotificationformat|WNS 잘못된 알림 형식|카운트|합계|알림의 형식이 잘못되었습니다(WNS 상태: 400). WNS가 잘못된 모든 페이로드를 거부하지는 않습니다.|차원 없음|
|outgoing.wns.invalidnotificationsize|WNS 잘못된 알림 크기 오류|카운트|합계|알림 페이로드가 너무 큽니다(WNS 상태: 413).|차원 없음|
|outgoing.wns.channelthrottled|WNS 채널 제한|카운트|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-NotificationStatus:channelThrottled).|차원 없음|
|outgoing.wns.channeldisconnected|WNS 채널 연결 끊김|카운트|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.dropped|WNS 삭제된 알림|카운트|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.pnserror|WNS 오류|카운트|합계|WNS와의 통신 오류로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.wns.authenticationerror|WNS 인증 오류|카운트|합계|Windows Live와의 통신 오류(잘못된 자격 증명 또는 잘못된 토큰)로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.apns.success|APNS 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.apns.invalidcredentials|APNS 권한 부여 오류|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.apns.badchannel|APNS 잘못된 채널 오류|카운트|합계|토큰이 잘못되어 실패한 푸시의 수입니다(APNS 상태 코드: 8).|차원 없음|
|outgoing.apns.expiredchannel|APNS 만료된 채널 오류|카운트|합계|APNS 피드백 채널에서 무효화된 토큰의 수입니다.|차원 없음|
|outgoing.apns.invalidnotificationsize|APNS 잘못된 알림 크기 오류|카운트|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(APNS 상태 코드: 7).|차원 없음|
|outgoing.apns.pnserror|APNS 오류|카운트|합계|APNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.success|GCM 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.gcm.invalidcredentials|GCM 권한 부여 오류(잘못된 자격 증명)|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.badchannel|GCM 잘못된 채널 오류|카운트|합계|등록의 registrationId가 인식되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: 잘못된 등록).|차원 없음|
|outgoing.gcm.expiredchannel|GCM 만료된 채널 오류|카운트|합계|등록의 registrationId가 만료되었기 때문에 실패한 푸시의 수입니다(GCM 결과: NotRegistered).|차원 없음|
|outgoing.gcm.throttled|GCM 제한된 알림|카운트|합계|GCM이 이 앱을 제한하기 때문에 실패한 푸시의 수(GCM 상태 코드: 501-599 또는 결과: Unavailable).|차원 없음|
|outgoing.gcm.invalidnotificationformat|GCM 잘못된 알림 형식|카운트|합계|페이로드 형식이 올바르지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidDataKey 또는 InvalidTtl).|차원 없음|
|outgoing.gcm.invalidnotificationsize|GCM 잘못된 알림 크기 오류|카운트|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(GCM 결과: MessageTooBig).|차원 없음|
|outgoing.gcm.wrongchannel|GCM 잘못된 채널 오류|카운트|합계|등록의 registrationId가 현재 앱과 연결되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidPackageName).|차원 없음|
|outgoing.gcm.pnserror|GCM 오류|카운트|합계|GCM과의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.authenticationerror|GCM 인증 오류|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나, 자격 증명이 차단되었거나, 앱에서 SenderId가 올바르게 구성되지 않았기 때문에 실패한 푸시의 수입니다(GCM 결과: MismatchedSenderId).|차원 없음|
|outgoing.mpns.success|MPNS 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.mpns.invalidcredentials|MPNS 잘못된 자격 증명|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.badchannel|MPNS 잘못된 채널 오류|카운트|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(MPNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.mpns.throttled|MPNS 제한된 알림|카운트|합계|MPNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS MPNS: 406 승인 금지).|차원 없음|
|outgoing.mpns.invalidnotificationformat|MPNS 잘못된 알림 형식|카운트|합계|알림의 페이로드가 너무 커서 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.channeldisconnected|MPNS 채널 연결 끊김|카운트|합계|등록의 ChannelURI 연결이 끊어졌으므로 실패한 푸시의 수입니다(MPNS 상태: 412 찾을 수 없음).|차원 없음|
|outgoing.mpns.dropped|MPNS 삭제된 알림|카운트|합계|MPNS에서 삭제된 푸시의 수입니다(MPNS 응답 헤더: X-NotificationStatus: QueueFull 또는 Suppressed).|차원 없음|
|outgoing.mpns.pnserror|MPNS 오류|카운트|합계|MPNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.authenticationerror|MPNS 인증 오류|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|notificationhub.pushes|모든 나가는 알림 수|카운트|합계|알림 허브의 모든 나가는 알림 수|차원 없음|
|incoming.all.requests|들어오는 모든 요청|카운트|합계|알림 허브에 대해 들어오는 전체 요청|차원 없음|
|incoming.all.failedrequests|들어오는 모든 실패한 요청|카운트|합계|알림 허브에 대해 들어오는 실패한 전체 요청|차원 없음|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|Average_% Free Inodes|% 사용 가능한 Inodes|카운트|평균|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% 사용 가능한 공간|카운트|평균|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% 사용된 Inodes|카운트|평균|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% 사용된 공간|카운트|평균|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|디스크 읽기 바이트/초|카운트|평균|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|디스크 읽기/초|카운트|평균|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|디스크 전송/초|카운트|평균|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|디스크 쓰기 바이트/초|개수|평균|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|디스크 쓰기/초|개수|평균|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|사용 가능한 메가바이트|카운트|평균|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|논리 디스크 바이트/초|카운트|평균|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% 사용 가능한 메모리|카운트|평균|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% 사용 가능한 스왑 공간|카운트|평균|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% 사용된 메모리|카운트|평균|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% 사용된 스왑 공간|카운트|평균|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|사용 가능한 MB 메모리|카운트|평균|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|사용 가능한 MB 스왑|카운트|평균|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|페이지 읽기/초|카운트|평균|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|페이지 쓰기/초|카운트|평균|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|페이지/초|카운트|평균|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|사용된 MB 스왑 공간|카운트|평균|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|사용된 메모리 MB|카운트|평균|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|전송된 총 바이트|카운트|평균|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|받은 총 바이트|카운트|평균|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|총 바이트|카운트|평균|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|전송된 총 패킷|카운트|평균|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|받은 총 패킷|카운트|평균|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|총 Rx 오류|카운트|평균|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|총 Tx 오류|카운트|평균|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|총 충돌|카운트|평균|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|평균 디스크 초/읽기|카운트|평균|Average_Avg. 디스크 초/읽기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/전송|평균 디스크 초/전송|카운트|평균|Average_Avg. 디스크 초/전송|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|평균 디스크 초/쓰기|카운트|평균|Average_Avg. 디스크 초/쓰기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|물리적 디스크 바이트/초|카운트|평균|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct 권한이 부여된 시간|카운트|평균|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct 사용자 시간|카운트|평균|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|사용된 메모리 KB|카운트|평균|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|가상 공유 메모리|카운트|평균|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC 시간|카운트|평균|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% 유휴 시간|카운트|평균|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% 인터럽트 시간|카운트|평균|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO 대기 시간|카운트|평균|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice 시간|카운트|평균|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% 권한이 부여된 시간|카운트|평균|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|카운트|평균|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% 사용자 시간|카운트|평균|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|사용 가능한 실제 메모리|카운트|평균|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|페이징 파일에 사용 가능한 공간|카운트|평균|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|사용 가능한 가상 메모리|카운트|평균|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|프로세스|카운트|평균|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|페이징 파일에 저장된 크기|카운트|평균|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|작동 시간|카운트|평균|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|사용자|카운트|평균|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|평균 디스크 초/읽기|카운트|평균|Average_Avg. 디스크 초/읽기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|평균 디스크 초/쓰기|카운트|평균|Average_Avg. 디스크 초/쓰기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|현재 디스크 큐 길이|카운트|평균|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|디스크 읽기/초|카운트|평균|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|디스크 전송/초|카운트|평균|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|디스크 쓰기/초|개수|평균|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|사용 가능한 메가바이트|카운트|평균|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% 사용 가능한 공간|카운트|평균|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|카운트|평균|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|카운트|평균|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|카운트|평균|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|카운트|평균|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|카운트|평균|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|카운트|평균|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|프로세서 큐 길이|카운트|평균|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|카운트|합계|Heartbeat|Computer, OSType, Version, SourceComputerId|
|주 지역에서|주 지역에서|카운트|평균|주 지역에서|Computer, Product, Classification, UpdateState, Optional, Approved|
|행사|행사|카운트|평균|행사|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QueryDuration|쿼리 기간|밀리초|평균|마지막 간격의 DAX 쿼리 기간|차원 없음|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|카운트|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|
|qpu_high_utilization_metric|QPU 높은 사용률|카운트|합계|지난 1분 간의 QPU 높은 사용률로, QPU 사용률이 높으면 1, 그렇지 않으면 0|차원 없음|
|memory_metric|메모리|바이트|평균|메모리. A1은 0-3GB, A2는 0-5GB, A3는 0-10GB, A4는 0-25GB, A5는 0-50GB, A6는 0-100GB 범위|차원 없음|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|차원 없음|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|카운트|합계|Microsoft.Relay의 성공적인 ListenerConnections.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|카운트|합계|Microsoft.Relay의 총 ListenerConnections에서의 ClientError.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|카운트|합계|Microsoft.Relay의 ListenerConnections에서의 ServerError.|EntityName|
|SenderConnections-Success|SenderConnections-Success|카운트|합계|Microsoft.Relay의 성공적인 SenderConnections.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|카운트|합계|Microsoft.Relay의 SenderConnections에서 ClientError.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|카운트|합계|Microsoft.Relay의 SenderConnections에서의 ServerError.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|카운트|합계|Microsoft.Relay의 총 ListenerConnections.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|카운트|합계|Microsoft.Relay의 총 SenderConnections 요청.|EntityName|
|ActiveConnections|ActiveConnections|카운트|합계|Microsoft.Relay의 총 ActiveConnections.|EntityName|
|ActiveListeners|ActiveListeners|카운트|합계|Microsoft.Relay의 총 ActiveListeners.|EntityName|
|BytesTransferred|BytesTransferred|카운트|합계|Microsoft.Relay의 총 BytesTransferred.|EntityName|
|ListenerDisconnects|ListenerDisconnects|카운트|합계|Microsoft.Relay의 총 ListenerDisconnects.|EntityName|
|SenderDisconnects|SenderDisconnects|카운트|합계|Microsoft.Relay의 총 SenderDisconnects.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|SearchLatency|검색 대기 시간|초|평균|검색 서비스에 대한 평균 검색 대기 시간|차원 없음|
|SearchQueriesPerSecond|초당 검색 쿼리 수|초당 개수|평균|Search 서비스에 대한 초당 검색 쿼리|차원 없음|
|ThrottledSearchQueriesPercentage|제한된 검색 쿼리 백분율|백분율|평균|검색 서비스에 대해 제한된 검색 쿼리의 백분율|차원 없음|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청(미리 보기)|카운트|합계|네임스페이스에 대한 총 성공한 요청(미리 보기)|EntityName|
|ServerErrors|서버 오류. (미리 보기)|카운트|합계|Microsoft.ServiceBus에 대한 서버 오류. (미리 보기)|EntityName|
|UserErrors|사용자 오류. (미리 보기)|카운트|합계|Microsoft.ServiceBus에 대한 사용자 오류. (미리 보기)|EntityName|
|ThrottledRequests|제한된 요청. (미리 보기)|카운트|합계|Microsoft.ServiceBus에 대한 제한된 요청. (미리 보기)|EntityName|
|IncomingRequests|들어오는 요청(미리 보기)|카운트|합계|Microsoft.ServiceBus에 대한 들어오는 요청. (미리 보기)|EntityName|
|IncomingMessages|들어오는 메시지(미리 보기)|카운트|합계|Microsoft.ServiceBus에 대한 들어오는 메시지. (미리 보기)|EntityName|
|OutgoingMessages|보내는 메시지(미리 보기)|카운트|합계|Microsoft.ServiceBus에 대한 보내는 메시지. (미리 보기)|EntityName|
|ActiveConnections|활성 연결(미리 보기)|카운트|합계|Microsoft.ServiceBus에 대한 총 활성 연결. (미리 보기)|차원 없음|
|크기|크기(미리 보기)|바이트|평균|큐/토픽 크기(바이트) (미리 보기)|EntityName|
|메시지|큐/토픽에 있는 메시지 수 (미리 보기)|카운트|평균|큐/토픽에 있는 메시지 수 (미리 보기)|EntityName|
|ActiveMessages|큐/토픽에 있는 활성 메시지 수 (미리 보기)|카운트|평균|큐/토픽에 있는 활성 메시지 수 (미리 보기)|EntityName|
|DeadletteredMessages|큐/토픽의 배달 못한 메시지 수입니다. (미리 보기)|카운트|평균|큐/토픽의 배달 못한 메시지 수입니다. (미리 보기)|EntityName|
|ScheduledMessages|큐/토픽의 예약된 메시지 수입니다. (미리 보기)|카운트|평균|큐/토픽의 예약된 메시지 수입니다. (미리 보기)|EntityName|
|CPUXNS|네임스페이스당 CPU 사용량|백분율|최대|서비스 버스 프리미엄 네임스페이스 CPU 사용량 메트릭|차원 없음|
|WSXNS|네임스페이스당 메모리 크기 사용|백분율|최대|서비스 버스 프리미엄 네임스페이스 메모리 사용량 메트릭|차원 없음|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|카운트|평균|(밀리 코어)이이 컨테이너에 할당 된 Cpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|바이트|평균|(Mb)이이 컨테이너에 할당 된 메모리|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|카운트|평균|실제 CPU 사용량 (밀리 코어)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|바이트|평균|실제 메모리 사용량 (mb)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|백분율|평균|AllocatedCpu 비율로이 컨테이너에 대 한 cpu 사용률|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|백분율|평균|AllocatedCpu 비율로이 컨테이너에 대 한 cpu 사용률|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|카운트|평균|Service Fabric 망상 조직 응용 프로그램의 상태|ApplicationName, 상태|
|ServiceStatus|ServiceStatus|카운트|평균|Service Fabric 망상 조직 응용 프로그램에서 서비스의 상태|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|카운트|평균|Service Fabric 망상 조직 응용 프로그램에서 서비스 복제본의 상태|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|카운트|평균|Service Fabric 망상 조직 응용 프로그램에서 컨테이너의 상태|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|카운트|평균|Service Fabric 망상 조직 응용 프로그램에서 컨테이너의 개수를 다시 시작|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ConnectionCount|연결 수|카운트|최대|사용자 연결의 양.|엔드포인트|
|MessageCount|메시지 수|카운트|합계|총 메시지 양|차원 없음|
|InboundTraffic|인바운드 트래픽|바이트|합계|서비스의 인바운드 트래픽|차원 없음|
|OutboundTraffic|아웃바운드 트래픽|바이트|합계|서비스의 아웃바운드 트래픽|차원 없음|
|UserErrors|사용자 오류|백분율|최대|사용자 오류의 비율|차원 없음|
|SystemErrors|시스템 오류|백분율|최대|시스템 오류의 비율|차원 없음|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|차원 없음|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|차원 없음|
|저장소|사용되는 데이터 공간|바이트|최대|총 데이터베이스 크기|차원 없음|
|connection_successful|성공적인 연결|카운트|합계|성공적인 연결|차원 없음|
|connection_failed|실패한 연결|카운트|합계|실패한 연결|차원 없음|
|blocked_by_firewall|방화벽에 의해 차단|카운트|합계|방화벽에 의해 차단|차원 없음|
|교착 상태|교착 상태|카운트|합계|교착 상태|차원 없음|
|storage_percent|데이터 사용 공간 (%)|백분율|최대|데이터베이스 크기 비율|차원 없음|
|xtp_storage_percent|메모리 내 OLTP 저장소 백분율|백분율|평균|메모리 내 OLTP 저장소 백분율|차원 없음|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|차원 없음|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|차원 없음|
|dtu_limit|DTU 제한|카운트|평균|DTU 제한|차원 없음|
|dtu_used|DTU 사용됨|카운트|평균|DTU 사용됨|차원 없음|
|cpu_limit|CPU 제한|카운트|평균|CPU 제한|차원 없음|
|cpu_used|CPU 사용량|카운트|평균|CPU 사용량|차원 없음|
|dwu_limit|DWU 제한|카운트|최대|DWU 제한|차원 없음|
|dwu_consumption_percent|DWU 백분율|백분율|최대|DWU 백분율|차원 없음|
|dwu_used|DWU 사용됨|카운트|최대|DWU 사용됨|차원 없음|
|dw_cpu_percent|DW 노드 수준 CPU 비율|백분율|평균|DW 노드 수준 CPU 비율|DwLogicalNodeId|
|dw_physical_data_read_percent|DW 노드 수준 데이터 IO 백분율|백분율|평균|DW 노드 수준 데이터 IO 백분율|DwLogicalNodeId|
    |cache_hit_percent|캐시 적중 비율|백분율|최대|캐시 적중 비율|차원 없음|
|cache_used_percent|캐시 사용 비율|백분율|최대|캐시 사용 비율|차원 없음|
|local_tempdb_usage_percent|로컬 tempdb 백분율|백분율|평균|로컬 tempdb 백분율|차원 없음|
|app_cpu_billed|청구된 앱 CPU|카운트|합계|청구된 앱 CPU|차원 없음|
|app_cpu_percent|응용 프로그램 CPU 비율|백분율|평균|응용 프로그램 CPU 비율|차원 없음|
|app_memory_percent|앱 메모리 사용 비율|백분율|평균|앱 메모리 사용 비율|차원 없음|
|allocated_data_storage|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|차원 없음|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|차원 없음|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|차원 없음|
|storage_percent|데이터 사용 공간 (%)||백분율|평균|저장소 비율|차원 없음|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|차원 없음|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|차원 없음|
|eDTU_limit|eDTU 제한|카운트|평균|eDTU 제한|차원 없음|
|storage_limit|데이터의 최대 크기|바이트|평균|저장소 제한|차원 없음|
|eDTU_used|eDTU 사용|카운트|평균|eDTU 사용|차원 없음|
|storage_used|사용되는 데이터 공간|바이트|평균|저장소 사용됨|차원 없음|
|xtp_storage_percent|메모리 내 OLTP 저장소 백분율|백분율|평균|메모리 내 OLTP 저장소 백분율|차원 없음|
|cpu_limit|CPU 제한|카운트|평균|CPU 제한|차원 없음|
|cpu_used|CPU 사용량|카운트|평균|CPU 사용량|차원 없음|
|allocated_data_storage|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|차원 없음|
|allocated_data_storage_percent|데이터 할당 된 공간 (%)|백분율|최대|데이터 할당 된 공간 (%)|차원 없음|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|virtual_core_count|가상 코어 수|카운트|평균|가상 코어 수|차원 없음|
|avg_cpu_percent|평균 CPU 비율|백분율|평균|평균 CPU 비율|차원 없음|
|reserved_storage_mb|예약된 저장소 공간|카운트|평균|예약된 저장소 공간|차원 없음|
|storage_space_used_mb|사용된 저장소 공간|카운트|평균|사용된 저장소 공간|차원 없음|
|io_requests|IO 요청 수|카운트|평균|IO 요청 수|차원 없음|
|io_bytes_read|읽은 IO 바이트|바이트|평균|읽은 IO 바이트|차원 없음|
|io_bytes_written|기록된 IO 바이트|바이트|평균|기록된 IO 바이트|차원 없음|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|UsedCapacity|사용된 용량|바이트|평균|계정 사용 용량|차원 없음|
|트랜잭션|트랜잭션|카운트|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BlobCapacity|Blob 용량|바이트|평균|저장소 계정의 Blob service가 사용하는 저장소의 양(바이트)입니다.|BlobType, Tier|
|BlobCount|Blob 수|카운트|합계|저장소 계정의 Blob service에 있는 Blob 수입니다.|BlobType|       |BlobCount|Blob 수|카운트|평균|저장소 계정의 Blob service에 있는 Blob 수입니다.|BlobType, Tier|
|ContainerCount|Blob 컨테이너 수|카운트|평균|저장소 계정의 Blob service에 있는 컨테이너 수입니다.|차원 없음|
|IndexCapacity|인덱스 용량|바이트|평균|바이트에서 ADLS Gen2 (계층적) 인덱스에서 사용 하는 저장소 양입니다.|차원 없음|
|트랜잭션|트랜잭션|카운트|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|FileCapacity|파일 용량|바이트|평균|저장소 계정의 파일 서비스가 사용하는 저장소의 양(바이트)입니다.|차원 없음|
|FileCount|파일 수|카운트|평균|저장소 계정의 파일 서비스에 있는 파일 수입니다.|차원 없음|
|FileShareCount|파일 공유 수|카운트|평균|저장소 계정의 파일 서비스에 있는 파일 공유 수입니다.|차원 없음|
|트랜잭션|트랜잭션|카운트|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|QueueCapacity|큐 용량|바이트|평균|저장소 계정의 큐 서비스가 사용하는 저장소의 양(바이트)입니다.|차원 없음|
|QueueCount|큐 수|카운트|평균|저장소 계정의 큐 서비스에 있는 큐 수입니다.|차원 없음|
|QueueMessageCount|큐 메시지 수|카운트|평균|저장소 계정의 큐 서비스에 있는 대략적인 큐 메시지 수입니다.|차원 없음|
|트랜잭션|트랜잭션|카운트|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|TableCapacity|테이블 용량|바이트|평균|저장소 계정의 Table service가 사용하는 저장소의 양(바이트)입니다.|차원 없음|
|TableCount|테이블 수|카운트|평균|저장소 계정의 Table service에 있는 테이블 수입니다.|차원 없음|
|TableEntityCount|테이블 엔터티 수|카운트|평균|저장소 계정의 Table service에 있는 테이블 엔터티 수입니다.|차원 없음|
|트랜잭션|트랜잭션|카운트|합계|저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|가용성|가용성|백분율|평균|저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ServerSyncSessionResult|동기화 세션 결과|카운트|평균|메트릭 값 1에 각 로그 시간 서버 끝점에 성공적으로 완료 클라우드 끝점을 사용 하 여 동기화 세션|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|동기화된 파일 수|카운트|합계|수의 파일 동기화|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|동기화 상태가 아닌 파일|카운트|합계|파일 수를 동기화 하지 못함|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|동기화되는 바이트 수|바이트|합계|동기화 세션에 대 한 전송 된 총 파일 크기|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|서버 온라인 상태|카운트|최대|로그 값 1의 등록된 된 서버를 성공적으로 시간 메트릭 클라우드 끝점을 사용 하 여 하트 비트 레코드|데이터 열이 추적에서 캡처되고 서버를 사용할 수 있으면|
|StorageSyncRecallIOTotalSizeBytes|클라우드 계층화 회수|바이트|합계|서버에 의해 회수 하는 데이터의 총 크기|데이터 열이 추적에서 캡처되고 서버를 사용할 수 있으면|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 사용률|백분율|최대|SU % 사용률|LogicalName, PartitionId|
|InputEvents|입력 이벤트|카운트|합계|입력 이벤트|LogicalName, PartitionId|
|InputEventBytes|입력 이벤트 바이트|바이트|합계|입력 이벤트 바이트|LogicalName, PartitionId|
|LateInputEvents|늦은 입력 이벤트|카운트|합계|늦은 입력 이벤트|LogicalName, PartitionId|
|OutputEvents|출력 이벤트|카운트|합계|출력 이벤트|LogicalName, PartitionId|
|ConversionErrors|데이터 변환 오류|카운트|합계|데이터 변환 오류|LogicalName, PartitionId|
|오류|런타임 오류|카운트|합계|런타임 오류|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|잘못된 이벤트|카운트|합계|잘못된 이벤트|LogicalName, PartitionId|
|AMLCalloutRequests|기능 요청|카운트|합계|기능 요청|LogicalName, PartitionId|
|AMLCalloutFailedRequests|실패한 기능 요청|카운트|합계|실패한 기능 요청|LogicalName, PartitionId|
|AMLCalloutInputEvents|함수 이벤트|카운트|합계|함수 이벤트|LogicalName, PartitionId|
|DeserializationError|입력 역직렬화 오류|카운트|합계|입력 역직렬화 오류|LogicalName, PartitionId|
|EarlyInputEvents|초기 입력 이벤트|카운트|합계|초기 입력 이벤트|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|워터마크 지연|초|최대|워터마크 지연|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|백로그된 입력 이벤트|카운트|최대|백로그된 입력 이벤트|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|수신된 입력 원본|카운트|합계|수신된 입력 원본|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|카운트|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 메시지 수입니다.|차원 없음|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|카운트|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 잘못된 메시지 수입니다.|차원 없음|
|IngressReceivedBytes|수신된 바이트|바이트|합계|모든 이벤트 원본에서 읽은 바이트 수입니다.|차원 없음|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|저장된 수신 이벤트|카운트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|카운트|평균|이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이입니다.|차원 없음|
|WarmStorageMaxProperties|웜 저장소 Max 속성|카운트|최대|사용 되는 속성에 허용 된 최대 환경에서 S1/S2 SKU와 PAYG SKU에 대 한 웜 저장소에서 허용 하는 속성의 최대 수|차원 없음|
|WarmStorageUsedProperties|속성을 사용 하는 웜 저장소 |카운트|최대|S1/S2 SKU 및 다양 한 PAYG SKU에 대 한 웜 저장소에서 사용 하는 속성에 대 한 환경에서 사용 되는 속성 수|차원 없음|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|카운트|합계|이벤트 원본에서 읽은 메시지 수|차원 없음|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|카운트|합계|이벤트 원본에서 읽은 잘못된 메시지 수|차원 없음|
|IngressReceivedBytes|수신된 바이트|바이트|합계|이벤트 원본에서 읽은 바이트 수|차원 없음|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|저장된 수신 이벤트|카운트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|카운트|평균|이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이입니다.|차원 없음|
|WarmStorageMaxProperties|웜 저장소 Max 속성|카운트|최대|사용 되는 속성에 허용 된 최대 환경에서 S1/S2 SKU와 PAYG SKU에 대 한 웜 저장소에서 허용 하는 속성의 최대 수|차원 없음|
|WarmStorageUsedProperties|속성을 사용 하는 웜 저장소 |카운트|최대|S1/S2 SKU 및 다양 한 PAYG SKU에 대 한 웜 저장소에서 사용 하는 속성에 대 한 환경에서 사용 되는 속성 수|차원 없음|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|디스크 읽기 바이트/초|초당 바이트 수|평균|샘플 기간 동안 읽기 작업으로 인해 처리량이 평균 디스크.|차원 없음|
|DiskWriteBytesPerSecond|디스크 쓰기 바이트/초|초당 바이트 수|평균|샘플 기간 동안 쓰기 작업으로 인해 처리량이 평균 디스크.|차원 없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|샘플 기간 동안 읽기 작업으로 인해 총 디스크 처리량입니다.|차원 없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|샘플 기간 동안 쓰기 작업으로 인해 총 디스크 처리량입니다.|차원 없음|
|DiskReadOperations|디스크 읽기 작업|카운트|합계|IO 수가 이전 샘플 기간에 작업을 읽습니다. 이러한 작업 변수 크기의 수를 참고 합니다.|차원 없음|
|DiskWriteOperations|디스크 쓰기 작업|카운트|합계|IO 수가 이전 샘플 기간에 작업을 작성 합니다. 이러한 작업 변수 크기의 수를 참고 합니다.|차원 없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|평균 IO 읽기 이전 샘플 기간 동안 작업 합니다. 이러한 작업 변수 크기의 수를 참고 합니다.|차원 없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|평균 IO 쓰기 이전 샘플 기간에 작업입니다. 이러한 작업 변수 크기의 수를 참고 합니다.|차원 없음|
|DiskReadLatency|디스크 읽기 대기 시간|밀리초|평균|총 대기 시간을 읽기입니다. 장치 및 커널 총 대기 시간을 읽습니다.|차원 없음|
|DiskWriteLatency|디스크 쓰기 대기 시간|밀리초|평균|총 쓰기 대기 시간입니다. 장치 및 커널 총 쓰기 대기 시간입니다.|차원 없음|
|NetworkInBytesPerSecond|네트워크 바이트/초|초당 바이트 수|평균|받은 트래픽에 대 한 평균 네트워크 처리량입니다.|차원 없음|
|NetworkOutBytesPerSecond|네트워크 출력 바이트 수/초|초당 바이트 수|평균|전송 된 트래픽에 대 한 평균 네트워크 처리량입니다.|차원 없음|
|네트워크 인|네트워크 인|바이트|합계|받은 트래픽에 대 한 총 네트워크 처리량입니다.|차원 없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|전송 된 트래픽에 대 한 총 네트워크 처리량입니다.|차원 없음|
|MemoryUsed|사용 된 메모리|바이트|평균|VM에서 사용 되는 컴퓨터 메모리의 양입니다.|차원 없음|
|MemoryGranted|메모리 부여|바이트|평균|호스트에서 VM에 부여 된 메모리 양입니다. 한 번 연결 하 고 부여 메모리 스와핑할 되었거나는 VMkernel 메모리를 필요로 하는 경우 지금 ballooned 될 때까지 호스트에 메모리 부여 되지 않습니다.|차원 없음|
|MemoryActive|활성 메모리|바이트|평균|지난 짧은 기간에는 VM에서 사용 되는 메모리 양입니다. 이 메모리 양을 필요의 현재 VM의 "true"입니다. 추가, 사용 되지 않는 메모리 스와핑할 하거나 게스트의 성능에 영향을 미치지 않음 ballooned 수입니다.|차원 없음|
|백분율 CPU|백분율 CPU|백분율|평균|CPU 사용률입니다. 이 값은 100%를 나타내는 시스템에서 모든 프로세서 코어를 사용 하 여 보고 됩니다. 예를 들어 4 코어 시스템의 50%를 사용 하는 양방향 VM은 두 개의 코어를 완전히 사용 합니다.|차원 없음|
|PercentageCpuReady|백분율 CPU 준비|밀리초|합계|준비 시간은 cpu가 지난 업데이트 간격에 사용할 수 있으려면 대기 소요 된 시간이입니다.|차원 없음|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|카운트|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|카운트|평균|Http 큐 길이|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites(Functions 제외)

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|카운트|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|카운트|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|카운트|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|카운트|합계|Http 3xx|인스턴스|
|Http401|Http 401|카운트|합계|Http 401|인스턴스|
|Http403|Http 403|카운트|합계|Http 403|인스턴스|
|Http404|Http 404|카운트|합계|Http 404|인스턴스|
|Http406|Http 406|카운트|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|카운트|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|AppConnections|연결|카운트|평균|연결|인스턴스|
|핸들|핸들 개수|카운트|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|카운트|평균|스레드 개수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|카운트|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|카운트|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|카운트|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|카운트|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|카운트|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|카운트|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|카운트|합계|Gen 2 가비지 수집|인스턴스|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites(functions)

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|FunctionExecutionUnits|함수 실행 단위|MB / 시간 (밀리초)|합계|[함수 실행 단위](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|인스턴스|
|FunctionExecutionCount|함수 실행 횟수|카운트|합계|함수 실행 횟수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|카운트|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|카운트|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|카운트|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|카운트|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|카운트|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|카운트|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|카운트|합계|Gen 2 가비지 수집|인스턴스|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|카운트|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|카운트|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|카운트|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|카운트|합계|Http 3xx|인스턴스|
|Http401|Http 401|카운트|합계|Http 401|인스턴스|
|Http403|Http 403|카운트|합계|Http 403|인스턴스|
|Http404|Http 404|카운트|합계|Http 404|인스턴스|
|Http406|Http 406|카운트|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|카운트|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|FunctionExecutionUnits|함수 실행 단위|카운트|합계|함수 실행 단위|인스턴스|
|FunctionExecutionCount|함수 실행 횟수|카운트|합계|함수 실행 횟수|인스턴스|
|AppConnections|연결|카운트|평균|연결|인스턴스|
|핸들|핸들 개수|카운트|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|카운트|평균|스레드 개수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|카운트|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|카운트|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|카운트|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|카운트|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|카운트|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|카운트|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|카운트|합계|Gen 2 가비지 수집|인스턴스|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|요청|요청|카운트|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|카운트|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|카운트|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|카운트|합계|Http 3xx|인스턴스|
|Http401|Http 401|카운트|합계|Http 401|인스턴스|
|Http403|Http 403|카운트|합계|Http 403|인스턴스|
|Http404|Http 404|카운트|합계|Http 404|인스턴스|
|Http406|Http 406|카운트|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|카운트|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|카운트|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|카운트|평균|Http 큐 길이|인스턴스|
|ActiveRequests|활성 요청|카운트|합계|활성 요청|인스턴스|
|TotalFrontEnds|총 프런트 엔드|카운트|평균|총 프런트 엔드|차원 없음|
|SmallAppServicePlanInstances|소형 App Service 계획 작업자|카운트|평균|소형 App Service 계획 작업자|차원 없음|
|MediumAppServicePlanInstances|중형 App Service 계획 작업자|카운트|평균|중형 App Service 계획 작업자|차원 없음|
|LargeAppServicePlanInstances|대형 App Service 계획 작업자|카운트|평균|대형 App Service 계획 작업자|차원 없음|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|WorkersTotal|총 작업자|카운트|평균|총 작업자|차원 없음|
|WorkersAvailable|사용 가능한 작업자|카운트|평균|사용 가능한 작업자|차원 없음|
|WorkersUsed|사용된 작업자|카운트|평균|사용된 작업자|차원 없음|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|

## <a name="next-steps"></a>다음 단계
* [Azure Monitor의 메트릭에 대해 읽기](data-platform.md)
* [메트릭에 대한 경고 만들기](alerts-overview.md)
* [저장소, 이벤트 허브 또는 Log Analytics에 메트릭 내보내기](diagnostic-logs-overview.md)
