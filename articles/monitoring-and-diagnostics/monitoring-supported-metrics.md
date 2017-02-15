---
title: "Azure Monitor 메트릭 - 리소스 유형별 지원 메트릭 | Microsoft Docs"
description: "Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: e64b544d8785b7453f4d77333fd0eb6673d32504
ms.openlocfilehash: f5a6ee866891fdfb27ed00a22cbd784484ae5e5f


---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor에서 지원되는 메트릭
Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 다음은 현재 Azure Monitor의 메트릭 파이프라인을 통해 사용할 수 있는 모든 메트릭의 전체 목록입니다.

> [!NOTE]
> 레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다. 이 목록에는 통합 Azure Monitor 메트릭 파이프라인의 공개 미리 보기를 통해 사용할 수 있는 공개 미리보기 메트릭만 포함됩니다.
> 
> 

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|qpu_metric|QPU|개수|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|
|memory_metric|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|CoreCount|코어 수|개수|합계|배치 계정의 총 코어 수|
|TotalNodeCount|노드 수|개수|합계|배치 계정의 총 노드 수|
|CreatingNodeCount|노드 수 만들기|개수|합계|만든 노드 수|
|StartingNodeCount|시작 노드 수|개수|합계|시작 노드 수|
|WaitingForStartTaskNodeCount|작업 시작 대기 노드 수|개수|합계|시작 작업 완료를 기다리는 노드 수|
|StartTaskFailedNodeCount|시작 작업 실패 노드 수|개수|합계|시작 작업이 실패한 노드 수|
|IdleNodeCount|유휴 상태인 노드 수|개수|합계|유휴 노드 수|
|OfflineNodeCount|오프라인 노드 수|개수|합계|오프라인 노드 수|
|RebootingNodeCount|재부팅 노드 수|개수|합계|다시 부팅하는 노드의 수|
|ReimagingNodeCount|이미지로 다시 설치하는 노드 수|개수|합계|이미지로 다시 설치하는 노드의 수|
|RunningNodeCount|실행 노드 수|개수|합계|실행 중인 노드의 수|
|LeavingPoolNodeCount|나가는 풀 노드 수|개수|합계|풀을 나가는 노드 수|
|UnusableNodeCount|사용 불가 노드 수|개수|합계|사용할 수 없는 노드 수|
|TaskStartEvent|작업 시작 이벤트|개수|합계|시작된 총 작업 수|
|TaskCompleteEvent|작업 완료 이벤트|개수|합계|완료된 총 작업 수|
|TaskFailEvent|작업 실패 이벤트|개수|합계|실패한 상태로 완료된 총 작업 수|
|PoolCreateEvent|풀 만들기 이벤트|개수|합계|만든 총 풀 수|
|PoolResizeStartEvent|풀 크기 조정 시작 이벤트|개수|합계|시작된 총 풀 크기 조정 작업 수|
|PoolResizeCompleteEvent|풀 크기 조정 완료 이벤트|개수|합계|완료된 총 풀 크기 조정 수|
|PoolDeleteStartEvent|풀 삭제 시작 이벤트|개수|합계|시작된 총 풀 삭제 수|
|PoolDeleteCompleteEvent|풀 삭제 완료 이벤트|개수|합계|완료된 총 풀 삭제 수|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|connectedclients|연결된 클라이언트|개수|최대||
|totalcommandsprocessed|총 작업|개수|합계||
|cachehits|캐시 적중|개수|합계||
|cachemisses|캐시 누락|개수|합계||
|getcommands|가져오기|개수|합계||
|setcommands|설정|개수|합계||
|evictedkeys|제거된 키|개수|합계||
|totalkeys|전체 키|개수|최대||
|expiredkeys|만료된 키|개수|합계||
|usedmemory|사용된 메모리|바이트|최대||
|usedmemoryRss|사용된 메모리 RSS|바이트|최대||
|serverLoad|서버 부하|백분율|최대||
|cacheWrite|캐시 쓰기|초당 바이트 수|최대||
|cacheRead|캐시 읽기|초당 바이트 수|최대||
|percentProcessorTime|CPU|백분율|최대||
|connectedclients0|연결된 클라이언트(분할 0)|개수|최대||
|totalcommandsprocessed0|총 작업(분할 0)|개수|합계||
|cachehits0|캐시 적중(분할 0)|개수|합계||
|cachemisses0|캐시 누락(분할 0)|개수|합계||
|getcommands0|가져오기(분할 0)|개수|합계||
|setcommands0|설정(분할 0)|개수|합계||
|evictedkeys0|제거된 키(분할 0)|개수|합계||
|totalkeys0|총 키(분할 0)|개수|최대||
|expiredkeys0|만료된 키(분할 0)|개수|합계||
|usedmemory0|사용된 메모리(분할 0)|바이트|최대||
|usedmemoryRss0|사용된 메모리 RSS(분할 0)|바이트|최대||
|serverLoad0|서버 부하(분할 0)|백분율|최대||
|cacheWrite0|캐시 쓰기(분할 0)|초당 바이트 수|최대||
|cacheRead0|캐시 읽기(분할 0)|초당 바이트 수|최대||
|percentProcessorTime0|CPU(분할 0)|백분율|최대||
|connectedclients1|연결된 클라이언트(분할 1)|개수|최대||
|totalcommandsprocessed1|총 작업(분할 1)|개수|합계||
|cachehits1|캐시 적중(분할 1)|개수|합계||
|cachemisses1|캐시 누락(분할 1)|개수|합계||
|getcommands1|가져오기(분할 1)|개수|합계||
|setcommands1|집합(분할 1)|개수|합계||
|evictedkeys1|제거된 키(분할 1)|개수|합계||
|totalkeys1|총 키(분할 1)|개수|최대||
|expiredkeys1|만료된 키(분할 1)|개수|합계||
|usedmemory1|사용된 메모리(분할 1)|바이트|최대||
|usedmemoryRss1|사용된 메모리 RSS(분할 1)|바이트|최대||
|serverLoad1|서버 부하(분할 1)|백분율|최대||
|cacheWrite1|캐시 쓰기(분할 1)|초당 바이트 수|최대||
|cacheRead1|캐시 읽기(분할 1)|초당 바이트 수|최대||
|percentProcessorTime1|CPU(분할 1)|백분율|최대||
|connectedclients2|연결된 클라이언트(분할 2)|개수|최대||
|totalcommandsprocessed2|총 작업(분할 2)|개수|합계||
|cachehits2|캐시 적중(분할 2)|개수|합계||
|cachemisses2|캐시 누락(분할 2)|개수|합계||
|getcommands2|가져오기(분할 2)|개수|합계||
|setcommands2|설정(분할 2)|개수|합계||
|evictedkeys2|제거된 키(분할 2)|개수|합계||
|totalkeys2|총 키(분할 2)|개수|최대||
|expiredkeys2|만료된 키(분할 2)|개수|합계||
|usedmemory2|사용된 메모리(분할 2)|바이트|최대||
|usedmemoryRss2|사용된 메모리 RSS(분할 2)|바이트|최대||
|serverLoad2|서버 부하(분할 2)|백분율|최대||
|cacheWrite2|캐시 쓰기(분할 2)|초당 바이트 수|최대||
|cacheRead2|캐시 읽기(분할 2)|초당 바이트 수|최대||
|percentProcessorTime2|CPU(분할 2)|백분율|최대||
|connectedclients3|연결된 클라이언트(분할 3)|개수|최대||
|totalcommandsprocessed3|총 작업(분할 3)|개수|합계||
|cachehits3|캐시 적중(분할 3)|개수|합계||
|cachemisses3|캐시 누락(분할 3)|개수|합계||
|getcommands3|가져오기(분할 3)|개수|합계||
|setcommands3|설정(분할 3)|개수|합계||
|evictedkeys3|제거된 키(분할 3)|개수|합계||
|totalkeys3|총 키(분할 3)|개수|최대||
|expiredkeys3|만료된 키(분할 3)|개수|합계||
|usedmemory3|사용된 메모리(분할 3)|바이트|최대||
|usedmemoryRss3|사용된 메모리 RSS(분할 3)|바이트|최대||
|serverLoad3|서버 부하(분할 3)|백분율|최대||
|cacheWrite3|캐시 쓰기(분할 3)|초당 바이트 수|최대||
|cacheRead3|캐시 읽기(분할 3)|초당 바이트 수|최대||
|percentProcessorTime3|CPU(분할 3)|백분율|최대||
|connectedclients4|연결된 클라이언트(분할 4)|개수|최대||
|totalcommandsprocessed4|총 작업(분할 4)|개수|합계||
|cachehits4|캐시 적중(분할 4)|개수|합계||
|cachemisses4|캐시 누락(분할 4)|개수|합계||
|getcommands4|가져오기(분할 4)|개수|합계||
|setcommands4|설정(분할 4)|개수|합계||
|evictedkeys4|제거된 키(분할 4)|개수|합계||
|totalkeys4|총 키(분할 4)|개수|최대||
|expiredkeys4|만료된 키(분할 4)|개수|합계||
|usedmemory4|사용된 메모리(분할 4)|바이트|최대||
|usedmemoryRss4|사용된 메모리 RSS(분할 4)|바이트|최대||
|serverLoad4|서버 부하(분할 4)|백분율|최대||
|cacheWrite4|캐시 쓰기(분할 4)|초당 바이트 수|최대||
|cacheRead4|캐시 읽기(분할 4)|초당 바이트 수|최대||
|percentProcessorTime4|CPU(분할 4)|백분율|최대||
|connectedclients5|연결된 클라이언트(분할 5)|개수|최대||
|totalcommandsprocessed5|총 작업(분할 5)|개수|합계||
|cachehits5|캐시 적중(분할 5)|개수|합계||
|cachemisses5|캐시 누락(분할 5)|개수|합계||
|getcommands5|가져오기(분할 5)|개수|합계||
|setcommands5|설정(분할 5)|개수|합계||
|evictedkeys5|제거된 키(분할 5)|개수|합계||
|totalkeys5|총 키(분할 5)|개수|최대||
|expiredkeys5|만료된 키(분할 5)|개수|합계||
|usedmemory5|사용된 메모리(분할 5)|바이트|최대||
|usedmemoryRss5|사용된 메모리 RSS(분할 5)|바이트|최대||
|serverLoad5|서버 부하(분할 5)|백분율|최대||
|cacheWrite5|캐시 쓰기(분할 5)|초당 바이트 수|최대||
|cacheRead5|캐시 읽기(분할 5)|초당 바이트 수|최대||
|percentProcessorTime5|CPU(분할 5)|백분율|최대||
|connectedclients6|연결된 클라이언트(분할 6)|개수|최대||
|totalcommandsprocessed6|총 작업(분할 6)|개수|합계||
|cachehits6|캐시 적중(분할 6)|개수|합계||
|cachemisses6|캐시 누락(분할 6)|개수|합계||
|getcommands6|가져오기(분할 6)|개수|합계||
|setcommands6|설정(분할 6)|개수|합계||
|evictedkeys6|제거된 키(분할 6)|개수|합계||
|totalkeys6|총 키(분할 6)|개수|최대||
|expiredkeys6|만료된 키(분할 6)|개수|합계||
|usedmemory6|사용된 메모리(분할 6)|바이트|최대||
|usedmemoryRss6|사용된 메모리 RSS(분할 6)|바이트|최대||
|serverLoad6|서버 부하(분할 6)|백분율|최대||
|cacheWrite6|캐시 쓰기(분할 6)|초당 바이트 수|최대||
|cacheRead6|캐시 읽기(분할 6)|초당 바이트 수|최대||
|percentProcessorTime6|CPU(분할 6)|백분율|최대||
|connectedclients7|연결된 클라이언트(분할 7)|개수|최대||
|totalcommandsprocessed7|총 작업(분할 7)|개수|합계||
|cachehits7|캐시 적중(분할 7)|개수|합계||
|cachemisses7|캐시 누락(분할 7)|개수|합계||
|getcommands7|가져오기(분할 7)|개수|합계||
|setcommands7|설정(분할 7)|개수|합계||
|evictedkeys7|제거된 키(분할 7)|개수|합계||
|totalkeys7|총 키(분할 7)|개수|최대||
|expiredkeys7|만료된 키(분할 7)|개수|합계||
|usedmemory7|사용된 메모리(분할 7)|바이트|최대||
|usedmemoryRss7|사용된 메모리 RSS(분할 7)|바이트|최대||
|serverLoad7|서버 부하(분할 7)|백분율|최대||
|cacheWrite7|캐시 쓰기(분할 7)|초당 바이트 수|최대||
|cacheRead7|캐시 읽기(분할 7)|초당 바이트 수|최대||
|percentProcessorTime7|CPU(분할 7)|백분율|최대||
|connectedclients8|연결된 클라이언트(분할 8)|개수|최대||
|totalcommandsprocessed8|총 작업(분할 8)|개수|합계||
|cachehits8|캐시 적중(분할 8)|개수|합계||
|cachemisses8|캐시 누락(분할 8)|개수|합계||
|getcommands8|가져오기(분할 8)|개수|합계||
|setcommands8|설정(분할 8)|개수|합계||
|evictedkeys8|제거된 키(분할 8)|개수|합계||
|totalkeys8|총 키(분할 8)|개수|최대||
|expiredkeys8|만료된 키(분할 8)|개수|합계||
|usedmemory8|사용된 메모리(분할 8)|바이트|최대||
|usedmemoryRss8|사용된 메모리 RSS(분할 8)|바이트|최대||
|serverLoad8|서버 부하(분할 8)|백분율|최대||
|cacheWrite8|캐시 쓰기(분할 8)|초당 바이트 수|최대||
|cacheRead8|캐시 읽기(분할 8)|초당 바이트 수|최대||
|percentProcessorTime8|CPU(분할 8)|백분율|최대||
|connectedclients9|연결된 클라이언트(분할 9)|개수|최대||
|totalcommandsprocessed9|총 작업(분할 9)|개수|합계||
|cachehits9|캐시 적중(분할 9)|개수|합계||
|cachemisses9|캐시 누락(분할 9)|개수|합계||
|getcommands9|가져오기(분할 9)|개수|합계||
|setcommands9|설정(분할 9)|개수|합계||
|evictedkeys9|제거된 키(분할 9)|개수|합계||
|totalkeys9|총 키(분할 9)|개수|최대||
|expiredkeys9|만료된 키(분할 9)|개수|합계||
|usedmemory9|사용된 메모리(분할 9)|바이트|최대||
|usedmemoryRss9|사용된 메모리 RSS(분할 9)|바이트|최대||
|serverLoad9|서버 부하(분할 9)|백분율|최대||
|cacheWrite9|캐시 쓰기(분할 9)|초당 바이트 수|최대||
|cacheRead9|캐시 읽기(분할 9)|초당 바이트 수|최대||
|percentProcessorTime9|CPU(분할 9)|백분율|최대||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|TotalCalls|총 호출|개수|합계|총 호출 수.|
|SuccessfulCalls|성공한 호출|개수|합계|성공한 호출 수입니다.|
|TotalErrors|총 오류|개수|합계|오류 응답(HTTP 응답 코드 4xx 또는 5xx)이 있는 총 호출 수입니다.|
|BlockedCalls|차단된 호출|개수|합계|요금 또는 할당량 한도를 초과한 호출 수입니다.|
|ServerErrors|서버 오류|개수|합계|서비스 내부 오류(HTTP 응답 코드 5xx)가 있는 호출 수입니다.|
|ClientErrors|클라이언트 오류|개수|합계|클라이언트 쪽 오류(HTTP 응답 코드 4xx)가 있는 호출 수입니다.|
|DataIn|데이터 입력|바이트|합계|들어오는 데이터 크기(바이트)입니다.|
|DataOut|데이터 출력|바이트|합계|나가는 데이터 크기(바이트)입니다.|
|대기 시간|대기 시간|밀리초|평균|대기 시간(밀리초)입니다.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 가상 컴퓨터에서 사용 중인 할당된 계산 단위의 백분율|
|네트워크 인|네트워크 인|바이트|합계|가상 컴퓨터가 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|
|네트워크 아웃|네트워크 아웃|바이트|합계|가상 컴퓨터가 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 총 바이트|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 총 바이트|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 가상 컴퓨터에서 사용 중인 할당된 계산 단위의 백분율|
|네트워크 인|네트워크 인|바이트|합계|가상 컴퓨터가 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|
|네트워크 아웃|네트워크 아웃|바이트|합계|가상 컴퓨터가 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 총 바이트|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 총 바이트|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 가상 컴퓨터에서 사용 중인 할당된 계산 단위의 백분율|
|네트워크 인|네트워크 인|바이트|합계|가상 컴퓨터가 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|
|네트워크 아웃|네트워크 아웃|바이트|합계|가상 컴퓨터가 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 총 바이트|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 총 바이트|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs
| 메트릭 | 매트릭 표시 이름 | 단위 | 집계 형식 | 설명 |
| --- | --- | --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |원격 분석 메시지 보내기 시도 |개수 |합계 |IoT Hub로 보내려 한 장치-클라우드 원격 분석 메시지 수 |
| d2c.telemetry.ingress.success |보낸 원격 분석 메시지 |개수 |합계 |IoT Hub로 보내기 성공한 장치-클라우드 원격 분석 메시지 수 |
| d2c.telemetry.egress.success | 배달된 원격 분석 메시지 | 개수 | 합계 | 끝점에 대한 쓰기가 모두 성공한 수 |
| d2c.telemetry.egress.invalid | 잘못된 원격 분석 메시지 배달 시도 | 개수 | 합계 | 끝점과 호환되지 않아서 배달되지 않은 메시지 수 |
| d2c.telemetry.egress.dropped | 삭제된 원격 분석 메시지 | 개수 | 합계 | 끝점이 비정상적이어서 삭제된 메시지 수 |
| d2c.telemetry.egress.fallback | 대체 조건과 일치하는 원격 분석 메시지 | 개수 | 합계 | 대체 경로와 일치하는 메시지 수 |
| d2c.telemetry.egress.orphaned | 분리된 원격 분석 메시지 | 개수 | 합계 | 대체 경로를 포함하여 경로가 일치하지 않는 메시지 수 |
| d2c.endpoints.latency.eventHubs | 이벤트 허브 끝점에 대한 메시지 대기 시간 | 밀리초 | 평균 | IoT Hub에 대한 메시지 수신과 이벤트 허브 끝점에 대한 메시지 수신 간의 평균, 최소 및 최대 대기 시간(밀리초) |
| d2c.endpoints.latency.serviceBusQueues | Service Bus 큐 끝점에 대한 메시지 대기 시간 | 밀리초 | 평균 | IoT Hub에 대한 메시지 수신과 Service Bus 큐 끝점에 대한 메시지 수신 간의 평균, 최소 및 최대 대기 시간(밀리초) |
| d2c.endpoints.latency.serviceBusTopics | Service Bus 항목 끝점에 대한 메시지 대기 시간 | 밀리초 | 평균 | IoT Hub에 대한 메시지 수신과 Service Bus 항목 끝점에 대한 메시지 수신 간의 평균, 최소 및 최대 대기 시간(밀리초) |
| c2d.commands.egress.complete.success |완료된 명령 |개수 |합계 |장치에서 성공적으로 완료한 클라우드-장치 명령 수 |
| c2d.commands.egress.abandon.success |명령 중단됨 |개수 |합계 |장치에서 중단한 클라우드-장치 명령 수 |
| c2d.commands.egress.reject.success |명령 거부됨 |개수 |합계 |장치에서 거부한 클라우드-장치 명령 수 |
| devices.totalDevices |총 장치 |개수 |합계 |IoT 허브에 등록된 장치 수 |
| devices.connectedDevices.allProtocol |연결된 장치 |개수 |합계 |IoT 허브에 연결된 장치 수 |

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|INREQS|들어오는 요청|개수|합계|Event Hub 네임스페이스에 대한 들어오는 메시지 처리량|
|SUCCREQ|성공한 요청|개수|합계|네임스페이스에 대한 총 성공한 요청|
|FAILREQ|실패한 요청|개수|합계|네임스페이스에 대한 총 실패한 요청|
|SVRBSY|서버 작업 중 오류|개수|합계|네임스페이스에 대한 총 서버 작업 중 오류|
|INTERR|내부 서버 오류|개수|합계|네임스페이스에 대한 총 내부 서버 오류|
|MISCERR|다른 오류|개수|합계|네임스페이스에 대한 총 실패한 요청|
|INMSGS|들어오는 메시지 |개수|합계|네임스페이스에 대한 총 들어오는 메시지|
|OUTMSGS|보내는 메시지 |개수|합계|네임스페이스에 대한 총 나가는 메시지|
|EHINMBS|초당 들어오는 바이트|초당 바이트 수|합계|Event Hub 네임스페이스에 대한 들어오는 메시지 처리량|
|EHOUTMBS|초당 나가는 바이트|초당 바이트 수|합계|네임스페이스에 대한 총 나가는 메시지|
|EHABL|백로그 메시지 보관|개수|합계|Event Hub 네임스페이스에 대한 백로그에 메시지 보관|
|EHAMSGS|메시지 보관|개수|합계|Event Hub 네임스페이스에 보관된 메시지|
|EHAMBS|메시지 보관 처리량|초당 바이트 수|합계|Event Hub 네임스페이스에서 보관된 메시지 처리량|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|RunsStarted|실행 시작됨|개수|합계|실행 시작된 워크플로 수|
|RunsCompleted|실행 완료됨|개수|합계|실행 완료된 워크플로 수|
|RunsSucceeded|실행 성공함|개수|합계|실행 성공한 워크플로 수|
|RunsFailed|실행 실패함|개수|합계|실행 실패한 워크플로 수|
|RunsCancelled|실행 취소됨|개수|합계|실행 취소된 워크플로 수|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간 |
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간 |
|RunThrottledEvents|실행 제한 이벤트|개수|합계|워크플로 작업 또는 트리거 제한 이벤트 수|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|
|ActionsStarted|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|
|ActionsCompleted|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|
|ActionsSucceeded|작업 성공함 |개수|합계|성공한 워크플로 작업 수|
|ActionsFailed|작업 실패함|개수|합계|실패한 워크플로 작업 수|
|ActionsSkipped|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간 |
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간 |
|ActionThrottledEvents|작업 제한 이벤트|개수|합계|워크플로 작업 제한 이벤트 수|
|TriggersStarted|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|
|TriggersCompleted|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|
|TriggersSucceeded|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|
|TriggersFailed|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|
|TriggersSkipped|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|
|TriggersFired|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간 |
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|
|TriggerThrottledEvents|트리거 제한 이벤트|개수|합계|워크플로 트리거 제한 이벤트 수|
|BillableActionExecutions|청구 가능한 작업 실행|개수|합계|청구되는 워크플로 작업 실행 수.|
|BillableTriggerExecutions|청구 가능한 트리거 실행|개수|합계|청구되는 워크플로 트리거 실행 수.|
|TotalBillableExecutions|총 청구 가능 실행|개수|합계|청구되는 워크플로 실행 수.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|처리량|처리량|초당 바이트 수|평균||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|SearchLatency|검색 대기 시간|초|평균|검색 서비스에 대한 평균 검색 대기 시간|
|SearchQueriesPerSecond|초당 검색 쿼리 수|초당 개수|평균|검색 서비스에 대한 초당 검색 쿼리|
|ThrottledSearchQueriesPercentage|제한된 검색 쿼리 백분율|백분율|평균|검색 서비스에 대해 제한된 검색 쿼리의 백분율|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|CPUXNS|네임스페이스당 CPU 사용량|백분율|최대|서비스 버스 프리미엄 네임스페이스 CPU 사용량 메트릭|
|WSXNS|네임스페이스당 메모리 크기 사용|백분율|최대|서비스 버스 프리미엄 네임스페이스 메모리 사용량 메트릭|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|
|저장소|총 데이터베이스 크기|바이트|최대|총 데이터베이스 크기|
|connection_successful|성공적인 연결|개수|합계|성공적인 연결|
|connection_failed|실패한 연결|개수|합계|실패한 연결|
|blocked_by_firewall|방화벽에 의해 차단|개수|합계|방화벽에 의해 차단|
|교착 상태|교착 상태|개수|합계|교착 상태|
|storage_percent|데이터베이스 크기 비율|백분율|최대|데이터베이스 크기 비율|
|xtp_storage_percent|메모리 내 OLTP 저장소 백분율(미리 보기)|백분율|평균|메모리 내 OLTP 저장소 백분율(미리 보기)|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|
|dtu_limit|DTU 제한|개수|평균|DTU 제한|
|dtu_used|DTU 사용됨|개수|평균|DTU 사용됨|
|service_level_objective|데이터베이스의 서비스 수준 목표|개수|합계|데이터베이스의 서비스 수준 목표|
|dwu_limit|dwu 제한|개수|최대|dwu 제한|
|dwu_consumption_percent|DWU 백분율|백분율|평균|DWU 백분율|
|dwu_used|DWU 사용됨|개수|평균|DWU 사용됨|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|
|storage_percent|저장소 비율|백분율|평균|저장소 비율|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|
|eDTU_limit|eDTU 제한|개수|평균|eDTU 제한|
|storage_limit|저장소 제한|바이트|평균|저장소 제한|
|eDTU_used|eDTU 사용|개수|평균|eDTU 사용|
|storage_used|저장소 사용됨|바이트|평균|저장소 사용됨|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|ResourceUtilization|SU % 사용률|백분율|최대|SU % 사용률|
|InputEvents|입력 이벤트|개수|합계|입력 이벤트|
|InputEventBytes|입력 이벤트 바이트|바이트|합계|입력 이벤트 바이트|
|LateInputEvents|늦은 입력 이벤트|개수|합계|늦은 입력 이벤트|
|OutputEvents|출력 이벤트|개수|합계|출력 이벤트|
|ConversionErrors|데이터 변환 오류|개수|합계|데이터 변환 오류|
|오류|런타임 오류|개수|합계|런타임 오류|
|DroppedOrAdjustedEvents|잘못된 이벤트|개수|합계|잘못된 이벤트|
|AMLCalloutRequests|기능 요청|개수|합계|기능 요청|
|AMLCalloutFailedRequests|실패한 기능 요청|개수|합계|실패한 기능 요청|
|AMLCalloutInputEvents|함수 이벤트|개수|합계|함수 이벤트|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|
|DiskQueueLength|디스크 큐 길이|개수|합계|디스크 큐 길이|
|HttpQueueLength|Http 큐 길이|개수|합계|Http 큐 길이|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|

## <a name="microsoftwebsites-including-azure-functions"></a>Microsoft.Web/sites(Azure Functions 포함)

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|
|요청|요청|개수|합계|요청|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|
|Http101|Http 101|개수|합계|Http 101|
|Http2xx|Http 2xx|개수|합계|Http 2xx|
|Http3xx|Http 3xx|개수|합계|Http 3xx|
|Http401|Http 401|개수|합계|Http 401|
|Http403|Http 403|개수|합계|Http 403|
|Http404|Http 404|개수|합계|Http 404|
|Http406|Http 406|개수|합계|Http 406|
|Http4xx|Http 4xx|개수|합계|Http 4xx|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|
|MemoryWorkingSet|메모리 작업 집합|바이트|합계|메모리 작업 집합|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|합계|평균 메모리 작업 집합|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|
|FunctionExecutionUnits|함수 실행 단위|개수|평균|함수 실행 단위|
|FunctionExecutionCount|함수 실행 횟수|개수|평균|함수 실행 횟수|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|메트릭|매트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|
|요청|요청|개수|합계|요청|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|
|Http101|Http 101|개수|합계|Http 101|
|Http2xx|Http 2xx|개수|합계|Http 2xx|
|Http3xx|Http 3xx|개수|합계|Http 3xx|
|Http401|Http 401|개수|합계|Http 401|
|Http403|Http 403|개수|합계|Http 403|
|Http404|Http 404|개수|합계|Http 404|
|Http406|Http 406|개수|합계|Http 406|
|Http4xx|Http 4xx|개수|합계|Http 4xx|
|Http5xx|Http 서버 오류|개수|합계|Http 서버 오류|
|MemoryWorkingSet|메모리 작업 집합|바이트|합계|메모리 작업 집합|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|합계|평균 메모리 작업 집합|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|
|FunctionExecutionUnits|함수 실행 단위|개수|평균|함수 실행 단위|
|FunctionExecutionCount|함수 실행 횟수|개수|평균|함수 실행 횟수|

## <a name="next-steps"></a>다음 단계
* [Azure Monitor의 메트릭에 대해 읽기](monitoring-overview.md#monitoring-sources)
* [메트릭에 대한 경고 만들기](insights-receive-alert-notifications.md)
* [저장소, 이벤트 허브 또는 Log Analytics에 메트릭 내보내기](monitoring-overview-of-diagnostic-logs.md)




<!--HONumber=Feb17_HO2-->


