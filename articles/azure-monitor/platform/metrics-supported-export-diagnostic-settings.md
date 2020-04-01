---
title: 진단 설정을 통해 내보낼 수 있는 Azure Monitor 플랫폼 메트릭
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 6be8cb1b7e74301d16a1174f5ca2b774334dac3f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422118"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>진단 설정을 통해 내보낼 수 있는 Azure Monitor 플랫폼 메트릭

Azure Monitor는 기본적으로 구성 없이 [플랫폼 메트릭을](data-platform-metrics.md) 제공합니다. 포털에서 차트를 표시하거나, REST API를 통해 액세스하거나, PowerShell 또는 CLI를 사용하여 쿼리하는 등 플랫폼 메트릭과 상호 작용하는 여러 가지 방법을 제공합니다. Azure Monitor의 통합 메트릭 파이프라인에서 현재 사용할 수 있는 전체 플랫폼 메트릭 목록은 [메트릭을](metrics-supported.md) 참조하세요. 이러한 메트릭을 쿼리하고 액세스하려면 [2018-01-01 api 버전을](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)사용하십시오. 레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다.

두 가지 방법 중 하나로 Azure 모니터 파이프라인에서 다른 위치로 플랫폼 메트릭을 내보낼 수 있습니다.
1. [진단 설정을](diagnostic-settings.md) 사용하여 로그 분석, 이벤트 허브 또는 Azure 저장소로 전송합니다.
2. REST [메트릭 사용](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Azure Monitor 백 엔드의 복잡성으로 인해 진단 설정을 사용하여 모든 메트릭을 내보낼 수 있는 것은 아닙니다. 아래 표에는 진단 설정을 사용하여 내보낼 수 있는 및 내보낼 수 없는 목록이 나열되어 있습니다.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>눌 및 0 값에 대 한 동작으로 변경 
 
진단 설정을 통해 내보낼 수 있는 플랫폼 메트릭의 경우 Azure Monitor에서 '0s'를 'Null'로 해석하는 몇 가지 메트릭이 있습니다. 이로 인해 실제 '0s'(리소스에서 내보낸 값)와 '0s'(Nulls) 해석 간에 약간의 혼동이 발생했습니다. 곧 변경이 발생하고 진단 설정을 통해 내보낸 플랫폼 메트릭이 기본 리소스에서 실제로 내보내지 않는 한 더 이상 '0s'를 내보내지 않습니다. 변경은 2020년 4월 1일로 예정되었지만 COVID-19로 인해 우선 이동으로 인해 지연되었습니다. 

참고:

1.  리소스 그룹 또는 특정 리소스를 삭제하면 영향을 받는 리소스의 메트릭 데이터가 더 이상 진단 설정 내보내기 대상으로 전송되지 않습니다. 즉, 이벤트 허브, 저장소 계정 및 로그 분석 작업 영역에 더 이상 표시되지 않습니다.
2.  이 개선 사항은 모든 퍼블릭 및 프라이빗 클라우드에서 사용할 수 있습니다.
3.  이 변경 사항은 다음 경험의 동작에 영향을 주지 않습니다. 
   - 진단 설정을 통해 내보낸 플랫폼 리소스 로그
   - 메트릭 탐색기에서 차트를 차트로 하는 메트릭
   - 플랫폼 메트릭에 대한 알림
 
## <a name="metrics-exportable-table"></a>메트릭 내보내기 테이블 

테이블에는 다음과 같은 열이 있습니다. 
- 진단 설정을 통해 내보낼 수 있습니까? 
- NULL / 0에 의해 영향을 
- ResourceType 
- 메트릭 
- 메트릭표시이름
- 단위 
- AggregationType


> [!NOTE]
> 아래 표아래에는 가로 스크롤 막대가 있을 수 있습니다. 정보가 누락되었다고 생각되면 스크롤 막대가 왼쪽끝까지 있는지 확인합니다.  


| 진단 설정을 통해 내보낼 수 있습니까?  | 이미 NULL을 방출 |  ResourceType  |  메트릭  |  메트릭표시이름  |  단위  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  메모리: 클리너 현재 가격  |  개수  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  메모리: 클리너 메모리 축소 불가능  |  바이트  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  메모리: 클리너 메모리 축소 가능  |  바이트  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  스레드: 명령 풀 사용 중인 스레드  |  개수  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  스레드: 명령 풀 유휴 상태 스레드  |  개수  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  명령 풀의 작업 큐 길이  |  개수  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  연결: 현재 연결  |  개수  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  현재 사용자 세션 수  |  개수  |  평균 | 
| 예****  | 예 |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  스레드: 긴 구문 분석 사용 중인 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  스레드: 긴 구문 분석 유휴 상태 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  스레드: 긴 구문 분석 작업 큐 길이  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  M 엔진 메모리  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M 엔진 프라이빗 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M 엔진 QPU  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  M 엔진 버추얼 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  memory_metric  |  메모리  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  메모리 쓰래싱  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  메모리: 메모리 제한 하드  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  메모리: 메모리 제한 상한  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  메모리: 메모리 제한 하한  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  메모리: 메모리 제한 VertiPaq  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  메모리: 메모리 사용량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  프라이빗 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  스레드: 처리 풀 사용 중인 I/O 작업 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  스레드: 처리 풀 사용 중인 비-I/O 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  스레드: 처리 풀 유휴 상태 I/O 작업 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  스레드: 처리 풀 유휴 상태 비-I/O 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  스레드: 처리 풀 I/O 작업 큐 길이  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  처리 풀의 작업 큐 길이  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  쿼리 풀의 사용 중인 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  스레드: 쿼리 풀 유휴 상태 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  스레드: 쿼리 풀 작업 큐 길이  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  할당량  |  메모리: 할당량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  메모리: 차단된 할당량  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  처리: 초당 변환된 행  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  처리: 초당 읽은 행  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  처리: 초당 작성된 행  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  스레드: 짧은 구문 분석 사용 중인 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  스레드: 짧은 구문 분석 유휴 상태 스레드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  스레드: 짧은 구문 분석 작업 큐 길이  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  초당 성공한 연결 수  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  총 연결 실패 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  총 연결 요청 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  메모리: 페이징되지 않은 VertiPaq  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  메모리: 페이징된 VertiPaq  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  가상 바이트  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  백엔드지속시간  |  백 엔드 요청 의 기간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.ApiManagement/service  |  용량  |  용량  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  Duration  |  게이트웨이 요청의 전체 기간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브드롭이벤트  |  삭제된 이벤트 허브 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브거부이벤트  |  거부된 이벤트 허브 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브성공이벤트  |  성공적인 이벤트 허브 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브스로틀드이벤트  |  제한 이벤트 허브 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브시간아웃이벤트  |  시간 시간 반 이벤트 허브 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브토탈바이트센트  |  이벤트 허브 이벤트 크기  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브토탈이벤트  |  총 이벤트 허브 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  이벤트허브토탈실패이벤트  |  실패한 이벤트 허브 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  FailedRequests  |  실패한 게이트웨이 요청(더 이상 사용되지 않는)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  OtherRequests  |  기타 게이트웨이 요청(더 이상 사용되지 않는)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  요청  |  요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  성공적인 게이트웨이 요청(더 이상 사용되지 않는)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  TotalRequests  |  총 게이트웨이 요청(더 이상 사용되지 않는)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  무단 게이트웨이 요청(더 이상 사용되지 않음)  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  앱Cpu사용률  |  앱 CPU 사용량 비율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  앱메모리커밋  |  앱 메모리 할당  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  앱메모리맥스  |  앱 메모리 최대  |  바이트  |  최대 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  앱메모리사용  |  사용된 앱 메모리  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  GCPause토탈카운트  |  GC 일시 정지 수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  GCPause토탈타임  |  GC 일시 정지 총 시간  |  밀리초  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  맥스올드젠메모리풀바이트  |  사용 가능한 최대 이전 세대 데이터 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  올드젠메모리풀바이트  |  이전 세대 데이터 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  올드젠프로모션바이트  |  이전 세대 데이터 크기로 승격  |  바이트  |  최대 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  시스템Cpu사용률  |  시스템 CPU 사용률  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣오류카운트  |  톰캣 전역 오류  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣수신바이트  |  톰캣 총 수신 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣인퀘스트맥스타임  |  톰캣 요청 최대 시간  |  밀리초  |  최대 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣요청토탈카운트  |  톰캣 요청 총 수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣인퀘스트토탈타임  |  톰캣 요청 총 시간  |  밀리초  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣응답에이브그타임  |  톰캣 요청 평균 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰카트센트바이트  |  톰캣 총 전송 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣세션액티브전류카운트  |  톰캣 세션 얼라이브 카운트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣세션액티브맥스카운트  |  톰캣 세션 최대 활성 카운트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣세션얼맥스타임  |  톰캣 세션 최대 살아있는 시간  |  밀리초  |  최대 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣세션생성카운트  |  톰캣 세션 생성 개수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣세션 만료카운트  |  톰캣 세션 만료 개수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  톰캣세션거부카운트  |  톰캣 세션 거부 카운트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.앱플랫폼/봄  |  영젠프로모션바이트  |  젊은 세대 데이터 크기로 승격  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Automation/automationAccounts  |  TotalJob  |  총 작업 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Automation/automationAccounts  |  전체 업데이트 배포컴퓨터 실행  |  총 업데이트 배포 머신 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Automation/automationAccounts  |  총 업데이트 배포 실행  |  총 업데이트 배포 실행  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  CoreCount  |  전용된 코어 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  노드 수 만들기  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  유휴 상태인 노드 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  작업 삭제 완료 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  작업 삭제 시작 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  작업 비활성화 완료 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  작업 비활성화 시작 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  작업 시작 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  작업 종료 완료 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  작업 종료 시작 이벤트  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  나가는 풀 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority 코어 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  오프라인 노드 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  풀 만들기 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  풀 삭제 완료 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  풀 삭제 시작 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  풀 크기 조정 완료 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  풀 크기 조정 시작 이벤트  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  선점된 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  재부팅 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  이미지로 다시 설치하는 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  실행 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  시작 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  시작 작업 실패 노드 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  작업 완료 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  작업 실패 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  작업 시작 이벤트  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  우선 순위가 낮은 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  전용된 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  사용 불가 노드 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  작업 시작 대기 노드 수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  액티브 코어  |  액티브 코어  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  활성 노드  |  활성 노드  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  유휴 코어  |  유휴 코어  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  유휴 노드  |  유휴 노드  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  작업이 완료되었습니다.  |  작업이 완료되었습니다.  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  제출된 작업  |  제출된 작업  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  코어를 남기기  |  코어를 남기기  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  노드 를 떠나기  |  노드 를 떠나기  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  선점코어  |  선점코어  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  선점노드  |  선점노드  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  할당량 사용률  |  할당량 사용률  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  총 코어  |  총 코어  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  총 노드  |  총 노드  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  사용할 수 없는 코어  |  사용할 수 없는 코어  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.배치AI/작업 공간  |  사용할 수 없는 노드  |  사용할 수 없는 노드  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  연결 허용됨  |  허용된 연결  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  연결활성  |  활성 연결 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  연결 처리됨  |  처리된 연결  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  CPU 사용률백분율더블  |  CPU 사용률  |  백분율  |  최대 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  아이오레드바이트  |  IO 읽기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  아이오필바이트  |  IO 쓰기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  MemoryLimit  |  메모리 제한  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  MemoryUsage  |  메모리 사용량  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  메모리 사용률백분율더블  |  메모리 사용량 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  보류 중인 트랜잭션  |  보류 중인 트랜잭션  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  처리된 블록  |  가공블록  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  처리된 트랜잭션  |  처리된 트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  큐에 대기된 트랜잭션  |  큐에 대기된 트랜잭션  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  요청 처리  |  처리된 요청  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.블록체인/블록체인 회원  |  스토리지 사용법  |  스토리지 사용  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits  |  캐시 적중  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits0  |  캐시 적중(분할 0)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits1  |  캐시 적중(분할 1)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits2  |  캐시 적중(분할 2)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits3  |  캐시 적중(분할 3)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits4  |  캐시 적중(분할 4)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits5  |  캐시 적중(분할 5)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits6  |  캐시 적중(분할 6)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits7  |  캐시 적중(분할 7)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits8  |  캐시 적중(분할 8)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachehits9  |  캐시 적중(분할 9)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheLatency  |  캐시 대기 시간 마이크로초(미리 보기)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses  |  캐시 누락  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses0  |  캐시 누락(분할 0)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses1  |  캐시 누락(분할 1)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses2  |  캐시 누락(분할 2)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses3  |  캐시 누락(분할 3)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses4  |  캐시 누락(분할 4)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses5  |  캐시 누락(분할 5)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses6  |  캐시 누락(분할 6)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses7  |  캐시 누락(분할 7)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses8  |  캐시 누락(분할 8)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cachemisses9  |  캐시 누락(분할 9)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead  |  캐시 읽기  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead0  |  캐시 읽기(분할 0)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead1  |  캐시 읽기(분할 1)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead2  |  캐시 읽기(분할 2)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead3  |  캐시 읽기(분할 3)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead4  |  캐시 읽기(분할 4)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead5  |  캐시 읽기(분할 5)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead6  |  캐시 읽기(분할 6)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead7  |  캐시 읽기(분할 7)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead8  |  캐시 읽기(분할 8)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheRead9  |  캐시 읽기(분할 9)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite  |  캐시 쓰기  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite0  |  캐시 쓰기(분할 0)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite1  |  캐시 쓰기(분할 1)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite2  |  캐시 쓰기(분할 2)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite3  |  캐시 쓰기(분할 3)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite4  |  캐시 쓰기(분할 4)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite5  |  캐시 쓰기(분할 5)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite6  |  캐시 쓰기(분할 6)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite7  |  캐시 쓰기(분할 7)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite8  |  캐시 쓰기(분할 8)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  cacheWrite9  |  캐시 쓰기(분할 9)  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients  |  연결된 클라이언트  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients0  |  연결된 클라이언트(분할 0)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients1  |  연결된 클라이언트(분할 1)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients2  |  연결된 클라이언트(분할 2)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients3  |  연결된 클라이언트(분할 3)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients4  |  연결된 클라이언트(분할 4)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients5  |  연결된 클라이언트(분할 5)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients6  |  연결된 클라이언트(분할 6)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients7  |  연결된 클라이언트(분할 7)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients8  |  연결된 클라이언트(분할 8)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  connectedclients9  |  연결된 클라이언트(분할 9)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  오류  |  오류  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys  |  제거된 키  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys0  |  제거된 키(분할 0)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys1  |  제거된 키(분할 1)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys2  |  제거된 키(분할 2)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys3  |  제거된 키(분할 3)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys4  |  제거된 키(분할 4)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys5  |  제거된 키(분할 5)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys6  |  제거된 키(분할 6)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys7  |  제거된 키(분할 7)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys8  |  제거된 키(분할 8)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  evictedkeys9  |  제거된 키(분할 9)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys  |  만료된 키  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys0  |  만료된 키(분할 0)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys1  |  만료된 키(분할 1)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys2  |  만료된 키(분할 2)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys3  |  만료된 키(분할 3)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys4  |  만료된 키(분할 4)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys5  |  만료된 키(분할 5)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys6  |  만료된 키(분할 6)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys7  |  만료된 키(분할 7)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys8  |  만료된 키(분할 8)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  expiredkeys9  |  만료된 키(분할 9)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands  |  가져오기  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands0  |  가져오기(분할 0)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands1  |  가져오기(분할 1)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands2  |  가져오기(분할 2)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands3  |  가져오기(분할 3)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands4  |  가져오기(분할 4)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands5  |  가져오기(분할 5)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands6  |  가져오기(분할 6)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands7  |  가져오기(분할 7)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands8  |  가져오기(분할 8)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  getcommands9  |  가져오기(분할 9)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond  |  초당 작업  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond0  |  초당 작업(분할 0)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond1  |  초당 작업(분할 1)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond2  |  초당 작업(분할 2)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond3  |  초당 작업(분할 3)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond4  |  초당 작업(분할 4)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond5  |  초당 작업(분할 5)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond6  |  초당 작업(분할 6)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond7  |  초당 작업(분할 7)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond8  |  초당 작업(분할 8)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  operationsPerSecond9  |  초당 작업(분할 9)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime  |  CPU  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU(분할 0)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU(분할 1)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU(분할 2)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU(분할 3)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU(분할 4)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU(분할 5)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU(분할 6)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU(분할 7)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU(분할 8)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU(분할 9)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad  |  서버 부하  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad0  |  서버 부하(분할 0)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad1  |  서버 부하(분할 1)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad2  |  서버 부하(분할 2)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad3  |  서버 부하(분할 3)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad4  |  서버 부하(분할 4)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad5  |  서버 부하(분할 5)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad6  |  서버 부하(분할 6)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad7  |  서버 부하(분할 7)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad8  |  서버 부하(분할 8)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  serverLoad9  |  서버 부하(분할 9)  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands  |  집합  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands0  |  설정(분할 0)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands1  |  집합(분할 1)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands2  |  설정(분할 2)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands3  |  설정(분할 3)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands4  |  설정(분할 4)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands5  |  설정(분할 5)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands6  |  설정(분할 6)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands7  |  설정(분할 7)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands8  |  설정(분할 8)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  setcommands9  |  설정(분할 9)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  총 작업  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  총 작업(분할 0)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  총 작업(분할 1)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  총 작업(분할 2)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  총 작업(분할 3)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  총 작업(분할 4)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  총 작업(분할 5)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  총 작업(분할 6)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  총 작업(분할 7)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  총 작업(분할 8)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  총 작업(분할 9)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys  |  전체 키  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys0  |  총 키(분할 0)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys1  |  총 키(분할 1)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys2  |  총 키(분할 2)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys3  |  총 키(분할 3)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys4  |  총 키(분할 4)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys5  |  총 키(분할 5)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys6  |  총 키(분할 6)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys7  |  총 키(분할 7)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys8  |  총 키(분할 8)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  totalkeys9  |  총 키(분할 9)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory  |  사용된 메모리  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory0  |  사용된 메모리(분할 0)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory1  |  사용된 메모리(분할 1)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory2  |  사용된 메모리(분할 2)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory3  |  사용된 메모리(분할 3)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory4  |  사용된 메모리(분할 4)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory5  |  사용된 메모리(분할 5)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory6  |  사용된 메모리(분할 6)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory7  |  사용된 메모리(분할 7)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory8  |  사용된 메모리(분할 8)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemory9  |  사용된 메모리(분할 9)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemorypercentage  |  사용된 메모리 비율  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss  |  사용된 메모리 RSS  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss0  |  사용된 메모리 RSS(분할 0)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss1  |  사용된 메모리 RSS(분할 1)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss2  |  사용된 메모리 RSS(분할 2)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss3  |  사용된 메모리 RSS(분할 3)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss4  |  사용된 메모리 RSS(분할 4)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss5  |  사용된 메모리 RSS(분할 5)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss6  |  사용된 메모리 RSS(분할 6)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss7  |  사용된 메모리 RSS(분할 7)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss8  |  사용된 메모리 RSS(분할 8)  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Cache/redis  |  usedmemoryRss9  |  사용된 메모리 RSS(분할 9)  |  바이트  |  최대 | 
| 예  | 예 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  디스크 읽기 바이트/초  |  디스크 읽기  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  디스크 읽기 작업/초  |  디스크 읽기 작업/초  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  디스크 쓰기 바이트/초  |  디스크 쓰기  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  디스크 쓰기 작업/초  |  디스크 쓰기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  네트워크 인  |  네트워크 인  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  네트워크 아웃  |  네트워크 아웃  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  백분율 CPU  |  백분율 CPU  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.ClassicCompute/virtualMachines  |  디스크 읽기 바이트/초  |  디스크 읽기  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicCompute/virtualMachines  |  디스크 읽기 작업/초  |  디스크 읽기 작업/초  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.ClassicCompute/virtualMachines  |  디스크 쓰기 바이트/초  |  디스크 쓰기  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicCompute/virtualMachines  |  디스크 쓰기 작업/초  |  디스크 쓰기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicCompute/virtualMachines  |  네트워크 인  |  네트워크 인  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.ClassicCompute/virtualMachines  |  네트워크 아웃  |  네트워크 아웃  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.ClassicCompute/virtualMachines  |  백분율 CPU  |  백분율 CPU  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicStorage/storageAccounts  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicStorage/storageAccounts  |  송신  |  송신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.ClassicStorage/storageAccounts  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.ClassicStorage/storageAccounts  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  사용된 용량  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  가용성  |  가용성  |  백분율  |  평균 | 
| 예  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  BlobCapacity  |  Blob 용량  |  바이트  |  평균 | 
| 예  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  BlobCount  |  Blob 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  ContainerCount  |  Blob 컨테이너 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  송신  |  송신  |  바이트  |  합계 | 
| 예  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  IndexCapacity  |  인덱스 용량  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/blobServices  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  송신  |  송신  |  바이트  |  합계 | 
| 예  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  FileCapacity  |  파일 용량  |  바이트  |  평균 | 
| 예  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  FileCount  |  파일 수  |  개수  |  평균 | 
| 예  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  FileShareCount  |  파일 공유 수  |  개수  |  평균 | 
| 예  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  파일 공유 할당량  |  파일 공유 할당량 크기  |  바이트  |  평균 | 
| 예  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  파일 공유스냅샷카운트  |  파일 공유 스냅샷 수  |  개수  |  평균 | 
| 예  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  파일 공유스냅샷 크기  |  파일 공유 스냅샷 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로 소프트.클래식 스토리지 / 스토리지계정 / 파일 서비스  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  송신  |  송신  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  QueueCapacity  |  큐 용량  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  QueueCount  |  큐 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  QueueMessageCount  |  큐 메시지 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/큐서비스  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  송신  |  송신  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  TableCapacity  |  테이블 용량  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  TableCount  |  테이블 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  TableEntityCount  |  테이블 엔터티 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.클래식스토리지/스토리지계정/테이블서비스  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  차단된 호출  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  문자 학습  |  훈련된 캐릭터  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  변환된 문자  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  클라이언트 오류  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  DataIn  |  데이터 입력  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  DataOut  |  데이터 출력  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  대기 시간  |  대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  서버 오류  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  음성 세션 기간  |  초  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  성공한 호출  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  총 호출  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  총 오류  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  총 토큰 호출  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  총 트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  사용된 CPU 크레딧  |  사용된 CPU 크레딧  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  남은 CPU 크레딧  |  남은 CPU 크레딧  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  데이터 디스크 큐 크기  |  데이터 디스크 큐 깊이(미리 보기)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  데이터 디스크 읽기 바이트/초  |  데이터 디스크 읽기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  데이터 디스크 읽기 작업/초  |  데이터 디스크 읽기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  데이터 디스크 쓰기 바이트/초  |  데이터 디스크 쓰기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  데이터 디스크 쓰기 작업/초  |  데이터 디스크 쓰기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크 읽기 바이트  |  디스크 읽기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크 읽기 작업/초  |  디스크 읽기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크 쓰기 바이트  |  디스크 쓰기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크 쓰기 작업/초  |  디스크 쓰기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  인바운드 흐름  |  인바운드 흐름  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  인바운드 흐름 최대 생성 속도  |  인바운드 흐름 최대 생성속도(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  네트워크 인  |  청구 가능한 네트워크(더 이상 사용되지 않습니다)  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  네트워크 총  |  네트워크 총  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  네트워크 아웃  |  네트워크 아웃 청구 가능(더 이상 사용되지 않습니다)  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  네트워크 아웃 합계  |  네트워크 아웃 합계  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  OS 디스크 큐 크기  |  OS 디스크 큐 깊이(미리 보기)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  OS 디스크 읽기 바이트/초  |  OS 디스크 읽기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  OS 디스크 읽기 작업/초  |  OS 디스크 읽기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  OS 디스크 쓰기 바이트/초  |  OS 디스크 쓰기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  OS 디스크 쓰기 작업/초  |  OS 디스크 쓰기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 OS QD  |  OS 디스크 QD(더 이상 사용되지 않습니다)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 OS 읽기 바이트/초  |  OS 디스크 읽기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 OS 읽기 작업/초  |  OS 디스크 읽기 작업/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 OS 쓰기 바이트/초  |  OS 디스크 쓰기 바이트/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 OS 쓰기 작업/초  |  OS 디스크 쓰기 작업/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  아웃바운드 흐름  |  아웃바운드 흐름  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  아웃바운드 흐름 최대 생성 속도  |  아웃바운드 흐름 최대 생성속도(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 QD  |  데이터 디스크 QD(더 이상 사용되지 않습니다)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 읽기 바이트/초  |  데이터 디스크 읽기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 읽기 작업/초  |  데이터 디스크 읽기 작업/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 쓰기 바이트/초  |  데이터 디스크 쓰기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  디스크당 쓰기 작업/초  |  데이터 디스크 쓰기 작업/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  백분율 CPU  |  백분율 CPU  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  프리미엄 데이터 디스크 캐시 읽기 히트  |  프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  프리미엄 데이터 디스크 캐시 읽기 미스  |  프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  프리미엄 OS 디스크 캐시 읽기 히트  |  프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachines  |  프리미엄 OS 디스크 캐시 읽기 미스  |  프리미엄 OS 디스크 캐시 읽기 미스(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  사용된 CPU 크레딧  |  사용된 CPU 크레딧  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  남은 CPU 크레딧  |  남은 CPU 크레딧  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  데이터 디스크 큐 크기  |  데이터 디스크 큐 깊이(미리 보기)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  데이터 디스크 읽기 바이트/초  |  데이터 디스크 읽기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  데이터 디스크 읽기 작업/초  |  데이터 디스크 읽기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  데이터 디스크 쓰기 바이트/초  |  데이터 디스크 쓰기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  데이터 디스크 쓰기 작업/초  |  데이터 디스크 쓰기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크 읽기 바이트  |  디스크 읽기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크 읽기 작업/초  |  디스크 읽기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크 쓰기 바이트  |  디스크 쓰기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크 쓰기 작업/초  |  디스크 쓰기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  인바운드 흐름  |  인바운드 흐름  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  인바운드 흐름 최대 생성 속도  |  인바운드 흐름 최대 생성속도(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  네트워크 인  |  청구 가능한 네트워크(더 이상 사용되지 않습니다)  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  네트워크 총  |  네트워크 총  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  네트워크 아웃  |  네트워크 아웃 청구 가능(더 이상 사용되지 않습니다)  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  네트워크 아웃 합계  |  네트워크 아웃 합계  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 디스크 큐 크기  |  OS 디스크 큐 깊이(미리 보기)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 디스크 읽기 바이트/초  |  OS 디스크 읽기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 디스크 읽기 작업/초  |  OS 디스크 읽기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 디스크 쓰기 바이트/초  |  OS 디스크 쓰기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 디스크 쓰기 작업/초  |  OS 디스크 쓰기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 OS QD  |  OS 디스크 QD(더 이상 사용되지 않습니다)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 OS 읽기 바이트/초  |  OS 디스크 읽기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 OS 읽기 작업/초  |  OS 디스크 읽기 작업/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 OS 쓰기 바이트/초  |  OS 디스크 쓰기 바이트/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 OS 쓰기 작업/초  |  OS 디스크 쓰기 작업/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  아웃바운드 흐름  |  아웃바운드 흐름  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  아웃바운드 흐름 최대 생성 속도  |  아웃바운드 흐름 최대 생성속도(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 QD  |  데이터 디스크 QD(더 이상 사용되지 않습니다)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 읽기 바이트/초  |  데이터 디스크 읽기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 읽기 작업/초  |  데이터 디스크 읽기 작업/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 쓰기 바이트/초  |  데이터 디스크 쓰기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  디스크당 쓰기 작업/초  |  데이터 디스크 쓰기 작업/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  백분율 CPU  |  백분율 CPU  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  프리미엄 데이터 디스크 캐시 읽기 히트  |  프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  프리미엄 데이터 디스크 캐시 읽기 미스  |  프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  프리미엄 OS 디스크 캐시 읽기 히트  |  프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets  |  프리미엄 OS 디스크 캐시 읽기 미스  |  프리미엄 OS 디스크 캐시 읽기 미스(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  사용된 CPU 크레딧  |  사용된 CPU 크레딧  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  남은 CPU 크레딧  |  남은 CPU 크레딧  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  데이터 디스크 큐 크기  |  데이터 디스크 큐 깊이(미리 보기)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  데이터 디스크 읽기 바이트/초  |  데이터 디스크 읽기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  데이터 디스크 읽기 작업/초  |  데이터 디스크 읽기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  데이터 디스크 쓰기 바이트/초  |  데이터 디스크 쓰기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  데이터 디스크 쓰기 작업/초  |  데이터 디스크 쓰기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크 읽기 바이트  |  디스크 읽기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크 읽기 작업/초  |  디스크 읽기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크 쓰기 바이트  |  디스크 쓰기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크 쓰기 작업/초  |  디스크 쓰기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  인바운드 흐름  |  인바운드 흐름  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  인바운드 흐름 최대 생성 속도  |  인바운드 흐름 최대 생성속도(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  네트워크 인  |  청구 가능한 네트워크(더 이상 사용되지 않습니다)  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  네트워크 총  |  네트워크 총  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  네트워크 아웃  |  네트워크 아웃 청구 가능(더 이상 사용되지 않습니다)  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  네트워크 아웃 합계  |  네트워크 아웃 합계  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 디스크 큐 크기  |  OS 디스크 큐 깊이(미리 보기)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 디스크 읽기 바이트/초  |  OS 디스크 읽기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 디스크 읽기 작업/초  |  OS 디스크 읽기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 디스크 쓰기 바이트/초  |  OS 디스크 쓰기 바이트/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 디스크 쓰기 작업/초  |  OS 디스크 쓰기 작업/초(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 OS QD  |  OS 디스크 QD(더 이상 사용되지 않습니다)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 OS 읽기 바이트/초  |  OS 디스크 읽기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 OS 읽기 작업/초  |  OS 디스크 읽기 작업/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 OS 쓰기 바이트/초  |  OS 디스크 쓰기 바이트/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 OS 쓰기 작업/초  |  OS 디스크 쓰기 작업/초(더 이상 사용되지 않습니다)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  아웃바운드 흐름  |  아웃바운드 흐름  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  아웃바운드 흐름 최대 생성 속도  |  아웃바운드 흐름 최대 생성속도(미리 보기)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 QD  |  데이터 디스크 QD(더 이상 사용되지 않습니다)  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 읽기 바이트/초  |  데이터 디스크 읽기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 읽기 작업/초  |  데이터 디스크 읽기 작업/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 쓰기 바이트/초  |  데이터 디스크 쓰기 바이트/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  디스크당 쓰기 작업/초  |  데이터 디스크 쓰기 작업/초(더 이상 사용되지)  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  백분율 CPU  |  백분율 CPU  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  프리미엄 데이터 디스크 캐시 읽기 히트  |  프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  프리미엄 데이터 디스크 캐시 읽기 미스  |  프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  프리미엄 OS 디스크 캐시 읽기 히트  |  프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  프리미엄 OS 디스크 캐시 읽기 미스  |  프리미엄 OS 디스크 캐시 읽기 미스(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  CPU 사용량  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  메모리 사용량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  초당 수신된 네트워크 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  초당 전송된 네트워크 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerRegistry/registries  |  실행 기간  |  실행 지속 시간  |  밀리초  |  합계 | 
| **예**  | 예 |  Microsoft.ContainerRegistry/registries  |  성공한 풀카운트  |  성공적인 풀 카운트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerRegistry/registries  |  성공적인 푸시카운트  |  성공적인 푸시 카운트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerRegistry/registries  |  총 풀카운트  |  총 당기기 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.ContainerRegistry/registries  |  총 푸시 카운트  |  총 푸시 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  관리 클러스터에서 사용 가능한 cpu 코어의 총 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  관리 클러스터에서 사용 가능한 총 메모리 양  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  다양한 노드 조건에 대한 상태  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  단계별 Pod 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  준비 상태인 Pod 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  사용 가능 용량  |  사용 가능한 용량  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  바이트업로드토클라우드  |  클라우드 바이트 업로드(장치)  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  바이트업로드토클라우드퍼쉐어  |  클라우드 바이트 업로드(공유)  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  클라우드읽기관통  |  클라우드 다운로드 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  클라우드리드스투퍼퍼쉐어  |  클라우드 다운로드 처리량(공유)  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  클라우드업로드스루풋  |  클라우드 업로드 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  클라우드업로드투퍼퍼쉐  |  클라우드 업로드 처리량(공유)  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  하이퍼V메모리 활용도  |  에지 계산 - 메모리 사용량  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  하이퍼V버추얼프로세서 사용률  |  에지 계산 - 백분율 CPU  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  NICReadThroughput  |  읽기 처리량(네트워크)  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  NICWrite스루풋  |  쓰기 처리량(네트워크)  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.데이터박스에지/데이터박스에지디바이스  |  총 용량  |  총 용량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  실패한 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  성공한 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  활동취소런  |  취소된 활동은 메트릭을 실행합니다.  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  실패한 활동 실행 메트릭  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  성공한 활동 실행 메트릭  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  팩토리사이즈인그유닛  |  총 공장 크기(GB 단위)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  통합 런타임 사용 가능한 메모리  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  통합런타임평균태스크픽업지연  |  통합 런타임 큐 기간  |  초  |  평균 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  통합 런타임 CPU 사용률  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  통합런타임큐길이  |  통합 런타임 큐 길이  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  최대허용팩토리크기인Gb단위  |  최대 허용 공장 크기(GB 단위)  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  최대 허용된 리소스 카운트  |  허용되는 최대 엔터티 수  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  파이프라인취소런  |  취소된 파이프라인은 메트릭을 실행합니다.  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  실패한 파이프라인 실행 메트릭  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  성공한 파이프라인 실행 메트릭  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  리소스 카운트  |  총 엔터티 수  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  트리거취소 런  |  취소된 트리거는 메트릭을 실행합니다.  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  TriggerFailedRuns  |  실패한 트리거 실행 메트릭  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataFactory/factories  |  TriggerSucceededRuns  |  성공한 트리거 실행 메트릭  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  취소된 AU 시간  |  초  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  실패한 AU 시간  |  초  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  성공한 AU 시간  |  초  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  취소된 작업  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  실패한 작업  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  성공한 작업  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeStore/accounts  |  DataRead  |  데이터 읽기  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  기록된 데이터  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  읽기 요청  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  총 스토리지  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  쓰기 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  활성 연결 수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  사용된 백업 스토리지  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  실패한 연결  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  CPU 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  IO 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  메모리 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  네트워크 아웃  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  네트워크 인  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  복제 지연 시간(초)  |  개수  |  최대 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  서버 로그 스토리지 제한  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  서버 로그 스토리지 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  사용된 서버 로그 스토리지  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  스토리지 제한  |  바이트  |  최대 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  스토리지 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  스토리지 사용됨  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  active_connections  |  활성 연결 수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  사용된 백업 스토리지  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  실패한 연결  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  CPU 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  IO 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  메모리 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  네트워크 아웃  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  네트워크 인  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  복제 지연 시간(초)  |  개수  |  최대 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  서버 로그 스토리지 제한  |  바이트  |  최대 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  서버 로그 스토리지 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  사용된 서버 로그 스토리지  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  스토리지 제한  |  바이트  |  최대 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  스토리지 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DBforMySQL/servers  |  storage_used  |  스토리지 사용됨  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  활성 연결 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  사용된 백업 스토리지  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  실패한 연결  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  CPU 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  IO 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  메모리 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  네트워크 아웃  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  네트워크 인  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  복제본 간 최대 지연 시간  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  복제본 지연 시간  |  초  |  최대 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  서버 로그 스토리지 제한  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  서버 로그 스토리지 비율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  사용된 서버 로그 스토리지  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  스토리지 제한  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  스토리지 비율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  스토리지 사용됨  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  active_connections  |  활성 연결 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  cpu_percent  |  CPU 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  Iops  |  IOPS  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  memory_percent  |  메모리 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  network_bytes_egress  |  네트워크 아웃  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  network_bytes_ingress  |  네트워크 인  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  storage_percent  |  스토리지 비율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.DBforPostgreSQL/서버v2  |  storage_used  |  스토리지 사용됨  |  바이트  |  평균 | 
| **예**  | **예** |  마이크로소프트.장치/계정  |  디지털트윈.원격 분석.노드  |  디지털 트윈 노드 원격 분석 자리 표시자  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  C2D 메시지가 중단되었습니다.  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  C2D 메시지 배달 완료  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  C2D 메시지가 거부됨  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  실패한 직접 메서드 호출  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  직접 메서드 호출의 요청 크기  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  직접 메서드 호출의 응답 크기  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  성공한 직접 메서드 호출  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  백 엔드에서의 실패한 쌍 읽기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  백 엔드에서의 쌍 읽기 응답 크기  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  백 엔드에서의 성공한 쌍 읽기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  백 엔드에서의 실패한 쌍 업데이트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  백 엔드에서의 쌍 업데이트 크기  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  백 엔드에서의 성공한 쌍 업데이트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  C2D메시지 만료됨  |  C2D 메시지가 만료됨(미리 보기)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  구성  |  구성 메트릭  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  연결된 디바이스(미리 보기)  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  라우팅: 메시지/이벤트에 배달된 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  라우팅: 이벤트 허브에 배달된 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  라우팅: Service Bus 큐에 배달된 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  라우팅: Service Bus 토픽에 배달된 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  라우팅: 스토리지에 배달된 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  라우팅: 스토리지에 배달된 Blob  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  라우팅: 스토리지에 배달된 데이터  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  라우팅: 메시지/이벤트에 대한 메시지 대기 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  라우팅: 이벤트 허브에 대한 메시지 대기 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  라우팅: Service Bus 큐에 대한 메시지 대기 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  라우팅: Service Bus 토픽에 대한 메시지 대기 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  라우팅: 스토리지에 대한 메시지 대기 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  라우팅: 삭제된 원격 분석 메시지   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  라우팅: 대체에 배달된 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  라우팅: 원격 분석 메시지 호환되지 않음  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  라우팅: 분리된 원격 분석 메시지   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  라우팅: 배달된 원격 분석 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  원격 분석 메시지 보내기 시도  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  제한 오류 수  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  보낸 원격 분석 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  디바이스에서의 실패한 쌍 읽기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  디바이스에서의 쌍 읽기 응답 크기  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  디바이스에서의 성공한 쌍 읽기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  디바이스에서의 실패한 쌍 업데이트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  디바이스에서의 쌍 업데이트 크기  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  디바이스에서의 성공한 쌍 업데이트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  사용된 전체 메시지 수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  총 장치 데이터 사용량  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  총 디바이스 데이터 사용량(미리 보기)  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  연결된 디바이스(사용되지 않음)   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  총 디바이스(사용되지 않음)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  이벤트그리드 배달  |  이벤트 그리드 배달(미리 보기)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  이벤트그리드지연  |  이벤트 그리드 대기 시간(미리 보기)  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  실패한 작업 취소  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  성공한 작업 취소  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  완료된 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  실패한 메서드 호출 작업 만들기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  메서드 호출 작업에 대한 성공한 만들기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  쌍 업데이트 작업에 대한 실패한 만들기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  쌍 업데이트 작업에 대한 성공한 만들기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  실패한 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  목록 작업에 대한 실패한 호출  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  목록 작업에 대한 성공한 호출  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  실패한 작업 쿼리  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  성공한 작업 쿼리  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  총 디바이스(미리 보기)  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  실패한 쌍 쿼리  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  쌍 쿼리 결과 크기  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  성공한 쌍 쿼리  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  증명 시도  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  할당된 디바이스  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  등록 시도  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  사용 가능한 스토리지  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra 연결 차단  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Cassandra 요청 요금  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra 요청  |  개수  |  개수 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  데이터 사용량  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  삭제가상네트워크  |  삭제가상네트워크  |  개수  |  개수 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  문서 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  문서 할당량  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  인덱스 사용량  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  메타데이터 요청  |  개수  |  개수 | 
| **예**  | **예** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Mongo 요청 요금  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo 요청  |  개수  |  개수 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  몽고어카운트카운트  |  몽고 요청 요금  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  몽고요청삭제  |  몽고 삭제 요청 속도  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  몽고 요청 삽입  |  몽고 삽입 요청 속도  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  몽고요청쿼리  |  몽고 쿼리 요청 속도  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  몽고요청업데이트  |  몽고 업데이트 요청 속도  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  프로비전된 처리량  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  P99 복제 대기 시간  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  서비스 가용성  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  총 요청 수  |  개수  |  개수 | 
| **예**  | **예** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  총 요청 단위  |  개수  |  합계 | 
| 예  | 예 |  마이크로소프트.엔터프라이즈 지식 그래프/서비스  |  오류 수  |  실패한 수  |  개수  |  개수 | 
| 예  | 예 |  마이크로소프트.엔터프라이즈 지식 그래프/서비스  |  SuccessCount  |  성공한 수  |  개수  |  개수 | 
| 예  | 예 |  마이크로소프트.엔터프라이즈 지식 그래프/서비스  |  성공지연  |  성공 대기 시간  |  밀리초  |  평균 | 
| 예  | 예 |  마이크로소프트.엔터프라이즈 지식 그래프/서비스  |  트랜잭션 카운트  |  트랜잭션 수  |  개수  |  개수 | 
| **예**  | **예** |  마이크로소프트.이벤트 그리드/도메인  |  DeadLetteredCount  |  배달 못한 편지 이벤트  |  개수  |  합계 | 
| 예  | 예 |  마이크로소프트.이벤트 그리드/도메인  |  DeliveryAttemptFailCount  |  배달 실패 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.이벤트 그리드/도메인  |  DeliverySuccessCount  |  배달된 이벤트  |  개수  |  합계 | 
| 예  | 예 |  마이크로소프트.이벤트 그리드/도메인  |  DestinationProcessingDurationInMs  |  대상 처리 기간  |  밀리초  |  평균 | 
| **예**  | **예** |  마이크로소프트.이벤트 그리드/도메인  |  DroppedEventCount  |  삭제된 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.이벤트 그리드/도메인  |  MatchedEventCount  |  일치하는 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.이벤트 그리드/도메인  |  PublishFailCount  |  실패한 이벤트 게시  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.이벤트 그리드/도메인  |  PublishSuccessCount  |  게시된 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.이벤트 그리드/도메인  |  게시성공지연InMs  |  성공 대기 시간 게시  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  배달 못한 편지 이벤트  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  배달 실패 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  배달된 이벤트  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  대상 처리 기간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  삭제된 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  일치하는 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  실패한 이벤트 게시  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  게시된 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/extensionTopics  |  게시성공지연InMs  |  성공 대기 시간 게시  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  일치하지 않는 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  실패한 이벤트 게시  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  게시된 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/topics  |  게시성공지연InMs  |  성공 대기 시간 게시  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  일치하지 않는 이벤트  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  AvailableMemory  |  사용 가능한 메모리  |  백분율  |  최대 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  캡처 백로그.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  CapturedBytes  |  캡처된 바이트.  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  CapturedMessages  |  캡처된 메시지.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  ConnectionsClosed  |  끊어진 연결.  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  열린 연결.  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  들어오는 바이트  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  들어오는 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  들어오는 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  보내는 바이트  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  보내는 메시지  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  할당량 초과 오류.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  ServerErrors  |  서버 오류.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  성공한 요청  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  제한된 요청.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/clusters  |  UserErrors  |  사용자 오류.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  캡처 백로그.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  캡처된 바이트.  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  캡처된 메시지.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  끊어진 연결.  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  열린 연결.  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHABL  |  백로그 메시지 보관(더 이상 사용되지 않습니다)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  메시지 처리량 보관(더 이상 사용되지)  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  메시지 보관(더 이상 사용되지 않습니다)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  들어오는 바이트(사용되지 않음)  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  들어오는 바이트(더 이상 사용되지 않음)  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  들어오는 메시지(사용되지 않음)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  보내는 바이트(사용되지 않음)  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  나가는 바이트(더 이상 사용되지 않음)  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  보내는 메시지(사용되지 않음)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  실패한 요청(더 이상 사용되지 않는)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  들어오는 바이트  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  들어오는 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  들어오는 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  INMSGS  |  들어오는 메시지(더 이상 사용되지 않음)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  INREQS  |  들어오는 요청(더 이상 사용되지 않는)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  INTERR  |  내부 서버 오류(더 이상 사용되지 않습니다)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  MISCERR  |  기타 오류(더 이상 사용되지 않습니다)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  보내는 바이트  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  보내는 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  발신 메시지(더 이상 사용되지 않음)  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  할당량 초과 오류.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  ServerErrors  |  서버 오류.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  크기  |  크기  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  성공한 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  성공한 요청(더 이상 사용되지 않는)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  서버 사용 중 오류(더 이상 사용되지 않습니다).  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  제한된 요청.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.EventHub/namespaces  |  UserErrors  |  사용자 오류.  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  분류된 게이트웨이 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  게이트웨이 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.HDInsight/clusters  |  누액티브 워커  |  활성 근로자 수  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.HDInsight/clusters  |  크기 조정 요청  |  요청 크기 조정  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  메트릭 임계값  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  관찰된 용량  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  관찰된 메트릭 값  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  시작된 크기 조정 작업  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  가용성결과/가용성백분율  |  가용성  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Insights/Components  |  가용성결과/수  |  가용성 테스트  |  개수  |  개수 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  availabilityResults/duration  |  가용성 테스트 기간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  페이지 로드 네트워크 연결 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  클라이언트 처리 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  응답 수신 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  요청 전송 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  브라우저 페이지 로드 시간  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.Insights/Components  |  dependencies/count  |  종속성 호출  |  개수  |  개수 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  dependencies/duration  |  종속성 기간  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.Insights/Components  |  dependencies/failed  |  종속성 호출 실패  |  개수  |  개수 | 
| 예  | 예 |  Microsoft.Insights/Components  |  exceptions/browser  |  브라우저 예외  |  개수  |  개수 | 
| **예**  | **예** |  Microsoft.Insights/Components  |  exceptions/count  |  예외  |  개수  |  개수 | 
| 예  | 예 |  Microsoft.Insights/Components  |  exceptions/server  |  서버 예외  |  개수  |  개수 | 
| **예**  | **예** |  Microsoft.Insights/Components  |  pageViews/count  |  페이지 보기  |  개수  |  개수 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  pageViews/duration  |  페이지 보기 로드 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  예외 속도  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  사용 가능한 메모리  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  CPU 프로세스  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  프로세스 IO 속도  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  프로세서 시간  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  프로세스 프라이빗 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  HTTP 요청 실행 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  애플리케이션 큐의 HTTP 요청  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  HTTP 요청 속도  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.Insights/Components  |  requests/count  |  서버 요청  |  개수  |  개수 | 
| **예**  | 예 |  Microsoft.Insights/Components  |  requests/duration  |  서버 응답 시간  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.Insights/Components  |  requests/failed  |  실패한 요청  |  개수  |  개수 | 
| 예  | 예 |  Microsoft.Insights/Components  |  요청/요금  |  서버 요청 속도  |  초당 개수  |  평균 | 
| **예**  | **예** |  Microsoft.Insights/Components  |  traces/count  |  Traces  |  개수  |  개수 | 
| **예**  | 예 |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Service API 총 방문 횟수  |  개수  |  개수 | 
| **예**  | 예 |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  전체 Service API 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Service API 총 결과  |  개수  |  개수 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  캐시 사용률  |  캐시 사용률  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  연속수출맥스대기분  |  연속 내보내기 최대 대기 시간  |  개수  |  최대 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  연속수출넘의기록수출  |  연속 내보내기 - 내보낸 레코드의 num  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  연속수출보류카운트  |  연속 내보내기 보류 중인 수  |  개수  |  최대 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  연속수출결과  |  연속 내보내기 결과  |  개수  |  개수 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  이벤트처리된ForEventHubs  |  처리된 이벤트(이벤트/IoT 허브용)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  내보내기 사용률  |  내보내기 활용도  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  인시션지연시간인초  |  섭취 대기 시간(초)  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  섭취 결과  |  개수  |  개수 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  인기 활용  |  섭취 활용도  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  섭취볼륨인MB  |  섭취 볼륨(MB)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  살아 남기  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  쿼리 기간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  김이 모락모락 나는요청서  |  스트리밍 인제스트 요청 속도  |  개수  |  속도 요청Per초 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  스트리밍가장데이터레이트  |  스트리밍 인제스트 데이터 속도  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  스트리밍가장지속시간  |  스트리밍 인더스트 기간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Kusto/Clusters  |  스트리밍가장결과  |  스트리밍 인제스트 결과  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  ActionLatency  |  작업 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  ActionsCompleted  |  작업 완료됨   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  ActionsFailed  |  작업 실패함   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  ActionsSkipped  |  작업 생략됨   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  ActionsStarted  |  작업 시작됨   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  ActionsSucceeded  |  작업 성공함   |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  ActionSuccessLatency  |  작업 성공 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  ActionThrottledEvents  |  작업 제한 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  통합 서비스환경커넥터메모리사용  |  통합 서비스 환경을 위한 커넥터 메모리 사용량  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  통합 서비스환경커넥터사용  |  통합 서비스 환경을 위한 커넥터 프로세서 사용  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  통합 서비스환경워크워크메모리사용  |  통합 서비스 환경을 위한 워크플로 메모리 사용량  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  통합 서비스환경워크플로우사용  |  통합 서비스 환경을 위한 워크플로 프로세서 사용  |  백분율  |  평균 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  RunFailurePercentage  |  실행 오류 비율  |  백분율  |  합계 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  RunLatency  |  실행 대기 시간  |  초  |  평균 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  RunsCancelled  |  취소된 실행  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  RunsCompleted  |  실행 완료됨  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  RunsFailed  |  실행 실패함  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  RunsStarted  |  실행 시작됨  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  RunsSucceeded  |  실행 성공함  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  런스타트스로틀이벤트  |  시작 제한 이벤트 실행  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  RunSuccessLatency  |  실행 성공 대기 시간  |  초  |  평균 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  RunThrottledEvents  |  실행 제한 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  TriggerFireLatency  |  트리거 실행 대기 시간   |  초  |  평균 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  TriggerLatency  |  트리거 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  TriggersCompleted  |  트리거 완료됨   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  TriggersFailed  |  트리거 실패함   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  TriggersFired  |  트리거 실행됨   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  TriggersSkipped  |  트리거 생략함  |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  TriggersStarted  |  트리거 시작됨   |  개수  |  합계 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  TriggersSucceeded  |  트리거 성공함   |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.로직/통합서비스환경  |  TriggerSuccessLatency  |  트리거 성공 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  마이크로소프트.로직/통합서비스환경  |  TriggerThrottledEvents  |  트리거 제한 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Logic/workflows  |  ActionLatency  |  작업 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  작업 완료됨   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  ActionsFailed  |  작업 실패함   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  작업 생략됨   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  ActionsStarted  |  작업 시작됨   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  작업 성공함   |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  작업 성공 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  작업 제한 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  청구 가능한 작업 실행  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  청구 가능한 트리거 실행  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  기본 작업 실행에 대한 청구 사용량  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  기본 작업 실행에 대한 청구 사용량  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  표준 커넥터 실행에 대한 청구 사용량  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  표준 커넥터 실행에 대한 청구 사용량  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  스토리지 소비 실행에 대한 청구 사용량  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  스토리지 소비 실행에 대한 청구 사용량  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  실행 오류 비율  |  백분율  |  합계 | 
| **예**  | 예 |  Microsoft.Logic/workflows  |  RunLatency  |  실행 대기 시간  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  RunsCancelled  |  취소된 실행  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  RunsCompleted  |  실행 완료됨  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  RunsFailed  |  실행 실패함  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  RunsStarted  |  실행 시작됨  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  실행 성공함  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  런스타트스로틀이벤트  |  시작 제한 이벤트 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  실행 성공 대기 시간  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  실행 제한 이벤트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  총 청구 가능 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  트리거 실행 대기 시간   |  초  |  평균 | 
| **예**  | 예 |  Microsoft.Logic/workflows  |  TriggerLatency  |  트리거 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  트리거 완료됨   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TriggersFailed  |  트리거 실패함   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TriggersFired  |  트리거 실행됨   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  트리거 생략함  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TriggersStarted  |  트리거 시작됨   |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  트리거 성공함   |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  트리거 성공 대기 시간   |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  트리거 제한 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  액티브 코어  |  액티브 코어  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  활성 노드  |  활성 노드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  완료된 실행  |  완료된 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  실패한 실행  |  실패한 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  유휴 코어  |  유휴 코어  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  유휴 노드  |  유휴 노드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  코어를 남기기  |  코어를 남기기  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  노드 를 떠나기  |  노드 를 떠나기  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  모델 배포 실패  |  모델 배포 실패  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  모델 배포 시작  |  모델 배포 시작  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  모델 배포 성공  |  모델 배포 성공  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  모델 레지스터 실패  |  모델 레지스터 실패  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  모델 레지스터 성공  |  모델 레지스터 성공  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  선점코어  |  선점코어  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  선점노드  |  선점노드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  할당량 사용률  |  할당량 사용률  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  시작 된 실행  |  시작 된 실행  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  총 코어  |  총 코어  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  총 노드  |  총 노드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  사용할 수 없는 코어  |  사용할 수 없는 코어  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.MachineLearningServices/workspaces  |  사용할 수 없는 노드  |  사용할 수 없는 노드  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Maps/accounts  |  가용성  |  가용성  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Maps/accounts  |  사용  |  사용  |  개수  |  개수 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  자산 계산  |  자산 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  자산 할당량  |  자산 할당량  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  자산할당량사용비율  |  자산 할당량 사용 비율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  콘텐츠 키정책 카운트  |  콘텐츠 키 정책 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  콘텐츠 키정책 할당량  |  콘텐츠 키 정책 할당량  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  콘텐츠 키정책사용백분율  |  콘텐츠 키 정책 할당량 사용 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  스트리밍정책카운트  |  스트리밍 정책 수  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  스트리밍정책할당량  |  스트리밍 정책 할당량  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스  |  스트리밍정책할당량사용비율  |  스트리밍 정책 할당량 사용 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스/스트리밍엔드포인트  |  송신  |  송신  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스/스트리밍엔드포인트  |  요청  |  요청  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.미디어/미디어 서비스/스트리밍엔드포인트  |  SuccessE2ELatency  |  성공은 종료 시간 끝까지  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  GCPause토탈카운트  |  GC 일시 정지 수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  GCPause토탈타임  |  GC 일시 정지 총 시간  |  밀리초  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  맥스올드젠메모리풀바이트  |  사용 가능한 최대 이전 세대 데이터 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  올드젠메모리풀바이트  |  이전 세대 데이터 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  올드젠프로모션바이트  |  이전 세대 데이터 크기로 승격  |  바이트  |  최대 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  서비스Cpu사용률  |  서비스 CPU 사용률  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  서비스 메모리 커밋됨  |  서비스 메모리 할당  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  서비스 메모리 맥스  |  서비스 메모리 최대  |  바이트  |  최대 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  서비스 메모리 사용  |  사용된 서비스 메모리  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  시스템Cpu사용률  |  시스템 CPU 사용률  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣오류카운트  |  톰캣 전역 오류  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣수신바이트  |  톰캣 총 수신 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣인퀘스트맥스타임  |  톰캣 요청 최대 시간  |  밀리초  |  최대 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣요청토탈카운트  |  톰캣 요청 총 수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣인퀘스트토탈타임  |  톰캣 요청 총 시간  |  밀리초  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣응답에이브그타임  |  톰캣 요청 평균 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰카트센트바이트  |  톰캣 총 전송 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣세션액티브전류카운트  |  톰캣 세션 얼라이브 카운트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣세션액티브맥스카운트  |  톰캣 세션 최대 활성 카운트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣세션얼맥스타임  |  톰캣 세션 최대 살아있는 시간  |  밀리초  |  최대 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣세션생성카운트  |  톰캣 세션 생성 개수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣세션 만료카운트  |  톰캣 세션 만료 개수  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  톰캣세션거부카운트  |  톰캣 세션 거부 카운트  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.마이크로 서비스4스프링 /앱클러스터  |  영젠프로모션바이트  |  젊은 세대 데이터 크기로 승격  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  할당된 사용되는 볼륨 풀  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  볼륨 풀 총 논리적 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.넷앱/넷앱계정/용량풀/볼륨  |  AverageReadLatency  |  평균 읽기 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.넷앱/넷앱계정/용량풀/볼륨  |  AverageWriteLatency  |  평균 쓰기 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.넷앱/넷앱계정/용량풀/볼륨  |  ReadIops  |  읽기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.넷앱/넷앱계정/용량풀/볼륨  |  VolumeLogicalSize  |  볼륨 논리적 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.넷앱/넷앱계정/용량풀/볼륨  |  VolumeSnapshotSize  |  볼륨 스냅샷 크기  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.넷앱/넷앱계정/용량풀/볼륨  |  WriteIops  |  쓰기 IOPS  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  애플리케이션 게이트웨이토탈타임  |  애플리케이션 게이트웨이 총 시간  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  평균 요청카운트퍼헬리호스트  |  건강한 호스트당 분당 요청  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  백엔드커넥트타임  |  백 엔드 연결 시간  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  백엔드첫번째바이트응답시간  |  백엔드 첫 번째 바이트 응답 시간  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  백엔드라스트바이트응답시간  |  백엔드 마지막 바이트 응답 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  백엔드응답 상태  |  백엔드 응답 상태  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  차단된 카운트  |  웹 응용 프로그램 방화벽 차단 된 요청 규칙 배포  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  차단된ReqCount  |  웹 응용 프로그램 방화벽 차단된 요청 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  BytesReceived  |  수신된 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  BytesSent  |  보낸 바이트  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  용량 단위  |  현재 용량 단위  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  클라이언트Rtt  |  클라이언트 RTT  |  밀리초  |  평균 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  계산 단위  |  현재 계산 단위  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  현재 연결  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  FailedRequests  |  실패한 요청  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  정상 호스트 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  일치하는 카운트  |  웹 응용 프로그램 방화벽 총 규칙 배포  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  응답 상태  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Network/applicationGateways  |  처리량  |  처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  Tls프로토콜  |  클라이언트 TLS 프로토콜  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  TotalRequests  |  총 요청 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  비정상 호스트 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/azurefirewalls  |  응용 프로그램규칙 히트  |  응용 프로그램 규칙 적중 횟수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/azurefirewalls  |  데이터 처리  |  처리된 데이터  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Network/azurefirewalls  |  방화벽Health  |  방화벽 상태  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Network/azurefirewalls  |  네트워크룰히트  |  네트워크 규칙 적중 횟수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/azurefirewalls  |  SNAT포트 사용률  |  SNAT 포트 사용률  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/dnszones  |  QueryVolume  |  쿼리 볼륨  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  레코드 집합 용량 사용률  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Network/dnszones  |  RecordSetCount  |  레코드 집합 수  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/expressRouteCircuits  |  Arp가용성  |  Arp 가용성  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Network/expressRouteCircuits  |  Bgp가용성  |  Bgp 가용성  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/expressRouteCircuits  |  글로벌 리치비트인퍼초  |  글로벌 리치비트인퍼초  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/expressRouteCircuits  |  글로벌 리치비트아웃퍼초  |  글로벌 리치비트아웃퍼초  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/expressRouteCircuits  |  코스드롭비트인퍼초  |  드롭인비트퍼초  |  초당 개수  |  평균 | 
| 예  | 예 |  Microsoft.Network/expressRouteCircuits  |  QosDropBits아웃퍼초  |  드롭 아웃 비트퍼초  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  초당 개수  |  평균 | 
| 예  | 예 |  마이크로소프트.네트워크/익스프레스루트게이트웨이  |  에르게이트웨이커넥션비트인퍼초  |  BitsInPerSecond  |  초당 개수  |  평균 | 
| 예  | 예 |  마이크로소프트.네트워크/익스프레스루트게이트웨이  |  어게이트웨이커넥션비트아웃퍼초  |  BitsOutPerSecond  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.네트워크/익스프레스루트포트  |  관리 상태  |  관리 상태  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.네트워크/익스프레스루트포트  |  라인 프로토콜  |  라인 프로토콜  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.네트워크/익스프레스루트포트  |  포트비트인퍼초  |  BitsInPerSecond  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.네트워크/익스프레스루트포트  |  포트비트아웃퍼초  |  BitsOutPerSecond  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.네트워크/익스프레스루트포트  |  Rx라이트 레벨  |  Rx라이트 레벨  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.네트워크/익스프레스루트포트  |  Tx라이트레벨  |  Tx라이트레벨  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  백 엔드 상태 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  백 엔드 요청 수  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  백 엔드 요청 대기 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  청구 가능응답 크기  |  청구 가능한 응답 크기  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  RequestCount  |  요청 수  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  RequestSize  |  요청 크기  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  ResponseSize  |  응답 크기  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  TotalLatency  |  총 대기 시간  |  밀리초  |  평균 | 
| **예**  | **예** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  웹 애플리케이션 방화벽 요청 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  할당된 SNAT 포트(미리 보기)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/loadBalancers  |  ByteCount  |  바이트 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/loadBalancers  |  DipAvailability  |  상태 프로브 상태  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/loadBalancers  |  PacketCount  |  패킷 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  SNAT 연결 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/loadBalancers  |  SYNCount  |  SYN 수  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  사용된 SNAT 포트(미리 보기)  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/loadBalancers  |  VipAvailability  |  데이터 경로 가용성  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  수신된 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  보낸 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  수신된 패킷  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  보낸 패킷  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  평균. 왕복 시간 (ms)  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Network/networkWatchers/connectionMonitors  |  체크실패퍼센트  |  실패한 백분율 검사(미리 보기)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  실패한 프로브 %  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Network/networkWatchers/connectionMonitors  |  왕복 타임  |  왕복 시간(ms) (미리 보기)  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  바이트 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  인바운드 바이트가 삭제된 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  인바운드 바이트가 전달된 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  인바운드 바이트 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  DDoS 완화를 트리거하는 인바운드 SYN 패킷  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  DDoS 완화를 트리거하는 인바운드 TCP 패킷  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  DDoS 완화를 트리거하는 인바운드 UDP 패킷  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  DDoS 공격 진행 여부  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  패킷 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  인바운드 패킷이 삭제된 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  인바운드 패킷이 전달된 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  인바운드 패킷 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  SynCount  |  SYN 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  인바운드 TCP 바이트가 삭제된 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  인바운드 TCP 바이트가 전달된 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  인바운드 TCP 바이트 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  인바운드 TCP 패킷이 삭제된 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  인바운드 TCP 패킷이 전달된 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  인바운드 TCP 패킷 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  인바운드 UDP 바이트가 삭제된 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  인바운드 UDP 바이트가 전달된 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  인바운드 UDP 바이트 DDoS  |  초당 바이트 수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  인바운드 UDP 패킷이 삭제된 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  인바운드 UDP 패킷이 전달된 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  인바운드 UDP 패킷 DDoS  |  초당 개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  데이터 경로 가용성  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  엔드포인트별 엔드포인트 상태  |  개수  |  최대 | 
| **예**  | **예** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  반환된 엔드포인트별 쿼리  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  게이트웨이 S2S 대역폭  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  게이트웨이 P2S 대역폭  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S 연결 수  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  터널 대역폭  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  터널 송신 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  터널 송신 TS 불일치 패킷 삭제  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  터널 송신 패킷  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  터널 수신 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  터널 수신 TS 불일치 패킷 삭제  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  터널 수신 패킷  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworks  |  핑메시 평균 왕복  |  핑을 VM으로 가는 왕복 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Network/virtualNetworks  |  핑메시 프로브실패퍼센트  |  VM에 실패한 Ping  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  들어오는 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  들어오는 모든 실패한 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  들어오는 모든 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  전송된 예약된 푸시 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  취소된 예약된 푸시 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  설치 관리 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  설치 작업 삭제  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  설치 작업 가져오기  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  설치 작업 패치  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  설치 작업 만들기 또는 업데이트  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  모든 나가는 알림 수  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  잘못되거나 만료된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  페이로드 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  외부 알림 시스템 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  성공적인 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  APNS 잘못된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  APNS 만료된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  APNS 권한 부여 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  APNS 잘못된 알림 크기 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  APNS 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  APNS 성공적인 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  GCM 인증 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  GCM 잘못된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  GCM 만료된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  GCM 권한 부여 오류(잘못된 자격 증명)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  GCM 잘못된 알림 형식  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  GCM 잘못된 알림 크기 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  GCM 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  GCM 성공적인 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  GCM 제한된 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  GCM 잘못된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  MPNS 인증 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  MPNS 잘못된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  MPNS 채널 연결 끊김  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  MPNS 삭제된 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  MPNS 잘못된 자격 증명  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  MPNS 잘못된 알림 형식  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  MPNS 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  MPNS 성공적인 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  MPNS 제한된 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  WNS 인증 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  WNS 잘못된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  WNS 채널 연결 끊김  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  WNS 채널 제한  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  WNS 삭제된 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  WNS 만료된 채널 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  WNS 권한 부여 오류(잘못된 자격 증명)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  WNS 잘못된 알림 형식  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  WNS 잘못된 알림 크기 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  WNS 권한 부여 오류(잘못된 토큰)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  WNS 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  WNS 성공적인 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  WNS 제한된 알림  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  WNS 권한 부여 오류(연결할 수 없음)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS 권한 부여 오류(잘못된 토큰)  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  등록 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  등록 만들기 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  등록 삭제 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  등록 읽기 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  등록 업데이트 작업  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  보류 중인 예약된 알림  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  % 사용 가능한 메모리  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  % 사용 가능한 스왑 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  % Committed Bytes In Use  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  % DPC 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  % 사용 가능한 Inodes  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % 사용 가능한 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % 사용 가능한 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  % 유휴 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  % 인터럽트 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  % IO 대기 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  % Nice 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  % 권한이 부여된 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Processor Time  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Processor Time  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  % 사용된 Inodes  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  % 사용된 메모리  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  % 사용된 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  % 사용된 스왑 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  % 사용자 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  Available MBytes  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  사용 가능한 MB 메모리  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  사용 가능한 MB 스왑  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. 디스크 초/읽기  |  평균 디스크 초/읽기  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. 디스크 초/읽기  |  평균 디스크 초/읽기  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. 디스크 초/전송  |  평균 디스크 초/전송  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. 디스크 초/쓰기  |  평균. 디스크 초/쓰기  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. 디스크 초/쓰기  |  평균. 디스크 초/쓰기  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Bytes Received/sec  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Bytes Sent/sec  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Bytes Total/sec  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Current Disk Queue Length  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  디스크 읽기 바이트/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  디스크 읽기/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  디스크 읽기/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  디스크 전송/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  디스크 전송/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |  디스크 쓰기 바이트/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  디스크 쓰기/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  디스크 쓰기/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  사용 가능한 메가바이트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  사용 가능한 메가바이트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  사용 가능한 실제 메모리  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  페이징 파일에 사용 가능한 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  사용 가능한 가상 메모리  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  논리 디스크 바이트/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  페이지 읽기/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  페이지 쓰기/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  페이지/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Pct 권한이 부여된 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Pct 사용자 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  물리적 디스크 바이트/초  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  프로세스  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  프로세서 큐 길이  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  페이징 파일에 저장된 크기  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  총 바이트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  받은 총 바이트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  전송된 총 바이트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  총 충돌  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  받은 총 패킷  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  전송된 총 패킷  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  총 Rx 오류  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  총 Tx 오류  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  작동 시간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  사용된 MB 스왑 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  사용된 메모리 KB  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  사용된 메모리 MB  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  사용자  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  가상 공유 메모리  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  행사  |  행사  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.OperationalInsights/workspaces  |  Heartbeat  |  Heartbeat  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.OperationalInsights/workspaces  |  업데이트  |  업데이트  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  메모리  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  메모리 스래싱(데이터 집합)  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  QPU 높은 사용률  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  쿼리 기간(데이터 집합)  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  쿼리 풀 작업 큐 길이(데이터 집합)  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Search/searchServices  |  SearchLatency  |  검색 대기 시간  |  초  |  평균 | 
| **예**  | 예 |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  초당 검색 쿼리 수  |  초당 개수  |  평균 | 
| **예**  | 예 |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  제한된 검색 쿼리 백분율  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  큐/토픽에 있는 활성 메시지 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  끊어진 연결.  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  열린 연결.  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU(더 이상 사용되지 않습니다)  |  백분율  |  최대 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  데드레터메시지  |  큐/토픽에서 배달 못한 문자메시지 수입니다.  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  들어오는 메시지  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  들어오는 요청  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  메시지  |  큐/토픽에 있는 메시지 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  네임스페이스Cpu사용법  |  CPU  |  백분율  |  최대 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  네임스페이스메모리사용  |  메모리 사용량  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  보내는 메시지  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  예약된 메시지  |  큐/토픽에서 예약된 메시지 수입니다.  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  서버 오류.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  크기  |  크기  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  성공한 요청  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  제한된 요청.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  사용자 오류.  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  메모리 사용량(더 이상 사용되지 않습니다)  |  백분율  |  최대 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  실제 Cpu  |  실제 Cpu  |  개수  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  실제 메모리  |  실제 메모리  |  바이트  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  할당된 Cpu  |  할당된 Cpu  |  개수  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  할당된 메모리  |  할당된 메모리  |  바이트  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  응용 프로그램 상태  |  응용 프로그램 상태  |  개수  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  컨테이너 상태  |  컨테이너 상태  |  개수  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  CPU 사용률  |  CPU 사용률  |  백분율  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  메모리 사용률  |  메모리 사용률  |  백분율  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  다시 시작 카운트  |  다시 시작 카운트  |  개수  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  서비스 복제 상태  |  서비스 복제 상태  |  개수  |  평균 | 
| 예  | 예 |  마이크로소프트.서비스패브릭 메쉬/응용 프로그램  |  ServiceStatus  |  ServiceStatus  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  연결 수  |  개수  |  최대 | 
| **예**  | **예** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  인바운드 트래픽  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  메시지 수  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  아웃바운드 트래픽  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  시스템 오류  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  사용자 오류  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  평균 CPU 비율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  읽은 IO 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  기록된 IO 바이트  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/managedInstances  |  io_requests  |  IO 요청 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  예약된 스토리지 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  사용된 스토리지 공간  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  가상 코어 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  DTU 비율  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers  |  database_storage_used  |  사용된 데이터 공간  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  DTU 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers  |  dtu_used  |  DTU 사용됨  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers  |  storage_used  |  사용된 데이터 공간  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  할당된 데이터 공간  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  앱 CPU 청구  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  앱 CPU 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  앱 메모리 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  방화벽에 의해 차단  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  캐시 적중 비율  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  캐시 사용 비율  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  connection_failed  |  실패한 연결  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  connection_successful  |  성공적인 연결  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  cpu_limit  |  CPU 한도  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  CPU 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  cpu_used  |  사용되는 CPU  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  교착 상태  |  교착 상태  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU 비율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU 제한  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU 사용됨  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU 백분율  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  dwu_limit  |  DWU 제한  |  개수  |  최대 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU 사용됨  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  로컬 tempdb 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  로그 IO 비율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  메모리 백분율  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  데이터 IO 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  세션 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  스토리지  |  사용된 데이터 공간  |  바이트  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  storage_percent  |  데이터 공간 사용 %  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Tempdb 데이터 파일 크기 킬로바이트  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Tempdb 로그 파일 크기 킬로바이트  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Tempdb 퍼센트 로그 사용  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  workers_percent  |  작업자 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  메모리 내 OLTP 스토리지 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  할당된 데이터 공간  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  데이터 공간 할당 %  |  백분율  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  CPU 한도  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  CPU 비율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  사용되는 CPU  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  할당된 데이터 공간  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  CPU 한도  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  CPU 비율  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  사용되는 CPU  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU 비율  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU 사용  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  로그 IO 비율  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  데이터 IO 비율  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  세션 백분율  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  사용된 데이터 공간  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  작업자 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  DTU 비율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU 제한  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU 사용  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  로그 IO 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  데이터 IO 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  세션 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  데이터 최대 크기  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  데이터 공간 사용 %  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  사용된 데이터 공간  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Tempdb 데이터 파일 크기 킬로바이트  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Tempdb 로그 파일 크기 킬로바이트  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Tempdb 퍼센트 로그 사용  |  백분율  |  최대 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  작업자 백분율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  메모리 내 OLTP 스토리지 백분율  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts  |  송신  |  송신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  사용된 용량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  가용성  |  가용성  |  백분율  |  평균 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Blob 용량  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Blob 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Blob 컨테이너 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  송신  |  송신  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  인덱스 용량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/blobServices  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  송신  |  송신  |  바이트  |  합계 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  파일 용량  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  파일 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  파일 공유 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  파일 공유 할당량  |  파일 공유 할당량 크기  |  바이트  |  평균 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  파일 공유스냅샷카운트  |  파일 공유 스냅샷 수  |  개수  |  평균 | 
| 예  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  파일 공유스냅샷 크기  |  파일 공유 스냅샷 크기  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/fileServices  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  송신  |  송신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  큐 용량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  큐 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  큐 메시지 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/queueServices  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  가용성  |  가용성  |  백분율  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  송신  |  송신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  수신  |  수신  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  성공 E2E 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  성공 서버 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  테이블 용량  |  바이트  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  테이블 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  테이블 엔터티 수  |  개수  |  평균 | 
| **예**  | 예 |  Microsoft.Storage/storageAccounts/tableServices  |  트랜잭션  |  트랜잭션  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트IOPS  |  총 클라이언트 IOPS  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트 지연  |  평균 클라이언트 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트록IOPS  |  클라이언트 잠금 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트 메타데이터ReadIOPS  |  클라이언트 메타데이터 읽기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트 메타데이터쓰기IOPS  |  클라이언트 메타데이터 쓰기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트ReadIOPS  |  클라이언트 읽기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트읽기Throughput  |  평균 캐시 읽기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트 쓰기IOPS  |  클라이언트 쓰기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  클라이언트쓰기스루풋  |  평균 캐시 쓰기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상Async쓰기처리가능풋  |  스토리지 대상 비동기 쓰기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상필스루풋  |  스토리지대상 채우기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상Health  |  스토리지 대상 상태  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상IOPS  |  총 스토리지대상 IOPS  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상 지연  |  스토리지대상 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상메타메타디시드읽기IOPS  |  스토리지 대상 메타데이터 읽기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상메타데이터쓰기IOPS  |  스토리지 대상 메타데이터 쓰기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상읽기전방처리장치  |  스토리지 대상 읽기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상읽기아이오PS  |  스토리지 대상 읽기 IOPS  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지타겟싱크쓰기스루풋  |  스토리지 대상 동기 쓰기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상토탈판독풋  |  스토리지대상 총 읽기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상토탈쓰기처리부  |  스토리지대상 총 쓰기 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  스토리지대상쓰기IOPS  |  스토리지 대상 쓰기 IOPS  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지캐시/캐시  |  작동 시간  |  작동 시간  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  서버싱크세션결과  |  세션 결과 동기화  |  개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크배치전송파일바이트  |  동기화되는 바이트 수  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크리콜네트워크바이트응용프로그램  |  애플리케이션별 클라우드 계층화 리콜 크기  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크리콜토탈네트워크바이트  |  클라우드 계층화 리콜 크기  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크리콜IO토탈사이즈바이트  |  클라우드 계층화 회수  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크리콜투입바이스초  |  클라우드 계층화 리콜 처리량  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크서버하트비트  |  서버 온라인 상태  |  개수  |  최대 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크싱크세션응용파일카운트  |  동기화된 파일  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.스토리지싱크/스토리지싱크서비스  |  스토리지싱크세션퍼아이템오류카운트  |  동기화 상태가 아닌 파일  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.storagesync/스토리지싱크서비스/등록된서버  |  서버하트비트  |  서버 온라인 상태  |  개수  |  최대 | 
| **예**  | 예 |  마이크로 소프트.storagesync/스토리지싱크서비스/등록된서버  |  서버리콜IO토탈사이즈바이트  |  클라우드 계층화 회수  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로 소프트.스토리지싱크/스토리지싱크서비스/싱크그룹  |  동기화 그룹배치 전송된 파일 바이트  |  동기화되는 바이트 수  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로 소프트.스토리지싱크/스토리지싱크서비스/싱크그룹  |  동기화 그룹동기화세션응용파일카운트  |  동기화된 파일  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.스토리지싱크/스토리지싱크서비스/싱크그룹  |  동기화 그룹싱크세션퍼항목오류카운트  |  동기화 상태가 아닌 파일  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.storagesync/스토리지싱크서비스/동기화그룹/서버엔드포인트  |  서버엔드포인트배치전송파일바이트  |  동기화되는 바이트 수  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로 소프트.storagesync/스토리지싱크서비스/동기화그룹/서버엔드포인트  |  서버엔드포인트싱크세션응용파일카운트  |  동기화된 파일  |  개수  |  합계 | 
| **예**  | 예 |  마이크로 소프트.storagesync/스토리지싱크서비스/동기화그룹/서버엔드포인트  |  서버엔드포인트싱크세션퍼아이템오류카운트  |  동기화 상태가 아닌 파일  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  실패한 기능 요청  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  함수 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  기능 요청  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  데이터 변환 오류  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  입력 역직렬화 오류  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  잘못된 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  초기 입력 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  오류  |  런타임 오류  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  입력 이벤트 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  입력 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  입력이벤트소스백로그  |  백로그된 입력 이벤트  |  개수  |  최대 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  입력이벤트소스퍼초  |  수신된 입력 원본  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  늦은 입력 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  출력 이벤트  |  개수  |  합계 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  워터마크 지연  |  초  |  최대 | 
| **예**  | 예 |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  SU % 사용률  |  백분율  |  최대 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 읽기 바이트  |  디스크 읽기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 읽기 작업/초  |  디스크 읽기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 쓰기 바이트  |  디스크 쓰기 바이트  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 쓰기 작업/초  |  디스크 쓰기 작업/초  |  초당 개수  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크판독바이스퍼초  |  디스크 읽기 바이트/초  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 판독지연  |  디스크 읽기 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 읽기연수  |  디스크 읽기 작업  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 쓰기바이트퍼초  |  디스크 쓰기 바이트/초  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 작성지연  |  디스크 쓰기 대기 시간  |  밀리초  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  디스크 쓰기작업  |  디스크 쓰기 작업  |  개수  |  합계 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  메모리 활성  |  메모리 활성  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  메모리 부여  |  메모리 부여  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  사용 된 메모리  |  사용된 메모리  |  바이트  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  네트워크 인  |  네트워크 인  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  네트워크 아웃  |  네트워크 아웃  |  바이트  |  합계 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  네트워크인바이트퍼초  |  초당 네트워크/초  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  네트워크아웃바이트퍼초  |  네트워크 아웃 바이트/초  |  초당 바이트 수  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  백분율 CPU  |  백분율 CPU  |  백분율  |  평균 | 
| **예**  | 예 |  마이크로소프트.VM웨어클라우드심플/가상머신  |  백분율CpuReady  |  백분율 CPU 준비  |  밀리초  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  활성 요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  평균 응답 시간  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  데이터 입력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  데이터 출력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  CPU 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  디스크 큐 길이  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Http 서버 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Http 큐 길이  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  대형 App Service 계획 작업자  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  중형 App Service 계획 작업자  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  메모리 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  요청  |  요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  소형 App Service 계획 작업자  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  총 프런트 엔드  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  CPU 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  메모리 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  사용 가능한 작업자  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  총 작업자  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  사용된 작업자  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  BytesReceived  |  데이터 입력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  BytesSent  |  데이터 출력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  CPU 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  디스크 큐 길이  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Http 큐 길이  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  메모리 비율  |  백분율  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  TCP 닫기 대기  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpClosing  |  TCP 닫기  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  Tcp 설립  |  TCP 설립  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP 핀 대기 1  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP 핀 대기 2  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP 라스트 아크  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP 신 수신  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP 신 전송  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  TCP 시간 대기  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  AppConnections  |  Connections  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  평균 메모리 작업 집합  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  AverageResponseTime  |  평균 응답 시간  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  BytesReceived  |  데이터 입력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  BytesSent  |  데이터 출력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  CpuTime  |  CPU 시간  |  초  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  CurrentAssemblies  |  현재 어셈블리  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  함수 실행 횟수  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  함수 실행 단위  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Gen0Collections  |  Gen 0 가비지 수집  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Gen1Collections  |  Gen 1 가비지 수집  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Gen2Collections  |  Gen 2 가비지 수집  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  핸들  |  핸들 개수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  상태 확인 상태  |  상태 확인 상태  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http5xx  |  Http 서버 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  Http응답 시간  |  응답 시간  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  초당 IO 기타 바이트 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  초당 IO 기타 작업 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  초당 IO 읽기 바이트 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  초당 IO 읽기 작업 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  초당 IO 쓰기 바이트 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  초당 IO 쓰기 작업 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  메모리 작업 집합  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  PrivateBytes  |  프라이빗 바이트  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  요청  |  요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  애플리케이션 큐의 요청 수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  스레드  |  스레드 개수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  TotalAppDomains  |  총 앱 도메인  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  언로드된 총 앱 도메인  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  AppConnections  |  Connections  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  평균 메모리 작업 집합  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  평균 응답 시간  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  BytesReceived  |  데이터 입력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  BytesSent  |  데이터 출력  |  바이트  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  CpuTime  |  CPU 시간  |  초  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  현재 어셈블리  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  함수 실행 횟수  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  함수 실행 단위  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Gen 0 가비지 수집  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Gen 1 가비지 수집  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Gen 2 가비지 수집  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  핸들  |  핸들 개수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  상태 확인 상태  |  상태 확인 상태  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http5xx  |  Http 서버 오류  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  Http응답 시간  |  응답 시간  |  초  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  초당 IO 기타 바이트 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  초당 IO 기타 작업 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  초당 IO 읽기 바이트 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  초당 IO 읽기 작업 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  초당 IO 쓰기 바이트 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  초당 IO 쓰기 작업 수  |  초당 바이트 수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  메모리 작업 집합  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  프라이빗 바이트  |  바이트  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  요청  |  요청  |  개수  |  합계 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  애플리케이션 큐의 요청 수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  스레드  |  스레드 개수  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  총 앱 도메인  |  개수  |  평균 | 
| **예**  | **예** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  언로드된 총 앱 도메인  |  개수  |  평균 | 
