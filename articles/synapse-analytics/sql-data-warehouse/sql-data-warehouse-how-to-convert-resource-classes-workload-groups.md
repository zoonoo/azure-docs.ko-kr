---
title: 리소스 클래스를 워크로드 그룹으로 변환
description: Azure SQL Data 웨어하우스의 리소스 클래스와 유사한 워크로드 그룹을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a249dfc4f04fbd7b6b73a0e9f37d53106bf82efd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633349"
---
# <a name="convert-resource-classes-to-workload-groups"></a>리소스 클래스를 워크로드 그룹으로 변환

워크로드 그룹은 시스템 리소스를 격리하고 포함하는 메커니즘을 제공합니다.  또한 워크로드 그룹을 사용하면 워크로드그룹에서 실행 중인 요청에 대한 실행 규칙을 설정할 수 있습니다.  쿼리 시간 시간 시간 실행 규칙을 사용하면 사용자 개입 없이 런어웨이 쿼리를 취소할 수 있습니다.  이 문서에서는 기존 리소스 클래스를 사용 하 고 비슷한 구성을 사용 하 여 워크로드 그룹을 만드는 방법에 대해 설명 합니다.  또한 선택적 쿼리 시간 시간 규칙이 추가됩니다.

## <a name="understanding-the-existing-resource-class-configuration"></a>기존 리소스 클래스 구성 이해

워크로드 그룹에는 요청당 할당된 전체 시스템 리소스의 백분율을 지정하는 매개 변수가 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 필요합니다.  리소스 할당은 동시성 슬롯을 할당하여 [리소스 클래스에](resource-classes-for-workload-management.md#what-are-resource-classes) 대해 수행됩니다.  에 대해 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`지정할 값을 결정하려면 sys.dm_workload_management_workload_groups_stats <link tbd> DMV를 사용합니다.  예를 들어 아래 쿼리 쿼리는 staticrc40과 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 유사한 워크로드 그룹을 만드는 데 매개 변수에 사용할 수 있는 값을 반환합니다.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 워크로드 그룹은 전체 시스템 리소스의 백분율에 따라 작동합니다.  

워크로드 그룹은 전체 시스템 리소스의 백분율을 기반으로 작동하므로 확장 및 축소할 때 전체 시스템 리소스에 대해 정적 리소스 클래스에 할당된 리소스의 백분율이 변경됩니다.  예를 들어 DW1000c의 staticrc40은 전체 시스템 리소스의 9.6%를 할당합니다.  DW2000c에서는 19.2%가 할당됩니다.  이 모델은 요청당 더 많은 리소스를 할당하는 것과 동시성을 위해 확장하려는 경우와 유사합니다.

## <a name="create-workload-group"></a>워크로드 그룹 만들기

알려진 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`을 사용하여 워크로드 그룹 <link> 만들기 구문을 사용하여 워크로드 그룹을 만들 수 있습니다.  선택적으로 0보다 `MIN_PERCENTAGE_RESOURCE` 큰 것을 지정하여 워크로드 그룹에 대한 리소스를 격리할 수 있습니다.  또한 선택적으로 100 개 미만을 지정하여 `CAP_PERCENTAGE_RESOURCE` 워크로드 그룹이 사용할 수 있는 리소스의 양을 제한할 수 있습니다.  

아래 예제는 `MIN_PERCENTAGE_RESOURCE` 시스템 리소스의 9.6%를 할당하도록 `wgDataLoads` 설정하고 하나의 쿼리가 항상 실행될 수 있도록 보장합니다.  또한 `CAP_PERCENTAGE_RESOURCE` 38.4%로 설정되고 이 워크로드 그룹을 4개의 동시 요청으로 제한합니다.  매개 변수를 `QUERY_EXECUTION_TIMEOUT_SEC` 3600으로 설정하면 1시간 이상 실행되는 모든 쿼리가 자동으로 취소됩니다.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>분류자 만들기

이전에는 [쿼리를](resource-classes-for-workload-management.md#change-a-users-resource-class)리소스 클래스에 매핑하는 sp_addrolemember 수행되었습니다.  동일한 기능을 달성하고 요청을 워크로드 그룹에 매핑하려면 [워크로드 분류자 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) 구문을 사용합니다.  sp_addrolemember 사용하면 로그인을 기반으로 리소스를 요청에 매핑할 수 있습니다.  분류기는 다음과 같은 로그인 외에 추가 옵션을 제공합니다.
    - label
    - 세션
    - 시간 아래 예제는 위에서 만든 `AdfLogin` 워크로드 그룹에 `wgDataLoads` [옵션 LABEL이](sql-data-warehouse-develop-label.md) `factloads` 설정된 로그인에서 쿼리를 할당합니다.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>샘플 쿼리로 테스트

다음은 워크로드 그룹 및 분류기가 올바르게 구성되었는지 확인하는 샘플 쿼리 및 DMV 쿼리입니다.

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

- [워크로드 격리](sql-data-warehouse-workload-isolation.md)
- [워크로드 그룹 링크를 만드는 방법](quickstart-configure-workload-isolation-tsql.md)
