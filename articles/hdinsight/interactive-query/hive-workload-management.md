---
title: Hive LLAP 워크로드 관리 기능
titleSuffix: Azure HDInsight
description: Hive LLAP 워크로드 관리 기능
ms.service: hdinsight
ms.topic: how-to
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 8dde65d0547552af90caddd214dd1ceda5a754ee
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482928"
---
# <a name="hive-llap-workload-management-wlm-feature"></a>Hive LLAP WLM(워크로드 관리) 기능
Interactive Query 클러스터에서 리소스 관리는 특히 다중 테넌트 환경에서 필수적입니다. Hive LLAP(낮은 대기 시간 분석 처리)는 워크로드 관리를 사용하여 사용자가 특정 워크로드 요구 사항을 일치시키고 해당 리소스에 대한 경합을 방지할 수 있도록 합니다. <br> 워크로드 관리는 Hive/LLAP에 사용할 수 있는 리소스를 특정 워크로드에 사용할 풀로 나눌 수 있는 리소스 풀(쿼리 풀이라고도 함)을 구현합니다.
또한 각 개별 리소스 풀에 대해 리소스의 백분율 및 쿼리 병렬을 구성할 수 있습니다.

!['LLAP 아키텍처'.](./media/hive-workload-management/llap-architecture.png)

## <a name="enable-hive-llap-workload-management-feature-for-hdinsight-clusters"></a>HDInsight 클러스터에 Hive LLAP 워크로드 관리 기능 사용하도록 설정

아래 나열된 단계에 따라 HDInsight Interactive Query 클러스터에서 워크로드 관리 기능을 사용하도록 설정합니다.
1. 워크로드 관리 Tez AM을 가져오는 데 사용할 수 있는 새 yarn 큐를 만듭니다.
2. Ambari를 통해 클러스터 구성을 변경하여 Hive에서 기능을 사용하도록 설정합니다.
3. 리소스 계획을 만들고 활성화합니다.

### <a name="create-a-new-yarn-queue-suitable-for-workload-management-feature"></a>워크로드 관리 기능에 적합한 새 yarn 큐 만들기
다음 [가이드](../hdinsight-troubleshoot-yarn.md)의 도움을 받아 `wm`이라는 새 yarn 큐를 만듭니다.
다음 구성에 따라 클러스터에서 `wm` 큐를 구성합니다.

| QueueName   | 용량 | 최대 용량 | 우선 순위 | 최대 AM 리소스 |
|------------|---------|--------------|----------|---------------------|
| `default`   | 5%       | 5%           | 0        | 33%                 |
| `llap`      | 85%      | 100%         | 10       | 33%                 |
| `wm`        | 10%      | 15%          | 9        | 100%                |

`wm` 큐 구성이 아래와 같이 보이는지 확인합니다.
:::image type="content" source="./media/hive-workload-management/wm-yarn-queue.png" alt-text="wm 큐 구성.":::

### <a name="enable-workload-management-feature-in-hive-configs"></a>Hive 구성에서 워크로드 관리 기능 사용하도록 설정
사용자 지정 hiveserver2-interactive-site에 다음 속성을 추가하고 해당 값을 새로 만든 yarn 큐의 이름(`wm`)으로 설정합니다. 구성 변경 내용을 적용하려면 대화형 HiveServer를 다시 시작합니다.
```
hive.server2.tez.interactive.queue=wm
```

### <a name="create-resource-plan"></a>리소스 계획 생성
다음은 기본 리소스 계획을 생성하는 방법에 대한 예입니다.
!['기본 리소스 계획'.](./media/hive-workload-management/wlm-resourceplan.jpg)

beeline을 통해 다음 명령을 실행하여 위의 리소스 계획을 만듭니다.

#### <a name="commands-to-create-view-and-drop-the-resource-plan"></a>리소스 계획을 생성, 확인, 삭제하는 명령
```hql
# CREATE RESOURCE PLAN
CREATE RESOURCE PLAN demo_plan;

# CREATE POOLS
ALTER POOL demo_plan.default SET ALLOC_FRACTION = 0.65, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.sys_accounts WITH ALLOC_FRACTION = 0.15, QUERY_PARALLELISM = 1;

# CREATE MAPPING
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
 
# CREATE TRIGGERS
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
ALTER TRIGGER demo_plan.defaultToETL ADD TO POOL default;
CREATE TRIGGER demo_plan.ETLKill  WHEN ELAPSED_TIME > 40000 DO KILL;
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# DISABLE PLAN
-- In case plan is in active state first run:
-- DISABLE WORKLOAD MANAGEMENT;
ALTER RESOURCE PLAN demo_plan DISABLE;

# DROP RESOURCE PLAN
DROP RESOURCE PLAN demo_plan;
```

## <a name="understanding-resource-plan"></a>리소스 계획 이해
최적의 리소스 계획을 얻으려면 워크로드 요구 사항에 대한 철저한 이해를 해야 합니다.

### <a name="number-of-pools"></a>풀 수
풀 수는 총 쿼리 병렬 처리로 제한됩니다(풀 당 최소한 하나의 쿼리).
대부분의 워크로드에는 3개 이상의 풀이 필요한 경우가 거의 없습니다. 
- 대화형 쿼리의 경우 기본값 
- 장기 쿼리의 경우 etl/batch
- 시스템 관리자의 경우 sys

### <a name="total-query_parallelism"></a>총 QUERY_PARALLELISM
다음 수식을 사용하여 총 동시 쿼리 수 또는 총 QUERY_PARALLELISM 수를 얻을 수 있습니다.

```
Number of total concurrent queries(Tez AMs) = Math.floor( (total cluster memory capacity / size of Tez AM container) x percentage of wm queue capacity)
```

예를 들면 다음과 같습니다. <br/>
Tez AM 컨테이너 크기가 4GB이고 yarn 클러스터의 총 메모리 용량이 400GB이며, 이 중에 wm 큐에 10%가 할당된 것으로 가정합니다. <br/>
총 동시 쿼리 수 = 층((400/4) x 0.10) = 10

> [!Tip]
> tez AM이 수락됨 상태로 고정되는 것을 방지하기 위해, 즉 `wm` 큐 용량을 10.01%로 만들 수 있고 `default` 큐 용량을 4.99%까지 줄일 수 있도록 하기 위해, wm 큐에 필요한 것 보다는 약간 더 많은 용량이 있습니다.

### <a name="mappings"></a>매핑
매핑은 쿼리를 특정 풀로 전달하는 메커니즘을 제공합니다. 매핑 수가 증가하면 지정된 쿼리에 여러 규칙이 적용될 수 있습니다. 우선적으로 적용할 규칙을 설정하려면: 선택적 `WITH ORDER` 절을 사용하여 순서를 지정하면 하위 규칙이 우선적으로 적용됩니다. 그렇지 않으면 `user` 규칙이 `application` 규칙보다 우선하고 `application` 규칙이 `group` 규칙보다 우선합니다. <br/>
우선 순위가 같은 그룹 규칙의 순서는 정의되지 않습니다.


> [!Note]
> * WLM 계획이 활성 상태일 때 `llap` 큐의 Tez AM은 사용되지 않는 상태로 유지됩니다. WLM 리소스 계획을 사용하지 않도록 설정하면, `llap` 큐에 있는 이러한 Tez AM을 쉽게 사용할 수 있습니다.
> * WLM 리소스 계획을 사용하도록 설정하면, 지정된 리소스 계획에 대해 구성된 총 `QUERY_PARALLELISM` 수와 동일한 수의 Tez AM이 시작됩니다. `wm` 큐 크기를 조정하여 이러한 Tez AM이 수락됨 상태로 고정되지 않도록 해야 합니다.
> * 리소스 계획 사용을 위해 다음 두 카운터만 사용할 수 있습니다.
>    * EXECUTION_TIME
>    * ELAPSED_TIME

## <a name="related-articles"></a>관련 문서
* [Hive LLAP 워크로드 관리 명령 요약](workload-management-commands.md)
* [Hive LLAP 워크로드 관리 문제 해결](troubleshoot-workload-management-issues.md)


