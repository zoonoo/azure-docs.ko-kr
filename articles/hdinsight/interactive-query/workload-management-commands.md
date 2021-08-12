---
title: Hive LLAP 워크로드 관리 명령
titleSuffix: Azure HDInsight
description: Hive LLAP 워크로드 관리 명령
ms.service: hdinsight
ms.topic: reference
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: dc05ae5ec7cad35d5cda549e654caf3d44d91a03
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483134"
---
# <a name="hive-llap-workload-management-commands"></a>Hive LLAP 워크로드 관리 명령

워크로드 관리 기능은 다음 Hive 명령을 통해 제어 및 관리될 수 있습니다. 이러한 명령은 기존 ALTER, CREATE, DROP 및 SHOW 문과 유사합니다.

## <a name="alter-mapping"></a>변경 매핑 
쿼리 라우팅을 리소스 풀로 변경합니다. 
#### <a name="syntax"></a>구문 
```hql
ALTER { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ]
```
#### <a name="example"></a>예제 
```hql
ALTER USER MAPPING 'hive' IN demo_plan TO etl WITH ORDER 1;
```

## <a name="alter-pool"></a>변경 풀 
쿼리 풀 속성을 수정하고, 트리거를 추가하고, 트리거를 제거합니다. 
#### <a name="syntax"></a>구문 
```hql
ALTER POOL plan_name.pool_path [ SET {property=value, ... } | UNSET { property, ... } ];
ALTER POOL plan_name.pool_path [ ADD | DROP ] TRIGGER name;
```
#### <a name="example"></a>예제 
```hql
ALTER POOL demo_plan.default ADD TRIGGER defaultToETL;
```

## <a name="alter-resource-plan"></a>리소스 계획 변경 
계획을 사용하는 것으로 설정, 사용하지 않는 것으로 설정, 활성화, 유효성 검사를 하거나 또는 변경합니다. 
#### <a name="syntax"></a>구문 
```hql
ALTER RESOURCE PLAN name [ VALIDATE | DISABLE | ENABLE | ACTIVATE | RENAME TO another_name | SET {property=value, ... } | UNSET {property, ... } ];
```
#### <a name="example"></a>예제 
```hql
ALTER RESOURCE PLAN demo_plan SET DEFAULT POOL=etl, QUERY_PARALLELISM=3;
```

## <a name="alter-trigger"></a>트리거 변경 
리소스 풀에 트리거를 추가하거나 리소스 풀에서 트리거를 제거합니다. 
#### <a name="syntax"></a>구문 
```hql
ALTER TRIGGER plan_name.name { ADD TO | DROP FROM } { POOL path | UNMANAGED };
```
#### <a name="example"></a>예제 
```hql
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;
```

## <a name="create-mapping"></a>매핑 생성 
쿼리를 리소스 풀로 라우팅합니다. 
#### <a name="syntax"></a>구문 
```hql
CREATE { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ];
```
#### <a name="example"></a>예제 
```hql
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
```

## <a name="create-pool"></a>풀 생성 
리소스 계획에 대한 쿼리 풀을 만들고 추가합니다. 
#### <a name="syntax"></a>구문 
```hql
CREATE POOL plan_name.path WITH ALLOC_FRACTION = decimal, QUERY_PARALLELISM = num, [ SCHEDULING_POLICY = scheduling_value ];
```
#### <a name="example"></a>예제 
```hql
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
```

## <a name="create-resource-plan"></a>리소스 계획 생성 
리소스 풀을 만듭니다 
#### <a name="syntax"></a>구문 
```hql
CREATE RESOURCE PLAN plan_name [ WITH QUERY PARALLELISM=number | LIKE name];
```
#### <a name="example"></a>예제 
```hql
CREATE RESOURCE PLAN demo_plan;
```

## <a name="create-trigger"></a>트리거 만들기 
트리거를 만들고 리소스 계획에 추가합니다. 
#### <a name="syntax"></a>구문 
```hql
CREATE TRIGGER plan_name.name WHEN condition DO action;
```
#### <a name="example"></a>예제 
```hql
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
```

## <a name="disable-workload-management"></a>워크로드 관리 비활성화 
활성 리소스 계획을 비활성화합니다. 
#### <a name="syntax"></a>구문 
```hql
DISABLE WORKLOAD MANAGEMENT;
```
#### <a name="example"></a>예제 
```hql
DISABLE WORKLOAD MANAGEMENT
```

## <a name="drop-mapping"></a>매핑 삭제 
리소스 계획에서 매핑을 제거합니다. 
#### <a name="syntax"></a>구문 
```hql
DROP { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name;
```
#### <a name="example"></a>예제 
```hql
DROP USER MAPPING 'hive' IN demo_plan;
```

## <a name="drop-pool"></a>풀 삭제 
리소스 계획에서 쿼리 풀을 제거합니다. 
#### <a name="syntax"></a>구문 
```hql
DROP POOL plan_name.pool_path;
```
#### <a name="example"></a>예제 
```hql
CREATE POOL demo_plan.etl;
```

## <a name="drop-resource-plan"></a>리소스 계획 삭제 
리소스 계획을 삭제합니다. 
#### <a name="syntax"></a>구문 
```hql
DROP RESOURCE PLAN plan_name;
```
#### <a name="example"></a>예제 
```hql
DROP RESOURCE PLAN demo_plan;
```

## <a name="drop-trigger"></a>트리거 삭제 
리소스 계획에서 트리거를 삭제합니다. 
#### <a name="syntax"></a>구문 
```hql
DROP TRIGGER plan_name.trigger_name;
```
#### <a name="example"></a>예제 
```hql
DROP TRIGGER demo_plan.defaultToETL;
```

## <a name="replace-resource-plan-with"></a>리소스 계획을 다음으로 바꾸기 
한 리소스 계획의 내용을 다른 리소스 계획의 콘텐츠로 바꿉니다. 
#### <a name="syntax"></a>구문 
```hql
REPLACE RESOURCE PLAN name1 WITH name2; 
REPLACE ACTIVE RESOURCE PLAN name1 WITH name2;
```
#### <a name="example"></a>예제 
```hql
REPLACE RESOURCE PLAN rp_plan1 WITH rp_plan2;
```

## <a name="show-resource-plan"></a>리소스 계획 표시 
계획 콘텐츠를 나열합니다. 
#### <a name="syntax"></a>구문 
```hql
SHOW RESOURCE PLAN plan_name;
```
#### <a name="example"></a>예제 
```hql
SHOW RESOURCE PLAN demo_plan;
```

## <a name="show-resource-plans"></a>리소스 계획 표시 
모든 리소스 계획을 나열합니다. 
#### <a name="syntax"></a>구문 
```hql
SHOW RESOURCE PLANS;
```
#### <a name="example"></a>예제 
```hql
SHOW RESOURCE PLANS;
```
