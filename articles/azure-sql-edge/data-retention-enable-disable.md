---
title: 데이터 보존 정책 사용 및 사용 안 함-Azure SQL Edge
description: Azure SQL Edge에서 데이터 보존 정책을 사용 및 사용 하지 않도록 설정 하는 방법을 알아봅니다.
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: ee2d65d66caef5cd9405d6e3d0e094de2e30ae87
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902502"
---
# <a name="enable-and-disable-data-retention-policies"></a>데이터 보존 정책 사용 및 사용 안 함

이 항목에서는 데이터베이스 및 테이블에 대 한 데이터 보존 정책을 사용 하거나 사용 하지 않도록 설정 하는 방법에 대해 설명 합니다. 

## <a name="enable-data-retention-for-a-database"></a>데이터베이스에 대 한 데이터 보존 사용

다음 예에서는 [Alter database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)를 사용 하 여 데이터 보존을 설정 하는 방법을 보여 줍니다.

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>데이터베이스에 대해 데이터 보존이 사용 되는지 확인

다음 명령을 사용 하 여 데이터베이스에 데이터 보존이 사용 되는지 여부를 확인할 수 있습니다.
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>테이블에 대 한 데이터 보존 사용

데이터를 자동으로 제거 하려는 각 테이블에 대해 데이터 보존을 사용 하도록 설정 해야 합니다. 데이터베이스와 테이블에 데이터 보존이 설정 되어 있으면 백그라운드 시스템 태스크에서 테이블을 정기적으로 검색 하 여 사용 되지 않는 (오래 된) 행을 식별 하 고 삭제 합니다. [Create table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 또는 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)을 사용 하 여 테이블을 만드는 동안 테이블에서 데이터 보존을 사용 하도록 설정할 수 있습니다.

다음 예에서는 [Create table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql)을 사용 하 여 테이블에 대 한 데이터 보존을 설정 하는 방법을 보여 줍니다. 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

`WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )`Create table 명령의 일부는 테이블에 대 한 데이터 보존을 설정 합니다. 이 명령은 다음과 같은 필수 매개 변수를 사용 합니다. 

- DATA_DELETION-데이터 보존이 설정 되었는지 여부를 나타냅니다.
- 테이블의 열에 FILTER_COLUMN-행이 사용 되지 않을 지 여부를 확인 하는 데 사용 됩니다. 필터 열은 다음 데이터 형식의 열만 될 수 있습니다. 
    - Date
    - SmallDateTime
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD-정수 값 뒤에 단위 설명자가 있습니다. 허용 되는 단위는 일, 일, 주, 주, 월, 월, 연도 및 연도입니다.

다음 예에서는 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)을 사용 하 여 테이블에 대 한 데이터 보존을 설정 하는 방법을 보여 줍니다.  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>테이블에 데이터 보존이 사용 되는지 확인

다음 명령을 사용 하 여 데이터 보존이 활성화 된 테이블을 확인할 수 있습니다.

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Data_retention_period =-1 및 data_retention_period_unit의 값은 테이블에 데이터 보존이 설정 되어 있지 않음을 나타냅니다.

다음 쿼리를 사용 하 여 데이터 보존에 대 한 filter_column으로 사용 되는 열을 식별할 수 있습니다. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>Corelating DB 및 테이블 데이터 보존 설정

데이터베이스 및 테이블에 대 한 데이터 보존 설정은 테이블에서 오래 된 행의 자동 정리가이 실행 되는지 여부를 결정 하는 데 함께 사용 됩니다. 

|데이터베이스 옵션 | 테이블 옵션 | 동작 |
|----------------|--------------|----------|
| OFF | OFF | 데이터 보존 정책을 사용 하지 않도록 설정 하 고 오래 된 레코드의 자동 및 수동 정리를 모두 사용 하지 않도록 설정 했습니다.|
| OFF | 켜기  | 테이블에 대해 데이터 보존 정책을 사용 하도록 설정 되어 있습니다. 사용 되지 않는 레코드의 자동 정리가 비활성화 되었지만 수동 정리 메서드를 사용 하 여 사용 되지 않는 레코드를 정리할 수 있습니다. |
| 켜기 | OFF | 데이터베이스 수준에서 데이터 보존 정책을 사용 하도록 설정 합니다. 그러나이 옵션은 테이블 수준에서 사용 하지 않도록 설정 되어 있으므로 오래 된 행의 보존 기반 정리가 없습니다.|
| 켜기 | 켜기 | 데이터베이스와 테이블 모두에 대해 데이터 보존 정책을 사용 하도록 설정 합니다. 사용 되지 않는 레코드의 자동 정리가 사용 됩니다. |

## <a name="disable-data-retention-on-a-table"></a>테이블에서 데이터 보존 사용 안 함 

[Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)을 사용 하 여 테이블에서 데이터 보존을 사용 하지 않도록 설정할 수 있습니다. 다음 명령을 사용 하 여 테이블에 대 한 데이터 보존을 사용 하지 않도록 설정할 수 있습니다.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>데이터베이스에서 데이터 보존 사용 안 함

[Alter database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)를 사용 하 여 테이블에서 데이터 보존을 사용 하지 않도록 설정할 수 있습니다. 다음 명령을 사용 하 여 데이터베이스에 대 한 데이터 보존을 사용 하지 않도록 설정할 수 있습니다.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>다음 단계
- [데이터 보존 및 자동 데이터 제거](data-retention-overview.md)
- [보존 정책을 사용 하 여 기록 데이터 관리](data-retention-cleanup.md)
