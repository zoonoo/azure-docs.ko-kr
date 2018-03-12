---
title: "bcp를 사용하여 SQL Data Warehouse로 데이터 로드 | Microsoft Docs"
description: "bcp 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 146c6fdada651551c05b2cbcadc3e1248a40b613
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
# <a name="load-data-with-bcp"></a>bcp를 사용하여 데이터 로드

**[bcp](/sql/tools/bcp-utility.md)** 는 명령줄 대량 로드 유틸리티로, SQL Server, 데이터 파일 및 SQL Data Warehouse 간에 데이터를 복사할 수 있습니다. bcp 유틸리티를 사용하여 SQL Data Warehouse 테이블로 많은 수의 행을 가져오거나, 또는 SQL Server 테이블에서 데이터 파일로 데이터를 내보냅니다. Queryout 옵션을 사용하는 경우를 제외하고, bcp를 사용하려면 TRANSACT-SQL 지식이 없어도 됩니다.

bcp는 SQL Data Warehouse 데이터베이스 내부 및 외부로 더 작은 데이터 집합을 이동하는 빠르고 쉬운 방법입니다. bcp를 통한 로드/추출을 권장하는 정확한 크기의 데이터는 Azure에 연결된 네트워크에 따라 다릅니다.  작은 차원 테이블은 bcp로 쉽게 로드 및 추출될 수 있습니다. 그러나 bcp가 아닌 Polybase는 대량의 데이터를 로드하고 추출하는 데 권장되는 도구입니다. PolyBase는 SQL Data Warehouse의 방대한 병렬 처리 아키텍처를 위해 디자인되었습니다.

bcp를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* 명령줄 유틸리티를 사용하여 SQL Data Warehouse에 데이터를 로드합니다.
* 명령줄 유틸리티를 사용하여 SQL Data Warehouse에서 데이터를 추출합니다.

이 자습서는 다음을 수행합니다.

* bcp in 명령을 사용하여 테이블로 데이터 가져오기
* bcp out 명령을 사용하여 테이블에서 데이터 내보내기

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

* SQL Data Warehouse 데이터베이스
* bcp 및 sqlcmd 명령줄 유틸리티 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=36433)에서 다운로드할 수 있습니다. 

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII 또는 UTF-16 형식 데이터
사용자의 데이터로 이 자습서를 수행하는 경우에는, bcp가 UTF-8을 지원하지 않으므로, 데이터에 ASCII 또는 UTF-16 인코딩을 사용해야 합니다. 

PolyBase는 UTF-8을 지원하지만 아직 UTF-16은 지원하지 않습니다. 데이터 내보내기에 bcp를 사용하고 데이터 로딩에 PolyBase를 사용하려면 SQL Server에서 내보낸 후에 UTF-8로 데이터를 변환해야 합니다. 

## <a name="import-data-into-sql-data-warehouse"></a>SQL Data Warehouse로 데이터 로드
이 자습서에서는 Azure SQL Data Warehouse에서 테이블을 만들고 테이블로 데이터를 가져옵니다.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>1단계: Azure SQL Data Warehouse에서 테이블 만들기
명령 프롬프트에서 sqlcmd를 사용하여 다음 쿼리를 실행하여 인스턴스에 테이블을 만듭니다.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

테이블 만들기에 대한 자세한 내용은 [테이블 개요](sql-data-warehouse-tables-overview.md) 또는 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md) 구문을 참조하세요.
 

### <a name="step-2-create-a-source-data-file"></a>2단계: 원본 데이터 파일 만들기
메모장을 열고 다음 데이터 줄을 새 텍스트 파일에 복사한 다음 이 파일을 로컬 임시 디렉터리 C:\Temp\DimDate2.txt에 저장합니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 해당 bcp.exe는 UTF-8 파일 인코딩을 지원하지 않습니다. bcp.exe 사용 시 ASCII 파일 또는 UTF-16 인코딩 파일을 사용합니다.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>3단계: 데이터 연결 및 가져오기
bcp를 사용하여, 연결하고 값을 적절하게 대체하는 다음 명령을 사용하여 데이터를 가져올 수 있습니다.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

sqlcmd에서 다음 쿼리를 실행하여 데이터가 로드되었음을 확인할 수 있습니다.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

쿼리는 다음 결과를 반환해야 합니다.

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>4단계: 새로 로드한 데이터에 대한 통계 만들기
데이터를 로드한 후 마지막 단계는 통계를 만들거나 업데이트하는 것입니다. 이를 통해 쿼리 성능을 향상시킬 수 있습니다. 자세한 내용은 [통계](sql-data-warehouse-tables-statistics.md)를 참조하세요. 다음 sqlcmd 예제에서는 테이블에서 새로 로드된 데이터를 포함하는 통계를 만듭니다.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>SQL Data Warehouse에서 데이터 내보내기
이 자습서에서는 SQL Data Warehouse의 테이블에서 데이터 파일을 만듭니다. 이전 섹션에서 가져온 데이터를 내보냅니다. 결과는 DimDate2_export.txt라는 파일로 이동합니다.

### <a name="step-1-export-the-data"></a>1단계: 데이터 내보내기
bcp 유틸리티를 사용하여, 값을 적절하게 대체하는 다음 명령을 사용하여 연결하고 데이터를 내보낼 수 있습니다.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
새 파일을 열어 데이터를 올바르게 내보냈는지 확인할 수 있습니다. 파일의 데이터는 다음 텍스트와 일치해야 합니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 분산된 시스템의 특성상 데이터 순서는 SQL Data Warehouse 데이터베이스에서 동일하지 않을 수 있습니다. 또 다른 옵션은 전체 테이블을 내보내는 것이 아니라 쿼리 추출을 작성하는 bcp의 **queryout** 함수를 사용하는 것입니다.
> 
> 

## <a name="next-steps"></a>다음 단계
로드 프로세스를 디자인하려면 [로드 개요](sql-data-warehouse-design-elt-data-loading)를 참조하세요.  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
