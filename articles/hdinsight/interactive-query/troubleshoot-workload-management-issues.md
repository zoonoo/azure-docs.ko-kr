---
title: Hive LLAP 워크로드 관리 문제 해결
titleSuffix: Azure HDInsight
description: Hive LLAP 워크로드 관리 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 6a06a67b5039bc3a7e25e8300128c85ee008be3a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483137"
---
# <a name="troubleshoot-hive-llap-workload-management-issues"></a>Hive LLAP 워크로드 관리 문제 해결

WLM(워크로드 관리)은 HDInsight 4.0 클러스터를 시작하는 고객이 사용할 수 있습니다. 다음 리소스를 사용하여 WLM 기능과 관련된 문제를 디버그할 수 있습니다.

## <a name="get-wlm-resource-plan-and-plan-entities"></a>WLM 리소스 계획 및 계획 엔터티 가져오기
#### <a name="to-get-all-resource-plans-on-the-cluster"></a>클러스터의 모든 리소스 계획을 가져오려면 다음을 수행합니다.
```hql
SHOW RESOURCE PLANS;
```

#### <a name="to-get-definition-of-a-given-resource-plan"></a>지정된 리소스 계획의 정의를 가져오려면
```hql
SHOW RESOURCE PLAN <plan_name>;
```

## <a name="get-wlm-entities-information-from-metastore-database"></a>메타스토어 데이터베이스에서 WLM 엔터티 정보 가져오기
> [!NOTE]
> 사용자 지정 Hive 메타스토어 데이터베이스에만 적용됩니다

또한 Hive 메타스토어 데이터베이스의 다음 테이블에서 WLM 엔터티 정보를 볼 수 있습니다 

* **WM_RESOURCEPLANS**(NAME string, STATUS string, QUERY_PARALLELISM int, DEFAULT_POOL_PATH string)
* **WM_POOLS**(RP_NAME string, PATH string, ALLOC_FRACTION double, QUERY_PARALLELISM int, SCHEDULING_POLICY string)
* **WM_MAPPINGS**(RP_NAME string, ENTITY_TYPE string, ENTITY_NAME string, POOL_PATH string, ORDERING int)
* **WM_TRIGGERS**(RP_NAME string, NAME string, TRIGGER_EXPRESSION string, ACTION_EXPRESSION string)
* **WM_POOLS_TO_TRIGGERS**(RP_NAME string, POOL_PATH string, TRIGGER_NAME string)

## <a name="wlm-metrics"></a>WLM 메트릭

WLM 메트릭은 메트릭 덤프 탭에서 HS2Interactive UI를 통해 직접 액세스할 수 있습니다. <br>
:::image type="content" source="./media/hive-workload-management/hs2-interactive-wlm.jpg" alt-text="HS2 대화형 UI." lightbox="./media/hive-workload-management/hs2-interactive-wlm.jpg":::

리소스 계획의 지정된 풀에 대해 WLM에서 게시한 예시 메트릭입니다.
```
    "name" : "Hadoop:service=hiveserver2,name=WmPoolMetrics.etl",
    "modelerType" : "WmPoolMetrics.etl",
    "tag.Context" : "HS2",
    "tag.SessionId" : "etl",
    "tag.Hostname" : "hn0-c2b-ll.cu1cgjaim53urggr4psrgczloa.cx.internal.cloudapp.net",
    "NumExecutors" : 10,
    "NumRunningQueries" : 2,
    "NumParallelQueries" : 3,
    "NumQueuedQueries" : 0,
    "NumExecutorsMax" : 10
```

HS2Interactive UI는 2021년 4월 이전에 릴리스된 ESP(Enterprise Security Package) 지원 클러스터에 대해 작동하지 않을 수 있습니다. 이러한 경우 사용자 지정된 Grafana 대시보드에서 WLM 관련 메트릭을 가져올 수 있습니다.
<br>
메트릭 이름은 아래 패턴을 따릅니다.
```
default.General.WM_<pool>_numExecutors
default.General.WM_<pool>_numExecutorsMax
default.General.WM_<pool>_numRunningQueries
default.General.WM_<pool>_numParallelQueries
default.General.WM_<pool>_numQueuedQueries
```
`<pool>`을 해당 풀 이름으로 바꾸어 grafana에서 메트릭을 얻습니다.

:::image type="content" source="./media/hive-workload-management/grafana-wlm.jpg" alt-text="Grafana WLM 메트릭." lightbox="./media/hive-workload-management/grafana-wlm.jpg":::

> 참고: 위의 필터 및 구성 요소 이름에서 hiveserver2 구성 요소가 선택되어 있는지 확인합니다.

<br>

## <a name="wlm-feature-characteristics"></a>WLM 기능 특성
### <a name="lifecycle-of-tez-ams-in-wlm-enabled-clusters"></a>**WLM 지원 클러스터에서 Tez AM의 수명 주기**
기본 LLAP 클러스터와 달리 WLM 지원 클러스터에는 다른 Tez AM 집합이 있습니다. 하이브 구성에서 *hive.server2.tez.interactive.queue=wm* 이 설정된 경우 이러한 Tez AM은 `wm` 큐에서 실행하도록 예약됩니다. <br>
이러한 Tez AM은 리소스 계획에 정의된 모든 풀의 QUERY_PARALLELISM 합계에 따라 WLM이 활성화될 때 생성됩니다. <br>
클러스터에서 워크로드 관리를 사용하지 않도록 설정하면 이러한 Tez AM이 자동으로 중지됩니다.
`{ DISABLE WORKLOAD MANAGEMENT; }`

### <a name="resource-contention"></a>**리소스 경합**
WLM 지원 LLAP 클러스터에서 리소스는 리소스 계획 구성에 따라 쿼리 간에 공유됩니다. 리소스 공유로 인해 쿼리 속도가 느려지는 경우가 있습니다.
풀 내에서 발생하는 리소스 경합을 줄이기 위해 리소스 계획에 대한 일부 조정을 수행할 수 있습니다. 예를 들어 `scheduling_policy`은 클러스터의 리소스를 풀에 할당된 각 쿼리와 동일하게 공유하도록 보장하는 `fair` 또는 풀에 제공되는 첫 번째 쿼리에 대한 모든 리소스를 보장하는 `fifo`으로 정의할 수 있습니다.<br>
다음 예시에서는 `wlm_basic` 리소스 계획에서 `etl`이라는 풀에 대한 예약 정책을 설정하는 방법을 보여 줍니다.
```hql
ALTER POOL wlm_basic.etl SET SCHEDULING_POLICY = fair;
```
풀을 만드는 동안 예약 정책을 설정할 수도 있습니다.
```hql
CREATE POOL wlm_basic.default WITH ALLOC_FRACTION = 0.5, QUERY_PARALLELISM = 2, SCHEDULING_POLICY = fifo;
```

### <a name="query-failures-for-some-specific-use-cases"></a>**일부 특정 사용 사례에 대한 쿼리 실패**
다음과 같은 경우 WLM에서 쿼리를 실행하면 자동으로 실행이 종료될 수 있습니다.
1. Tez AM을 사용할 수 없는 쿼리 및 대상 풀에 Move 트리거를 적용하면 쿼리가 대신 종료됩니다. <br>
위의 항목은 WLM 기능의 디자인 제한 사항입니다. 최대 로드 시나리오의 경우에도 클러스터에 제출된 쿼리를 이 풀에서 지원할 수 있도록 대상 풀의 `QUERY_PARALLELISM` 속성을 늘려서 이 기능을 해결할 수 있습니다. 또한 이 변경 내용을 수용하기 위해 `wm` 큐 크기를 조정합니다. <br>
2. WLM을 사용하지 않도록 설정하면 다음과 같은 예외 패턴으로 모든 처리 중인 쿼리가 실패합니다.
   ```
   FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.tez.TezTask. Dag received [DAG_TERMINATE, DAG_KILL] in RUNNING state.
   ```
3. WLM Tez AM이 수동으로 종료되면 다음 패턴을 사용하여 일부 쿼리가 실패할 수 있습니다. <br/>이러한 쿼리는 다시 전송에 대한 문제 없이 실행되어야 합니다.
```
java.util.concurrent.CancellationException: Task was cancelled.
    at com.google.common.util.concurrent.AbstractFuture.cancellationExceptionWithCause(AbstractFuture.java:1349) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.getDoneValue(AbstractFuture.java:550) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.get(AbstractFuture.java:513) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.get(AbstractFuture.java:90) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Uninterruptibles.getUninterruptibly(Uninterruptibles.java:237) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures.getDone(Futures.java:1064) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures$CallbackListener.run(Futures.java:1013) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.DirectExecutor.execute(DirectExecutor.java:30) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.executeListener(AbstractFuture.java:1137) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.complete(AbstractFuture.java:957) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.cancel(AbstractFuture.java:611) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.cancel(AbstractFuture.java:118) ~[guava-28.0-jre.jar:?]
    at org.apache.hadoop.hive.ql.exec.tez.WmTezSession$TimeoutRunnable.run(WmTezSession.java:264) ~[hive-exec-3.1.3.4.1.3.6.jar:3.1.3.4.1.3.6]
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511) [?:1.8.0_275]
    at java.util.concurrent.FutureTask.run(FutureTask.java:266) [?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:180) ~[?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293) ~[?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_275]
    at java.lang.Thread.run(Thread.java:748) [?:1.8.0_275]
```

## <a name="known-issues"></a>알려진 문제
* [HWC(Hive Warehouse Connector)](apache-hive-warehouse-connector.md)를 통해 제출된 Spark 작업은 대상 LLAP 클러스터에 WLM 기능이 사용하도록 설정된 경우 간헐적 오류가 발생할 수 있습니다. <br>
  위의 문제를 방지하기 위해, 고객은 하나는 WLM을 사용하고 다른 하나는 WLM을 사용하지 않는 두 개의 LLAP 클러스터를 사용할 수 있습니다.
  그런 다음 고객은 HWC를 사용하여 WLM 없이 해당 Spark 클러스터를 LLAP 클러스터에 연결할 수 있습니다.

* 경우에 따라 긴 시간 동안 `DISABLE WORKLOAD MANAGEMENT;` 명령이 중단됩니다. <br>
해당 명령을 취소하고 `SHOW RESOURCE PLANS;` 명령을 사용하여 리소스 계획 상태를 확인합니다.
`DISABLE WORKLOAD MANAGEMENT` 명령을 다시 실행하기 전에 활성 리소스 플랜을 사용할 수 있는지 확인합니다. <br>

* Tez AM 중 일부는 계속 실행 중일 수 있으며 `DISABLE WORKLOAD MANAGEMENT` 명령 또는 HS2 재시작으로 전환되지 않습니다. <br>
워크로드 관리를 사용하지 않도록 설정하고 나서 `yarn UI` 또는 `yarn console application`를 통해 이러한 Tez AM을 종료합니다.

## <a name="related-articles"></a>관련 문서
* [Hive LLAP 워크로드 관리](hive-workload-management.md)
* [Hive LLAP 워크로드 관리 명령 요약](workload-management-commands.md)

