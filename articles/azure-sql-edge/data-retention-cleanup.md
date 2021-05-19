---
title: 보존 정책을 사용하여 과거 데이터 관리 - Azure SQL Edge
description: Azure SQL Edge에서 보존 정책을 사용하여 기록 데이터를 관리하는 방법을 알아봅니다.
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392370"
---
# <a name="manage-historical-data-with-retention-policy"></a>보존 정책을 사용하여 과거 데이터 관리

사용자가 테이블 및 데이터베이스에 대해 유연한 에이징 정책을 만들 수 있도록 데이터베이스 및 기본 테이블에서 데이터 보존을 개별적으로 사용하도록 설정할 수 있습니다. 데이터 보존을 적용하는 작업은 간단합니다. 테이블을 만드는 동안 또는 ALTER TABLE 작업의 일부로 하나의 매개 변수만 설정하면 됩니다. 

데이터베이스 및 기본 테이블에 대해 데이터 보존 정책을 정의 한 후, 백그라운드 시간 타이머 태스크가 실행되어 데이터 보존이 사용되는 테이블에서 사용되지 않는 레코드를 제거 합니다 일치하는 행을 식별하고 이를 테이블에서 제거하는 작업은 시스템에서 예약하고 실행되는 백그라운드 작업으로 투명하게 수행됩니다. 테이블 정의에서 `filter_column`로 사용된 열을 기반으로 테이블 행에 대한 처리 기간 조건을 확인합니다. 예를 들어, 보존 기간이 일주일로 설정된 경우, 정리할 수 있는 테이블 행은 다음 중 하나의 조건을 만족합니다. 

- 필터 열에 DATETIMEOFFSET 데이터 형식이 사용되는 경우, 조건은 `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`입니다.
- 그 밖의 조건은 `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`입니다.

## <a name="data-retention-cleanup-phases"></a>데이터 보존 정리 단계

데이터 보존 정리 작업은 두 단계로 구성됩니다. 
- 검색 단계 - 이 단계에서는 정리 작업을 통해 사용자 데이터베이스 내의 모든 테이블을 식별하여 정리 목록을 작성합니다. 검색은 하루에 한 번 실행합니다.
- 정리 단계 - 이 단계에서 정리는 검색 단계에서 식별된 데이터 보존 기간이 한정된 모든 테이블에 대해 실행됩니다. 테이블에 대해 정리 작업을 수행할 수 없는 경우, 현재 실행에서는 해당 테이블을 건너뛰고 다음 반복에서 다시 시도됩니다. 정리하는 동안 다음 원칙이 사용됩니다.
    - 사용되지 않는 행이 다른 트랜잭션에 의해 잠긴 경우에는 해당 행을 건너뜁니다. 
    - 정리는 기본값 5초 잠금 시간 제한 설정을 사용하여 실행됩니다. 시간 제한 창 내의 테이블에서 잠금을 실행할 수 없는 경우, 현재 실행에서는 해당 테이블을 건너뛰고, 다음 반복에서 다시 시도됩니다.
    - 테이블을 정리하는 동안 오류가 발생하면, 해당 테이블은 건너뛰고 다음 반복에서 선택됩니다.

## <a name="manual-cleanup"></a>수동 정리

테이블의 데이터 보존 설정 및 데이터베이스의 워크로드 특성에 따라, 자동 정리 스레드가 실행되는 동안 사용되지 않는 모든 행을 완전히 제거하지 못할 수 있습니다. 이를 지원하고 사용자가 사용되지 않는 행을 수동으로 제거할 수 있도록 하기 위해, `sys.sp_cleanup_data_retention` 저장 프로시저가 Azure SQL Edge에 도입되었습니다. 

이 저장 프로시저는 3개의 매개 변수를 사용합니다. 
    - 스키마 이름 - 테이블에 대한 소유 스키마의 이름입니다. 필수 매개 변수입니다. 
    - 테이블 이름 - 수동 정리를 실행할 테이블의 이름입니다. 필수 매개 변수입니다. 
    - rowcount (Output) - 출력 변수입니다. 수동 정리 sp에 의해 정리된 행 수를 반환합니다. 선택적 매개 변수입니다. 

다음 예제에서는 테이블 `dbo.data_retention_table`에 대한 수동 정리 sp의 실행을 표시합니다.

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>사용되지 않는 행을 삭제하는 방법

정리 프로세스는 테이블의 인덱스 레이아웃에 따라 달라집니다. 보존 기간이 한정된 모든 테이블에 대한 사용하지 않는 데이터 정리를 수행하는 백그라운드 작업이 만들어집니다. rowstore(B-트리 또는 힙) 인덱스에 대한 정리 논리를 사용해 오래된 행을 더 작은 청크(최대 10K)로 삭제하여 데이터베이스 로그 및 IO 하위 시스템에 대한 압력을 최소화합니다. 정리 논리에서 필요한 B-트리 인덱스를 활용하긴 하지만 보존 기간보다 오래된 행의 삭제 순서를 확실히 보장할 수는 없습니다. 따라서 애플리케이션의 정리 순서를 신뢰하지 마세요.

클러스터형 columnstore에 대한 정리 태스크는 전체 행 그룹을 한 번에 제거하므로(일반적으로 각 그룹에 1백만 개 행 포함) 매우 효율적이며, 데이터가 빠른 속도로 생성되고 만료되는 경우 특히 효율적입니다.

![데이터 보존 정리](./media/data-retention-cleanup/data-retention-cleanup.png)

클러스터형 columnstore 인덱스는 데이터 압축이 뛰어나고 보존 정리가 효율적이므로 작업에서 대량의 데이터를 빠르게 생성하는 시나리오에 적합합니다.

> [!Note]
> B-트리 인덱스 및 힙의 경우, 데이터 보존은 기본 테이블에서 삭제 쿼리를 실행합니다. 이 쿼리는 테이블의 삭제 트리거와 충돌할 수 있습니다. 테이블에서 삭제 트리거를 제거하거나, 삭제 DML 트리거가 있는 테이블에서 데이터 보존을 사용하지 않도록 설정하는 것이 좋습니다.

## <a name="monitoring-data-retention-cleanup"></a>데이터 보존 정리 모니터링

데이터 보존 정책 정리 작업은 Azure SQL Edge의 확장 이벤트(XEvents)를 사용하여 모니터링할 수 있습니다. 확장 이벤트에 대한 자세한 내용은 [XEvents 개요](/sql/relational-databases/extended-events/extended-events)를 참조하세요. 

다음 6개의 확장 이벤트는 정리 작업의 상태를 추적하는 데 도움을 줍니다. 

| 이름 | 설명 |
|------| ------------|
| data_retention_task_started  | 보존 정책이 포함된 테이블의 정리를 위한 백그라운드 작업이 시작될 때 발생합니다. |
| data_retention_task_completed  | 보존 정책이 포함된 테이블의 정리를 위한 백그라운드 작업이 종료될 때 발생합니다. |
| data_retention_task_exception  | 보존 정책이 포함된 테이블의 정리를 위한 백그라운드 작업이 테이블과 관련된 보존 정리 프로세스를 벗어날 때 발생합니다. |
| data_retention_cleanup_started  | 데이터 보존 정책이 있는 테이블의 정리 프로세스가 시작될 때 발생합니다. |
| data_retention_cleanup_exception  | 데이터 보존 정책이 있는 테이블의 정리 프로세스가 실패할 때 발생합니다. |
| data_retention_cleanup_completed  | 데이터 보존 정책이 있는 테이블의 정리 프로세스가 종료될 때 발생합니다. |  

또한 sys.dm_os_ring_buffers 동적 관리 뷰에 `RING_BUFFER_DATA_RETENTION_CLEANUP`이라는 새 링 버퍼 유형이 추가되었습니다. 이 보기를 사용하여 데이터 보존 정리 작업을 모니터링할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
- [데이터 보존 정책](data-retention-overview.md)
- [데이터 보존 정책 사용 및 사용 안 함](data-retention-enable-disable.md)