---
title: 보존 정책을 사용 하 여 기록 데이터 관리-Azure SQL Edge
description: Azure SQL Edge에서 보존 정책을 사용 하 여 기록 데이터를 관리 하는 방법을 알아봅니다.
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392370"
---
# <a name="manage-historical-data-with-retention-policy"></a>보존 정책을 사용 하 여 기록 데이터 관리

사용자가 테이블 및 데이터베이스에 대해 유연한 에이징 정책을 만들 수 있도록 데이터베이스 및 기본 테이블 중 하나에서 데이터 보존을 개별적으로 사용 하도록 설정할 수 있습니다. 데이터 보존을 적용 하는 작업은 간단 합니다. 테이블을 만드는 동안 또는 alter table 작업의 일부로 하나의 매개 변수만 설정 해야 합니다. 

데이터베이스 및 기본 테이블에 대 한 데이터 보존 정책을 정의 한 후에는 백그라운드 시간 타이머 태스크가 실행 되어 데이터 보존에 사용 되는 테이블에서 사용 되지 않는 레코드를 제거 합니다. 시스템에서 예약 하 고 실행 하는 백그라운드 태스크에서는 일치 하는 행 및 테이블에서 제거 된 항목의 id가 투명 하 게 발생 합니다. 테이블 정의의로 사용 된 열을 기반으로 테이블 행에 대 한 사용 기간 조건을 확인 합니다 `filter_column` . 예를 들어 보존 기간이 1 주일으로 설정 된 경우 정리에 적합 한 테이블 행은 다음 조건 중 하나를 만족 합니다. 

- 필터 열에 DATETIMEOFFSET 데이터 형식이 사용 되는 경우 조건은입니다. `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- 그 밖의 조건은 `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>데이터 보존 정리 단계

데이터 보존 정리 작업은 두 단계로 구성 됩니다. 
- 검색 단계-이 단계에서는 정리 작업을 통해 사용자 데이터베이스 내의 모든 테이블을 식별 하 여 정리 목록을 작성 합니다. 검색은 하루에 한 번 실행 됩니다.
- 정리 단계-이 단계에서 정리는 검색 단계에서 식별 된 유한 데이터 보존이 있는 모든 테이블에 대해 실행 됩니다. 테이블에 대해 정리 작업을 수행할 수 없는 경우 현재 실행에서 해당 테이블을 건너뛰고 다음 반복에서 다시 시도 됩니다. 정리 하는 동안 다음 원칙이 사용 됩니다.
    - 사용 되지 않는 행이 다른 트랜잭션에 의해 잠긴 경우에는 해당 행을 건너뜁니다. 
    - 정리는 기본 5 초 잠금 제한 시간 설정을 사용 하 여 실행 됩니다. 제한 시간 창 내에서 테이블에 대 한 잠금을 획득할 수 없는 경우 현재 실행에서 테이블이 생략 되며 다음 반복에서 다시 시도 됩니다.
    - 테이블을 정리 하는 동안 오류가 발생 하면 해당 테이블은 건너뛰고 다음 반복에서 선택 됩니다.

## <a name="manual-cleanup"></a>수동 정리

테이블의 데이터 보존 설정 및 데이터베이스 작업의 특성에 따라 자동 정리 스레드가 실행 중 사용 되지 않는 모든 행을 완전히 제거 하지 못할 수 있습니다. 이를 지원 하 고 사용자가 사용 되지 않는 행을 수동으로 제거 하도록 허용 하기 위해 `sys.sp_cleanup_data_retention` 저장 프로시저가 AZURE SQL Edge에 도입 되었습니다. 

이 저장 프로시저는 세 개의 매개 변수를 사용 합니다. 
    - 스키마 이름-테이블에 대 한 소유 하는 스키마의 이름입니다. 필수 매개 변수입니다. 
    - 테이블 이름-수동 정리가 실행 되는 테이블의 이름입니다. 필수 매개 변수입니다. 
    - 행 개수 (출력)-출력 변수입니다. 수동 정리 sp에서 정리 된 행 수를 반환 합니다. 선택적 매개 변수입니다. 

다음 예에서는 테이블에 대 한 수동 정리 sp를 실행 하는 방법을 보여 줍니다 `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>사용 되지 않는 행을 삭제 하는 방법

정리 프로세스는 테이블의 인덱스 레이아웃에 따라 달라 집니다. 고정 기간이 한정 된 모든 테이블에 대해 사용 되지 않는 데이터 정리를 수행 하는 백그라운드 작업이 생성 됩니다. Rowstore (B-트리 또는 힙) 인덱스의 정리 논리는 더 작은 청크 (최대 10K)에서 오래 된 행을 삭제 하 여 데이터베이스 로그 및 IO 하위 시스템에 대 한 압력을 최소화 합니다. 정리 논리에서 필요한 B-트리 인덱스를 활용하긴 하지만 보존 기간보다 오래된 행의 삭제 순서를 확실히 보장할 수는 없습니다. 따라서 애플리케이션의 정리 순서를 신뢰하지 마세요.

클러스터형 columnstore에 대 한 정리 태스크는 전체 행 그룹을 한 번에 (일반적으로 각각 100만 행을 포함) 제거 하므로 매우 효율적입니다. 특히 데이터가 생성 되 고 높은 속도로 에이징됩니다.

![데이터 보존 정리](./media/data-retention-cleanup/data-retention-cleanup.png)

뛰어난 데이터 압축 및 효율적인 보존 정리를 사용 하면 작업에서 많은 양의 데이터를 신속 하 게 생성 하는 시나리오에 대해 클러스터형 columnstore 인덱스를 완벽 하 게 선택할 수 있습니다.

> [!Note]
> B-트리 인덱스 및 힙의 경우 데이터 보존은 테이블에서 delete 트리거와 충돌 하는 기본 테이블에 대 한 delete 쿼리를 실행 합니다. 테이블에서 delete 트리거를 제거 하거나 delete DML 트리거가 있는 테이블에 대 한 데이터 보존을 사용 하지 않도록 설정 하는 것이 좋습니다.

## <a name="monitoring-data-retention-cleanup"></a>데이터 보존 정리 모니터링

Azure SQL Edge에서 확장 이벤트 (Xevent)를 사용 하 여 데이터 보존 정책 정리 작업을 모니터링할 수 있습니다. 확장 이벤트에 대 한 자세한 내용은 [Xevent 개요](/sql/relational-databases/extended-events/extended-events)를 참조 하세요. 

다음 6 개의 확장 이벤트는 정리 작업의 상태를 추적 하는 데 도움이 됩니다. 

| 이름 | Description |
|------| ------------|
| data_retention_task_started  | 보존 정책이 있는 테이블의 정리를 위한 백그라운드 작업이 시작 될 때 발생 합니다. |
| data_retention_task_completed  | 보존 정책이 있는 테이블의 정리를 위한 백그라운드 작업이 종료 될 때 발생 합니다. |
| data_retention_task_exception  | 보존 정책이 있는 테이블의 정리를 위한 백그라운드 작업이 테이블과 관련 된 보존 정리 프로세스 외부에서 실패할 때 발생 합니다. |
| data_retention_cleanup_started  | 데이터 보존 정책이 있는 테이블의 정리 프로세스가 시작 될 때 발생 합니다. |
| data_retention_cleanup_exception  | 보존 정책이 있는 테이블의 정리 프로세스가 실패 하는 경우 발생 합니다. |
| data_retention_cleanup_completed  | 데이터 보존 정책이 있는 테이블의 정리 프로세스가 종료 될 때 발생 합니다. |  

또한 동적 관리 뷰에 sys.dm_os_ring_buffers 이라는 새 링 버퍼 유형이 `RING_BUFFER_DATA_RETENTION_CLEANUP` 추가 되었습니다. 이 보기를 사용 하 여 데이터 보존 정리 작업을 모니터링할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
- [데이터 보존 정책](data-retention-overview.md)
- [데이터 보존 정책 사용 및 사용 안 함](data-retention-enable-disable.md)