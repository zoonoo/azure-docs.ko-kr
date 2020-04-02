---
title: Azure Monitor에서 지원되는 리소스 종류별 메트릭
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 03/17/2020
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 674321878cfce2d05189700a8b5118e233d9044d
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520700"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor에서 지원되는 메트릭

Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 다음은 현재 Azure Monitor의 메트릭 파이프라인을 통해 사용할 수 있는 모든 메트릭의 전체 목록입니다. 레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다. 아래 목록에는 통합 Azure Monitor 메트릭 파이프라인을 통해 사용할 수 있는 메트릭만 포함됩니다. 메트릭은 네임스페이스별로 구성됩니다. 서비스 목록 및 해당 서비스에 속한 네임스페이스는 [Azure 서비스에 대한 리소스 공급자를](../../azure-resource-manager/management/azure-services-resource-providers.md)참조하십시오. 프로그래밍 방식으로 이러한 메트릭을 쿼리하고 액세스하려면 [2018-01-01 api 버전을](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) 사용하십시오.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
> 진단 설정을 통해 내보낼 수 있는 플랫폼 메트릭 목록은 [이 문서를](metrics-supported-export-diagnostic-settings.md)참조하십시오.






## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|qpu_metric|QPU|개수|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|ServerResourceType|
|memory_metric|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|ServerResourceType|
|private_bytes_metric|프라이빗 바이트|바이트|평균|전용 바이트입니다.|ServerResourceType|
|virtual_bytes_metric|가상 바이트|바이트|평균|가상 바이트.|ServerResourceType|
|TotalConnectionRequests|총 연결 요청 수|개수|평균|도착한 총 연결 요청 수입니다.|ServerResourceType|
|SuccessfullConnectionsPerSec|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|ServerResourceType|
|TotalConnectionFailures|총 연결 실패 수|개수|평균|실패한 총 연결 시도 수입니다.|ServerResourceType|
|CurrentUserSessions|현재 사용자 세션 수|개수|평균|현재 설정된 사용자 세션 수입니다.|ServerResourceType|
|QueryPoolBusyThreads|쿼리 풀의 사용 중인 스레드|개수|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolJobQueueLength|명령 풀의 작업 큐 길이|개수|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolJobQueueLength|처리 풀의 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|ServerResourceType|
|CurrentConnections|연결: 현재 연결|개수|평균|현재 설정된 클라이언트 연결 수입니다.|ServerResourceType|
|CleanerCurrentPrice|메모리: 클리너 현재 가격|개수|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|ServerResourceType|
|CleanerMemoryShrinkable|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|ServerResourceType|
|CleanerMemoryNonshrinkable|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|ServerResourceType|
|MemoryUsage|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 xVelocity 엔진 메모리 제한을 초과하여 xVelocity 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|ServerResourceType|
|MemoryLimitHard|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitHigh|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitLow|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitVertiPaq|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|ServerResourceType|
|할당량|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|ServerResourceType|
|QuotaBlocked|메모리: 차단된 할당량|개수|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|ServerResourceType|
|VertiPaqNonpaged|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|ServerResourceType|
|VertiPaqPaged|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|ServerResourceType|
|RowsReadPerSec|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|ServerResourceType|
|RowsConvertedPerSec|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|ServerResourceType|
|RowsWrittenPerSec|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|ServerResourceType|
|CommandPoolBusyThreads|스레드: 명령 풀 사용 중인 스레드|개수|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolIdleThreads|스레드: 명령 풀 유휴 상태 스레드|개수|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingBusyThreads|스레드: 긴 구문 분석 사용 중인 스레드|개수|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|LongParsingIdleThreads|스레드: 긴 구문 분석 유휴 상태 스레드|개수|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingJobQueueLength|스레드: 긴 구문 분석 작업 큐 길이|개수|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|스레드: 처리 풀 사용 중인 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|스레드: 처리 풀 사용 중인 비-I/O 스레드|개수|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|스레드: 처리 풀 I/O 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|스레드: 처리 풀 유휴 상태 비-I/O 스레드|개수|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolIdleThreads|스레드: 쿼리 풀 유휴 상태 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ShortParsingBusyThreads|스레드: 짧은 구문 분석 사용 중인 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|ShortParsingIdleThreads|스레드: 짧은 구문 분석 유휴 상태 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ShortParsingJobQueueLength|스레드: 짧은 구문 분석 작업 큐 길이|개수|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|ServerResourceType|
|mashup_engine_qpu_metric|M 엔진 QPU|개수|평균|매시업 엔진 프로세스별 QPU 사용량|ServerResourceType|
|mashup_engine_memory_metric|M 엔진 메모리|바이트|평균|매시업 엔진 프로세스별 메모리 사용량|ServerResourceType|
|mashup_engine_private_bytes_metric|M 엔진 프라이빗 바이트|바이트|평균|매시업 엔진 프로세스에 의한 개인 바이트 사용.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M 엔진 버추얼 바이트|바이트|평균|매시업 엔진 프로세스에 의한 가상 바이트 사용.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalRequests|총 게이트웨이 요청(더 이상 사용되지 않는)|개수|합계|게이트웨이 요청 수 - 게이트웨이응답코드코드범주 차원대신 다중 차원 요청 메트릭 사용|위치, 호스트 이름|
|SuccessfulRequests|성공적인 게이트웨이 요청(더 이상 사용되지 않는)|개수|합계|성공적인 게이트웨이 요청 수 - 대신 GatewayResponseCodeCategory 차원을 사용하여 다중 차원 요청 메트릭사용|위치, 호스트 이름|
|UnauthorizedRequests|무단 게이트웨이 요청(더 이상 사용되지 않음)|개수|합계|무단 게이트웨이 요청 수 - 대신 GatewayResponseCodeCategory 차원을 사용하여 다중 차원 요청 메트릭사용|위치, 호스트 이름|
|FailedRequests|실패한 게이트웨이 요청(더 이상 사용되지 않는)|개수|합계|게이트웨이 요청의 실패 수 - 게이트웨이ResponseCodeCategory 차원대신 다중 차원 요청 메트릭 사용|위치, 호스트 이름|
|OtherRequests|기타 게이트웨이 요청(더 이상 사용되지 않는)|개수|합계|다른 게이트웨이 요청 수 - 대신 GatewayResponseCodeCategory 차원을 사용하여 다중 차원 요청 메트릭사용|위치, 호스트 이름|
|Duration|게이트웨이 요청의 전체 기간|밀리초|평균|게이트웨이 요청의 전체 기간(밀리초)|위치, 호스트 이름|
|백엔드지속시간|백 엔드 요청 의 기간|밀리초|평균|백 엔드 요청의 시간(밀리초)|위치, 호스트 이름|
|용량|용량|백분율|평균|ApiManagement 서비스에 대한 사용률 메트릭|위치|
|이벤트허브토탈이벤트|총 이벤트 허브 이벤트|개수|합계|EventHub로 전송된 이벤트 수|위치|
|이벤트허브성공이벤트|성공적인 이벤트 허브 이벤트|개수|합계|성공적인 EventHub 이벤트 수|위치|
|이벤트허브토탈실패이벤트|실패한 이벤트 허브 이벤트|개수|합계|실패한 EventHub 이벤트 수|위치|
|이벤트허브거부이벤트|거부된 이벤트 허브 이벤트|개수|합계|거부된 EventHub 이벤트 수(구성이 잘못되었거나 승인되지 않음)|위치|
|이벤트허브스로틀드이벤트|제한 이벤트 허브 이벤트|개수|합계|제한된 EventHub 이벤트 수|위치|
|이벤트허브시간아웃이벤트|시간 시간 반 이벤트 허브 이벤트|개수|합계|시간 시간(시간)이 발생한 EventHub 이벤트 수|위치|
|이벤트허브드롭이벤트|삭제된 이벤트 허브 이벤트|개수|합계|큐 크기 제한에 도달하여 건너뛴 이벤트 수|위치|
|이벤트허브토탈바이트센트|이벤트 허브 이벤트 크기|바이트|합계|바이트내 의 EventHub 이벤트의 총 크기|위치|
|요청|요청|개수|합계|여러 차원의 게이트웨이 요청 메트릭|위치, 호스트 이름, LastErrorReason, 백엔드응답코드, 게이트웨이응답코드, 백엔드응답코드범주, 게이트웨이응답코드범주|
|네트워크 연결|네트워크 연결 리소스 상태(미리 보기)|개수|합계|API 관리 서비스에서 종속 리소스 유형의 네트워크 연결 상태|위치, 리소스 유형|


## <a name="microsoftappconfigurationconfigurationstores"></a>마이크로소프트.앱 구성/구성스토어

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|HttpIncoming 요청 카운트|HttpIncoming 요청 카운트|개수|개수|들어오는 http 요청의 총 수입니다.|StatusCode|
|HttpIncoming요청지속시간|HttpIncoming요청지속시간|개수|평균|http 요청에 대한 대기 시간입니다.|StatusCode|


## <a name="microsoftappplatformspring"></a>마이크로소프트.앱플랫폼/봄

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|시스템Cpu사용률|시스템 CPU 사용률|백분율|평균|전체 시스템에 대한 최근 CPU 사용량|앱 이름, 포드|
|앱Cpu사용률|앱 CPU 사용량 비율|백분율|평균|앱 JVM CPU 사용률|앱 이름, 포드|
|앱메모리커밋|앱 메모리 할당|바이트|평균|JVM에 할당된 메모리(바이트)|앱 이름, 포드|
|앱메모리사용|사용된 앱 메모리|바이트|평균|바이트에 사용되는 앱 메모리|앱 이름, 포드|
|앱메모리맥스|앱 메모리 최대|바이트|최대|메모리 관리에 사용할 수 있는 바이트의 최대 메모리 양|앱 이름, 포드|
|맥스올드젠메모리풀바이트|사용 가능한 최대 이전 세대 데이터 크기|바이트|평균|이전 세대 메모리 풀의 최대 크기|앱 이름, 포드|
|올드젠메모리풀바이트|이전 세대 데이터 크기|바이트|평균|전체 GC 후 이전 세대 메모리 풀 크기|앱 이름, 포드|
|올드젠프로모션바이트|이전 세대 데이터 크기로 승격|바이트|최대|GC 이후GC 이전의 이전 세대 메모리 풀 크기에서 양수 증가 횟수|앱 이름, 포드|
|영젠프로모션바이트|젊은 세대 데이터 크기로 승격|바이트|최대|한 GC 후 젊은 세대 메모리 풀의 크기가 다음 GC 이전으로 증가|앱 이름, 포드|
|GCPause토탈카운트|GC 일시 정지 수|개수|합계|GC 일시 정지 수|앱 이름, 포드|
|GCPause토탈타임|GC 일시 정지 총 시간|밀리초|합계|GC 일시 정지 총 시간|앱 이름, 포드|
|톰카트센트바이트|톰캣 총 전송 바이트|바이트|합계|톰캣 총 전송 바이트|앱 이름, 포드|
|톰캣수신바이트|톰캣 총 수신 바이트|바이트|합계|톰캣 총 수신 바이트|앱 이름, 포드|
|톰캣인퀘스트토탈타임|톰캣 요청 총 시간|밀리초|합계|톰캣 요청 총 시간|앱 이름, 포드|
|톰캣요청토탈카운트|톰캣 요청 총 수|개수|합계|톰캣 요청 총 수|앱 이름, 포드|
|톰캣응답에이브그타임|톰캣 요청 평균 시간|밀리초|평균|톰캣 요청 평균 시간|앱 이름, 포드|
|톰캣인퀘스트맥스타임|톰캣 요청 최대 시간|밀리초|최대|톰캣 요청 최대 시간|앱 이름, 포드|
|톰캣오류카운트|톰캣 전역 오류|개수|합계|톰캣 전역 오류|앱 이름, 포드|
|톰캣세션액티브맥스카운트|톰캣 세션 최대 활성 카운트|개수|합계|톰캣 세션 최대 활성 카운트|앱 이름, 포드|
|톰캣세션얼맥스타임|톰캣 세션 최대 살아있는 시간|밀리초|최대|톰캣 세션 최대 살아있는 시간|앱 이름, 포드|
|톰캣세션액티브전류카운트|톰캣 세션 얼라이브 카운트|개수|합계|톰캣 세션 얼라이브 카운트|앱 이름, 포드|
|톰캣세션생성카운트|톰캣 세션 생성 개수|개수|합계|톰캣 세션 생성 개수|앱 이름, 포드|
|톰캣세션 만료카운트|톰캣 세션 만료 개수|개수|합계|톰캣 세션 만료 개수|앱 이름, 포드|
|톰캣세션거부카운트|톰캣 세션 거부 카운트|개수|합계|톰캣 세션 거부 카운트|앱 이름, 포드|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalJob|총 작업 수|개수|합계|총 작업 수|Runbook, 상태|
|총 업데이트 배포 실행|총 업데이트 배포 실행|개수|합계|총 소프트웨어 업데이트 배포 실행|소프트웨어업데이트구성이름, 상태|
|전체 업데이트 배포컴퓨터 실행|총 업데이트 배포 머신 실행|개수|합계|소프트웨어 업데이트 배포 실행에서 총 소프트웨어 업데이트 배포 컴퓨터가 실행됩니다.|소프트웨어업데이트구성이름, 상태, 대상컴퓨터, 소프트웨어업데이트구성RunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CoreCount|전용된 코어 수|개수|합계|배치 계정의 총 전용 코어 수|None|
|TotalNodeCount|전용된 노드 수|개수|합계|배치 계정의 총 전용 노드 수|None|
|LowPriorityCoreCount|LowPriority 코어 수|개수|합계|배치 계정의 우선 순위가 낮은 총 코어 수|None|
|TotalLowPriorityNodeCount|우선 순위가 낮은 노드 수|개수|합계|배치 계정의 우선 순위가 낮은 총 노드 수|None|
|CreatingNodeCount|노드 수 만들기|개수|합계|만든 노드 수|None|
|StartingNodeCount|시작 노드 수|개수|합계|시작 노드 수|None|
|WaitingForStartTaskNodeCount|작업 시작 대기 노드 수|개수|합계|시작 작업 완료를 기다리는 노드 수|None|
|StartTaskFailedNodeCount|시작 작업 실패 노드 수|개수|합계|시작 작업이 실패한 노드 수|None|
|IdleNodeCount|유휴 상태인 노드 수|개수|합계|유휴 노드 수|None|
|OfflineNodeCount|오프라인 노드 수|개수|합계|오프라인 노드 수|None|
|RebootingNodeCount|재부팅 노드 수|개수|합계|다시 부팅하는 노드의 수|None|
|ReimagingNodeCount|이미지로 다시 설치하는 노드 수|개수|합계|이미지로 다시 설치하는 노드의 수|None|
|RunningNodeCount|실행 노드 수|개수|합계|실행 중인 노드의 수|None|
|LeavingPoolNodeCount|나가는 풀 노드 수|개수|합계|풀을 나가는 노드 수|None|
|UnusableNodeCount|사용 불가 노드 수|개수|합계|사용할 수 없는 노드 수|None|
|PreemptedNodeCount|선점된 노드 수|개수|합계|선점된 노드 수|None|
|TaskStartEvent|작업 시작 이벤트|개수|합계|시작된 총 작업 수|풀Id, 작업 ID|
|TaskCompleteEvent|작업 완료 이벤트|개수|합계|완료된 총 작업 수|풀Id, 작업 ID|
|TaskFailEvent|작업 실패 이벤트|개수|합계|실패한 상태로 완료된 총 작업 수|풀Id, 작업 ID|
|PoolCreateEvent|풀 만들기 이벤트|개수|합계|만든 총 풀 수|poolId|
|PoolResizeStartEvent|풀 크기 조정 시작 이벤트|개수|합계|시작된 총 풀 크기 조정 작업 수|poolId|
|PoolResizeCompleteEvent|풀 크기 조정 완료 이벤트|개수|합계|완료된 총 풀 크기 조정 수|poolId|
|PoolDeleteStartEvent|풀 삭제 시작 이벤트|개수|합계|시작된 총 풀 삭제 수|poolId|
|PoolDeleteCompleteEvent|풀 삭제 완료 이벤트|개수|합계|완료된 총 풀 삭제 수|poolId|
|JobDeleteCompleteEvent|작업 삭제 완료 이벤트|개수|합계|정상적으로 삭제된 총 작업 수입니다.|jobId|
|JobDeleteStartEvent|작업 삭제 시작 이벤트|개수|합계|삭제되도록 요청된 총 작업 수입니다.|jobId|
|JobDisableCompleteEvent|작업 비활성화 완료 이벤트|개수|합계|정상적으로 사용하지 않도록 설정된 총 작업 수입니다.|jobId|
|JobDisableStartEvent|작업 비활성화 시작 이벤트|개수|합계|비활성화되도록 요청된 총 작업 수입니다.|jobId|
|JobStartEvent|작업 시작 이벤트|개수|합계|정상적으로 시작된 총 작업 수입니다.|jobId|
|JobTerminateCompleteEvent|작업 종료 완료 이벤트|개수|합계|정상적으로 종료된 총 작업 수입니다.|jobId|
|JobTerminateStartEvent|작업 종료 시작 이벤트|개수|합계|종료되도록 요청된 총 작업 수입니다.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>마이크로소프트.배치AI/작업 공간

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|제출된 작업|제출된 작업|개수|합계|제출된 작업 수|시나리오, 클러스터 이름|
|작업이 완료되었습니다.|작업이 완료되었습니다.|개수|합계|완료된 작업 수|시나리오, 클러스터 이름, 결과 유형|
|총 노드|총 노드|개수|평균|총 노드 수|시나리오, 클러스터 이름|
|활성 노드|활성 노드|개수|평균|실행 중인 노드의 수|시나리오, 클러스터 이름|
|유휴 노드|유휴 노드|개수|평균|유휴 노드 수|시나리오, 클러스터 이름|
|사용할 수 없는 노드|사용할 수 없는 노드|개수|평균|사용할 수 없는 노드 수|시나리오, 클러스터 이름|
|선점노드|선점노드|개수|평균|선점된 노드 수|시나리오, 클러스터 이름|
|노드 를 떠나기|노드 를 떠나기|개수|평균|떠나는 노드 수|시나리오, 클러스터 이름|
|총 코어|총 코어|개수|평균|총 코어 수|시나리오, 클러스터 이름|
|액티브 코어|액티브 코어|개수|평균|활성 코어 수|시나리오, 클러스터 이름|
|유휴 코어|유휴 코어|개수|평균|유휴 코어 수|시나리오, 클러스터 이름|
|사용할 수 없는 코어|사용할 수 없는 코어|개수|평균|사용할 수 없는 코어 수|시나리오, 클러스터 이름|
|선점코어|선점코어|개수|평균|선점코어 수|시나리오, 클러스터 이름|
|코어를 남기기|코어를 남기기|개수|평균|코어를 남기는 수|시나리오, 클러스터 이름|
|할당량 사용률|할당량 사용률|개수|평균|할당량 사용 비율|시나리오, 클러스터 이름, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>마이크로소프트.블록체인/블록체인 회원

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CPU 사용률백분율더블|CPU 사용률|백분율|최대|CPU 사용률|노드|
|MemoryUsage|메모리 사용량|바이트|평균|메모리 사용량|노드|
|MemoryLimit|메모리 제한|바이트|평균|메모리 제한|노드|
|메모리 사용률백분율더블|메모리 사용량 백분율|백분율|평균|메모리 사용량 백분율|노드|
|스토리지 사용법|스토리지 사용|바이트|평균|스토리지 사용|노드|
|아이오레드바이트|IO 읽기 바이트|바이트|합계|IO 읽기 바이트|노드|
|아이오필바이트|IO 쓰기 바이트|바이트|합계|IO 쓰기 바이트|노드|
|연결 허용됨|허용된 연결|개수|합계|허용된 연결|노드|
|연결 처리됨|처리된 연결|개수|합계|처리된 연결|노드|
|연결활성|활성 연결 수|개수|평균|활성 연결 수|노드|
|요청 처리|처리된 요청|개수|합계|처리된 요청|노드|
|처리된 블록|가공블록|개수|합계|가공블록|노드|
|처리된 트랜잭션|처리된 트랜잭션|개수|합계|처리된 트랜잭션|노드|
|보류 중인 트랜잭션|보류 중인 트랜잭션|개수|평균|보류 중인 트랜잭션|노드|
|큐에 대기된 트랜잭션|큐에 대기된 트랜잭션|개수|평균|큐에 대기된 트랜잭션|노드|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|connectedclients|연결된 클라이언트|개수|최대||ShardId|
|totalcommandsprocessed|총 작업|개수|합계||ShardId|
|cachehits|캐시 적중|개수|합계||ShardId|
|cachemisses|캐시 누락|개수|합계||ShardId|
|캐시 누락|캐시 누락 률|백분율|캐시 누락||ShardId|
|getcommands|가져오기|개수|합계||ShardId|
|setcommands|집합|개수|합계||ShardId|
|operationsPerSecond|초당 작업|개수|최대||ShardId|
|evictedkeys|제거된 키|개수|합계||ShardId|
|totalkeys|전체 키|개수|최대||ShardId|
|expiredkeys|만료된 키|개수|합계||ShardId|
|usedmemory|사용된 메모리|바이트|최대||ShardId|
|usedmemorypercentage|사용된 메모리 비율|백분율|최대||ShardId|
|usedmemoryRss|사용된 메모리 RSS|바이트|최대||ShardId|
|serverLoad|서버 부하|백분율|최대||ShardId|
|cacheWrite|캐시 쓰기|초당 바이트 수|최대||ShardId|
|cacheRead|캐시 읽기|초당 바이트 수|최대||ShardId|
|percentProcessorTime|CPU|백분율|최대||ShardId|
|cacheLatency|캐시 대기 시간 마이크로초(미리 보기)|개수|평균||ShardId|
|오류|오류|개수|최대||샤드, 오류 유형|
|connectedclients0|연결된 클라이언트(분할 0)|개수|최대||None|
|totalcommandsprocessed0|총 작업(분할 0)|개수|합계||None|
|cachehits0|캐시 적중(분할 0)|개수|합계||None|
|cachemisses0|캐시 누락(분할 0)|개수|합계||None|
|getcommands0|가져오기(분할 0)|개수|합계||None|
|setcommands0|설정(분할 0)|개수|합계||None|
|operationsPerSecond0|초당 작업(분할 0)|개수|최대||None|
|evictedkeys0|제거된 키(분할 0)|개수|합계||None|
|totalkeys0|총 키(분할 0)|개수|최대||None|
|expiredkeys0|만료된 키(분할 0)|개수|합계||None|
|usedmemory0|사용된 메모리(분할 0)|바이트|최대||None|
|usedmemoryRss0|사용된 메모리 RSS(분할 0)|바이트|최대||None|
|serverLoad0|서버 부하(분할 0)|백분율|최대||None|
|cacheWrite0|캐시 쓰기(분할 0)|초당 바이트 수|최대||None|
|cacheRead0|캐시 읽기(분할 0)|초당 바이트 수|최대||None|
|percentProcessorTime0|CPU(분할 0)|백분율|최대||None|
|connectedclients1|연결된 클라이언트(분할 1)|개수|최대||None|
|totalcommandsprocessed1|총 작업(분할 1)|개수|합계||None|
|cachehits1|캐시 적중(분할 1)|개수|합계||None|
|cachemisses1|캐시 누락(분할 1)|개수|합계||None|
|getcommands1|가져오기(분할 1)|개수|합계||None|
|setcommands1|집합(분할 1)|개수|합계||None|
|operationsPerSecond1|초당 작업(분할 1)|개수|최대||None|
|evictedkeys1|제거된 키(분할 1)|개수|합계||None|
|totalkeys1|총 키(분할 1)|개수|최대||None|
|expiredkeys1|만료된 키(분할 1)|개수|합계||None|
|usedmemory1|사용된 메모리(분할 1)|바이트|최대||None|
|usedmemoryRss1|사용된 메모리 RSS(분할 1)|바이트|최대||None|
|serverLoad1|서버 부하(분할 1)|백분율|최대||None|
|cacheWrite1|캐시 쓰기(분할 1)|초당 바이트 수|최대||None|
|cacheRead1|캐시 읽기(분할 1)|초당 바이트 수|최대||None|
|percentProcessorTime1|CPU(분할 1)|백분율|최대||None|
|connectedclients2|연결된 클라이언트(분할 2)|개수|최대||None|
|totalcommandsprocessed2|총 작업(분할 2)|개수|합계||None|
|cachehits2|캐시 적중(분할 2)|개수|합계||None|
|cachemisses2|캐시 누락(분할 2)|개수|합계||None|
|getcommands2|가져오기(분할 2)|개수|합계||None|
|setcommands2|설정(분할 2)|개수|합계||None|
|operationsPerSecond2|초당 작업(분할 2)|개수|최대||None|
|evictedkeys2|제거된 키(분할 2)|개수|합계||None|
|totalkeys2|총 키(분할 2)|개수|최대||None|
|expiredkeys2|만료된 키(분할 2)|개수|합계||None|
|usedmemory2|사용된 메모리(분할 2)|바이트|최대||None|
|usedmemoryRss2|사용된 메모리 RSS(분할 2)|바이트|최대||None|
|serverLoad2|서버 부하(분할 2)|백분율|최대||None|
|cacheWrite2|캐시 쓰기(분할 2)|초당 바이트 수|최대||None|
|cacheRead2|캐시 읽기(분할 2)|초당 바이트 수|최대||None|
|percentProcessorTime2|CPU(분할 2)|백분율|최대||None|
|connectedclients3|연결된 클라이언트(분할 3)|개수|최대||None|
|totalcommandsprocessed3|총 작업(분할 3)|개수|합계||None|
|cachehits3|캐시 적중(분할 3)|개수|합계||None|
|cachemisses3|캐시 누락(분할 3)|개수|합계||None|
|getcommands3|가져오기(분할 3)|개수|합계||None|
|setcommands3|설정(분할 3)|개수|합계||None|
|operationsPerSecond3|초당 작업(분할 3)|개수|최대||None|
|evictedkeys3|제거된 키(분할 3)|개수|합계||None|
|totalkeys3|총 키(분할 3)|개수|최대||None|
|expiredkeys3|만료된 키(분할 3)|개수|합계||None|
|usedmemory3|사용된 메모리(분할 3)|바이트|최대||None|
|usedmemoryRss3|사용된 메모리 RSS(분할 3)|바이트|최대||None|
|serverLoad3|서버 부하(분할 3)|백분율|최대||None|
|cacheWrite3|캐시 쓰기(분할 3)|초당 바이트 수|최대||None|
|cacheRead3|캐시 읽기(분할 3)|초당 바이트 수|최대||None|
|percentProcessorTime3|CPU(분할 3)|백분율|최대||None|
|connectedclients4|연결된 클라이언트(분할 4)|개수|최대||None|
|totalcommandsprocessed4|총 작업(분할 4)|개수|합계||None|
|cachehits4|캐시 적중(분할 4)|개수|합계||None|
|cachemisses4|캐시 누락(분할 4)|개수|합계||None|
|getcommands4|가져오기(분할 4)|개수|합계||None|
|setcommands4|설정(분할 4)|개수|합계||None|
|operationsPerSecond4|초당 작업(분할 4)|개수|최대||None|
|evictedkeys4|제거된 키(분할 4)|개수|합계||None|
|totalkeys4|총 키(분할 4)|개수|최대||None|
|expiredkeys4|만료된 키(분할 4)|개수|합계||None|
|usedmemory4|사용된 메모리(분할 4)|바이트|최대||None|
|usedmemoryRss4|사용된 메모리 RSS(분할 4)|바이트|최대||None|
|serverLoad4|서버 부하(분할 4)|백분율|최대||None|
|cacheWrite4|캐시 쓰기(분할 4)|초당 바이트 수|최대||None|
|cacheRead4|캐시 읽기(분할 4)|초당 바이트 수|최대||None|
|percentProcessorTime4|CPU(분할 4)|백분율|최대||None|
|connectedclients5|연결된 클라이언트(분할 5)|개수|최대||None|
|totalcommandsprocessed5|총 작업(분할 5)|개수|합계||None|
|cachehits5|캐시 적중(분할 5)|개수|합계||None|
|cachemisses5|캐시 누락(분할 5)|개수|합계||None|
|getcommands5|가져오기(분할 5)|개수|합계||None|
|setcommands5|설정(분할 5)|개수|합계||None|
|operationsPerSecond5|초당 작업(분할 5)|개수|최대||None|
|evictedkeys5|제거된 키(분할 5)|개수|합계||None|
|totalkeys5|총 키(분할 5)|개수|최대||None|
|expiredkeys5|만료된 키(분할 5)|개수|합계||None|
|usedmemory5|사용된 메모리(분할 5)|바이트|최대||None|
|usedmemoryRss5|사용된 메모리 RSS(분할 5)|바이트|최대||None|
|serverLoad5|서버 부하(분할 5)|백분율|최대||None|
|cacheWrite5|캐시 쓰기(분할 5)|초당 바이트 수|최대||None|
|cacheRead5|캐시 읽기(분할 5)|초당 바이트 수|최대||None|
|percentProcessorTime5|CPU(분할 5)|백분율|최대||None|
|connectedclients6|연결된 클라이언트(분할 6)|개수|최대||None|
|totalcommandsprocessed6|총 작업(분할 6)|개수|합계||None|
|cachehits6|캐시 적중(분할 6)|개수|합계||None|
|cachemisses6|캐시 누락(분할 6)|개수|합계||None|
|getcommands6|가져오기(분할 6)|개수|합계||None|
|setcommands6|설정(분할 6)|개수|합계||None|
|operationsPerSecond6|초당 작업(분할 6)|개수|최대||None|
|evictedkeys6|제거된 키(분할 6)|개수|합계||None|
|totalkeys6|총 키(분할 6)|개수|최대||None|
|expiredkeys6|만료된 키(분할 6)|개수|합계||None|
|usedmemory6|사용된 메모리(분할 6)|바이트|최대||None|
|usedmemoryRss6|사용된 메모리 RSS(분할 6)|바이트|최대||None|
|serverLoad6|서버 부하(분할 6)|백분율|최대||None|
|cacheWrite6|캐시 쓰기(분할 6)|초당 바이트 수|최대||None|
|cacheRead6|캐시 읽기(분할 6)|초당 바이트 수|최대||None|
|percentProcessorTime6|CPU(분할 6)|백분율|최대||None|
|connectedclients7|연결된 클라이언트(분할 7)|개수|최대||None|
|totalcommandsprocessed7|총 작업(분할 7)|개수|합계||None|
|cachehits7|캐시 적중(분할 7)|개수|합계||None|
|cachemisses7|캐시 누락(분할 7)|개수|합계||None|
|getcommands7|가져오기(분할 7)|개수|합계||None|
|setcommands7|설정(분할 7)|개수|합계||None|
|operationsPerSecond7|초당 작업(분할 7)|개수|최대||None|
|evictedkeys7|제거된 키(분할 7)|개수|합계||None|
|totalkeys7|총 키(분할 7)|개수|최대||None|
|expiredkeys7|만료된 키(분할 7)|개수|합계||None|
|usedmemory7|사용된 메모리(분할 7)|바이트|최대||None|
|usedmemoryRss7|사용된 메모리 RSS(분할 7)|바이트|최대||None|
|serverLoad7|서버 부하(분할 7)|백분율|최대||None|
|cacheWrite7|캐시 쓰기(분할 7)|초당 바이트 수|최대||None|
|cacheRead7|캐시 읽기(분할 7)|초당 바이트 수|최대||None|
|percentProcessorTime7|CPU(분할 7)|백분율|최대||None|
|connectedclients8|연결된 클라이언트(분할 8)|개수|최대||None|
|totalcommandsprocessed8|총 작업(분할 8)|개수|합계||None|
|cachehits8|캐시 적중(분할 8)|개수|합계||None|
|cachemisses8|캐시 누락(분할 8)|개수|합계||None|
|getcommands8|가져오기(분할 8)|개수|합계||None|
|setcommands8|설정(분할 8)|개수|합계||None|
|operationsPerSecond8|초당 작업(분할 8)|개수|최대||None|
|evictedkeys8|제거된 키(분할 8)|개수|합계||None|
|totalkeys8|총 키(분할 8)|개수|최대||None|
|expiredkeys8|만료된 키(분할 8)|개수|합계||None|
|usedmemory8|사용된 메모리(분할 8)|바이트|최대||None|
|usedmemoryRss8|사용된 메모리 RSS(분할 8)|바이트|최대||None|
|serverLoad8|서버 부하(분할 8)|백분율|최대||None|
|cacheWrite8|캐시 쓰기(분할 8)|초당 바이트 수|최대||None|
|cacheRead8|캐시 읽기(분할 8)|초당 바이트 수|최대||None|
|percentProcessorTime8|CPU(분할 8)|백분율|최대||None|
|connectedclients9|연결된 클라이언트(분할 9)|개수|최대||None|
|totalcommandsprocessed9|총 작업(분할 9)|개수|합계||None|
|cachehits9|캐시 적중(분할 9)|개수|합계||None|
|cachemisses9|캐시 누락(분할 9)|개수|합계||None|
|getcommands9|가져오기(분할 9)|개수|합계||None|
|setcommands9|설정(분할 9)|개수|합계||None|
|operationsPerSecond9|초당 작업(분할 9)|개수|최대||None|
|evictedkeys9|제거된 키(분할 9)|개수|합계||None|
|totalkeys9|총 키(분할 9)|개수|최대||None|
|expiredkeys9|만료된 키(분할 9)|개수|합계||None|
|usedmemory9|사용된 메모리(분할 9)|바이트|최대||None|
|usedmemoryRss9|사용된 메모리 RSS(분할 9)|바이트|최대||None|
|serverLoad9|서버 부하(분할 9)|백분율|최대||None|
|cacheWrite9|캐시 쓰기(분할 9)|초당 바이트 수|최대||None|
|cacheRead9|캐시 읽기(분할 9)|초당 바이트 수|최대||None|
|percentProcessorTime9|CPU(분할 9)|백분율|최대||None|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>마이크로소프트.Cdn/cdnwebapplication방화벽정책

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|웹 애플리케이션 방화벽 요청 수|개수|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|정책 이름, 규칙 이름, 작업|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|None|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|None|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|None|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|None|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|None|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|None|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|RoleInstanceId|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|RoleInstanceId|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|RoleInstanceId|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|RoleInstanceId|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|RoleInstanceId|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|RoleInstanceId|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|UsedCapacity|사용된 용량|바이트|평균|계정 사용 용량|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용하는 대기 시간(밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대한 성공적인 요청의 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>마이크로소프트.클래식스토리지/스토리지계정/blobServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BlobCapacity|Blob 용량|바이트|평균|저장소 계정의 Blob 서비스에서 바이트로 사용하는 저장소의 양입니다.|BlobType, 계층|
|BlobCount|Blob 수|개수|평균|저장소 계정의 Blob 서비스의 Blob 수입니다.|BlobType, 계층|
|ContainerCount|Blob 컨테이너 수|개수|평균|저장소 계정의 Blob 서비스의 컨테이너 수입니다.|None|
|IndexCapacity|인덱스 용량|바이트|평균|ADLS Gen2(계층적) 인덱스에서 바이트로 사용하는 저장소의 양입니다.|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용하는 대기 시간(밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대한 성공적인 요청의 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>마이크로소프트.클래식스토리지/스토리지계정/테이블서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TableCapacity|테이블 용량|바이트|평균|저장소 계정의 테이블 서비스에서 바이트로 사용하는 저장소의 양입니다.|None|
|TableCount|테이블 수|개수|평균|저장소 계정의 테이블 서비스의 테이블 수입니다.|None|
|TableEntityCount|테이블 엔터티 수|개수|평균|저장소 계정의 테이블 서비스의 테이블 엔터티 수입니다.|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용하는 대기 시간(밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대한 성공적인 요청의 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|FileCapacity|파일 용량|바이트|평균|저장소 계정의 파일 서비스에서 바이트로 사용하는 저장소의 양입니다.|FileShare|
|FileCount|파일 수|개수|평균|저장소 계정의 파일 서비스에 있는 파일 수입니다.|FileShare|
|FileShareCount|파일 공유 수|개수|평균|저장소 계정의 파일 서비스에서 파일 공유 수입니다.|None|
|파일 공유스냅샷카운트|파일 공유 스냅샷 수|개수|평균|저장소 계정의 파일 서비스에서 공유에 있는 스냅숏 수입니다.|FileShare|
|파일 공유스냅샷 크기|파일 공유 스냅샷 크기|바이트|평균|저장소 계정의 파일 서비스에서 스냅숏에서 바이트로 사용하는 저장소의 양입니다.|FileShare|
|파일 공유 할당량|파일 공유 할당량 크기|바이트|평균|Azure 파일 서비스에서 바이트 단위로 사용할 수 있는 저장소 양에 대한 상한입니다.|FileShare|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지오 타입, ApiName, 인증, 파일 쉐어|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용하는 대기 시간(밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대한 성공적인 요청의 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증, 파일 쉐어|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>마이크로소프트.클래식스토리지/스토리지계정/큐서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueueCapacity|큐 용량|바이트|평균|저장소 계정의 Queue 서비스에서 바이트로 사용하는 저장소 의 양입니다.|None|
|QueueCount|큐 수|개수|평균|저장소 계정의 Queue 서비스의 큐 수입니다.|None|
|QueueMessageCount|큐 메시지 수|개수|평균|저장소 계정의 Queue 서비스에 있는 대략적인 큐 메시지 수입니다.|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용하는 대기 시간(밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정된 API 작업에 대한 성공적인 요청의 종단 간 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalCalls|총 호출|개수|합계|총 호출 수.|ApiName, 작업 이름, 지역|
|SuccessfulCalls|성공한 호출|개수|합계|성공한 호출 수입니다.|ApiName, 작업 이름, 지역|
|TotalErrors|총 오류|개수|합계|오류 응답(HTTP 응답 코드 4xx 또는 5xx)이 있는 총 호출 수입니다.|ApiName, 작업 이름, 지역|
|BlockedCalls|차단된 호출|개수|합계|요금 또는 할당량 한도를 초과한 호출 수입니다.|ApiName, 작업 이름, 지역|
|ServerErrors|서버 오류|개수|합계|서비스 내부 오류(HTTP 응답 코드 5xx)가 있는 호출 수입니다.|ApiName, 작업 이름, 지역|
|ClientErrors|클라이언트 오류|개수|합계|클라이언트 쪽 오류(HTTP 응답 코드 4xx)가 있는 호출 수입니다.|ApiName, 작업 이름, 지역|
|DataIn|데이터 입력|바이트|합계|들어오는 데이터 크기(바이트)입니다.|ApiName, 작업 이름, 지역|
|DataOut|데이터 출력|바이트|합계|나가는 데이터 크기(바이트)입니다.|ApiName, 작업 이름, 지역|
|대기 시간|대기 시간|밀리초|평균|대기 시간(밀리초)입니다.|ApiName, 작업 이름, 지역|
|TotalTokenCalls|총 토큰 호출|개수|합계|총 토큰 호출 수입니다.|ApiName, 작업 이름, 지역|
|CharactersTranslated|변환된 문자|개수|합계|들어오는 텍스트 요청에 있는 문자의 총 수입니다.|ApiName, 작업 이름, 지역|
|문자 학습|훈련된 캐릭터|개수|합계|훈련된 총 문자 수입니다.|ApiName, 작업 이름, 지역|
|SpeechSessionDuration|음성 세션 기간|초|합계|음성 세션의 총 기간(초)입니다.|ApiName, 작업 이름, 지역|
|TotalTransactions|총 트랜잭션|개수|합계|총 트랜잭션 수|None|
|처리된 이미지|처리된 이미지|개수|합계| 이미지 처리를 위한 트랜잭션 수입니다.|ApiName, 기능 이름, 채널, 지역|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|None|
|네트워크 인|청구 가능한 네트워크(더 이상 사용되지 않습니다)|바이트|합계|가상 컴퓨터(들)(들어오는 트래픽)에 의해 모든 네트워크 인터페이스에서 수신된 청구 가능한 바이트 수(더 이상 사용되지 않습니다).|None|
|네트워크 아웃|네트워크 아웃 청구 가능(더 이상 사용되지 않습니다)|바이트|합계|가상 컴퓨터(들)(나가는 트래픽)(더 이상 사용되지 않습니다)에 의해 모든 네트워크 인터페이스에서 청구 가능한 바이트 수|None|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트|None|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 기록된 바이트|None|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|None|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|None|
|남은 CPU 크레딧|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|None|
|사용된 CPU 크레딧|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|None|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 [작업/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 [작업/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|SlotId|
|디스크당 QD|[데이터 디스크 [QD(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|None|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 [바이트/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|None|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 [작업/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 읽기|None|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 [작업/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|None|
|디스크당 OS QD|OS 디스크 [QD(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|None|
|데이터 디스크 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|LUN|
|데이터 디스크 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|LUN|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|LUN|
|데이터 디스크 큐 크기|데이터 디스크 큐 깊이(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|OS 디스크 읽기 바이트/초|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|None|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|None|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 읽기|None|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|None|
|OS 디스크 큐 크기|OS 디스크 큐 깊이(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|None|
|인바운드 흐름|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 전류 흐름 수입니다(VM으로 유입되는 트래픽).|None|
|아웃바운드 흐름|아웃바운드 흐름|개수|평균|아웃바운드 흐름은 아웃바운드 방향의 전류 흐름 수입니다(VM에서 나가는 트래픽).|None|
|인바운드 흐름 최대 생성 속도|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름의 최대 생성 속도(VM으로 유입되는 트래픽)|None|
|아웃바운드 흐름 최대 생성 속도|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃바운드 흐름의 최대 생성 속도(VM에서 나가는 트래픽)|None|
|프리미엄 데이터 디스크 캐시 읽기 히트|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 히트|LUN|
|프리미엄 데이터 디스크 캐시 읽기 미스|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 미스|LUN|
|프리미엄 OS 디스크 캐시 읽기 히트|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 히트|None|
|프리미엄 OS 디스크 캐시 읽기 미스|프리미엄 OS 디스크 캐시 읽기 미스(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 미스|None|
|네트워크 총|네트워크 총|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|None|
|네트워크 아웃 합계|네트워크 아웃 합계|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|VMName|
|네트워크 인|청구 가능한 네트워크(더 이상 사용되지 않습니다)|바이트|합계|가상 컴퓨터(들)(들어오는 트래픽)에 의해 모든 네트워크 인터페이스에서 수신된 청구 가능한 바이트 수(더 이상 사용되지 않습니다).|VMName|
|네트워크 아웃|네트워크 아웃 청구 가능(더 이상 사용되지 않습니다)|바이트|합계|가상 컴퓨터(들)(나가는 트래픽)(더 이상 사용되지 않습니다)에 의해 모든 네트워크 인터페이스에서 청구 가능한 바이트 수|VMName|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트|VMName|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 기록된 바이트|VMName|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|VMName|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|VMName|
|남은 CPU 크레딧|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|None|
|사용된 CPU 크레딧|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|None|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 [작업/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 [작업/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|SlotId|
|디스크당 QD|데이터 디스크 [QD(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|None|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 [바이트/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|None|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 [작업/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 읽기|None|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 [작업/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|None|
|디스크당 OS QD|OS 디스크 [QD(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|None|
|데이터 디스크 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|룬, VM네임|
|데이터 디스크 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|룬, VM네임|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 읽기|룬, VM네임|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|룬, VM네임|
|데이터 디스크 큐 크기|데이터 디스크 큐 깊이(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|룬, VM네임|
|OS 디스크 읽기 바이트/초|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|VMName|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|VMName|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 읽기|VMName|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|VMName|
|OS 디스크 큐 크기|OS 디스크 큐 깊이(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|VMName|
|인바운드 흐름|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 전류 흐름 수입니다(VM으로 유입되는 트래픽).|VMName|
|아웃바운드 흐름|아웃바운드 흐름|개수|평균|아웃바운드 흐름은 아웃바운드 방향의 전류 흐름 수입니다(VM에서 나가는 트래픽).|VMName|
|인바운드 흐름 최대 생성 속도|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름의 최대 생성 속도(VM으로 유입되는 트래픽)|VMName|
|아웃바운드 흐름 최대 생성 속도|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃바운드 흐름의 최대 생성 속도(VM에서 나가는 트래픽)|VMName|
|프리미엄 데이터 디스크 캐시 읽기 히트|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 히트|룬, VM네임|
|프리미엄 데이터 디스크 캐시 읽기 미스|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 미스|룬, VM네임|
|프리미엄 OS 디스크 캐시 읽기 히트|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 히트|VMName|
|프리미엄 OS 디스크 캐시 읽기 미스|프리미엄 OS 디스크 캐시 읽기 미스(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 미스|VMName|
|네트워크 총|네트워크 총|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|VMName|
|네트워크 아웃 합계|네트워크 아웃 합계|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|None|
|네트워크 인|청구 가능한 네트워크(더 이상 사용되지 않습니다)|바이트|합계|가상 컴퓨터(들)(들어오는 트래픽)에 의해 모든 네트워크 인터페이스에서 수신된 청구 가능한 바이트 수(더 이상 사용되지 않습니다).|None|
|네트워크 아웃|네트워크 아웃 청구 가능(더 이상 사용되지 않습니다)|바이트|합계|가상 컴퓨터(들)(나가는 트래픽)(더 이상 사용되지 않습니다)에 의해 모든 네트워크 인터페이스에서 청구 가능한 바이트 수|None|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트|None|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 기록된 바이트|None|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|None|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|None|
|남은 CPU 크레딧|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|None|
|사용된 CPU 크레딧|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|None|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 [작업/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 [작업/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|SlotId|
|디스크당 QD|데이터 디스크 [QD(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS 디스크 읽기 [바이트/초(더 이상 사용되지)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|None|
|디스크당 OS 쓰기 바이트/초|OS 디스크 쓰기 [바이트/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|None|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 [작업/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 읽기|None|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 [작업/초(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|None|
|디스크당 OS QD|OS 디스크 [QD(더 이상 사용되지 않습니다)](portal-disk-metrics-deprecation.md)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|None|
|데이터 디스크 읽기 바이트/초|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|LUN|
|데이터 디스크 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|LUN|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|LUN|
|데이터 디스크 큐 크기|데이터 디스크 큐 깊이(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|OS 디스크 읽기 바이트/초|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 바이트/초 읽기|None|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에 기록된 바이트/Sec|None|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 읽기|None|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간 동안 단일 디스크에서 IOPS 쓰기|None|
|OS 디스크 큐 크기|OS 디스크 큐 깊이(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|None|
|인바운드 흐름|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 전류 흐름 수입니다(VM으로 유입되는 트래픽).|None|
|아웃바운드 흐름|아웃바운드 흐름|개수|평균|아웃바운드 흐름은 아웃바운드 방향의 전류 흐름 수입니다(VM에서 나가는 트래픽).|None|
|인바운드 흐름 최대 생성 속도|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름의 최대 생성 속도(VM으로 유입되는 트래픽)|None|
|아웃바운드 흐름 최대 생성 속도|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃바운드 흐름의 최대 생성 속도(VM에서 나가는 트래픽)|None|
|프리미엄 데이터 디스크 캐시 읽기 히트|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 히트|LUN|
|프리미엄 데이터 디스크 캐시 읽기 미스|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 미스|LUN|
|프리미엄 OS 디스크 캐시 읽기 히트|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 히트|None|
|프리미엄 OS 디스크 캐시 읽기 미스|프리미엄 OS 디스크 캐시 읽기 미스(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 미스|None|
|네트워크 총|네트워크 총|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|None|
|네트워크 아웃 합계|네트워크 아웃 합계|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuUsage|CPU 사용량|개수|평균|모든 코어의 CPU 사용량(밀리코어)|containerName|
|MemoryUsage|메모리 사용량|바이트|평균|총 메모리 사용량(바이트)|containerName|
|NetworkBytesReceivedPerSecond|초당 수신된 네트워크 바이트|바이트|평균|초당 수신된 네트워크 바이트입니다.|None|
|NetworkBytesTransmittedPerSecond|초당 전송된 네트워크 바이트|바이트|평균|초당 전송된 네트워크 바이트입니다.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|총 풀카운트|총 당기기 수|개수|평균|총 이미지 끌어당기는 수|None|
|성공한 풀카운트|성공적인 풀 카운트|개수|평균|성공한 이미지 끌어당기수|None|
|총 푸시 카운트|총 푸시 수|개수|평균|총 이미지 푸시 수|None|
|성공적인 푸시카운트|성공적인 푸시 카운트|개수|평균|성공한 이미지 푸시 수|None|
|실행 기간|실행 지속 시간|밀리초|합계|시간(밀리초) 실행|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|개수|평균|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|None|
|kube_node_status_allocatable_memory_bytes|관리 클러스터에서 사용 가능한 총 메모리 양|바이트|평균|관리 클러스터에서 사용 가능한 총 메모리 양|None|
|kube_pod_status_ready|준비 상태인 Pod 수|개수|평균|준비 상태인 Pod 수|네임 스페이스, 포드|
|kube_node_status_condition|다양한 노드 조건에 대한 상태|개수|평균|다양한 노드 조건에 대한 상태|조건, 상태, 상태 2, 노드|
|kube_pod_status_phase|단계별 Pod 수|개수|평균|단계별 Pod 수|위상, 네임 스페이스, 포드|



## <a name="microsoftcustomprovidersresourceproviders"></a>마이크로소프트.사용자 지정 공급자/리소스 공급자

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|성공적인 요청|성공한 요청|개수|합계|사용자 지정 공급자가 요청한 성공|HttpMethod, 통화 경로, 상태 코드|
|FailedRequests|실패한 요청|개수|합계|사용자 지정 리소스 공급자에 사용할 수 있는 로그 가져옵니다.|HttpMethod, 통화 경로, 상태 코드|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>마이크로소프트.데이터박스에지/데이터박스에지디바이스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|NICReadThroughput|읽기 처리량(네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대한 보고 기간 동안 장치의 네트워크 인터페이스 읽기 처리량입니다.|InstanceName|
|NICWrite스루풋|쓰기 처리량(네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대한 보고 기간 동안 디바이스의 네트워크 인터페이스의 쓰기 처리량입니다.|InstanceName|
|클라우드리드스투퍼퍼쉐어|클라우드 다운로드 처리량(공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure에 대한 다운로드 처리량입니다.|공유|
|클라우드업로드투퍼퍼쉐|클라우드 업로드 처리량(공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure에 업로드 처리량입니다.|공유|
|바이트업로드토클라우드퍼쉐어|클라우드 바이트 업로드(공유)|바이트|평균|보고 기간 동안 공유에서 Azure에 업로드되는 총 바이트 수입니다.|공유|
|총 용량|총 용량|바이트|평균|총 용량|None|
|사용 가능 용량|사용 가능한 용량|바이트|평균|보고 기간 동안 바이트로 사용 가능한 용량입니다.|None|
|클라우드업로드스루풋|클라우드 업로드 처리량|초당 바이트 수|평균|보고 기간 동안 클라우드업로드 처리량을 Azure에 업로드합니다.|None|
|클라우드읽기관통|클라우드 다운로드 처리량|초당 바이트 수|평균|보고 기간 동안 Azure에 대한 클라우드 다운로드 처리량입니다.|None|
|바이트업로드토클라우드|클라우드 바이트 업로드(장치)|바이트|평균|보고 기간 동안 장치에서 Azure에 업로드되는 총 바이트 수입니다.|None|
|하이퍼V버추얼프로세서 사용률|에지 계산 - 백분율 CPU|백분율|평균|CPU 사용량 비율|InstanceName|
|하이퍼V메모리 활용도|에지 계산 - 메모리 사용량|백분율|평균|사용 중 RAM 양|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>마이크로소프트.데이터 카탈로그/데이터 카탈로그

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|자산분포분류|분류별 자산 분배|개수|합계|특정 분류가 할당된 자산 의 수를 나타냅니다(예: 해당 레이블로 분류됨).|분류, 소스|
|자산 분포ByStorageType|저장소 유형별 자산 배포|개수|합계|특정 저장소 유형이 있는 자산 수를 나타냅니다.|StorageType|
|번호OfAssets분류|분류가 하나 이상인 자산 수|개수|평균|태그 분류가 하나 이상 있는 자산 수를 나타냅니다.|None|
|스캔취소|스캔 취소|개수|합계|취소된 검사 수를 나타냅니다.|None|
|스캔 완료됨|스캔 완료|개수|합계|성공적으로 완료된 검사 수를 나타냅니다.|None|
|스캔 실패|스캔 실패|개수|합계|실패한 검사 수를 나타냅니다.|None|
|스캔시간 촬영|촬영 시간 스캔|초|합계|총 검색 시간(초)을 나타냅니다.|None|
|카탈로그활성 사용자|일일 활성 사용자|개수|합계|일일 활성 사용자 수|None|
|카탈로그 사용|작업별 사용량 분포|개수|합계|사용자가 카탈로그에 만드는 작업 수(예: 액세스, 검색, 용어집)를 나타냅니다.|작업(Operation)|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|FailedRuns|실패한 실행|개수|합계||파이프라인 이름, 활동이름|
|SuccessfulRuns|성공한 실행|개수|합계||파이프라인 이름, 활동이름|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PipelineFailedRuns|실패한 파이프라인 실행 메트릭|개수|합계||실패 유형, 이름|
|PipelineSucceededRuns|성공한 파이프라인 실행 메트릭|개수|합계||실패 유형, 이름|
|파이프라인취소런|취소된 파이프라인은 메트릭을 실행합니다.|개수|합계||실패 유형, 이름|
|ActivityFailedRuns|실패한 활동 실행 메트릭|개수|합계||활동 유형, 파이프 라인 이름, 실패 유형, 이름|
|ActivitySucceededRuns|성공한 활동 실행 메트릭|개수|합계||활동 유형, 파이프 라인 이름, 실패 유형, 이름|
|활동취소런|취소된 활동은 메트릭을 실행합니다.|개수|합계||활동 유형, 파이프 라인 이름, 실패 유형, 이름|
|TriggerFailedRuns|실패한 트리거 실행 메트릭|개수|합계||이름,실패 유형|
|TriggerSucceededRuns|성공한 트리거 실행 메트릭|개수|합계||이름,실패 유형|
|트리거취소 런|취소된 트리거는 메트릭을 실행합니다.|개수|합계||이름,실패 유형|
|IntegrationRuntimeCpuPercentage|통합 런타임 CPU 사용률|백분율|평균||통합런타임이름, 노드이름|
|IntegrationRuntimeAvailableMemory|통합 런타임 사용 가능한 메모리|바이트|평균||통합런타임이름, 노드이름|
|통합런타임평균태스크픽업지연|통합 런타임 큐 기간|초|평균||통합런타임네임|
|통합런타임큐길이|통합 런타임 큐 길이|개수|평균||통합런타임네임|
|통합런타임사용노드번호|통합 런타임 사용 가능한 노드 수|개수|평균||통합런타임네임|
|최대 허용된 리소스 카운트|허용되는 최대 엔터티 수|개수|최대||None|
|최대허용팩토리크기인Gb단위|최대 허용 공장 크기(GB 단위)|개수|최대||None|
|리소스 카운트|총 엔터티 수|개수|최대||None|
|팩토리사이즈인그유닛|총 공장 크기(GB 단위)|개수|최대||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|JobEndedSuccess|성공한 작업|개수|합계|성공한 작업의 수입니다.|None|
|JobEndedFailure|실패한 작업|개수|합계|실패한 작업 수입니다.|None|
|JobEndedCancelled|취소된 작업|개수|합계|취소된 작업 수입니다.|None|
|JobAUEndedSuccess|성공한 AU 시간|초|합계|성공한 작업에 대한 총 AU 시간입니다.|None|
|JobAUEndedFailure|실패한 AU 시간|초|합계|실패한 작업에 대한 총 AU 시간입니다.|None|
|JobAUEndedCancelled|취소된 AU 시간|초|합계|취소된 작업에 대한 총 AU 시간입니다.|None|
|작업 스테이지|스테이지의 채용|개수|합계|각 단계의 작업 수입니다.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalStorage|총 스토리지|바이트|최대|계정에 저장된 총 데이터 양.|None|
|DataWritten|기록된 데이터|바이트|합계|계정에 기록된 총 데이터 양.|None|
|DataRead|데이터 읽기|바이트|합계|계정에서 읽어 온 총 데이터 양.|None|
|WriteRequests|쓰기 요청|개수|합계|계정에 데이터 쓰기 요청 수.|None|
|ReadRequests|읽기 요청|개수|합계|계정에 데이터 읽기 요청 수.|None|


## <a name="microsoftdatashareaccounts"></a>마이크로소프트.데이터 쉐어/계정

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|공유 카운트|보낸 공유|개수|최대|계정에서 보낸 공유 수|공유 이름|
|공유 구독 수|받은 주식|개수|최대|계정에서 수신된 주식 수|공유 구독 이름|
|성공한 공유 동기화|보낸 공유 성공 스냅샷|개수|개수|계정에서 성공한 공유 스냅샷 수|None|
|실패한 공유 동기화|보낸 공유 실패 스냅샷|개수|개수|계정에서 보낸 공유 실패 스냅숏 수|None|
|성공한 공유 구독 동기화|받은 공유 성공 스냅샷|개수|개수|계정에서 수신된 공유 성공 스냅숏 수|None|
|실패한 공유 구독 동기화|받은 공유 실패 스냅샷|개수|개수|계정에서 수신된 공유 실패 스냅샷 수|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|None|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|None|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|None|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|None|
|storage_used|스토리지 사용됨|바이트|평균|스토리지 사용됨|None|
|storage_limit|스토리지 제한|바이트|최대|스토리지 제한|None|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|None|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|None|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|평균|서버 로그 스토리지 제한|None|
|active_connections|활성 연결 수|개수|평균|활성 연결 수|None|
|connections_failed|실패한 연결|개수|합계|실패한 연결|None|
|seconds_behind_master|복제 지연 시간(초)|개수|최대|복제 지연 시간(초)|None|
|backup_storage_used|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|None|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|None|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|None|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|None|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|None|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|None|
|storage_used|스토리지 사용됨|바이트|평균|스토리지 사용됨|None|
|storage_limit|스토리지 제한|바이트|최대|스토리지 제한|None|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|None|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|None|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|None|
|active_connections|활성 연결 수|개수|평균|활성 연결 수|None|
|connections_failed|실패한 연결|개수|합계|실패한 연결|None|
|seconds_behind_master|복제 지연 시간(초)|개수|최대|복제 지연 시간(초)|None|
|backup_storage_used|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|None|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|None|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|None|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|None|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|None|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|None|
|storage_used|스토리지 사용됨|바이트|평균|스토리지 사용됨|None|
|storage_limit|스토리지 제한|바이트|최대|스토리지 제한|None|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|None|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|None|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|None|
|active_connections|활성 연결 수|개수|평균|활성 연결 수|None|
|connections_failed|실패한 연결|개수|합계|실패한 연결|None|
|backup_storage_used|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|None|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|None|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|None|
|pg_replica_log_delay_in_seconds|복제본 지연 시간|초|최대|몇 초 만에 복제본 지연|None|
|pg_replica_log_delay_in_bytes|복제본 간 최대 지연 시간|바이트|최대|지연이 가장 지연되는 복제본의 바이트 지연|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>마이크로소프트.DBforPostgreSQL/서버v2

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|None|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|None|
|Iops|IOPS|개수|평균|초당 IO 운영|None|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|None|
|storage_used|스토리지 사용됨|바이트|평균|스토리지 사용됨|None|
|active_connections|활성 연결 수|개수|평균|활성 연결 수|None|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|None|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|None|


## <a name="microsoftdbforpostgresqlsingleservers"></a>마이크로소프트.DBforPostgreSQL/단일 서버

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|None|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|None|
|Iops|IOPS|개수|평균|초당 IO 운영|None|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|None|
|storage_used|스토리지 사용됨|바이트|평균|스토리지 사용됨|None|
|active_connections|활성 연결 수|개수|평균|활성 연결 수|None|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|None|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|None|
|connections_failed|실패한 연결|개수|합계|실패한 연결|None|
|connections_succeeded|성공한 연결|개수|합계|성공한 연결|None|
|maximum_used_transactionIDs|최대 사용된 트랜잭션 아이디|개수|평균|최대 사용된 트랜잭션 아이디|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|None|
|d2c.telemetry.ingress.success|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|None|
|c2d.commands.egress.complete.success|C2D 메시지 배달 완료|개수|합계|장치에서 성공적으로 완료된 클라우드-장치 메시지 배달 수|None|
|c2d.commands.egress.abandon.success|C2D 메시지가 중단되었습니다.|개수|합계|장치에서 포기한 클라우드-장치 메시지 수|None|
|c2d.commands.egress.reject.success|C2D 메시지가 거부됨|개수|합계|장치에서 거부된 클라우드-장치 메시지 수|None|
|C2D메시지 만료됨|C2D 메시지가 만료됨(미리 보기)|개수|합계|만료된 클라우드-장치 메시지 수|None|
|devices.totalDevices|총 디바이스(사용되지 않음)|개수|합계|IoT 허브에 등록된 디바이스 수|None|
|devices.connectedDevices.allProtocol|연결된 디바이스(사용되지 않음) |개수|합계|IoT 허브에 연결된 디바이스 수|None|
|d2c.telemetry.egress.success|라우팅: 배달된 원격 분석 메시지|개수|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|None|
|d2c.telemetry.egress.dropped|라우팅: 삭제된 원격 분석 메시지 |개수|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|None|
|d2c.telemetry.egress.orphaned|라우팅: 분리된 원격 분석 메시지 |개수|합계|메시지가 라우팅 규칙(대체 규칙 포함)과 일치하지 않았으므로 IoT Hub 라우팅에 의해 분리된 횟수입니다. |None|
|d2c.telemetry.egress.invalid|라우팅: 원격 분석 메시지 호환되지 않음|개수|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. 이 값은 재시도를 포함하지 않습니다.|None|
|d2c.telemetry.egress.fallback|라우팅: 대체에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|None|
|d2c.endpoints.egress.eventHubs|라우팅: 이벤트 허브에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 이벤트 허브 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|None|
|d2c.endpoints.latency.eventHubs|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 엔드포인트에 대한 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|None|
|d2c.endpoints.egress.serviceBusQueues|라우팅: Service Bus 큐에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|None|
|d2c.endpoints.latency.serviceBusQueues|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|None|
|d2c.endpoints.egress.serviceBusTopics|라우팅: Service Bus 토픽에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|None|
|d2c.endpoints.latency.serviceBusTopics|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|None|
|d2c.endpoints.egress.builtIn.events|라우팅: 메시지/이벤트에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 기본 제공 엔드포인트(메시지/이벤트)에 메시지를 성공적으로 배달한 횟수입니다.|None|
|d2c.endpoints.latency.builtIn.events|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 엔드포인트(메시지.이벤트)에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|None|
|d2c.endpoints.egress.storage|라우팅: 스토리지에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|None|
|d2c.endpoints.latency.storage|라우팅: 스토리지에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 스토리지 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|None|
|d2c.endpoints.egress.storage.bytes|라우팅: 스토리지에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 배달된 데이터 양입니다(바이트).|None|
|d2c.endpoints.egress.storage.blobs|라우팅: 스토리지에 배달된 Blob|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 Blob을 배달한 횟수입니다.|None|
|이벤트그리드 배달|이벤트 그리드 배달(미리 보기)|개수|합계|이벤트 그리드에 게시된 IoT Hub 이벤트 수입니다. 성공 및 실패 한 요청의 수에 대 한 결과 차원을 사용 합니다. EventType 차원은 이벤트 유형(를https://aka.ms/ioteventgrid)표시합니다.|리소스 ID, 결과, 이벤트 유형|
|이벤트그리드지연|이벤트 그리드 대기 시간(미리 보기)|밀리초|평균|Iot Hub 이벤트가 생성된 시점부터 이벤트 그리드에 이벤트가 게시된 시점까지의 평균 대기 시간(밀리초)입니다. 이 숫자는 모든 이벤트 유형 간의 평균입니다. EventType 차원을 사용하여 특정 유형의 이벤트의 대기 시간을 확인합니다.|리소스 ID, 이벤트 유형|
|라우팅 배달|라우팅 배달(미리 보기)|밀리초|합계|라우팅을 사용하여 모든 끝점에 메시지를 배달하려고 시도한 시간입니다. 성공 또는 실패 시도 수를 보려면 결과 차원을 사용합니다. 실패의 원인을 확인하려면 유효하지 않거나 삭제되었거나 분리된 경우와 같이 FailureReasonCategory 차원을 사용합니다. 또한 EndpointName 및 EndpointType 차원을 사용하여 다른 끝점에 전달된 메시지 수를 파악할 수도 있습니다. 메트릭 값은 메시지가 여러 끝점으로 배달되는 경우 또는 메시지가 동일한 끝점에 여러 번 배달되는 경우를 포함하여 각 배달 시도에 대해 하나씩 증가합니다.|ResourceId, 끝점 유형, 끝점 이름, 실패이유카테고리, 결과, 라우팅 소스|
|라우팅배달 지연|라우팅 배달 대기 시간(미리 보기)|밀리초|평균|IoT Hub에 메시지를 받는 것과 원격 분석 메시지 사이의 평균 대기 시간(밀리초)은 끝점으로 유입됩니다. 끝점 이름 및 끝점 유형 차원을 사용하여 다른 끝점에 대한 대기 시간을 이해할 수 있습니다.|ResourceId, 끝점 유형, 끝점 이름, 라우팅 소스|
|d2c.twin.read.success|디바이스에서의 성공한 쌍 읽기|개수|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|None|
|d2c.twin.read.failure|디바이스에서의 실패한 쌍 읽기|개수|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|None|
|d2c.twin.read.size|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|None|
|d2c.twin.update.success|디바이스에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|None|
|d2c.twin.update.failure|디바이스에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|None|
|d2c.twin.update.size|디바이스에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|None|
|c2d.methods.success|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|None|
|c2d.methods.failure|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|None|
|c2d.methods.requestSize|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|None|
|c2d.methods.responseSize|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|None|
|c2d.twin.read.success|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|None|
|c2d.twin.read.failure|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|None|
|c2d.twin.read.size|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|None|
|c2d.twin.update.success|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|None|
|c2d.twin.update.failure|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|None|
|c2d.twin.update.size|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|None|
|twinQueries.success|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|None|
|twinQueries.failure|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|None|
|twinQueries.resultSize|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|None|
|jobs.createTwinUpdateJob.success|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|None|
|jobs.createTwinUpdateJob.failure|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|None|
|jobs.createDirectMethodJob.success|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|None|
|jobs.createDirectMethodJob.failure|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|None|
|jobs.listJobs.success|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|None|
|jobs.listJobs.failure|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|None|
|jobs.cancelJob.success|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|None|
|jobs.cancelJob.failure|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|None|
|jobs.queryJobs.success|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|None|
|jobs.queryJobs.failure|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|None|
|jobs.completed|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|None|
|jobs.failed|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|None|
|d2c.telemetry.ingress.sendThrottle|제한 오류 수|개수|합계|디바이스 처리량 제한으로 인한 제한 오류 수|None|
|dailyMessageQuotaUsed|사용된 전체 메시지 수|개수|평균|현재 사용되는 전체 메시지 수|None|
|deviceDataUsage|총 장치 데이터 사용량|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|None|
|deviceDataUsageV2|총 디바이스 데이터 사용량(미리 보기)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|None|
|totalDeviceCount|총 디바이스(미리 보기)|개수|평균|IoT 허브에 등록된 디바이스 수|None|
|connectedDeviceCount|연결된 디바이스(미리 보기)|개수|평균|IoT 허브에 연결된 디바이스 수|None|
|구성|구성 메트릭|개수|합계|구성 작업에 대한 메트릭|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RegistrationAttempts|등록 시도|개수|합계|디바이스 등록 시도 수|프로비저닝 서비스 이름, IotHubName, 상태|
|DeviceAssignments|할당된 디바이스|개수|합계|IoT Hub에 할당된 디바이스 수|프로비저닝 서비스 이름, IotHubName|
|AttestationAttempts|증명 시도|개수|합계|디바이스 증명 시도 수|프로비저닝 서비스 이름, 상태, 프로토콜|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|추가 영역|지역 추가|개수|개수|지역 추가|지역|
|AvailableStorage|사용 가능한 스토리지|바이트|합계|5분 분 단위로 보고된 총 사용 가능한 저장소|컬렉션 이름, 데이터베이스 이름, 지역|
|CassandraConnectionClosures|Cassandra 연결 차단|개수|합계|닫혀 있는 카산드라 연결 수, 1분 분 단위로 보고|APIType, 지역, 폐쇄 이유|
|카산드라키스페이스삭제|카산드라 키스페이스 삭제|개수|개수|카산드라 키스페이스 삭제|리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|카산드라키스페이스스어풋업데이트|카산드라 키스페이스 처리량 업데이트|개수|개수|카산드라 키스페이스 처리량 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|카산드라키스페이스 업데이트|카산드라 키스페이스 업데이트|개수|개수|카산드라 키스페이스 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|CassandraRequestCharges|Cassandra 요청 요금|개수|합계|카산드라 요청에 대해 사용되는 루|APIType, 데이터베이스 이름, 컬렉션 이름, 지역, 작업 유형, 리소스 유형|
|CassandraRequests|Cassandra 요청|개수|개수|카산드라 요청 건수|APIType, 데이터베이스 이름, 컬렉션 이름, 지역, 작업 유형, 리소스 유형, 오류 코드|
|카산드라테이블삭제|카산드라 테이블 삭제|개수|개수|카산드라 테이블 삭제|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|카산드라테이블스루풋업데이트|카산드라 테이블 처리량 업데이트|개수|개수|카산드라 테이블 처리량 업데이트|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|카산드라테이블업데이트|카산드라 테이블 업데이트|개수|개수|카산드라 테이블 업데이트|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|계정 만들기|생성된 계정|개수|개수|생성된 계정|None|
|DataUsage|데이터 사용량|바이트|합계|총 데이터 사용량이 5분 분 단위로 보고되었습니다.|컬렉션 이름, 데이터베이스 이름, 지역|
|삭제 계정|계정 삭제됨|개수|개수|계정 삭제됨|None|
|DocumentCount|문서 수|개수|합계|총 문서 수 5분 세분으로 보고됨|컬렉션 이름, 데이터베이스 이름, 지역|
|DocumentQuota|문서 할당량|바이트|합계|총 스토리지 할당량이 5분 분 단위로 보고되었습니다.|컬렉션 이름, 데이터베이스 이름, 지역|
|그렘린데이터베이스삭제|그렘린 데이터베이스 삭제|개수|개수|그렘린 데이터베이스 삭제|리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|그렘린데이터베이스스루풋업데이트|그렘린 데이터베이스 처리량 업데이트|개수|개수|그렘린 데이터베이스 처리량 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|그렘린데이터베이스업데이트|그렘린 데이터베이스 업데이트|개수|개수|그렘린 데이터베이스 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|그렘린그래프삭제|그렘린 그래프 삭제|개수|개수|그렘린 그래프 삭제|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|그렘린그래프스루풋업데이트|그렘린 그래프 처리량 업데이트|개수|개수|그렘린 그래프 처리량 업데이트|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|그렘린그래프업데이트|그렘린 그래프 업데이트|개수|개수|그렘린 그래프 업데이트|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|인덱스 사용량|바이트|합계|총 인덱스 사용량이 5분 분 단위로 보고되었습니다.|컬렉션 이름, 데이터베이스 이름, 지역|
|MetadataRequests|메타데이터 요청|개수|개수|메타데이터 요청 수. Cosmos DB는 컬렉션, 데이터베이스 등과 해당 구성을 무료로 열거할 수 있는 각 계정에 대한 시스템 메타데이터 컬렉션을 유지 관리합니다.|데이터베이스 이름, 컬렉션 이름, 지역, 상태 코드, 역할|
|몽고컬렉션삭제|몽고 컬렉션 삭제됨|개수|개수|몽고 컬렉션 삭제됨|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|몽고컬렉션스루풋업데이트|몽고 컬렉션 처리량 업데이트|개수|개수|몽고 컬렉션 처리량 업데이트|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|몽고컬렉션업데이트|몽고 컬렉션 업데이트|개수|개수|몽고 컬렉션 업데이트|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|몽고DB데이터베이스업데이트|몽고 데이터베이스 업데이트|개수|개수|몽고 데이터베이스 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|몽고데이터베이스삭제|몽고 데이터베이스 삭제|개수|개수|몽고 데이터베이스 삭제|리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|몽고데이터베이스스루풋업데이트|몽고 데이터베이스 처리량 업데이트|개수|개수|몽고 데이터베이스 처리량 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|MongoRequestCharge|Mongo 요청 요금|개수|합계|사용된 Mongo 요청 단위|데이터베이스 이름, 컬렉션 이름, 지역, 명령 이름, 오류 코드, 상태|
|MongoRequests|Mongo 요청|개수|개수|생성된 Mongo 요청 수|데이터베이스 이름, 컬렉션 이름, 지역, 명령 이름, 오류 코드, 상태|
|몽고어카운트카운트|몽고 요청 요금|초당 개수|평균|몽고 요청 초당 카운트|데이터베이스 이름, 컬렉션 이름, 지역, 명령 이름, 오류 코드|
|몽고요청삭제|몽고 삭제 요청 속도|초당 개수|평균|몽고 초당 요청 삭제|데이터베이스 이름, 컬렉션 이름, 지역, 명령 이름, 오류 코드|
|몽고 요청 삽입|몽고 삽입 요청 속도|초당 개수|평균|몽고 삽입 초당 개수|데이터베이스 이름, 컬렉션 이름, 지역, 명령 이름, 오류 코드|
|몽고요청쿼리|몽고 쿼리 요청 속도|초당 개수|평균|초당 몽고 쿼리 요청|데이터베이스 이름, 컬렉션 이름, 지역, 명령 이름, 오류 코드|
|몽고요청업데이트|몽고 업데이트 요청 속도|초당 개수|평균|초당 몽고 업데이트 요청|데이터베이스 이름, 컬렉션 이름, 지역, 명령 이름, 오류 코드|
|정규화RU소비|정규화된 RU 소비량|백분율|최대|분당 최대 RU 소비 비율|컬렉션 이름, 데이터베이스 이름, 지역|
|ProvisionedThroughput|프로비전된 처리량|개수|최대|프로비전된 처리량|데이터베이스 이름, 컬렉션 이름|
|리전페노버|지역 이월|개수|개수|지역 이월|None|
|제거 지역|영역 제거됨|개수|개수|영역 제거됨|지역|
|ReplicationLatency|P99 복제 대기 시간|밀리초|평균|지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간|소스 지역, 대상 지역|
|서버사이드지연|서버 측 대기 시간|밀리초|평균|서버 측 대기 시간|데이터베이스 이름, 컬렉션 이름, 지역, 연결 모드, 작업 유형, PublicAPIType|
|ServiceAvailability|서비스 가용성|백분율|평균|계정 요청 가용성 1시간, 일 또는 월 세분성|None|
|SqlContainer삭제|Sql 컨테이너 삭제됨|개수|개수|Sql 컨테이너 삭제됨|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|SqlContainerThroughputUpdate|Sql 컨테이너 처리량이 업데이트되었습니다.|개수|개수|Sql 컨테이너 처리량이 업데이트되었습니다.|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainer업데이트|Sql 컨테이너 업데이트|개수|개수|Sql 컨테이너 업데이트|리소스 이름, 자식 리소스 이름, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabase삭제|Sql 데이터베이스 삭제됨|개수|개수|Sql 데이터베이스 삭제됨|리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|SqlDatabaseThroughput업데이트|Sql 데이터베이스 처리량이 업데이트되었습니다.|개수|개수|Sql 데이터베이스 처리량이 업데이트되었습니다.|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|SqlDatabase업데이트|Sql 데이터베이스 업데이트|개수|개수|Sql 데이터베이스 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|테이블 테이블 삭제|AzureTable 테이블 삭제됨|개수|개수|AzureTable 테이블 삭제됨|리소스 이름, ApiKind, ApiKindResourceType, 작업 유형|
|테이블 테이블스루풋업데이트|AzureTable 테이블 처리량 업데이트|개수|개수|AzureTable 테이블 처리량 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|테이블 테이블 업데이트|AzureTable 테이블 업데이트|개수|개수|AzureTable 테이블 업데이트|리소스 이름, ApiKind, ApiKind리소스 유형, IsThroughputRequest|
|TotalRequestUnits|총 요청 단위|개수|합계|사용된 요청 단위|데이터베이스 이름, 컬렉션 이름, 지역, 상태 코드, 작업 유형, 상태|
|TotalRequests|총 요청 수|개수|개수|요청 수|데이터베이스 이름, 컬렉션 이름, 지역, 상태 코드, 작업 유형, 상태|
|계정 키 업데이트|계정 키 업데이트|개수|개수|계정 키 업데이트|KeyType|
|업데이트계정네트워크설정|계정 네트워크 설정 업데이트|개수|개수|계정 네트워크 설정 업데이트|None|
|업데이트계정복제설정|계정 복제 설정 업데이트|개수|개수|계정 복제 설정 업데이트|None|
|업데이트진단설정|계정 진단 설정 업데이트|개수|개수|계정 진단 설정 업데이트|진단설정이름, 리소스그룹명|



## <a name="microsoftenterpriseknowledgegraphservices"></a>마이크로소프트.엔터프라이즈 지식 그래프/서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|트랜잭션 카운트|트랜잭션 수|개수|개수|총 거래 수|트랜잭션 카운트|
|SuccessCount|성공한 수|개수|개수|성공한 트랜잭션 수|SuccessCount|
|오류 수|실패한 수|개수|개수|실패한 트랜잭션 수|오류 수|
|성공지연|성공 대기 시간|밀리초|평균|성공적인 트랜잭션의 대기 시간|SuccessCount|

## <a name="microsofteventgriddomains"></a>마이크로소프트.이벤트 그리드/도메인

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|항목|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|주제, 오류 유형, 오류|
|게시성공지연InMs|성공 대기 시간 게시|밀리초|합계|성공 대기 시간을 밀리초 단위로 게시|None|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|주제, 이벤트 구독 이름, 도메인이벤트 구독이름|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|주제, 이벤트 구독 이름, 도메인이벤트구독이름, 오류, 오류 유형|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|주제, 이벤트 구독 이름, 도메인이벤트 구독이름|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|주제, 이벤트 구독 이름, 도메인이벤트 구독이름|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|주제, 이벤트 구독 이름, 도메인이벤트 구독이름, 드롭이유|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|주제, 이벤트 구독 이름, 도메인이벤트 구독이름, 데드레터이유|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|None|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|오류 유형, 오류|
|UnmatchedEventCount|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|None|
|게시성공지연InMs|성공 대기 시간 게시|밀리초|합계|성공 대기 시간을 밀리초 단위로 게시|None|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|이벤트 구독 이름|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, 오류 유형, 이벤트 구독 이름|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|이벤트 구독 이름|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|이벤트 구독 이름|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, 이벤트 구독 이름|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|데드레터이유, 이벤트 구독이름|

## <a name="microsofteventgridsystemtopics"></a>마이크로소프트.이벤트 그리드/시스템주제

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|None|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|오류 유형, 오류|
|UnmatchedEventCount|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|None|
|게시성공지연InMs|성공 대기 시간 게시|밀리초|합계|성공 대기 시간을 밀리초 단위로 게시|None|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|이벤트 구독 이름|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, 오류 유형, 이벤트 구독 이름|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|이벤트 구독 이름|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|이벤트 구독 이름|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, 이벤트 구독 이름|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|데드레터이유, 이벤트 구독이름|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|None|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, 오류 유형|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|None|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|None|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|드롭 이유|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|None|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|오류 유형, 오류|
|UnmatchedEventCount|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|None|
|게시성공지연InMs|성공 대기 시간 게시|밀리초|합계|성공 대기 시간을 밀리초 단위로 게시|None|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청|개수|합계|Microsoft.EventHub에 대한 성공한 요청.|엔터티 이름, 작업결과|
|ServerErrors|서버 오류.|개수|합계|Microsoft.EventHub에 대한 서버 오류.|엔터티 이름, 작업결과|
|UserErrors|사용자 오류.|개수|합계|Microsoft.EventHub에 대한 사용자 오류.|엔터티 이름, 작업결과|
|QuotaExceededErrors|할당량 초과 오류.|개수|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|엔터티 이름, 작업결과|
|ThrottledRequests|제한된 요청.|개수|합계|Microsoft.EventHub에 대한 제한된 요청.|엔터티 이름, 작업결과|
|IncomingRequests|들어오는 요청|개수|합계|Microsoft.EventHub에 대한 들어오는 요청.|EntityName|
|IncomingMessages|들어오는 메시지|개수|합계|Microsoft.EventHub에 대한 들어오는 메시지.|EntityName|
|OutgoingMessages|보내는 메시지|개수|합계|Microsoft.EventHub에 대한 보내는 메시지.|EntityName|
|IncomingBytes|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|EntityName|
|OutgoingBytes|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|EntityName|
|ActiveConnections|ActiveConnections|개수|평균|Microsoft.EventHub에 대한 총 활성 연결.|None|
|ConnectionsOpened|열린 연결.|개수|평균|Microsoft.EventHub에 대한 열린 연결.|EntityName|
|ConnectionsClosed|끊어진 연결.|개수|평균|Microsoft.EventHub에 대한 끊어진 연결.|EntityName|
|CaptureBacklog|캡처 백로그.|개수|합계|Microsoft.EventHub에 대한 캡처 백로그.|EntityName|
|CapturedMessages|캡처된 메시지.|개수|합계|Microsoft.EventHub에 대한 캡처된 메시지.|EntityName|
|CapturedBytes|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|EntityName|
|크기|크기|바이트|평균|EventHub 크기(바이트)|EntityName|
|INREQS|들어오는 요청(더 이상 사용되지 않는)|개수|합계|네임스페이스에 대한 총 수신 요청(더 이상 사용되지 않는 요청)|None|
|SUCCREQ|성공한 요청(더 이상 사용되지 않는)|개수|합계|네임스페이스에 대한 총 성공 요청(더 이상 사용되지 않는 요청)|None|
|FAILREQ|실패한 요청(더 이상 사용되지 않는)|개수|합계|네임스페이스에 대한 총 실패한 요청(더 이상 사용되지 않는 요청)|None|
|SVRBSY|서버 사용 중 오류(더 이상 사용되지 않습니다).|개수|합계|네임스페이스에 대한 총 서버 사용 중 오류(더 이상 사용되지 않습니다).|None|
|INTERR|내부 서버 오류(더 이상 사용되지 않습니다)|개수|합계|네임스페이스에 대한 총 내부 서버 오류(더 이상 사용되지 않습니다).|None|
|MISCERR|기타 오류(더 이상 사용되지 않습니다)|개수|합계|네임스페이스에 대한 총 실패한 요청(더 이상 사용되지 않는 요청)|None|
|INMSGS|들어오는 메시지(더 이상 사용되지 않음)|개수|합계|네임스페이스에 들어오는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 수신 메시지 메트릭을 사용하십시오(더 이상 사용되지 않습니다).|None|
|EHINMSGS|들어오는 메시지(사용되지 않음)|개수|합계|네임스페이스에 대한 총 수신 메시지(더 이상 사용되지 않습니다).|None|
|OUTMSGS|발신 메시지(더 이상 사용되지 않음)|개수|합계|네임스페이스에 보내는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 나가는 메시지 메트릭을 사용하십시오(더 이상 사용되지 않습니다).|None|
|EHOUTMSGS|보내는 메시지(사용되지 않음)|개수|합계|네임스페이스에 대한 총 나가는 메시지(더 이상 사용되지 않습니다).|None|
|EHINMBS|들어오는 바이트(더 이상 사용되지 않음)|바이트|합계|네임스페이스에 들어오는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 들어오는 바이트 메트릭을 사용하십시오(더 이상 사용되지 않습니다).|None|
|EHINBYTES|들어오는 바이트(사용되지 않음)|바이트|합계|네임스페이스에 대한 이벤트 허브 수신 메시지 처리량(더 이상 사용되지 않습니다).|None|
|EHOUTMBS|나가는 바이트(더 이상 사용되지 않음)|바이트|합계|네임스페이스에 보내는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 나가는 바이트 메트릭을 사용하십시오(더 이상 사용되지 않습니다).|None|
|EHOUTBYTES|보내는 바이트(사용되지 않음)|바이트|합계|네임스페이스에 대한 이벤트 허브 나가는 메시지 처리량(더 이상 사용되지 않습니다).|None|
|EHABL|백로그 메시지 보관(더 이상 사용되지 않습니다)|개수|합계|네임스페이스에 대한 백로그에 있는 이벤트 허브 아카이브 메시지(더 이상 사용되지 않습니다).|None|
|EHAMSGS|메시지 보관(더 이상 사용되지 않습니다)|개수|합계|네임스페이스에 이벤트 허브가 보관된 메시지(더 이상 사용되지)|None|
|EHAMBS|메시지 처리량 보관(더 이상 사용되지)|바이트|합계|네임스페이스에서 이벤트 허브에 메시지 처리량을 보관했습니다(더 이상 사용되지 않습니다).|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청|개수|합계|Microsoft.EventHub에 대한 성공한 요청.|OperationResult|
|ServerErrors|서버 오류.|개수|합계|Microsoft.EventHub에 대한 서버 오류.|OperationResult|
|UserErrors|사용자 오류.|개수|합계|Microsoft.EventHub에 대한 사용자 오류.|OperationResult|
|QuotaExceededErrors|할당량 초과 오류.|개수|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|OperationResult|
|ThrottledRequests|제한된 요청.|개수|합계|Microsoft.EventHub에 대한 제한된 요청.|OperationResult|
|IncomingRequests|들어오는 요청|개수|합계|Microsoft.EventHub에 대한 들어오는 요청.|None|
|IncomingMessages|들어오는 메시지|개수|합계|Microsoft.EventHub에 대한 들어오는 메시지.|None|
|OutgoingMessages|보내는 메시지|개수|합계|Microsoft.EventHub에 대한 보내는 메시지.|None|
|IncomingBytes|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|None|
|OutgoingBytes|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|None|
|ActiveConnections|ActiveConnections|개수|평균|Microsoft.EventHub에 대한 총 활성 연결.|None|
|ConnectionsOpened|열린 연결.|개수|평균|Microsoft.EventHub에 대한 열린 연결.|None|
|ConnectionsClosed|끊어진 연결.|개수|평균|Microsoft.EventHub에 대한 끊어진 연결.|None|
|CaptureBacklog|캡처 백로그.|개수|합계|Microsoft.EventHub에 대한 캡처 백로그.|None|
|CapturedMessages|캡처된 메시지.|개수|합계|Microsoft.EventHub에 대한 캡처된 메시지.|None|
|CapturedBytes|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|None|
|CPU|CPU|백분율|최대|이벤트 허브 클러스터에 대한 CPU 사용률(백분율)|역할|
|AvailableMemory|사용 가능한 메모리|백분율|최대|총 메모리의 백분율로 이벤트 허브 클러스터에 사용할 수 있는 메모리입니다.|역할|
|크기|EventHub 크기(바이트)|바이트|평균|EventHub 크기(바이트)|역할|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|GatewayRequests|게이트웨이 요청|개수|합계|게이트웨이 요청 수|HttpStatus|
|CategorizedGatewayRequests|분류된 게이트웨이 요청|개수|합계|범주별 게이트웨이 요청 수(1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|누액티브 워커|활성 근로자 수|개수|최대|활성 근로자 수|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ObservedMetricValue|관찰된 메트릭 값|개수|평균|실행될 때 자동 크기 조정에서 계산된 값|MetricTriggerSource|
|MetricThreshold|메트릭 임계값|개수|평균|자동 크기 조정이 실행되었을 때 구성된 자동 크기 조정 임계값입니다.|MetricTriggerRule|
|ObservedCapacity|관찰된 용량|개수|평균|실행될 때 자동 크기 조정을 위해 보고된 용량입니다.|None|
|ScaleActionsInitiated|시작된 크기 조정 작업|개수|합계|크기 조정 작업의 방향입니다.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|가용성결과/가용성백분율|가용성|백분율|평균|성공적으로 완료된 가용성 테스트의 백분율|가용성결과/이름, 가용성결과/위치|
|가용성결과/수|가용성 테스트|개수|개수|가용성 테스트 수|가용성결과/이름, 가용성결과/위치, 가용성결과/성공|
|availabilityResults/duration|가용성 테스트 기간|밀리초|평균|가용성 테스트 기간|가용성결과/이름, 가용성결과/위치, 가용성결과/성공|
|browserTimings/networkDuration|페이지 로드 네트워크 연결 시간|밀리초|평균|사용자 요청과 네트워크 연결 사이의 시간입니다. DNS 조회 및 전송 연결을 포함합니다.|None|
|browserTimings/processingDuration|클라이언트 처리 시간|밀리초|평균|DOM을 로드할 때부터 문서의 마지막 바이트를 받는 사이의 시간입니다. 비동기 요청은 계속 처리 중일 수 있습니다.|None|
|browserTimings/receiveDuration|응답 수신 시간|밀리초|평균|첫 번째 및 마지막 바이트 사이 또는 연결 끊기까지의 시간입니다.|None|
|browserTimings/sendDuration|요청 전송 시간|밀리초|평균|네트워크 연결과 첫 번째 바이트 받기 사이의 시간입니다.|None|
|browserTimings/totalDuration|브라우저 페이지 로드 시간|밀리초|평균|사용자가 요청한 때부터 DOM, 스타일시트, 스크립트 및 이미지가 로드될 때까지 소요된 시간입니다.|None|
|dependencies/count|종속성 호출|개수|개수|외부 리소스에 대해 애플리케이션이 만든 호출 수입니다.|종속성/ 유형, 종속성/성능버킷, 종속성/성공, 종속성/대상, 종속성/결과코드, 작업/합성, 클라우드/역할인스턴스, 클라우드/역할이름|
|dependencies/duration|종속성 기간|밀리초|평균|외부 리소스에 대한 서버 애플리케이션의 호출 기간입니다.|종속성/ 유형, 종속성/성능버킷, 종속성/성공, 종속성/대상, 종속성/결과코드, 작업/합성, 클라우드/역할인스턴스, 클라우드/역할이름|
|dependencies/failed|종속성 호출 실패|개수|개수|외부 리소스에 대해 애플리케이션이 만든 실패한 종속성 호출 수입니다.|종속성/ 유형, 종속성/성능버킷, 종속성/성공, 종속성/대상, 종속성/결과코드, 작업/합성, 클라우드/역할인스턴스, 클라우드/역할이름|
|pageViews/count|페이지 보기|개수|개수|페이지 보기 수입니다.|작업/합성, 클라우드/역할이름|
|pageViews/duration|페이지 보기 로드 시간|밀리초|평균|페이지 보기 로드 시간|작업/합성, 클라우드/역할이름|
|performanceCounters/requestExecutionTime|HTTP 요청 실행 시간|밀리초|평균|가장 최근 요청의 실행 시간입니다.|cloud/roleInstance|
|performanceCounters/requestsInQueue|애플리케이션 큐의 HTTP 요청|개수|평균|애플리케이션 요청 큐의 길이입니다.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 요청 속도|초당 개수|평균|ASP.NET에서 애플리케이션에 전송된 모든 요청의 속도(초)입니다.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|예외 속도|초당 개수|평균|.NET 예외 및 .NET 예외로 변환된 관리되지 않는 예외를 포함하여 Windows에 보고된 처리된 예외 및 처리되지 않은 예외 수입니다.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|프로세스 IO 속도|초당 바이트 수|평균|파일, 네트워크 및 디바이스에서 읽고 쓴 초당 총 바이트 수입니다.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU 프로세스|백분율|평균|모든 프로세스 스레드가 명령을 실행하기 위해 프로세서를 사용한 경과된 시간의 백분율입니다. 0~100 사이로 달라질 수 있습니다. 이 메트릭은 w3wp 프로세스만의 성능을 나타냅니다.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|프로세서 시간|백분율|평균|프로세서가 비 유휴 스레드에 소요한 시간의 비율입니다.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|사용 가능한 메모리|바이트|평균|프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리입니다.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|프로세스 프라이빗 바이트|바이트|평균|모니터링되는 애플리케이션의 프로세스에 독점적으로 할당된 메모리입니다.|cloud/roleInstance|
|requests/duration|서버 응답 시간|밀리초|평균|HTTP 요청을 받은 후 응답 전송을 완료한 때까지의 시간입니다.|요청/성능버킷, 요청/결과코드, 작업/합성, 클라우드/역할 인스턴스, 요청/성공, 클라우드/역할 이름|
|requests/count|서버 요청|개수|개수|완료된 HTTP 요청 수입니다.|요청/성능버킷, 요청/결과코드, 작업/합성, 클라우드/역할 인스턴스, 요청/성공, 클라우드/역할 이름|
|requests/failed|실패한 요청|개수|개수|실패한 것으로 표시된 HTTP 요청 수입니다. 대부분의 경우 응답 코드가 >= 400이고 401과 같지 않은 요청입니다.|요청/성능버킷, 요청/결과코드, 요청/성공, 작업/합성, 클라우드/역할인스턴스, 클라우드/역할이름|
|요청/요금|서버 요청 속도|초당 개수|평균|초당 서버 요청 속도|요청/성능버킷, 요청/결과코드, 작업/합성, 클라우드/역할 인스턴스, 요청/성공, 클라우드/역할 이름|
|exceptions/count|예외|개수|개수|모든 Catch되지 않은 예외의 결합된 수입니다.|클라우드/역할 이름, 클라우드/역할인스턴스, 클라이언트/유형|
|exceptions/browser|브라우저 예외|개수|개수|브라우저에서 발생한 확인할 수 없는 예외의 개수입니다.|클라이언트/isServer, 클라우드/역할 이름|
|exceptions/server|서버 예외|개수|개수|서버 애플리케이션에서 발생된 확인할 수 없는 예외의 수입니다.|클라이언트/isServer, 클라우드/역할 이름, 클라우드/역할인스턴스|
|traces/count|Traces|개수|개수|문서 개수 추적|추적/심각도 수준, 작업/합성, 클라우드/역할 이름, 클라우드/역할인스턴스|


## <a name="microsoftiotcentraliotapps"></a>마이크로소프트.IoTCentral/IoTApps

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|connectedDeviceCount|연결된 총 장치|개수|평균|IoT Central에 연결된 장치 수|None|
|c2d.property.read.success|IoT Central에서 성공적인 장치 속성 읽기|개수|합계|IoT Central에서 시작된 모든 성공적인 속성 읽기 수|None|
|c2d.property.read.실패|IoT Central에서 실패한 장치 속성 읽기|개수|합계|IoT Central에서 시작된 실패한 모든 속성 읽기 수|None|
|d2c.property.read.success|장치에서 성공적인 장치 속성 읽기|개수|합계|장치에서 시작된 모든 성공적인 속성 읽기 수|None|
|d2c.property.read.실패|장치에서 실패한 장치 속성 읽기|개수|합계|장치에서 시작된 실패한 모든 속성 읽기 수|None|
|c2d.property.update.success|IoT Central의 성공적인 장치 속성 업데이트|개수|합계|IoT Central에서 시작된 모든 성공적인 속성 업데이트 수|None|
|c2d.property.update.실패|IoT Central에서 실패한 장치 속성 업데이트|개수|합계|IoT Central에서 시작된 실패한 모든 속성 업데이트 수|None|
|d2c.property.update.성공|장치에서 성공적인 장치 속성 업데이트|개수|합계|장치에서 시작된 모든 성공적인 속성 업데이트 수|None|
|d2c.property.update.실패|장치에서 실패한 장치 속성 업데이트|개수|합계|장치에서 시작된 실패한 모든 속성 업데이트 수|None|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ServiceApiHit|Service API 총 방문 횟수|개수|개수|Service API의 총 방문 횟수|활동 유형, 활동 이름|
|ServiceApiLatency|전체 Service API 대기 시간|밀리초|평균|Service API 요청의 전체 대기 시간|활동 유형, 활동 이름, 상태 코드, 상태 코드 클래스|
|ServiceApiResult|Service API 총 결과|개수|개수|Service API의 총 결과 수|활동 유형, 활동 이름, 상태 코드, 상태 코드 클래스|
|채도 신발 상자|전체 볼트 채도|백분율|평균|사용된 볼트 용량|활동 유형, 활동 이름, 트랜잭션 유형|
|가용성|전체 볼트 가용성|백분율|평균|볼트, 가용성 요청|활동 유형, 활동 이름, 상태 코드, 상태 코드 클래스|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|캐시 사용률|캐시 사용률|백분율|평균|클러스터 범위의 사용률 수준|None|
|QueryDuration|쿼리 기간|밀리초|평균|쿼리 지속 시간(초)|QueryStatus|
|인기 활용|섭취 활용도|백분율|평균|클러스터에서 사용되는 수집 슬롯의 비율|None|
|KeepAlive|살아 남기|개수|평균|온전성 검사는 쿼리에 대한 클러스터 응답을 나타냅니다.|None|
|섭취볼륨인MB|섭취 볼륨(MB)|개수|합계|클러스터에 수집된 데이터의 전체 볼륨(MB)|데이터베이스|
|인시션지연시간인초|섭취 대기 시간(초)|초|평균|원본(예: EventHub에 있는 메시지)에서 클러스터로 수집 시간(초)|None|
|이벤트처리된ForEventHubs|처리된 이벤트(이벤트/IoT 허브용)|개수|합계|이벤트/IoT Hub에서 인과시 클러스터에서 처리되는 이벤트 수|EventStatus|
|IngestionResult|섭취 결과|개수|개수|수집 작업 수|IngestionResultDetails|
|CPU|CPU|백분율|평균|CPU 사용률 수준|None|
|연속수출넘의기록수출|연속 내보내기 – 내보낸 레코드의 num|개수|합계|내보내기 작업 중에 작성된 모든 저장소 아티팩트에 대해 내보낸 레코드 수|연속내보내기 이름, 데이터베이스|
|내보내기 사용률|내보내기 활용도|백분율|최대|수출 활용도|None|
|연속수출보류카운트|연속 내보내기 보류 중인 수|개수|최대|실행 준비가 보류 중인 연속 내보내기 작업 수|None|
|연속수출맥스대기분|지속적인 내보내기 최대 지각|개수|최대|클러스터의 지속적인 내보내기 작업에 의해 보고된 대기 시간(분)|None|
|연속수출결과|연속 내보내기 결과|개수|개수|연속 내보내기성공 또는 실패 여부|연속내보내기 이름, 결과, 데이터베이스|
|스트리밍가장지속시간|스트리밍 인더스트 기간|밀리초|평균|밀리초 단위로 스트리밍 인더스트리 기간|None|
|스트리밍가장데이터레이트|스트리밍 인제스트 데이터 속도|개수|평균|스트리밍 수집 데이터 속도(초당 MB)|None|
|김이 모락모락 나는요청서|스트리밍 인제스트 요청 속도|개수|속도 요청Per초|스트리밍 인제스트 요청 속도(초당 요청)|None|
|스트리밍가장결과|스트리밍 인제스트 결과|개수|평균|스트리밍 인제스트 결과|결과|
|총 수의 동시 전류쿼리|총 동시 쿼리 수|개수|합계|총 동시 쿼리 수|None|
|총 수의제한이있는 쿼리|제한된 쿼리의 총 수|개수|합계|제한된 쿼리의 총 수|None|
|총 수의제한 명령|제한 명령의 총 수|개수|합계|제한 명령의 총 수|CommandType|
|총 수범위|총 익스텐트 수|개수|합계|총 데이터 범위 수|None|
|InstanceCount|인스턴스 수|개수|평균|총 인스턴스 수|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RunsStarted|실행 시작됨|개수|합계|실행 시작된 워크플로 수|None|
|RunsCompleted|실행 완료됨|개수|합계|실행 완료된 워크플로 수|None|
|RunsSucceeded|실행 성공함|개수|합계|실행 성공한 워크플로 수|None|
|RunsFailed|실행 실패함|개수|합계|실행 실패한 워크플로 수|None|
|RunsCancelled|실행 취소됨|개수|합계|실행 취소된 워크플로 수|None|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|None|
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|None|
|RunThrottledEvents|실행 제한 이벤트|개수|합계|워크플로 작업 또는 트리거 제한 이벤트 수|None|
|런스타트스로틀이벤트|시작 제한 이벤트 실행|개수|합계|워크플로 실행 시작 제한 이벤트 수입니다.|None|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|None|
|ActionsStarted|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|None|
|ActionsCompleted|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|None|
|ActionsSucceeded|작업 성공함 |개수|합계|성공한 워크플로 작업 수|None|
|ActionsFailed|작업 실패함 |개수|합계|실패한 워크플로 작업 수|None|
|ActionsSkipped|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|None|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|None|
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|None|
|ActionThrottledEvents|작업 제한 이벤트|개수|합계|워크플로 작업 제한 이벤트 수|None|
|TriggersStarted|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|None|
|TriggersCompleted|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|None|
|TriggersSucceeded|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|None|
|TriggersFailed|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|None|
|TriggersSkipped|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|None|
|TriggersFired|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|None|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|None|
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|None|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|None|
|TriggerThrottledEvents|트리거 제한 이벤트|개수|합계|워크플로 트리거 제한 이벤트 수|None|
|BillableActionExecutions|청구 가능한 작업 실행|개수|합계|청구되는 워크플로 작업 실행 수.|None|
|BillableTriggerExecutions|청구 가능한 트리거 실행|개수|합계|청구되는 워크플로 트리거 실행 수.|None|
|TotalBillableExecutions|총 청구 가능 실행|개수|합계|청구되는 워크플로 실행 수.|None|
|BillingUsageNativeOperation|기본 작업 실행에 대한 청구 사용량|개수|합계|청구되는 기본 작업 실행 수입니다.|None|
|BillingUsageStandardConnector|표준 커넥터 실행에 대한 청구 사용량|개수|합계|청구되는 표준 커넥터 실행 수입니다.|None|
|BillingUsageStorageConsumption|스토리지 소비 실행에 대한 청구 사용량|개수|합계|청구되는 스토리지 소비 실행 수입니다.|None|

## <a name="microsoftlogicintegrationserviceenvironments"></a>마이크로소프트.로직/통합서비스환경

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RunsStarted|실행 시작됨|개수|합계|실행 시작된 워크플로 수|None|
|RunsCompleted|실행 완료됨|개수|합계|실행 완료된 워크플로 수|None|
|RunsSucceeded|실행 성공함|개수|합계|실행 성공한 워크플로 수|None|
|RunsFailed|실행 실패함|개수|합계|실행 실패한 워크플로 수|None|
|RunsCancelled|실행 취소됨|개수|합계|실행 취소된 워크플로 수|None|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|None|
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|None|
|RunThrottledEvents|실행 제한 이벤트|개수|합계|워크플로 작업 또는 트리거 제한 이벤트 수|None|
|런스타트스로틀이벤트|시작 제한 이벤트 실행|개수|합계|워크플로 실행 시작 제한 이벤트 수입니다.|None|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|None|
|ActionsStarted|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|None|
|ActionsCompleted|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|None|
|ActionsSucceeded|작업 성공함 |개수|합계|성공한 워크플로 작업 수|None|
|ActionsFailed|작업 실패함 |개수|합계|실패한 워크플로 작업 수|None|
|ActionsSkipped|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|None|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|None|
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|None|
|ActionThrottledEvents|작업 제한 이벤트|개수|합계|워크플로 작업 제한 이벤트 수|None|
|TriggersStarted|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|None|
|TriggersCompleted|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|None|
|TriggersSucceeded|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|None|
|TriggersFailed|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|None|
|TriggersSkipped|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|None|
|TriggersFired|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|None|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|None|
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|None|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|None|
|TriggerThrottledEvents|트리거 제한 이벤트|개수|합계|워크플로 트리거 제한 이벤트 수|None|
|통합 서비스환경워크플로우사용|통합 서비스 환경을 위한 워크플로 프로세서 사용|백분율|평균|통합 서비스 환경에 대한 워크플로 프로세서 사용.|None|
|통합 서비스환경워크워크메모리사용|통합 서비스 환경을 위한 워크플로 메모리 사용량|백분율|평균|통합 서비스 환경에 대한 워크플로 메모리 사용량입니다.|None|
|통합 서비스환경커넥터사용|통합 서비스 환경을 위한 커넥터 프로세서 사용|백분율|평균|통합 서비스 환경에 대한 커넥터 프로세서 사용.|None|
|통합 서비스환경커넥터메모리사용|통합 서비스 환경을 위한 커넥터 메모리 사용량|백분율|평균|통합 서비스 환경에 대한 커넥터 메모리 사용.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|취소된 실행|취소된 실행|개수|합계|이 작업 영역에 대해 취소된 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|요청된 실행 취소|요청된 실행 취소|개수|합계|이 작업 영역에 대해 취소가 요청된 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|완료된 실행|완료된 실행|개수|합계|이 작업 영역에 대해 성공적으로 완료된 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|실패한 실행|실패한 실행|개수|합계|이 작업 영역에 대해 실패한 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|실행 종료|실행 종료|개수|합계|이 작업 영역에 대해 종료 상태로 진입한 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|실행에 응답하지 않음|실행에 응답하지 않음|개수|합계|이 작업 영역에 응답하지 않는 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|시작되지 않음 실행|시작되지 않음 실행|개수|합계|이 작업 영역에 대해 시작되지 않은 상태의 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|실행 준비|실행 준비|개수|합계|이 작업 영역에 대해 준비하는 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|프로비저닝 실행|프로비저닝 실행|개수|합계|이 작업 영역에 대해 프로비전중인 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|큐에 대기된 실행|큐에 대기된 실행|개수|합계|이 작업 영역에 대해 큐에 대기중인 실행 수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|시작 된 실행|시작 된 실행|개수|합계|이 작업 영역에 대해 시작된 실행 횟수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|실행 시작|실행 시작|개수|합계|이 작업 영역에 대해 시작된 실행 횟수|시나리오, 실행 유형, 게시 된 파이프 라인 Id, 계산 유형, 파이프 라인 단계 유형|
|오류|오류|개수|합계|이 작업 영역의 실행 오류 수|시나리오|
|경고|경고|개수|합계|이 작업 영역의 실행 경고 수|시나리오|
|모델 레지스터 성공|모델 레지스터 성공|개수|합계|이 작업 영역에서 성공한 모델 등록 수|시나리오|
|모델 레지스터 실패|모델 레지스터 실패|개수|합계|이 작업 영역에서 실패한 모델 등록 수|시나리오, 상태 코드|
|모델 배포 시작|모델 배포 시작|개수|합계|이 작업 영역에서 시작된 모델 배포 수|시나리오|
|모델 배포 성공|모델 배포 성공|개수|합계|이 작업 영역에서 성공한 모델 배포 수|시나리오|
|모델 배포 실패|모델 배포 실패|개수|합계|이 작업 영역에서 실패한 모델 배포 수|시나리오, 상태 코드|
|총 노드|총 노드|개수|평균|총 노드 수입니다. 이 합계에는 일부 활성 노드, 유휴 노드, 사용할 수 없는 노드, 미리 예약된 노드, 노드 를 떠나는 것이 포함됩니다.|시나리오, 클러스터 이름|
|활성 노드|활성 노드|개수|평균|Acitve 노드 수입니다. 작업을 적극적으로 실행하는 노드입니다.|시나리오, 클러스터 이름|
|유휴 노드|유휴 노드|개수|평균|유휴 노드 수입니다. 유휴 노드는 작업을 실행하지 않지만 가능한 경우 새 작업을 수락할 수 있는 노드입니다.|시나리오, 클러스터 이름|
|사용할 수 없는 노드|사용할 수 없는 노드|개수|평균|사용할 수 없는 노드 수입니다. 일부 해결할 수 없는 문제로 인해 사용할 수 없는 노드가 작동하지 않습니다. Azure는 이러한 노드를 재활용합니다.|시나리오, 클러스터 이름|
|선점노드|선점노드|개수|평균|선점된 노드 수입니다. 이러한 노드는 사용 가능한 노드 풀에서 제거되는 우선 순위가 낮은 노드입니다.|시나리오, 클러스터 이름|
|노드 를 떠나기|노드 를 떠나기|개수|평균|떠나는 노드 수입니다. 노드를 떠나는 것은 작업 처리를 막 완료한 노드이며 유휴 상태로 이동합니다.|시나리오, 클러스터 이름|
|총 코어|총 코어|개수|평균|총 코어 수|시나리오, 클러스터 이름|
|액티브 코어|액티브 코어|개수|평균|활성 코어 수|시나리오, 클러스터 이름|
|유휴 코어|유휴 코어|개수|평균|유휴 코어 수|시나리오, 클러스터 이름|
|사용할 수 없는 코어|사용할 수 없는 코어|개수|평균|사용할 수 없는 코어 수|시나리오, 클러스터 이름|
|선점코어|선점코어|개수|평균|선점코어 수|시나리오, 클러스터 이름|
|코어를 남기기|코어를 남기기|개수|평균|코어를 남기는 수|시나리오, 클러스터 이름|
|할당량 사용률|할당량 사용률|개수|평균|할당량 사용 비율|시나리오, 클러스터 이름, VmFamilyName, VmPriority|
|CPU 사용률|CPU 사용률|개수|평균|CPU(미리 보기)|시나리오, runId, 노드 Id, 생성 된 시간|
|Gpu 사용률|Gpu 사용률|개수|평균|GPU(미리 보기)|시나리오, runId, 노드 Id, 생성 된 시간, 장치 ID|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|사용|사용|개수|개수|API 호출 수|ApiCategory, ApiName, 결과 유형, 응답 코드|
|가용성|가용성|백분율|평균|API 의 가용성|Api범주, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>마이크로소프트.미디어/미디어 서비스/스트리밍엔드포인트

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|송신|송신|바이트|합계|송신 데이터의 양(바이트)입니다.|OutputFormat|
|SuccessE2ELatency|성공은 종료 시간 끝까지|밀리초|평균|밀리초 단위로 성공한 요청에 대한 평균 대기 시간입니다.|OutputFormat|
|요청|요청|개수|합계|스트리밍 엔드포인트에 대한 요청입니다.|출력 형식, httpStatusCode, 오류 코드|


## <a name="microsoftmediamediaservices"></a>마이크로소프트.미디어/미디어 서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|자산 할당량|자산 할당량|개수|평균|현재 미디어 서비스 계정에 허용되는 자산 수|None|
|자산 계산|자산 수|개수|평균|현재 미디어 서비스 계정에 이미 생성된 자산 수|None|
|자산할당량사용비율|자산 할당량 사용 비율|백분율|평균|현재 미디어 서비스 계정에서 사용된 자산 백분율|None|
|콘텐츠 키정책 할당량|콘텐츠 키 정책 할당량|개수|평균|현재 미디어 서비스 계정에 허용되는 콘텐츠 키 경찰 수|None|
|콘텐츠 키정책 카운트|콘텐츠 키 정책 수|개수|평균|현재 미디어 서비스 계정에서 이미 생성된 콘텐츠 키 정책 수|None|
|콘텐츠 키정책사용백분율|콘텐츠 키 정책 할당량 사용 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용되는 콘텐츠 키 정책|None|
|스트리밍정책할당량|스트리밍 정책 할당량|개수|평균|현재 미디어 서비스 계정에 허용되는 스트리밍 정책 수|None|
|스트리밍정책카운트|스트리밍 정책 수|개수|평균|현재 미디어 서비스 계정에 이미 생성된 스트리밍 정책 수|None|
|스트리밍정책할당량사용비율|스트리밍 정책 할당량 사용 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용되는 스트리밍 정책 백분율|None|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>마이크로 소프트.혼합현실 / 원격 렌더링 계정

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|자산변환됨|변환된 자산|개수|합계|변환된 총 자산 수|AppId, 리소스 ID, SDKVersion|
|액티브 렌더링 세션|활성 렌더링 세션|개수|합계|활성 렌더링 세션의 총 수|AppId, 리소스 ID, 세션 유형, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>마이크로소프트.넷앱/넷앱계정/용량풀/볼륨

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|AverageReadLatency|평균 읽기 대기 시간|밀리초|평균|작업당 평균 읽기 대기 시간(밀리초)|None|
|AverageWriteLatency|평균 쓰기 대기 시간|밀리초|평균|작업당 평균 쓰기 대기 시간(밀리초)|None|
|VolumeLogicalSize|볼륨 소비 크기|바이트|평균|볼륨의 논리적 크기(사용되는 바이트)|None|
|VolumeSnapshotSize|볼륨 스냅샷 크기|바이트|평균|볼륨의 모든 스냅샷 크기|None|
|ReadIops|읽기 IOPS|초당 개수|평균|초당 읽기 입력/출력 작업|None|
|WriteIops|쓰기 IOPS|초당 개수|평균|초당 쓰기 입력/출력 작업|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|볼륨 크기에 할당된 풀|바이트|평균|풀의 사용되는 할당된 크기|None|
|VolumePoolTotalLogicalSize|풀 소비 크기|바이트|평균|풀에 속한 모든 볼륨의 논리적 크기의 합계|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BytesSentRate|보낸 바이트|바이트|합계|네트워크 인터페이스가 보낸 바이트 수|None|
|BytesReceivedRate|수신된 바이트|바이트|합계|네트워크 인터페이스가 수신한 바이트 수|None|
|PacketsSentRate|보낸 패킷|개수|합계|네트워크 인터페이스가 보낸 패킷 수|None|
|PacketsReceivedRate|수신된 패킷|개수|합계|네트워크 인터페이스가 수신한 패킷 수|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|VipAvailability|데이터 경로 가용성|개수|평균|기간당 평균 Load Balancer 데이터 경로 가용성|프론트 엔드 IP 주소, 프론트 엔드 포트|
|DipAvailability|상태 프로브 상태|개수|평균|기간당 평균 Load Balancer 상태 프로브 상태|프로토콜 유형, 백엔드포트, 프런트 엔드IP주소, 프런트 엔드 포트, 백엔드IP주소|
|ByteCount|바이트 수|개수|합계|기간 내에 전송된 총 바이트 수|프런트 엔드IP 주소, 프론트 엔드 포트, 방향|
|PacketCount|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|프런트 엔드IP 주소, 프론트 엔드 포트, 방향|
|SYNCount|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|프런트 엔드IP 주소, 프론트 엔드 포트, 방향|
|SnatConnectionCount|SNAT 연결 수|개수|합계|기간 내에 만들어진 새 SNAT 연결의 총 수|프런트 엔드 IP주소, 백엔드IP주소, 연결상태|
|AllocatedSnatPorts|할당된 SNAT 포트(미리 보기)|개수|합계|기간 내에 할당된 총 SNAT 포트 수|프런트 엔드 IP주소, 백엔드IP주소, 프로토콜유형, Isawaiting제거|
|UsedSnatPorts|사용된 SNAT 포트(미리 보기)|개수|합계|기간 내에 사용된 총 SNAT 포트 수|프런트 엔드 IP주소, 백엔드IP주소, 프로토콜유형, Isawaiting제거|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueryVolume|쿼리 볼륨|개수|합계|DNS 영역에 대해 제공된 쿼리 수|None|
|RecordSetCount|레코드 집합 수|개수|최대|DNS 영역의 레코드 집합 수|None|
|RecordSetCapacityUtilization|레코드 집합 용량 사용률|백분율|최대|DNS 영역에서 사용된 레코드 집합 용량 백분율|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PacketsInDDoS|인바운드 패킷 DDoS|초당 개수|최대|인바운드 패킷 DDoS|None|
|PacketsDroppedDDoS|인바운드 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 패킷이 삭제된 DDoS|None|
|PacketsForwardedDDoS|인바운드 패킷이 전달된 DDoS|초당 개수|최대|인바운드 패킷이 전달된 DDoS|None|
|TCPPacketsInDDoS|인바운드 TCP 패킷 DDoS|초당 개수|최대|인바운드 TCP 패킷 DDoS|None|
|TCPPacketsDroppedDDoS|인바운드 TCP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 삭제된 DDoS|None|
|TCPPacketsForwardedDDoS|인바운드 TCP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 전달된 DDoS|None|
|UDPPacketsInDDoS|인바운드 UDP 패킷 DDoS|초당 개수|최대|인바운드 UDP 패킷 DDoS|None|
|UDPPacketsDroppedDDoS|인바운드 UDP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 삭제된 DDoS|None|
|UDPPacketsForwardedDDoS|인바운드 UDP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 전달된 DDoS|None|
|BytesInDDoS|인바운드 바이트 DDoS|초당 바이트 수|최대|인바운드 바이트 DDoS|None|
|BytesDroppedDDoS|인바운드 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 바이트가 삭제된 DDoS|None|
|BytesForwardedDDoS|인바운드 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 바이트가 전달된 DDoS|None|
|TCPBytesInDDoS|인바운드 TCP 바이트 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트 DDoS|None|
|TCPBytesDroppedDDoS|인바운드 TCP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 삭제된 DDoS|None|
|TCPBytesForwardedDDoS|인바운드 TCP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 전달된 DDoS|None|
|UDPBytesInDDoS|인바운드 UDP 바이트 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트 DDoS|None|
|UDPBytesDroppedDDoS|인바운드 UDP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 삭제된 DDoS|None|
|UDPBytesForwardedDDoS|인바운드 UDP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 전달된 DDoS|None|
|IfUnderDDoSAttack|DDoS 공격 진행 여부|개수|최대|DDoS 공격 진행 여부|None|
|DDoSTriggerTCPPackets|DDoS 완화를 트리거하는 인바운드 TCP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 TCP 패킷|None|
|DDoSTriggerUDPPackets|DDoS 완화를 트리거하는 인바운드 UDP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 UDP 패킷|None|
|DDoSTriggerSYNPackets|DDoS 완화를 트리거하는 인바운드 SYN 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 SYN 패킷|None|
|VipAvailability|데이터 경로 가용성|개수|평균|기간당 평균 IP 주소 가용성|포트|
|ByteCount|바이트 수|개수|합계|기간 내에 전송된 총 바이트 수|포트, 방향|
|PacketCount|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|포트, 방향|
|SynCount|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|포트, 방향|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|핑메시 평균 왕복|핑을 VM으로 가는 왕복 시간|밀리초|평균|대상 VM으로 전송된 Ping의 왕복 시간|소스고객주소, 대상고객주소|
|핑메시 프로브실패퍼센트|VM에 실패한 Ping|백분율|평균|실패한 Ping 수의 백분율로 대상 VM의 전송된 총 Ping|소스고객주소, 대상고객주소|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|응용 프로그램규칙 히트|응용 프로그램 규칙 적중 횟수|개수|합계|응용 프로그램 규칙이 적중한 횟수|상태, 이유, 프로토콜|
|네트워크룰히트|네트워크 규칙 적중 횟수|개수|합계|네트워크 규칙이 적중한 횟수|상태, 이유, 프로토콜|
|방화벽Health|방화벽 상태|백분율|평균|방화벽 상태|상태, 이유|
|데이터 처리|처리된 데이터|바이트|합계|방화벽에서 처리한 총 데이터 수|None|
|SNAT포트 사용률|SNAT 포트 사용률|백분율|평균|SNAT 포트 사용률|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|처리량|처리량|초당 바이트 수|평균|Application Gateway에서 제공하는 초당 바이트 수|None|
|UnhealthyHostCount|비정상 호스트 수|개수|평균|비정상 백 엔드 호스트 수|BackendSettingsPool|
|HealthyHostCount|정상 호스트 수|개수|평균|정상 백 엔드 호스트 수|BackendSettingsPool|
|TotalRequests|총 요청 수|개수|합계|Application Gateway가 제공하는 성공한 요청 수|BackendSettingsPool|
|평균 요청카운트퍼헬리호스트|건강한 호스트당 분당 요청|개수|평균|풀의 정상 백 엔드 호스트당 평균 요청 수|BackendSettingsPool|
|FailedRequests|실패한 요청|개수|합계|Application Gateway가 제공하는 실패한 요청 수|BackendSettingsPool|
|ResponseStatus|응답 상태|개수|합계|Application Gateway에서 반환된 HTTP 응답 상태|HttpStatusGroup|
|CurrentConnections|현재 연결|개수|합계|Application Gateway와 설정된 현재 연결 수|None|
|뉴커넥션스퍼세컨드|초당 새 연결|초당 개수|평균|애플리케이션 게이트웨이로 설정된 초당 새 연결|None|
|CPU 사용률|CPU Utilization|백분율|평균|응용 프로그램 게이트웨이의 현재 CPU 사용률|None|
|용량 단위|현재 용량 단위|개수|평균|소비된 용량 단위|None|
|고정청구 가능용량단위|고정 청구 가능 용량 단위|개수|평균|충전될 최소 용량 단위|None|
|예상 청구 용량 단위|예상 청구 용량 단위|개수|평균|충전될 예상 용량 단위|None|
|계산 단위|현재 계산 단위|개수|평균|소모된 단위 계산|None|
|백엔드응답 상태|백엔드 응답 상태|개수|합계|백 엔드 멤버에서 생성된 HTTP 응답 코드 수입니다. 응용 프로그램 게이트웨이에서 생성된 응답 코드는 포함되지 않습니다.|백 엔드 서버, 백 엔드풀, 백엔드Http세팅, HttpStatusGroup|
|Tls프로토콜|클라이언트 TLS 프로토콜|개수|합계|응용 프로그램 게이트웨이와의 연결을 설정한 클라이언트에서 시작한 TLS 및 비 TLS 요청 수입니다. TLS 프로토콜 분포를 보려면 차원 TLS 프로토콜별로 필터링합니다.|청취자, Tls프로토콜|
|BytesSent|보낸 바이트|바이트|합계|응용 프로그램 게이트웨이에서 클라이언트로 전송한 총 바이트 수|수신기|
|BytesReceived|수신된 바이트|바이트|합계|클라이언트에서 응용 프로그램 게이트웨이에서 받은 총 바이트 수|수신기|
|클라이언트Rtt|클라이언트 RTT|밀리초|평균|클라이언트와 응용 프로그램 게이트웨이 간의 평균 왕복 시간입니다. 이 메트릭은 연결을 설정하고 승인을 반환하는 데 걸리는 시간을 나타냅니다.|수신기|
|애플리케이션 게이트웨이토탈타임|애플리케이션 게이트웨이 총 시간|밀리초|평균|요청을 처리하는 데 걸리는 평균 시간및 응답을 전송하는 데 걸리는 시간입니다. 이는 응용 프로그램 게이트웨이가 HTTP 요청의 첫 번째 바이트를 수신한 시점부터 응답 전송 작업이 완료된 시점까지의 간격의 평균으로 계산됩니다. 일반적으로 응용 프로그램 게이트웨이 처리 시간, 요청 및 응답 패킷이 네트워크를 통해 이동하는 시간 및 백 엔드 서버가 응답하는 데 걸린 시간이 포함됩니다.|수신기|
|백엔드커넥트타임|백 엔드 연결 시간|밀리초|평균|백 엔드 서버와의 연결을 설정하는 데 소요된 시간|청취자, 백엔드서버, 백엔드풀, 백엔드Http세팅|
|백엔드첫번째바이트응답시간|백엔드 첫 번째 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대한 연결을 설정하고 응답 헤더의 첫 번째 바이트를 수신하는 시작 사이의 시간 간격, 백 엔드 서버의 처리 시간 근사|청취자, 백엔드서버, 백엔드풀, 백엔드Http세팅|
|백엔드라스트바이트응답시간|백엔드 마지막 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대한 연결을 설정하고 응답 본문의 마지막 바이트를 받는 사이의 시간 간격|청취자, 백엔드서버, 백엔드풀, 백엔드Http세팅|
|일치하는 카운트|웹 응용 프로그램 방화벽 v1 총 규칙 배포|개수|합계|들어오는 트래픽에 대한 웹 응용 프로그램 방화벽 v1 총 규칙 분포|규칙 그룹, 규칙 ID|
|차단된 카운트|웹 응용 프로그램 방화벽 v1 차단된 요청 규칙 배포|개수|합계|웹 응용 프로그램 방화벽 v1 차단 된 요청 규칙 배포|규칙 그룹, 규칙 ID|
|차단된ReqCount|웹 응용 프로그램 방화벽 v1 차단된 요청 수|개수|합계|웹 응용 프로그램 방화벽 v1 차단된 요청 수|None|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|AverageBandwidth|게이트웨이 S2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 사이트 간 대역폭(바이트)|None|
|P2SBandwidth|게이트웨이 P2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 지점 및 사이트 간 대역폭(바이트)|None|
|P2SConnectionCount|P2S 연결 수|개수|최대|게이트웨이의 지점 및 사이트 간 연결 수|프로토콜|
|TunnelAverageBandwidth|터널 대역폭|초당 바이트 수|평균|초당 터널의 평균 대역폭(바이트)|연결 이름, 원격 IP|
|TunnelEgressBytes|터널 송신 바이트|바이트|합계|터널의 송신 바이트|연결 이름, 원격 IP|
|TunnelIngressBytes|터널 수신 바이트|바이트|합계|터널의 수신 바이트|연결 이름, 원격 IP|
|TunnelEgressPackets|터널 송신 패킷|개수|합계|터널의 송신 패킷 수|연결 이름, 원격 IP|
|TunnelIngressPackets|터널 수신 패킷|개수|합계|터널의 수신 패킷 수|연결 이름, 원격 IP|
|TunnelEgressPacketDropTSMismatch|터널 송신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수|연결 이름, 원격 IP|
|TunnelIngressPacketDropTSMismatch|터널 수신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수|연결 이름, 원격 IP|


## <a name="microsoftnetworkexpressrouteports"></a>마이크로소프트.네트워크/익스프레스루트포트

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|Rx라이트 레벨|Rx라이트 레벨|개수|평균|dBm의 Rx 라이트 레벨|링크, 레인|
|Tx라이트레벨|Tx라이트레벨|개수|평균|dBm의 Tx 라이트 레벨|링크, 레인|
|관리 상태|관리 상태|개수|평균|포트의 관리 상태|링크|
|라인 프로토콜|라인 프로토콜|개수|평균|포트의 라인 프로토콜 상태|링크|
|포트비트인퍼초|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|링크|
|포트비트아웃퍼초|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|링크|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|피어링 타입|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|피어링 타입|
|글로벌 리치비트인퍼초|글로벌 리치비트인퍼초|초당 개수|평균|초당 Azure 수신 비트|피어드서킷스키|
|글로벌 리치비트아웃퍼초|글로벌 리치비트아웃퍼초|초당 개수|평균|초당 Azure 송신 비트|피어드서킷스키|
|Bgp가용성|Bgp 가용성|백분율|평균|모든 동료를 향해 MSEE에서 BGP 가용성.|피어링 타입, 피어|
|Arp가용성|Arp 가용성|백분율|평균|모든 동료를 향해 MSEE에서 ARP 가용성.|피어링 타입, 피어|
|코스드롭비트인퍼초|드롭인비트퍼초|초당 개수|평균|초당 데이터 비트 가 삭제|None|
|QosDropBits아웃퍼초|드롭 아웃 비트퍼초|초당 개수|평균|초당 데이터 비트가 삭제되었습니다.|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|None|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|None|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|None|

## <a name="microsoftnetworkexpressroutegateways"></a>마이크로소프트.네트워크/익스프레스루트게이트웨이

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|에르게이트웨이커넥션비트인퍼초|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|연결 이름|
|어게이트웨이커넥션비트아웃퍼초|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|연결 이름|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QpsByEndpoint|반환된 엔드포인트별 쿼리|개수|합계|Traffic Manager 엔드포인트가 주어진 시간 내에 반환된 횟수|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|엔드포인트별 엔드포인트 상태|개수|최대|엔드포인트의 프로브 상태가 "사용"이면 1이고 그렇지 않으면 0입니다.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ProbesFailedPercent|실패한 프로브 %|백분율|평균|실패한 연결 모니터링 프로브 %|None|
|AverageRoundtripMs|평균. 왕복 시간 (ms)|밀리초|평균|원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 왕복 시간(ms)|None|
|체크실패퍼센트|실패한 백분율 검사(미리 보기)|백분율|평균|연결 모니터링 검사 실패%|소스 주소, 소스 이름, 소스 리소스 Id, 소스 유형, 프로토콜, 대상 주소, 대상 이름, 대상 리소스 ID, 대상 유형, 대상 포트, 테스트 그룹 이름, 테스트 구성 이름|
|왕복 타임|왕복 시간(ms) (미리 보기)|밀리초|평균|연결 모니터링 검사를 위한 왕복 시간(밀리초)|소스 주소, 소스 이름, 소스 리소스 Id, 소스 유형, 프로토콜, 대상 주소, 대상 이름, 대상 리소스 ID, 대상 유형, 대상 포트, 테스트 그룹 이름, 테스트 구성 이름|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RequestCount|요청 수|개수|합계|HTTP/S 프록시에서 제공하는 클라이언트 요청 수|HttpStatus, HttpStatusGroup, 클라이언트 지역, 클라이언트 국가|
|RequestSize|요청 크기|바이트|합계|클라이언트에서 HTTP/S 프록시로 요청으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, 클라이언트 지역, 클라이언트 국가|
|ResponseSize|응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트로 응답으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, 클라이언트 지역, 클라이언트 국가|
|청구 가능응답 크기|청구 가능한 응답 크기|바이트|합계|클라이언트에 대한 HTTP/S 프록시의 응답으로 전송된 청구 가능한 바이트 수(요청당 최소 2KB)입니다.|HttpStatus, HttpStatusGroup, 클라이언트 지역, 클라이언트 국가|
|BackendRequestCount|백 엔드 요청 수|개수|합계|HTTP/S 프록시에서 백 엔드로 전송된 요청 수|HttpStatus, HttpStatusGroup, 백엔드|
|BackendRequestLatency|백 엔드 요청 대기 시간|밀리초|평균|HTTP/S 프록시에서 백 엔드의 마지막 응답 바이트를 받을 때까지 HTTP/S 프록시에서 백 엔드로 요청이 전송될 때 계산된 시간|백 엔드|
|TotalLatency|총 대기 시간|밀리초|평균|클라이언트가 HTTP/S 프록시의 마지막 응답 바이트를 승인할 때까지 클라이언트 요청이 HTTP/S 프록시에서 수신될 때 계산된 시간|HttpStatus, HttpStatusGroup, 클라이언트 지역, 클라이언트 국가|
|BackendHealthPercentage|백 엔드 상태 비율|백분율|평균|HTTP/S 프록시에서 백 엔드로 성공한 상태 프로브의 비율|백 엔드, 백 엔드풀|
|WebApplicationFirewallRequestCount|웹 애플리케이션 방화벽 요청 수|개수|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|정책 이름, 규칙 이름, 작업|


## <a name="microsoftnetworkprivatednszones"></a>마이크로소프트.네트워크/프라이빗DnsZones

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueryVolume|쿼리 볼륨|개수|합계|개인 DNS 영역에 대해 제공되는 쿼리 수|None|
|RecordSetCount|레코드 집합 수|개수|최대|개인 DNS 영역의 레코드 집합 수|None|
|RecordSetCapacityUtilization|레코드 집합 용량 사용률|백분율|최대|개인 DNS 영역에서 사용하는 레코드 집합 용량 비율|None|
|가상 네트워크링크카운트|가상 네트워크 링크 수|개수|최대|개인 DNS 영역에 연결된 가상 네트워크 수|None|
|가상 네트워크링크용량 활용|가상 네트워크 링크 용량 사용률|백분율|최대|프라이빗 DNS 영역에서 사용하는 가상 네트워크 링크 용량 비율|None|
|가상 네트워크등록링크카운트|가상 네트워크 등록 링크 수|개수|최대|자동 등록이 활성화된 개인 DNS 영역에 연결된 가상 네트워크 수|None|
|가상 네트워크등록능력 활용|가상 네트워크 등록 링크 용량 활용|백분율|최대|개인 DNS 영역에서 사용하는 자동 등록 용량을 가진 가상 네트워크 링크의 백분율|None|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|registration.all|등록 작업|개수|합계|성공한 모든 등록 작업(만들기 업데이트 쿼리 및 삭제)입니다. |None|
|registration.create|등록 만들기 작업|개수|합계|성공한 모든 등록 만들기의 수입니다.|None|
|registration.update|등록 업데이트 작업|개수|합계|성공한 모든 등록 업데이트의 수입니다.|None|
|registration.get|등록 읽기 작업|개수|합계|성공한 모든 등록 쿼리의 수입니다.|None|
|registration.delete|등록 삭제 작업|개수|합계|성공한 모든 등록 삭제의 수입니다.|None|
|incoming|들어오는 메시지|개수|합계|성공한 모든 API 호출 전송의 수입니다. |None|
|incoming.scheduled|전송된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|None|
|incoming.scheduled.cancel|취소된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|None|
|scheduled.pending|보류 중인 예약된 알림|개수|합계|보류 중인 예약된 알림|None|
|installation.all|설치 관리 작업|개수|합계|설치 관리 작업|None|
|installation.get|설치 작업 가져오기|개수|합계|설치 작업 가져오기|None|
|installation.upsert|설치 작업 만들기 또는 업데이트|개수|합계|설치 작업 만들기 또는 업데이트|None|
|installation.patch|설치 작업 패치|개수|합계|설치 작업 패치|None|
|installation.delete|설치 작업 삭제|개수|합계|설치 작업 삭제|None|
|outgoing.allpns.success|성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|None|
|outgoing.allpns.invalidpayload|페이로드 오류|개수|합계|PNS가 잘못된 페이로드 오류를 반환하기 때문에 실패한 푸시의 수입니다.|None|
|outgoing.allpns.pnserror|외부 알림 시스템 오류|개수|합계|PNS와 통신하는 데 문제(인증 문제 제외)가 있기 때문에 실패한 푸시의 수입니다.|None|
|outgoing.allpns.channelerror|채널 오류|개수|합계|채널이 잘못되었거나 제한 또는 만료된 올바른 앱과 연결되지 않았으므로 실패 한 푸시의 수입니다.|None|
|outgoing.allpns.badorexpiredchannel|잘못되거나 만료된 채널 오류|개수|합계|등록의 channel/token/registrationId가 만료되었거나 올바르지 않기 때문에 실패한 푸시의 수입니다.|None|
|outgoing.wns.success|WNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|None|
|outgoing.wns.invalidcredentials|WNS 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다. (Windows Live는 자격 증명을 인식하지 못함)|None|
|outgoing.wns.badchannel|WNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(WNS 상태: 404 찾을 수 없음).|None|
|outgoing.wns.expiredchannel|WNS 만료된 채널 오류|개수|합계|ChannelURI가 만료되어 실패한 푸시의 수입니다(WNS 상태: 410 없음).|None|
|outgoing.wns.throttled|WNS 제한된 알림|개수|합계|WNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS 상태: 406 승인 금지).|None|
|outgoing.wns.tokenproviderunreachable|WNS 권한 부여 오류(연결할 수 없음)|개수|합계|Windows Live에 연결할 수 없습니다.|None|
|outgoing.wns.invalidtoken|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공한 토큰이 잘못되었습니다(WNS 상태: 401 권한 없음).|None|
|outgoing.wns.wrongtoken|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공된 토큰은 유효하지만 다른 애플리케이션에 대해서는 유효하지 않습니다(WNS 상태: 403 사용 권한 없음). 등록의 ChannelURI가 다른 앱에 연결된 경우 이 문제가 발생할 수 있습니다. 클라이언트 앱은 자격 증명이 알림 허브에 있는 동일한 앱과 연결되어 있는지 확인합니다.|None|
|outgoing.wns.invalidnotificationformat|WNS 잘못된 알림 형식|개수|합계|알림의 형식이 잘못되었습니다(WNS 상태: 400). WNS가 잘못된 모든 페이로드를 거부하지는 않습니다.|None|
|outgoing.wns.invalidnotificationsize|WNS 잘못된 알림 크기 오류|개수|합계|알림 페이로드가 너무 큽니다(WNS 상태: 413).|None|
|outgoing.wns.channelthrottled|WNS 채널 제한|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-NotificationStatus:channelThrottled).|None|
|outgoing.wns.channeldisconnected|WNS 채널 연결 끊김|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-DeviceConnectionStatus: disconnected).|None|
|outgoing.wns.dropped|WNS 삭제된 알림|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|None|
|outgoing.wns.pnserror|WNS 오류|개수|합계|WNS와의 통신 오류로 인해 알림이 배달되지 않습니다.|None|
|outgoing.wns.authenticationerror|WNS 인증 오류|개수|합계|Windows Live와의 통신 오류(잘못된 자격 증명 또는 잘못된 토큰)로 인해 알림이 배달되지 않습니다.|None|
|outgoing.apns.success|APNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|None|
|outgoing.apns.invalidcredentials|APNS 권한 부여 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|None|
|outgoing.apns.badchannel|APNS 잘못된 채널 오류|개수|합계|토큰이 잘못되어 실패한 푸시의 수입니다(APNS 상태 코드: 8).|None|
|outgoing.apns.expiredchannel|APNS 만료된 채널 오류|개수|합계|APNS 피드백 채널에서 무효화된 토큰의 수입니다.|None|
|outgoing.apns.invalidnotificationsize|APNS 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(APNS 상태 코드: 7).|None|
|outgoing.apns.pnserror|APNS 오류|개수|합계|APNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|None|
|outgoing.gcm.success|GCM 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|None|
|outgoing.gcm.invalidcredentials|GCM 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|None|
|outgoing.gcm.badchannel|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 인식되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: 잘못된 등록).|None|
|outgoing.gcm.expiredchannel|GCM 만료된 채널 오류|개수|합계|등록의 registrationId가 만료되었기 때문에 실패한 푸시의 수입니다(GCM 결과: NotRegistered).|None|
|outgoing.gcm.throttled|GCM 제한된 알림|개수|합계|GCM이 이 앱을 제한하기 때문에 실패한 푸시의 수(GCM 상태 코드: 501-599 또는 결과: Unavailable).|None|
|outgoing.gcm.invalidnotificationformat|GCM 잘못된 알림 형식|개수|합계|페이로드 형식이 올바르지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidDataKey 또는 InvalidTtl).|None|
|outgoing.gcm.invalidnotificationsize|GCM 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(GCM 결과: MessageTooBig).|None|
|outgoing.gcm.wrongchannel|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 현재 앱과 연결되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidPackageName).|None|
|outgoing.gcm.pnserror|GCM 오류|개수|합계|GCM과의 통신 오류로 인해 실패한 푸시의 수입니다.|None|
|outgoing.gcm.authenticationerror|GCM 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나, 자격 증명이 차단되었거나, 앱에서 SenderId가 올바르게 구성되지 않았기 때문에 실패한 푸시의 수입니다(GCM 결과: MismatchedSenderId).|None|
|outgoing.mpns.success|MPNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|None|
|outgoing.mpns.invalidcredentials|MPNS 잘못된 자격 증명|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|None|
|outgoing.mpns.badchannel|MPNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(MPNS 상태: 404 찾을 수 없음).|None|
|outgoing.mpns.throttled|MPNS 제한된 알림|개수|합계|MPNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS MPNS: 406 승인 금지).|None|
|outgoing.mpns.invalidnotificationformat|MPNS 잘못된 알림 형식|개수|합계|알림의 페이로드가 너무 커서 실패한 푸시의 수입니다.|None|
|outgoing.mpns.channeldisconnected|MPNS 채널 연결 끊김|개수|합계|등록의 ChannelURI 연결이 끊어졌으므로 실패한 푸시의 수입니다(MPNS 상태: 412 찾을 수 없음).|None|
|outgoing.mpns.dropped|MPNS 삭제된 알림|개수|합계|MPNS에서 삭제된 푸시의 수입니다(MPNS 응답 헤더: X-NotificationStatus: QueueFull 또는 Suppressed).|None|
|outgoing.mpns.pnserror|MPNS 오류|개수|합계|MPNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|None|
|outgoing.mpns.authenticationerror|MPNS 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|None|
|notificationhub.pushes|모든 나가는 알림 수|개수|합계|알림 허브의 모든 나가는 알림 수|None|
|incoming.all.requests|들어오는 모든 요청|개수|합계|알림 허브에 대해 들어오는 전체 요청|None|
|incoming.all.failedrequests|들어오는 모든 실패한 요청|개수|합계|알림 허브에 대해 들어오는 실패한 전체 요청|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|Average_% Free Inodes|% 사용 가능한 Inodes|개수|평균|Average_% Free Inodes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Free Space|% 사용 가능한 공간|개수|평균|Average_% Free Space|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Used Inodes|% 사용된 Inodes|개수|평균|Average_% Used Inodes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Used Space|% 사용된 공간|개수|평균|Average_% Used Space|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Disk Read Bytes/sec|디스크 읽기 바이트/초|개수|평균|Average_Disk Read Bytes/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Disk Reads/sec|디스크 읽기/초|개수|평균|Average_Disk Reads/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Disk Transfers/sec|디스크 전송/초|개수|평균|Average_Disk Transfers/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Disk Write Bytes/sec|디스크 쓰기 바이트/초|개수|평균|Average_Disk Write Bytes/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Disk Writes/sec|디스크 쓰기/초|개수|평균|Average_Disk Writes/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Free Megabytes|사용 가능한 메가바이트|개수|평균|Average_Free Megabytes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Logical Disk Bytes/sec|논리 디스크 바이트/초|개수|평균|Average_Logical Disk Bytes/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Available Memory|% 사용 가능한 메모리|개수|평균|Average_% Available Memory|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Available Swap Space|% 사용 가능한 스왑 공간|개수|평균|Average_% Available Swap Space|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Used Memory|% 사용된 메모리|개수|평균|Average_% Used Memory|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Used Swap Space|% 사용된 스왑 공간|개수|평균|Average_% Used Swap Space|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Available MBytes Memory|사용 가능한 MB 메모리|개수|평균|Average_Available MBytes Memory|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Available MBytes Swap|사용 가능한 MB 스왑|개수|평균|Average_Available MBytes Swap|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Page Reads/sec|페이지 읽기/초|개수|평균|Average_Page Reads/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Page Writes/sec|페이지 쓰기/초|개수|평균|Average_Page Writes/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Pages/sec|페이지/초|개수|평균|Average_Pages/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Used MBytes Swap Space|사용된 MB 스왑 공간|개수|평균|Average_Used MBytes Swap Space|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Used Memory MBytes|사용된 메모리 MB|개수|평균|Average_Used Memory MBytes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Bytes Transmitted|전송된 총 바이트|개수|평균|Average_Total Bytes Transmitted|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Bytes Received|받은 총 바이트|개수|평균|Average_Total Bytes Received|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Bytes|총 바이트|개수|평균|Average_Total Bytes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Packets Transmitted|전송된 총 패킷|개수|평균|Average_Total Packets Transmitted|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Packets Received|받은 총 패킷|개수|평균|Average_Total Packets Received|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Rx Errors|총 Rx 오류|개수|평균|Average_Total Rx Errors|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Tx Errors|총 Tx 오류|개수|평균|Average_Total Tx Errors|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Total Collisions|총 충돌|개수|평균|Average_Total Collisions|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Avg. 디스크 초/읽기|평균 디스크 초/읽기|개수|평균|Average_Avg. 디스크 초/읽기|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Avg. 디스크 초/전송|평균 디스크 초/전송|개수|평균|Average_Avg. 디스크 초/전송|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Avg. 디스크 초/쓰기|평균. 디스크 초/쓰기|개수|평균|Average_Avg. 디스크 초/쓰기|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Physical Disk Bytes/sec|물리적 디스크 바이트/초|개수|평균|Average_Physical Disk Bytes/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Pct Privileged Time|Pct 권한이 부여된 시간|개수|평균|Average_Pct Privileged Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Pct User Time|Pct 사용자 시간|개수|평균|Average_Pct User Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Used Memory kBytes|사용된 메모리 KB|개수|평균|Average_Used Memory kBytes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Virtual Shared Memory|가상 공유 메모리|개수|평균|Average_Virtual Shared Memory|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% DPC Time|% DPC 시간|개수|평균|Average_% DPC Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Idle Time|% 유휴 시간|개수|평균|Average_% Idle Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Interrupt Time|% 인터럽트 시간|개수|평균|Average_% Interrupt Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% IO Wait Time|% IO 대기 시간|개수|평균|Average_% IO Wait Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Nice Time|% Nice 시간|개수|평균|Average_% Nice Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Privileged Time|% 권한이 부여된 시간|개수|평균|Average_% Privileged Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Processor Time|% Processor Time|개수|평균|Average_% Processor Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% User Time|% 사용자 시간|개수|평균|Average_% User Time|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Free Physical Memory|사용 가능한 실제 메모리|개수|평균|Average_Free Physical Memory|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Free Space in Paging Files|페이징 파일에 사용 가능한 공간|개수|평균|Average_Free Space in Paging Files|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Free Virtual Memory|사용 가능한 가상 메모리|개수|평균|Average_Free Virtual Memory|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Processes|프로세스|개수|평균|Average_Processes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Size Stored In Paging Files|페이징 파일에 저장된 크기|개수|평균|Average_Size Stored In Paging Files|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Uptime|작동 시간|개수|평균|Average_Uptime|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Users|사용자|개수|평균|Average_Users|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Current Disk Queue Length|Current Disk Queue Length|개수|평균|Average_Current Disk Queue Length|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Available MBytes|Available MBytes|개수|평균|Average_Available MBytes|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|개수|평균|Average_% Committed Bytes In Use|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Bytes Received/sec|Bytes Received/sec|개수|평균|Average_Bytes Received/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Bytes Sent/sec|Bytes Sent/sec|개수|평균|Average_Bytes Sent/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Bytes Total/sec|Bytes Total/sec|개수|평균|Average_Bytes Total/sec|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Average_Processor Queue Length|프로세서 큐 길이|개수|평균|Average_Processor Queue Length|컴퓨터, 개체 이름, 인스턴스 이름, 카운터 패스, 소스 시스템|
|Heartbeat|Heartbeat|개수|합계|Heartbeat|컴퓨터, OSType, 버전, 소스컴퓨터Id|
|업데이트|업데이트|개수|평균|업데이트|컴퓨터, 제품, 분류, 업데이트 상태, 선택 사항, 승인됨|
|행사|행사|개수|평균|행사|소스, 이벤트 로그, 컴퓨터, 이벤트 카테고리, 이벤트 레벨, 이벤트 레벨 이름, 이벤트 ID|

## <a name="microsoftpeeringpeeringservices"></a>마이크로소프트.피어링/피어링서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|접두사지연|접두사 대기 시간|밀리초|평균|중앙값 접두사 대기 시간|접두사 이름|

## <a name="microsoftpeeringpeerings"></a>마이크로소프트.피어링/피어링

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|세션가용성V4|세션 가용성 V4|백분율|평균|V4 세션의 가용성|ConnectionId|
|세션가용성V6|세션 가용성 V6|백분율|평균|V6 세션의 가용성|ConnectionId|
|침투속도|침투 트래픽 속도|비트스퍼세컨드|평균|초당 비트로 트래픽 속도 침투|ConnectionId|
|출구 트래픽 속도|트래픽 비율|비트스퍼세컨드|평균|초당 비트로 트래픽 속도를 송귀화|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueryDuration|쿼리 기간|밀리초|평균|마지막 간격의 DAX 쿼리 기간|차원 없음|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|
|qpu_high_utilization_metric|QPU 높은 사용률|개수|합계|지난 1분 간의 QPU 높은 사용률로, QPU 사용률이 높으면 1, 그렇지 않으면 0|차원 없음|
|memory_metric|메모리|바이트|평균|메모리. A1은 0-3GB, A2는 0-5GB, A3는 0-10GB, A4는 0-25GB, A5는 0-50GB, A6는 0-100GB 범위|차원 없음|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|차원 없음|


## <a name="microsoftprojectbabylonaccounts"></a>마이크로소프트.프로젝트바빌론/계정

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|자산분포분류|분류별 자산 분배|개수|합계|특정 분류가 할당된 자산 의 수를 나타냅니다(예: 해당 레이블로 분류됨).|분류, 소스, 리소스 ID|
|자산 분포ByStorageType|저장소 유형별 자산 배포|개수|합계|특정 저장소 유형이 있는 자산 수를 나타냅니다.|스토리지 유형, 리소스 ID|
|카탈로그활성 사용자|일일 활성 사용자|개수|합계|일일 활성 사용자 수|ResourceId|
|카탈로그 사용|작업별 사용량 분포|개수|합계|사용자가 카탈로그에 만드는 작업 수(예: 액세스, 검색, 용어집)를 나타냅니다.|작업, 리소스 ID|
|번호OfAssets분류|분류가 하나 이상인 자산 수|개수|평균|태그 분류가 하나 이상 있는 자산 수를 나타냅니다.|ResourceId|
|스캔취소|스캔 취소|개수|합계|취소된 검사 수를 나타냅니다.|ResourceId|
|스캔 완료됨|스캔 완료|개수|합계|성공적으로 완료된 검사 수를 나타냅니다.|ResourceId|
|스캔 실패|스캔 실패|개수|합계|실패한 검사 수를 나타냅니다.|ResourceId|
|스캔시간 촬영|촬영 시간 스캔|초|합계|총 검색 시간(초)을 나타냅니다.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|개수|합계|Microsoft.Relay의 성공적인 ListenerConnections.|엔터티 이름, 작업결과|
|ListenerConnections-ClientError|ListenerConnections-ClientError|개수|합계|Microsoft.Relay의 총 ListenerConnections에서의 ClientError.|엔터티 이름, 작업결과|
|ListenerConnections-ServerError|ListenerConnections-ServerError|개수|합계|Microsoft.Relay의 ListenerConnections에서의 ServerError.|엔터티 이름, 작업결과|
|SenderConnections-Success|SenderConnections-Success|개수|합계|Microsoft.Relay의 성공적인 SenderConnections.|엔터티 이름, 작업결과|
|SenderConnections-ClientError|SenderConnections-ClientError|개수|합계|Microsoft.Relay의 SenderConnections에서 ClientError.|엔터티 이름, 작업결과|
|SenderConnections-ServerError|SenderConnections-ServerError|개수|합계|Microsoft.Relay의 SenderConnections에서의 ServerError.|엔터티 이름, 작업결과|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 ListenerConnections.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 SenderConnections 요청.|EntityName|
|ActiveConnections|ActiveConnections|개수|합계|Microsoft.Relay의 총 ActiveConnections.|EntityName|
|ActiveListeners|ActiveListeners|개수|합계|Microsoft.Relay의 총 ActiveListeners.|EntityName|
|BytesTransferred|BytesTransferred|개수|합계|Microsoft.Relay의 총 BytesTransferred.|EntityName|
|ListenerDisconnects|ListenerDisconnects|개수|합계|Microsoft.Relay의 총 ListenerDisconnects.|EntityName|
|SenderDisconnects|SenderDisconnects|개수|합계|Microsoft.Relay의 총 SenderDisconnects.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SearchLatency|검색 대기 시간|초|평균|검색 서비스에 대한 평균 검색 대기 시간|None|
|SearchQueriesPerSecond|초당 검색 쿼리 수|초당 개수|평균|Search 서비스에 대한 초당 검색 쿼리|None|
|ThrottledSearchQueriesPercentage|제한된 검색 쿼리 백분율|백분율|평균|검색 서비스에 대해 제한된 검색 쿼리의 백분율|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청|개수|합계|네임스페이스에 대한 총 성공한 요청|엔터티 이름, 작업결과|
|ServerErrors|서버 오류.|개수|합계|Microsoft.ServiceBus에 대한 서버 오류.|엔터티 이름, 작업결과|
|UserErrors|사용자 오류.|개수|합계|Microsoft.ServiceBus에 대한 사용자 오류.|엔터티 이름, 작업결과|
|ThrottledRequests|제한된 요청.|개수|합계|Microsoft.ServiceBus에 대한 제한된 요청.|엔터티 이름, 작업결과|
|IncomingRequests|들어오는 요청|개수|합계|Microsoft.ServiceBus에 대한 들어오는 요청.|EntityName|
|IncomingMessages|들어오는 메시지|개수|합계|Microsoft.ServiceBus에 대한 들어오는 메시지.|EntityName|
|OutgoingMessages|보내는 메시지|개수|합계|Microsoft.ServiceBus에 대한 보내는 메시지.|EntityName|
|ActiveConnections|ActiveConnections|개수|합계|Microsoft.ServiceBus에 대한 총 활성 연결.|None|
|ConnectionsOpened|열린 연결.|개수|평균|Microsoft.ServiceBus에 대한 열린 연결.|EntityName|
|ConnectionsClosed|끊어진 연결.|개수|평균|Microsoft.ServiceBus에 대한 끊어진 연결.|EntityName|
|크기|크기|바이트|평균|큐/토픽 크기(바이트)|EntityName|
|메시지|큐/토픽에 있는 메시지 수|개수|평균|큐/토픽에 있는 메시지 수|EntityName|
|ActiveMessages|큐/토픽에 있는 활성 메시지 수|개수|평균|큐/토픽에 있는 활성 메시지 수|EntityName|
|데드레터메시지|큐/토픽에서 배달 못한 문자메시지 수입니다.|개수|평균|큐/토픽에서 배달 못한 문자메시지 수입니다.|EntityName|
|예약된 메시지|큐/토픽에서 예약된 메시지 수입니다.|개수|평균|큐/토픽에서 예약된 메시지 수입니다.|EntityName|
|네임스페이스Cpu사용법|CPU|백분율|최대|서비스 버스 프리미엄 네임스페이스 CPU 사용 메트릭입니다.|복제본|
|네임스페이스메모리사용|메모리 사용량|백분율|최대|서비스 버스 프리미엄 네임스페이스 메모리 사용 메트릭입니다.|복제본|
|CPUXNS|CPU(더 이상 사용되지 않습니다)|백분율|최대|서비스 버스 프리미엄 네임스페이스 CPU 사용 메트릭입니다. 이 메트릭은 삭제됩니다. 대신 CPU 메트릭(네임스페이스Cpu사용법)을 사용하십시오.|복제본|
|WSXNS|메모리 사용량(더 이상 사용되지 않습니다)|백분율|최대|서비스 버스 프리미엄 네임스페이스 메모리 사용 메트릭입니다. 이 메트릭은 사용되지 않습니다. 대신 메모리 사용량(네임스페이스메모리사용) 메트릭을 사용하십시오.|복제본|


## <a name="microsoftservicefabricmeshapplications"></a>마이크로소프트.서비스패브릭 메쉬/응용 프로그램

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|할당된 Cpu|할당된 Cpu|개수|평균|밀리 코어에서 이 컨테이너에 할당된 CPU|응용 프로그램 이름, 서비스 이름, 코드 패키지 이름, 서비스 복제 이름|
|할당된 메모리|할당된 메모리|바이트|평균|MB에서 이 컨테이너에 할당된 메모리|응용 프로그램 이름, 서비스 이름, 코드 패키지 이름, 서비스 복제 이름|
|실제 Cpu|실제 Cpu|개수|평균|밀리 코어의 실제 CPU 사용량|응용 프로그램 이름, 서비스 이름, 코드 패키지 이름, 서비스 복제 이름|
|실제 메모리|실제 메모리|바이트|평균|MB의 실제 메모리 사용량|응용 프로그램 이름, 서비스 이름, 코드 패키지 이름, 서비스 복제 이름|
|CPU 사용률|CPU 사용률|백분율|평균|할당된 Cpu의 백분율로 이 컨테이너에 대한 CPU 사용률|응용 프로그램 이름, 서비스 이름, 코드 패키지 이름, 서비스 복제 이름|
|메모리 사용률|메모리 사용률|백분율|평균|할당된 Cpu의 백분율로 이 컨테이너에 대한 CPU 사용률|응용 프로그램 이름, 서비스 이름, 코드 패키지 이름, 서비스 복제 이름|
|응용 프로그램 상태|응용 프로그램 상태|개수|평균|서비스 패브릭 메쉬 응용 프로그램의 상태|응용 프로그램 이름, 상태|
|ServiceStatus|ServiceStatus|개수|평균|서비스 패브릭 메시 응용 프로그램에서 서비스의 상태 상태|응용 프로그램 이름, 상태, 서비스 이름|
|서비스 복제 상태|서비스 복제 상태|개수|평균|서비스 패브릭 메시 응용 프로그램에서 서비스 복제본의 상태 상태|응용 프로그램 이름, 상태, 서비스 이름, 서비스 복제 이름|
|컨테이너 상태|컨테이너 상태|개수|평균|서비스 패브릭 메시 응용 프로그램의 컨테이너 상태|응용 프로그램 이름, 서비스 이름, 코드 패키지 이름, 서비스 복제 이름, 상태|
|다시 시작 카운트|다시 시작 카운트|개수|평균|서비스 패브릭 메시 응용 프로그램에서 컨테이너 의 재시작 개수|응용 프로그램 이름, 상태, 서비스 이름, 서비스 복제 이름, 코드 패키지 이름|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ConnectionCount|연결 수|개수|최대|사용자 연결의 양.|엔드포인트|
|MessageCount|메시지 수|개수|합계|총 메시지 양|None|
|InboundTraffic|인바운드 트래픽|바이트|합계|서비스의 인바운드 트래픽|None|
|OutboundTraffic|아웃바운드 트래픽|바이트|합계|서비스의 아웃바운드 트래픽|None|
|UserErrors|사용자 오류|백분율|최대|사용자 오류의 비율|None|
|SystemErrors|시스템 오류|백분율|최대|시스템 오류의 비율|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|None|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|None|
|log_write_percent|로그 IO 비율|백분율|평균|IO 백분율로 기록합니다. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 백분율입니다. DTU 기반 데이터베이스에 적용됩니다.|None|
|스토리지|사용된 데이터 공간|바이트|최대|데이터 공간이 사용됩니다. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|connection_successful|성공적인 연결|개수|합계|성공적인 연결|None|
|connection_failed|실패한 연결|개수|합계|실패한 연결|None|
|blocked_by_firewall|방화벽에 의해 차단|개수|합계|방화벽에 의해 차단|None|
|교착 상태|교착 상태|개수|합계|교착 상태. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|storage_percent|데이터 공간 사용 %|백분율|최대|데이터 공간 사용%. 데이터 웨어하우스 또는 하이퍼스케일 데이터베이스에는 적용되지 않습니다.|None|
|xtp_storage_percent|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 스토리지 비율. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|workers_percent|작업자 백분율|백분율|평균|근로자 백분율입니다. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|sessions_percent|세션 백분율|백분율|평균|세션 비율입니다. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|dtu_limit|DTU 제한|개수|평균|DTU 제한. DTU 기반 데이터베이스에 적용됩니다.|None|
|dtu_used|DTU 사용됨|개수|평균|DTU가 사용되었습니다. DTU 기반 데이터베이스에 적용됩니다.|None|
|cpu_limit|CPU 한도|개수|평균|CPU 한도. vCore 기반 데이터베이스에 적용됩니다.|None|
|cpu_used|사용되는 CPU|개수|평균|CPU가 사용됩니다. vCore 기반 데이터베이스에 적용됩니다.|None|
|dwu_limit|DWU 제한|개수|최대|DWU 제한. 데이터 웨어하우스에만 적용됩니다.|None|
|dwu_consumption_percent|DWU 백분율|백분율|최대|DWU 백분율. 데이터 웨어하우스에만 적용됩니다.|None|
|dwu_used|DWU 사용됨|개수|최대|DWU를 사용 했습니다. 데이터 웨어하우스에만 적용됩니다.|None|
|cache_hit_percent|캐시 적중 비율|백분율|최대|캐시 적중률입니다. 데이터 웨어하우스에만 적용됩니다.|None|
|cache_used_percent|캐시 사용 비율|백분율|최대|캐시 사용 백분율입니다. 데이터 웨어하우스에만 적용됩니다.|None|
|sqlserver_process_core_percent|SQL Server 프로세스 코어 백분율|백분율|최대|SQL DB 프로세스의 백분율로 CPU 사용량을 차지합니다. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|sqlserver_process_memory_percent|SQL Server 프로세스 메모리 백분율|백분율|최대|SQL DB 프로세스의 백분율로 메모리 사용량을 사용합니다. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|tempdb_data_size|Tempdb 데이터 파일 크기 킬로바이트|개수|최대|Tempdb 데이터 파일 크기 킬로바이트. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|tempdb_log_size|Tempdb 로그 파일 크기 킬로바이트|개수|최대|Tempdb 로그 파일 크기 킬로바이트. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|tempdb_log_used_percent|Tempdb 퍼센트 로그 사용|백분율|최대|Tempdb 퍼센트 로그 사용. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|local_tempdb_usage_percent|로컬 tempdb 백분율|백분율|평균|로컬 tempdb 백분율입니다. 데이터 웨어하우스에만 적용됩니다.|None|
|app_cpu_billed|앱 CPU 청구|개수|합계|앱 CPU 요금이 청구되었습니다. 서버없는 데이터베이스에 적용됩니다.|None|
|app_cpu_percent|앱 CPU 백분율|백분율|평균|앱 CPU 백분율입니다. 서버없는 데이터베이스에 적용됩니다.|None|
|app_memory_percent|앱 메모리 백분율|백분율|평균|앱 메모리 백분율입니다. 서버없는 데이터베이스에 적용됩니다.|None|
|allocated_data_storage|할당된 데이터 공간|바이트|평균|할당된 데이터 저장소입니다. 데이터 웨어하우스에 적용할 수 없습니다.|None|
|memory_usage_percent|메모리 백분율|백분율|최대|메모리 백분율입니다. 데이터 웨어하우스에만 적용됩니다.|None|
|dw_backup_size_gb|데이터 스토리지 크기|개수|합계|데이터 저장소 크기는 데이터 크기와 트랜잭션 로그로 구성됩니다. 이 지표는 청구서의 '저장소' 부분에 계산됩니다. 데이터 웨어하우스에만 적용됩니다.|None|
|dw_snapshot_size_gb|스냅샷 스토리지 크기|개수|합계|스냅숏 저장소 크기는 사용자 정의 및 자동 복원 지점을 만들기 위해 스냅숏에 의해 캡처된 증분 변경 사항의 크기입니다. 이 지표는 청구서의 '저장소' 부분에 계산됩니다. 데이터 웨어하우스에만 적용됩니다.|None|
|dw_geosnapshot_size_gb|재해 복구 저장소 크기|개수|합계|재해 복구 저장소 크기는 청구서에 '재해 복구 저장소'로 반영됩니다. 데이터 웨어하우스에만 적용됩니다.|None|
|wlg_allocation_relative_to_system_percent|시스템 백분율별 워크로드 그룹 할당|백분율|최대|워크로드 그룹당 전체 시스템을 기준으로 리소스의 백분율을 할당합니다. 데이터 웨어하우스에만 적용됩니다.|워크로드 그룹 이름, 사용자 정의|
|wlg_allocation_relative_to_wlg_effective_cap_percent|한도 리소스%별 워크로드 그룹 할당|백분율|최대|워크로드 그룹당 지정된 한도 리소스에 대해 할당된 리소스 비율입니다. 데이터 웨어하우스에만 적용됩니다.|워크로드 그룹 이름, 사용자 정의|
|wlg_active_queries|워크로드 그룹 활성 쿼리|개수|합계|워크로드 그룹 내의 활성 쿼리입니다. 데이터 웨어하우스에만 적용됩니다.|워크로드 그룹 이름, 사용자 정의|
|wlg_queued_queries|워크로드 그룹 큐에 대기된 쿼리|개수|합계|워크로드 그룹 내에서 큐에 대기된 쿼리입니다. 데이터 웨어하우스에만 적용됩니다.|워크로드 그룹 이름, 사용자 정의|
|active_queries|활성 쿼리|개수|합계|모든 워크로드 그룹에 대한 활성 쿼리입니다. 데이터 웨어하우스에만 적용됩니다.|None|
|queued_queries|큐에 대기된 쿼리|개수|합계|모든 워크로드 그룹에 걸쳐 큐에 대기된 쿼리입니다. 데이터 웨어하우스에만 적용됩니다.|None|
|wlg_active_queries_timeouts|워크로드 그룹 쿼리 시간 시간|개수|합계|워크로드 그룹에 대해 시간 시간이 만료된 쿼리입니다. 데이터 웨어하우스에만 적용됩니다.|워크로드 그룹 이름, 사용자 정의|
|wlg_effective_min_resource_percent|유효 최소 리소스 백분율|백분율|최대|서비스 수준 최소를 고려하여 워크로드 그룹에 대해 예약되고 격리된 리소스의 최소 백분율입니다. 데이터 웨어하우스에만 적용됩니다.|워크로드 그룹 이름, 사용자 정의|
|wlg_effective_cap_resource_percent|유효 한도 리소스 백분율|백분율|최대|다른 워크로드 그룹에 할당된 유효 최소 최소 리소스 백분율을 고려하여 워크로드 그룹에 허용되는 리소스 비율에 대한 하드 제한입니다. 데이터 웨어하우스에만 적용됩니다.|워크로드 그룹 이름, 사용자 정의|
|full_backup_size_bytes|전체 백업 스토리지 크기|바이트|최대|누적 전체 백업 저장소 크기입니다. vCore 기반 데이터베이스에 적용됩니다. 하이퍼스케일 데이터베이스에는 적용되지 않습니다.|None|
|diff_backup_size_bytes|차등 백업 스토리지 크기|바이트|최대|누적 차동 백업 저장소 크기. vCore 기반 데이터베이스에 적용됩니다. 하이퍼스케일 데이터베이스에는 적용되지 않습니다.|None|
|log_backup_size_bytes|백업 저장소 크기 로그|바이트|최대|누적 로그 백업 저장소 크기입니다. vCore 기반 및 하이퍼스케일 데이터베이스에 적용됩니다.|None|
|snapshot_backup_size_bytes|스냅샷 백업 저장소 크기|바이트|최대|누적 스냅샷 백업 저장소 크기입니다. 하이퍼스케일 데이터베이스에 적용됩니다.|None|
|base_blob_size_bytes|기본 Blob 저장 크기|바이트|최대|기본 Blob 저장 크기입니다. 하이퍼스케일 데이터베이스에 적용됩니다.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|None|
|database_cpu_percent|CPU 비율|백분율|평균|CPU 비율|DatabaseResourceId|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|None|
|database_physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|DatabaseResourceId|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|None|
|database_log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|DatabaseResourceId|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 백분율입니다. DTU 기반 탄력적 풀에 적용됩니다.|None|
|database_dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|DatabaseResourceId|
|storage_percent|데이터 공간 사용 %|백분율|평균|데이터 공간 사용 %|None|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|None|
|database_workers_percent|작업자 백분율|백분율|평균|작업자 백분율|DatabaseResourceId|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|None|
|database_sessions_percent|세션 백분율|백분율|평균|세션 백분율|DatabaseResourceId|
|eDTU_limit|eDTU 제한|개수|평균|eDTU 제한. DTU 기반 탄력적 풀에 적용됩니다.|None|
|storage_limit|데이터 최대 크기|바이트|평균|데이터 최대 크기|None|
|eDTU_used|eDTU 사용|개수|평균|eDTU가 사용됩니다. DTU 기반 탄력적 풀에 적용됩니다.|None|
|database_eDTU_used|eDTU 사용|개수|평균|eDTU 사용|DatabaseResourceId|
|storage_used|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|None|
|database_storage_used|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|DatabaseResourceId|
|xtp_storage_percent|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 스토리지 백분율|None|
|cpu_limit|CPU 한도|개수|평균|CPU 한도. vCore 기반 탄력적 풀에 적용됩니다.|None|
|database_cpu_limit|CPU 한도|개수|평균|CPU 한도|DatabaseResourceId|
|cpu_used|사용되는 CPU|개수|평균|CPU가 사용됩니다. vCore 기반 탄력적 풀에 적용됩니다.|None|
|database_cpu_used|사용되는 CPU|개수|평균|사용되는 CPU|DatabaseResourceId|
|sqlserver_process_core_percent|SQL Server 프로세스 코어 백분율|백분율|최대|SQL DB 프로세스의 백분율로 CPU 사용량을 차지합니다. 탄력적 풀에 적용됩니다.|None|
|sqlserver_process_memory_percent|SQL Server 프로세스 메모리 백분율|백분율|최대|SQL DB 프로세스의 백분율로 메모리 사용량을 사용합니다. 탄력적 풀에 적용됩니다.|None|
|tempdb_data_size|Tempdb 데이터 파일 크기 킬로바이트|개수|최대|Tempdb 데이터 파일 크기 킬로바이트|None|
|tempdb_log_size|Tempdb 로그 파일 크기 킬로바이트|개수|최대|Tempdb 로그 파일 크기 킬로바이트|None|
|tempdb_log_used_percent|Tempdb 퍼센트 로그 사용|백분율|최대|Tempdb 퍼센트 로그 사용|None|
|allocated_data_storage|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|None|
|database_allocated_data_storage|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|DatabaseResourceId|
|allocated_data_storage_percent|데이터 공간 할당 %|백분율|최대|데이터 공간 할당 %|None|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|데이터베이스리소스Id, 탄성풀자원|
|storage_used|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|ElasticPoolResourceId|
|database_storage_used|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|데이터베이스리소스Id, 탄성풀자원|
|dtu_used|DTU 사용됨|개수|평균|DTU 사용됨|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|virtual_core_count|가상 코어 수|개수|평균|가상 코어 수|None|
|avg_cpu_percent|평균 CPU 비율|백분율|평균|평균 CPU 비율|None|
|reserved_storage_mb|예약된 스토리지 공간|개수|평균|예약된 스토리지 공간|None|
|storage_space_used_mb|사용된 스토리지 공간|개수|평균|사용된 스토리지 공간|None|
|io_requests|IO 요청 수|개수|평균|IO 요청 수|None|
|io_bytes_read|읽은 IO 바이트|바이트|평균|읽은 IO 바이트|None|
|io_bytes_written|기록된 IO 바이트|바이트|평균|기록된 IO 바이트|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|UsedCapacity|사용된 용량|바이트|평균|계정 사용 용량|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BlobCapacity|Blob 용량|바이트|평균|저장소 계정의 Blob 서비스에서 바이트로 사용하는 저장소의 양입니다.|BlobType, 계층|
|BlobCount|Blob 수|개수|평균|저장소 계정의 Blob 서비스의 Blob 수입니다.|BlobType, 계층|
|ContainerCount|Blob 컨테이너 수|개수|평균|저장소 계정의 Blob 서비스의 컨테이너 수입니다.|None|
|IndexCapacity|인덱스 용량|바이트|평균|ADLS Gen2(계층적) 인덱스에서 바이트로 사용하는 저장소의 양입니다.|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TableCapacity|테이블 용량|바이트|평균|저장소 계정의 테이블 서비스에서 바이트로 사용하는 저장소의 양입니다.|None|
|TableCount|테이블 수|개수|평균|저장소 계정의 테이블 서비스의 테이블 수입니다.|None|
|TableEntityCount|테이블 엔터티 수|개수|평균|저장소 계정의 테이블 서비스의 테이블 엔터티 수입니다.|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|FileCapacity|파일 용량|바이트|평균|저장소 계정의 파일 서비스에서 바이트로 사용하는 저장소의 양입니다.|FileShare|
|FileCount|파일 수|개수|평균|저장소 계정의 파일 서비스에 있는 파일 수입니다.|FileShare|
|FileShareCount|파일 공유 수|개수|평균|저장소 계정의 파일 서비스에서 파일 공유 수입니다.|None|
|파일 공유스냅샷카운트|파일 공유 스냅샷 수|개수|평균|저장소 계정의 파일 서비스에서 공유에 있는 스냅숏 수입니다.|FileShare|
|파일 공유스냅샷 크기|파일 공유 스냅샷 크기|바이트|평균|저장소 계정의 파일 서비스에서 스냅숏에서 바이트로 사용하는 저장소의 양입니다.|FileShare|
|파일 공유 할당량|파일 공유 할당량 크기|바이트|평균|Azure 파일 서비스에서 바이트 단위로 사용할 수 있는 저장소 양에 대한 상한입니다.|FileShare|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지오 타입, ApiName, 인증, 파일 쉐어|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증, 파일 쉐어|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증, 파일 쉐어|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueueCapacity|큐 용량|바이트|평균|저장소 계정의 Queue 서비스에서 바이트로 사용하는 저장소 의 양입니다.|None|
|QueueCount|큐 수|개수|평균|저장소 계정의 Queue 서비스의 큐 수입니다.|None|
|QueueMessageCount|큐 메시지 수|개수|평균|저장소 계정의 Queue 서비스에 있는 대략적인 큐 메시지 수입니다.|None|
|트랜잭션|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|응답 유형, 지리적 유형, ApiName, 인증|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|지오 타입, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|지오 타입, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|지오 타입, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|지오 타입, ApiName, 인증|





## <a name="microsoftstoragecachecaches"></a>마이크로소프트.스토리지캐시/캐시

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|클라이언트IOPS|총 클라이언트 IOPS|개수|평균|캐시에서 처리하는 클라이언트 파일 작업의 비율입니다.|None|
|클라이언트 지연|평균 클라이언트 대기 시간|밀리초|평균|저장소 캐시에 대한 클라이언트 파일 작업의 평균 대기 시간입니다.|None|
|클라이언트ReadIOPS|클라이언트 읽기 IOPS|초당 개수|평균|초당 클라이언트 읽기 작업입니다.|None|
|클라이언트읽기Throughput|평균 캐시 읽기 처리량|초당 바이트 수|평균|클라이언트 읽기 데이터 전송 속도입니다.|None|
|클라이언트 쓰기IOPS|클라이언트 쓰기 IOPS|초당 개수|평균|초당 클라이언트 쓰기 작업입니다.|None|
|클라이언트쓰기스루풋|평균 캐시 쓰기 처리량|초당 바이트 수|평균|클라이언트쓰기 데이터 전송 속도.|None|
|클라이언트 메타데이터ReadIOPS|클라이언트 메타데이터 읽기 IOPS|초당 개수|평균|영구 상태를 수정하지 않는 데이터 읽기를 제외한 캐시로 전송된 클라이언트 파일 작업의 비율입니다.|None|
|클라이언트 메타데이터쓰기IOPS|클라이언트 메타데이터 쓰기 IOPS|초당 개수|평균|영구 상태를 수정하는 데이터 쓰기를 제외한 캐시로 전송된 클라이언트 파일 작업의 비율입니다.|None|
|클라이언트록IOPS|클라이언트 잠금 IOPS|초당 개수|평균|초당 클라이언트 파일 잠금 작업.|None|
|스토리지대상Health|스토리지 대상 상태|개수|평균|캐시와 저장소 대상 간의 연결 테스트 결과.|None|
|작동 시간|작동 시간|개수|평균|캐시와 모니터링 시스템 간의 연결 테스트 결과.|None|
|스토리지대상IOPS|총 스토리지대상 IOPS|개수|평균|캐시가 특정 StorageTarget에 보내는 모든 파일 작업의 비율입니다.|스토리지 대상|
|스토리지대상쓰기IOPS|스토리지 대상 쓰기 IOPS|개수|평균|캐시가 특정 StorageTarget에 보내는 파일 쓰기 작업의 비율입니다.|스토리지 대상|
|스토리지대상Async쓰기처리가능풋|스토리지 대상 비동기 쓰기 처리량|초당 바이트 수|평균|캐시가 비동기적으로 특정 StorageTarget에 데이터를 쓰는 속도입니다. 이러한 쓰기는 클라이언트가 차단되지 않는 기회 쓰기입니다.|스토리지 대상|
|스토리지타겟싱크쓰기스루풋|스토리지 대상 동기 쓰기 처리량|초당 바이트 수|평균|캐시가 특정 StorageTarget에 데이터를 동기적으로 쓰는 속도입니다. 클라이언트가 차단되도록 하는 쓰기입니다.|스토리지 대상|
|스토리지대상토탈쓰기처리부|스토리지대상 총 쓰기 처리량|초당 바이트 수|평균|캐시가 특정 StorageTarget에 데이터를 쓰는 총 속도입니다.|스토리지 대상|
|스토리지대상 지연|스토리지대상 대기 시간|밀리초|평균|캐시가 partricular StorageTarget에 보내는 모든 파일 작업의 평균 왕복 대기 시간입니다.|스토리지 대상|
|스토리지대상메타메타디시드읽기IOPS|스토리지 대상 메타데이터 읽기 IOPS|초당 개수|평균|영구 상태를 수정하지 않고 읽기 작업을 제외하여 캐시가 특정 StorageTarget으로 보내는 파일 작업의 비율입니다.|스토리지 대상|
|스토리지대상메타데이터쓰기IOPS|스토리지 대상 메타데이터 쓰기 IOPS|초당 개수|평균|캐시가 특정 StorageTarget에 보내는 영구 상태를 수정하고 쓰기 작업을 제외하는 파일 작업의 비율입니다.|스토리지 대상|
|스토리지대상읽기아이오PS|스토리지 대상 읽기 IOPS|초당 개수|평균|캐시가 특정 StorageTarget에 보내는 파일 읽기 작업의 비율입니다.|스토리지 대상|
|스토리지대상읽기전방처리장치|스토리지 대상 읽기 처리량|초당 바이트 수|평균|캐시가 StorageTarget에서 데이터를 기회적으로 읽는 속도입니다.|스토리지 대상|
|스토리지대상필스루풋|스토리지대상 채우기 처리량|초당 바이트 수|평균|캐시 가 캐시 누락을 처리하기 위해 StorageTarget에서 데이터를 읽는 속도입니다.|스토리지 대상|
|스토리지대상토탈판독풋|스토리지대상 총 읽기 처리량|초당 바이트 수|평균|캐시가 특정 StorageTarget의 데이터를 읽는 총 속도입니다.|스토리지 대상|

## <a name="microsoftstoragesyncstoragesyncservices"></a>마이크로소프트.스토리지싱크/스토리지싱크서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|서버싱크세션결과|세션 결과 동기화|개수|평균|서버 끝점이 클라우드 끝점으로 동기화 세션을 성공적으로 완료할 때마다 값을 1로 기록하는 메트릭|동기화 그룹 이름, 서버EndpointName, 동기화 방향|
|스토리지싱크싱크세션응용파일카운트|동기화된 파일|개수|합계|동기화된 파일 수|동기화 그룹 이름, 서버EndpointName, 동기화 방향|
|스토리지싱크세션퍼아이템오류카운트|동기화 상태가 아닌 파일|개수|합계|동기화에 실패한 파일 수|동기화 그룹 이름, 서버EndpointName, 동기화 방향|
|스토리지싱크배치전송파일바이트|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송된 총 파일 크기|동기화 그룹 이름, 서버EndpointName, 동기화 방향|
|스토리지싱크서버하트비트|서버 온라인 상태|개수|최대|resigtered 서버가 클라우드 엔드포인트로 하트비트를 성공적으로 기록할 때마다 값을 1로 기록하는 메트릭|ServerName|
|스토리지싱크리콜IO토탈사이즈바이트|클라우드 계층화 회수|바이트|합계|서버에서 회수한 데이터의 총 크기|ServerName|
|스토리지싱크리콜토탈네트워크바이트|클라우드 계층화 리콜 크기|바이트|합계|회수되는 데이터 크기|동기화 그룹 이름, 서버 이름|
|스토리지싱크리콜투입바이스초|클라우드 계층화 리콜 처리량|초당 바이트 수|평균|데이터 회수 처리량 크기|동기화 그룹 이름, 서버 이름|
|스토리지싱크리콜네트워크바이트응용프로그램|애플리케이션별 클라우드 계층화 리콜 크기|바이트|합계|응용 프로그램에서 회수하는 데이터 크기|동기화 그룹 이름, 서버 이름, 응용 프로그램 이름|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>마이크로 소프트.스토리지싱크/스토리지싱크서비스/싱크그룹

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|동기화 그룹동기화세션응용파일카운트|동기화된 파일|개수|합계|동기화된 파일 수|동기화 그룹 이름, 서버EndpointName, 동기화 방향|
|동기화 그룹싱크세션퍼항목오류카운트|동기화 상태가 아닌 파일|개수|합계|동기화에 실패한 파일 수|동기화 그룹 이름, 서버EndpointName, 동기화 방향|
|동기화 그룹배치 전송된 파일 바이트|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송된 총 파일 크기|동기화 그룹 이름, 서버EndpointName, 동기화 방향|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>마이크로 소프트.storagesync/스토리지싱크서비스/동기화그룹/서버엔드포인트

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|서버엔드포인트싱크세션응용파일카운트|동기화된 파일|개수|합계|동기화된 파일 수|서버엔드포인트네임, 동기화방향|
|서버엔드포인트싱크세션퍼아이템오류카운트|동기화 상태가 아닌 파일|개수|합계|동기화에 실패한 파일 수|서버엔드포인트네임, 동기화방향|
|서버엔드포인트배치전송파일바이트|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송된 총 파일 크기|서버엔드포인트네임, 동기화방향|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>마이크로 소프트.storagesync/스토리지싱크서비스/등록된서버

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|서버하트비트|서버 온라인 상태|개수|최대|resigtered 서버가 클라우드 엔드포인트로 하트비트를 성공적으로 기록할 때마다 값을 1로 기록하는 메트릭|서버리소스ID, 서버 이름|
|서버리콜IO토탈사이즈바이트|클라우드 계층화 회수|바이트|합계|서버에서 회수한 데이터의 총 크기|서버리소스ID, 서버 이름|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 사용률|백분율|최대|SU % 사용률|논리 이름, 파티션ID|
|InputEvents|입력 이벤트|개수|합계|입력 이벤트|논리 이름, 파티션ID|
|InputEventBytes|입력 이벤트 바이트|바이트|합계|입력 이벤트 바이트|논리 이름, 파티션ID|
|LateInputEvents|늦은 입력 이벤트|개수|합계|늦은 입력 이벤트|논리 이름, 파티션ID|
|OutputEvents|출력 이벤트|개수|합계|출력 이벤트|논리 이름, 파티션ID|
|ConversionErrors|데이터 변환 오류|개수|합계|데이터 변환 오류|논리 이름, 파티션ID|
|오류|런타임 오류|개수|합계|런타임 오류|논리 이름, 파티션ID|
|DroppedOrAdjustedEvents|잘못된 이벤트|개수|합계|잘못된 이벤트|논리 이름, 파티션ID|
|AMLCalloutRequests|기능 요청|개수|합계|기능 요청|논리 이름, 파티션ID|
|AMLCalloutFailedRequests|실패한 기능 요청|개수|합계|실패한 기능 요청|논리 이름, 파티션ID|
|AMLCalloutInputEvents|함수 이벤트|개수|합계|함수 이벤트|논리 이름, 파티션ID|
|DeserializationError|입력 역직렬화 오류|개수|합계|입력 역직렬화 오류|논리 이름, 파티션ID|
|EarlyInputEvents|초기 입력 이벤트|개수|합계|초기 입력 이벤트|논리 이름, 파티션ID|
|OutputWatermarkDelaySeconds|워터마크 지연|초|최대|워터마크 지연|논리 이름, 파티션ID|
|입력이벤트소스백로그|백로그된 입력 이벤트|개수|최대|백로그된 입력 이벤트|논리 이름, 파티션ID|
|입력이벤트소스퍼초|수신된 입력 원본|개수|합계|수신된 입력 원본|논리 이름, 파티션ID|

## <a name="microsoftsynapseworkspaces"></a>마이크로소프트.시냅스/작업 공간

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|오케스트레이션파이프라인실행종료|파이프라인 실행이 종료되었습니다.|개수|합계|성공, 실패 또는 취소된 오케스트레이션 파이프라인 실행 횟수|결과, 실패 유형, 파이프 라인|
|오케스트레이션활동종료|활동 실행이 종료되었습니다.|개수|합계|성공, 실패 또는 취소된 오케스트레이션 활동 수|결과, 실패 유형, 활동, 활동 유형, 파이프 라인|
|오케스트레이션트리거종료|트리거 종료|개수|합계|성공, 실패 또는 취소된 오케스트레이션 트리거 수|결과, 실패 유형, 트리거|
|SQLOnDemand 로그인 시도|로그인 시도|개수|합계|고소하거나 실패한 로그인 시도 수|결과|
|SQLOn수요쿼리종료|쿼리가 종료되었습니다.|개수|합계|성공, 실패 또는 취소된 쿼리 수|결과|
|SQLOnDemand쿼리처리바이트|처리된 데이터|바이트|합계|쿼리에서 처리되는 데이터 양|None|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>마이크로소프트.시냅스/작업 공간/빅데이터풀

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|스파크잡스엔드|종료된 응용 프로그램|개수|합계|종료된 응용 프로그램 수|작업 유형, 작업 결과|
|코어용량|코어 용량|개수|최대|코어 용량|None|
|메모리 용량GB|메모리 용량(GB)|개수|최대|메모리 용량(GB)|None|

## <a name="microsoftsynapseworkspacessqlpools"></a>마이크로소프트.시냅스/작업 공간/sqlPools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|DWULimit|DWU 제한|개수|최대|SQL 풀의 서비스 수준 목표|None|
|DWU사용|DWU 사용됨|개수|최대|SQL 풀 전체에서 사용량을 상시 적으로 나타냅니다. DWU 한계 * DWU 백분율로 측정|None|
|DWUUsed퍼센트|DWU 사용 백분율|백분율|최대|SQL 풀 전체에서 사용량을 상시 적으로 나타냅니다. CPU 백분율과 데이터 IO 백분율 간의 최대 값으로 측정|None|
|연결차단By방화벽|방화벽에 의해 차단된 연결|개수|합계|방화벽 규칙에 의해 차단된 연결 수입니다. SQL 풀에 대한 액세스 제어 정책을 다시 방문하고 수가 많으면 이러한 연결을 모니터링합니다.|None|
|어댑티브캐시히트퍼센트|적응형 캐시 적중률|백분율|최대|워크로드가 적응형 캐시를 얼마나 잘 활용하고 있는지 측정합니다. 캐시 적중률 메트릭과 함께 이 메트릭을 사용하여 추가 용량을 위해 확장할지 아니면 워크로드를 다시 실행하여 캐시에 수화할지 여부를 결정합니다.|None|
|어댑티브캐시유퍼센트|적응형 캐시 사용 백분율|백분율|최대|워크로드가 적응형 캐시를 얼마나 잘 활용하고 있는지 측정합니다. 캐시에 사용된 백분율 메트릭과 함께 이 메트릭을 사용하여 추가 용량을 위해 확장할지 아니면 워크로드를 다시 실행하여 캐시에 수화할지 여부를 결정합니다.|None|
|로컬템포DB사용퍼센트|로컬 tempdb 사용 백분율|백분율|최대|모든 계산 노드에서 로컬 tempdb 사용률 - 5분마다 값이 내보됩니다.|None|
|메모리사용퍼센트|메모리 사용 백분율|백분율|최대|SQL 풀의 모든 노드에서 메모리 사용률|None|
|Connections|Connections|개수|합계|SQL 풀에 대한 총 로그인 수|결과|
|WLG액티브쿼리|워크로드 그룹 활성 쿼리|개수|합계|워크로드 그룹 내의 활성 쿼리입니다. 이 메트릭을 사용하면 필터링되지 않고 분할되지 않은 모든 활성 쿼리가 시스템에서 실행됩니다.|IsUser정의, 워크로드 그룹|
|WLGActive쿼리타임아웃|워크로드 그룹 쿼리 시간 시간|개수|합계|시간 만료된 워크로드 그룹에 대한 쿼리입니다. 이 메트릭에 의해 보고된 쿼리 시간 시간은 쿼리실행이 시작된 후에만 실행됩니다(잠금 또는 리소스 대기로 인한 대기 시간은 포함되지 않음).|IsUser정의, 워크로드 그룹|
|WLG할당바이시스템퍼센트|시스템 백분율별 워크로드 그룹 할당|백분율|최대|전체 시스템을 기준으로 한 리소스 할당 비율|IsUser정의, 워크로드 그룹|
|WLG할당바이맥스리소스퍼센트|워크로드 그룹 최대 리소스 백분율별 할당|백분율|최대|워크로드 그룹당 유효 한도 리소스 백분율을 기준으로 리소스 할당 비율을 표시합니다. 이 메트릭은 워크로드 그룹의 효과적인 활용도를 제공합니다.|IsUser정의, 워크로드 그룹|
|WLGEffectiveCapResource퍼센트|유효 한도 리소스 백분율|백분율|최대|워크로드 그룹에 대한 유효 한도 리소스 백분율입니다. min_percentage_resource > 0인 다른 워크로드 그룹이 있는 경우 effective_cap_percentage_resource 비례적으로 낮아집니다.|IsUser정의, 워크로드 그룹|
|wlg_effective_min_resource_percent|유효 최소 리소스 백분율|백분율|최소|유효 최소 리소스 백분율 설정은 서비스 수준 및 워크로드 그룹 설정을 고려할 수 있었습니다. 효과적인 min_percentage_resource 낮은 서비스 수준에서 더 높게 조정할 수 있습니다.|IsUser정의, 워크로드 그룹|
|WLG큐어쿼리|워크로드 그룹 큐에 대기된 쿼리|개수|합계|최대 동시성 한도에 도달한 후 큐에 대기된 요청의 누적 수|IsUser정의, 워크로드 그룹|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 메시지 수입니다.|None|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 잘못된 메시지 수입니다.|None|
|IngressReceivedBytes|수신된 바이트|바이트|합계|모든 이벤트 원본에서 읽은 바이트 수입니다.|None|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|None|
|IngressStoredEvents|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|None|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|None|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|개수|평균|이벤트 소스 파티션에서 마지막으로 큐에 대기된 메시지의 시퀀스 수와 수신에서 처리중인 메시지의 시퀀스 수 간의 차이|None|
|웜스토리지맥스프로퍼티|웜 스토리지 최대 속성|개수|최대|S1/S2 SKU에 대한 환경에서 허용되는 최대 속성 수 및 PAYG SKU에 대한 웜 스토어에서 허용하는 최대 속성 수|None|
|웜스토리지사용프로퍼티|웜 스토리지 사용 속성 |개수|최대|S1/S2 SKU에 대한 환경에서 사용되는 속성 수 및 PAYG SKU에 대한 웜 스토어에서 사용하는 속성 수|None|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|개수|합계|이벤트 원본에서 읽은 메시지 수|None|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|개수|합계|이벤트 원본에서 읽은 잘못된 메시지 수|None|
|IngressReceivedBytes|수신된 바이트|바이트|합계|이벤트 원본에서 읽은 바이트 수|None|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|None|
|IngressStoredEvents|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|None|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|None|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|개수|평균|이벤트 소스 파티션에서 마지막으로 큐에 대기된 메시지의 시퀀스 수와 수신에서 처리중인 메시지의 시퀀스 수 간의 차이|None|
|웜스토리지맥스프로퍼티|웜 스토리지 최대 속성|개수|최대|S1/S2 SKU에 대한 환경에서 허용되는 최대 속성 수 및 PAYG SKU에 대한 웜 스토어에서 허용하는 최대 속성 수|None|
|웜스토리지사용프로퍼티|웜 스토리지 사용 속성 |개수|최대|S1/S2 SKU에 대한 환경에서 사용되는 속성 수 및 PAYG SKU에 대한 웜 스토어에서 사용하는 속성 수|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>마이크로소프트.VM웨어클라우드심플/가상머신

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|디스크판독바이스퍼초|디스크 읽기 바이트/초|초당 바이트 수|평균|샘플 기간 동안 읽기 작업으로 인한 평균 디스크 처리량입니다.|None|
|디스크 쓰기바이트퍼초|디스크 쓰기 바이트/초|초당 바이트 수|평균|샘플 기간 동안 쓰기 작업으로 인한 평균 디스크 처리량입니다.|None|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|샘플 기간 동안 읽기 작업으로 인한 총 디스크 처리량입니다.|None|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|샘플 기간 동안 쓰기 작업으로 인한 총 디스크 처리량입니다.|None|
|디스크 읽기연수|디스크 읽기 작업|개수|합계|이전 샘플 기간의 IO 읽기 작업 수입니다. 이러한 작업의 크기는 가변적일 수 있습니다.|None|
|디스크 쓰기작업|디스크 쓰기 작업|개수|합계|이전 샘플 기간의 IO 쓰기 작업 수입니다. 이러한 작업의 크기는 가변적일 수 있습니다.|None|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 읽기 작업 수입니다. 이러한 작업의 크기는 가변적일 수 있습니다.|None|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 쓰기 작업 수입니다. 이러한 작업의 크기는 가변적일 수 있습니다.|None|
|디스크 판독지연|디스크 읽기 대기 시간|밀리초|평균|총 읽기 대기 시간입니다. 장치와 커널의 합계는 대기 시간을 읽습니다.|None|
|디스크 작성지연|디스크 쓰기 대기 시간|밀리초|평균|총 쓰기 대기 시간입니다. 장치 와 커널의 합계는 대기 시간을 씁니다.|None|
|네트워크인바이트퍼초|초당 네트워크/초|초당 바이트 수|평균|수신된 트래픽에 대한 평균 네트워크 처리량입니다.|None|
|네트워크아웃바이트퍼초|네트워크 아웃 바이트/초|초당 바이트 수|평균|전송된 트래픽에 대한 평균 네트워크 처리량입니다.|None|
|네트워크 인|네트워크 인|바이트|합계|수신된 트래픽에 대한 총 네트워크 처리량입니다.|None|
|네트워크 아웃|네트워크 아웃|바이트|합계|전송된 트래픽에 대한 총 네트워크 처리량입니다.|None|
|사용 된 메모리|사용된 메모리|바이트|평균|VM에서 사용 하는 컴퓨터 메모리의 양입니다.|None|
|메모리 부여|메모리 부여|바이트|평균|호스트가 VM에 부여한 메모리 양입니다. 메모리는 한 번 만질 때까지 호스트에 부여되지 않으며 VMkernel에 메모리가 필요한 경우 메모리가 교체되거나 풍선이 될 수 있습니다.|None|
|메모리 활성|메모리 활성|바이트|평균|VM이 과거의 작은 시간 창에서 사용한 메모리 양입니다. 이는 VM이 현재 필요로 하는 메모리의 "실제" 수입니다. 사용하지 않은 추가 메모리는 게스트의 성능에 영향을 미치지 않고 교체되거나 풍선으로 바뀝질 수 있습니다.|None|
|백분율 CPU|백분율 CPU|백분율|평균|CPU 사용률입니다. 이 값은 시스템의 모든 프로세서 코어를 나타내는 100%로 보고됩니다. 예를 들어, 4코어 시스템의 50%를 사용하는 2방향 VM은 두 개의 코어를 완전히 사용하고 있습니다.|None|
|백분율CpuReady|백분율 CPU 준비|밀리초|합계|준비 시간은 CPU가 과거 업데이트 간격에서 사용할 수 있게 되기를 기다리는 시간입니다.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|개수|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|개수|평균|Http 큐 길이|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|TcpSynSent|TCP 신 전송|개수|평균|TCP 신 전송|인스턴스|
|TcpSynReceived|TCP 신 수신|개수|평균|TCP 신 수신|인스턴스|
|Tcp 설립|TCP 설립|개수|평균|TCP 설립|인스턴스|
|TcpFinWait1|TCP 핀 대기 1|개수|평균|TCP 핀 대기 1|인스턴스|
|TcpFinWait2|TCP 핀 대기 2|개수|평균|TCP 핀 대기 2|인스턴스|
|TcpClosing|TCP 닫기|개수|평균|TCP 닫기|인스턴스|
|TcpCloseWait|TCP 닫기 대기|개수|평균|TCP 닫기 대기|인스턴스|
|TcpLastAck|TCP 라스트 아크|개수|평균|TCP 라스트 아크|인스턴스|
|TcpTimeWait|TCP 시간 대기|개수|평균|TCP 시간 대기|인스턴스|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

> [!NOTE]
> **파일 시스템 사용은** 전 세계적으로 롤아웃되는 새로운 메트릭으로, 비공개 미리 보기에 대해 화이트리스트에 등록되지 않은 경우 데이터가 예상되지 않습니다.

> [!IMPORTANT]
> **메트릭** 집계와 혼동을 피하기 위해 평균 응답 시간이 더 이상 사용되지 않습니다. **응답 시간을** 대체로 사용합니다.

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|개수|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|Http 401|개수|합계|Http 401|인스턴스|
|Http403|Http 403|개수|합계|Http 403|인스턴스|
|Http404|Http 404|개수|합계|Http 404|인스턴스|
|Http406|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|ResponseTime|응답 시간|초|합계|응답 시간|인스턴스|
|AverageResponseTime|평균 응답 시간(더 이상 사용되지 않습니다)|초|평균|평균 응답 시간|인스턴스|
|AppConnections|Connections|개수|평균|Connections|인스턴스|
|핸들|핸들 개수|개수|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|개수|평균|스레드 개수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|개수|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|
|상태 확인 상태|상태 확인 상태|개수|평균|상태 확인 상태|인스턴스|
|파일 시스템 사용|파일 시스템 사용|바이트|평균|파일 시스템 사용|None|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|개수|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|Http 401|개수|합계|Http 401|인스턴스|
|Http403|Http 403|개수|합계|Http 403|인스턴스|
|Http404|Http 404|개수|합계|Http 404|인스턴스|
|Http406|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|Http응답 시간|응답 시간|초|평균|응답 시간|인스턴스|
|FunctionExecutionUnits|함수 실행 단위|개수|합계|함수 실행 단위|인스턴스|
|FunctionExecutionCount|함수 실행 횟수|개수|합계|함수 실행 횟수|인스턴스|
|AppConnections|Connections|개수|평균|Connections|인스턴스|
|핸들|핸들 개수|개수|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|개수|평균|스레드 개수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|개수|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|
|상태 확인 상태|상태 확인 상태|개수|평균|상태 확인 상태|인스턴스|
|파일 시스템 사용|파일 시스템 사용|바이트|평균|파일 시스템 사용|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|요청|요청|개수|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|Http 401|개수|합계|Http 401|인스턴스|
|Http403|Http 403|개수|합계|Http 403|인스턴스|
|Http404|Http 404|개수|합계|Http 404|인스턴스|
|Http406|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|개수|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|개수|평균|Http 큐 길이|인스턴스|
|ActiveRequests|활성 요청|개수|합계|활성 요청|인스턴스|
|TotalFrontEnds|총 프런트 엔드|개수|평균|총 프런트 엔드|None|
|SmallAppServicePlanInstances|소형 App Service 계획 작업자|개수|평균|소형 App Service 계획 작업자|None|
|MediumAppServicePlanInstances|중형 App Service 계획 작업자|개수|평균|중형 App Service 계획 작업자|None|
|LargeAppServicePlanInstances|대형 App Service 계획 작업자|개수|평균|대형 App Service 계획 작업자|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|WorkersTotal|총 작업자|개수|평균|총 작업자|None|
|WorkersAvailable|사용 가능한 작업자|개수|평균|사용 가능한 작업자|None|
|WorkersUsed|사용된 작업자|개수|평균|사용된 작업자|None|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
## <a name="next-steps"></a>다음 단계
* [Azure Monitor의 메트릭에 대해 읽기](data-platform.md)
* [메트릭에 대한 경고 만들기](alerts-overview.md)
* [스토리지, 이벤트 허브 또는 Log Analytics에 메트릭 내보내기](platform-logs-overview.md)
