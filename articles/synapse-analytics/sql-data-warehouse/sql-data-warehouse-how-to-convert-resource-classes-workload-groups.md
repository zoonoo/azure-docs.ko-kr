---
title: 리소스 클래스를 작업 그룹으로 변환
description: Azure SQL Data Warehouse에서 리소스 클래스와 유사한 작업 그룹을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d73ba8f21fe7731fb751d42a8497ff8e1ebba7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383636"
---
# <a name="convert-resource-classes-to-workload-groups"></a>리소스 클래스를 작업 그룹으로 변환

작업 그룹은 시스템 리소스를 격리 하 고 포함 하는 메커니즘을 제공 합니다.  또한 작업 그룹을 사용 하 여 실행 중인 요청에 대 한 실행 규칙을 설정할 수 있습니다.  쿼리 제한 시간 실행 규칙은 사용자 개입 없이 런어웨이 쿼리를 취소할 수 있도록 허용 합니다.  이 문서에서는 기존 리소스 클래스를 사용 하 고 유사한 구성을 사용 하 여 작업 그룹을 만드는 방법을 설명 합니다.  또한 선택적 쿼리 제한 시간 규칙이 추가 됩니다.

> [!NOTE]
> 작업 그룹 및 리소스 클래스를 동시에 사용 하는 방법에 대 한 지침은 [워크 로드 분류](sql-data-warehouse-workload-classification.md) 개념 문서의 [분류자를 사용 하 여 리소스 클래스 할당 혼합](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) 섹션을 참조 하세요.

## <a name="understanding-the-existing-resource-class-configuration"></a>기존 리소스 클래스 구성 이해

작업 그룹에는 요청당 할당 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 된 전체 시스템 리소스의 백분율을 지정 하는 라는 매개 변수가 필요 합니다.  리소스 [클래스](resource-classes-for-workload-management.md#what-are-resource-classes) 에 대 한 리소스 할당은 동시성 슬롯을 할당 하 여 수행 됩니다.  에 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`지정할 값을 확인 하려면 dm_workload_management_workload_groups_stats <link tbd> DMV를 사용 합니다.  예를 들어 아래 쿼리 쿼리는 staticrc40과 유사한 작업 그룹을 만들기 위해 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 매개 변수에 사용할 수 있는 값을 반환 합니다.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 작업 그룹은 전체 시스템 리소스의 백분율을 기준으로 작동 합니다.  

작업 그룹은 전체 시스템 리소스의 백분율을 기준으로 작동 하기 때문에 전체 시스템 리소스를 기준으로 하는 정적 리소스 클래스에 할당 된 리소스의 백분율이 변경 됩니다.  예를 들어 staticrc40 at DW1000c는 전체 시스템 리소스의 9.6%를 할당 합니다.  DW2000c에는 19.2%가 할당 됩니다.  이 모델은 요청 당 더 많은 리소스를 할당 하는 것과 동시성을 위해 확장 하려는 경우에도 유사 합니다.

## <a name="create-workload-group"></a>작업 그룹 만들기

알려진 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 사용 하 여 작업 그룹 만들기 <link> 구문을 사용 하 여 작업 그룹을 만들 수 있습니다.  필요에 따라 0 보다 `MIN_PERCENTAGE_RESOURCE` 큰를 지정 하 여 작업 그룹에 대 한 리소스를 격리할 수 있습니다.  또한 필요에 따라 100 미만 `CAP_PERCENTAGE_RESOURCE` 으로 지정 하 여 작업 그룹에서 사용할 수 있는 리소스의 양을 제한할 수 있습니다.  

아래 예제에서는를로 `MIN_PERCENTAGE_RESOURCE` `wgDataLoads` 설정 하 여 시스템 리소스의 9.6%를 전용으로 설정 하 고 하나의 쿼리가 항상 실행 될 수 있도록 보장 합니다.  또한 `CAP_PERCENTAGE_RESOURCE` 가 38.4%로 설정 되 고이 작업 그룹을 동시 요청 4 개로 제한 합니다.  `QUERY_EXECUTION_TIMEOUT_SEC` 매개 변수를 3600로 설정 하면 1 시간 이상 실행 되는 모든 쿼리가 자동으로 취소 됩니다.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>분류자 만들기

이전에는 리소스 클래스에 대 한 쿼리 매핑이 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class)로 수행 되었습니다.  동일한 기능을 수행 하 고 요청을 작업 그룹에 매핑하려면 [CREATE 워크 로드 분류자](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문을 사용 합니다.  Sp_addrolemember를 사용 하는 경우 로그인을 기반으로 하는 요청에만 리소스를 매핑할 수 있습니다.  분류자는 로그인 외에 다음과 같은 추가 옵션을 제공 합니다.
    - label
    - 세션
    - 아래 예에서는 [옵션 레이블이](sql-data-warehouse-develop-label.md) 로 `factloads` 설정 `wgDataLoads` 된 `AdfLogin` 로그인의 쿼리를 위에서 만든 작업 그룹으로 할당 합니다.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>예제 쿼리를 사용 하 여 테스트

다음은 작업 그룹 및 분류자가 올바르게 구성 되었는지 확인 하는 샘플 쿼리 및 DMV 쿼리입니다.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>다음 단계

- [워크 로드 격리](sql-data-warehouse-workload-isolation.md)
- [작업 그룹을 만드는 방법](quickstart-configure-workload-isolation-tsql.md)
- [워크로드 분류자 만들기(Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP(Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
