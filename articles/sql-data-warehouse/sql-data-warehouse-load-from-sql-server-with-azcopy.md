---
title: "SQL Server에서 Azure SQL Data Warehouse로 데이터 로드(PolyBase) | Microsoft Docs"
description: "bcp를 사용하여 SQL Server에서 플랫 파일로 데이터를 내보내고 AZCopy를 사용하여 Azure Blob 저장소로 데이터를 가져오고, PolyBase를 사용하여 Azure SQL 데이터 웨어하우스로 데이터를 수집합니다."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 21b4cc704e271ac220fd606305f8f97c9b2593bb
ms.lasthandoff: 03/22/2017



---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(AZCopy)
SQL Server에서 Azure Blob 저장소로 데이터를 로드하려면 bcp 및 AZCopy 명령줄 유틸리티를 사용합니다. 그 다음 PolyBase 또는 Azure Data Factory를 사용하여 Azure SQL 데이터 웨어하우스로 데이터를 로드합니다. 

## <a name="prerequisites"></a>필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

* SQL 데이터 웨어하우스 데이터베이스
* 설치된 bcp 명령줄 유틸리티
* 설치된 SQLCMD 명령줄 유틸리티

> [!NOTE]
> [Microsoft 다운로드 센터][Microsoft Download Center]에서 bcp 및 sqlcmd 유틸리티를 다운로드할 수 있습니다.
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>SQL 데이터 웨어하우스로 데이터 로드
이 자습서에서는 Azure SQL 데이터 웨어하우스에서 테이블을 만들고 테이블로 데이터를 가져옵니다.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>1단계: Azure SQL 데이터 웨어하우스에서 테이블 만들기
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

> [!NOTE]
> SQL Data Warehouse에서 테이블을 만드는 방법과 WITH 절에서 사용 가능한 옵션에 대한 자세한 내용은 [테이블 개요][Table Overview] 또는 [CREATE TABLE 구문][CREATE TABLE syntax]을 참조하세요.
> 
> 

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
> 해당 bcp.exe는 UTF-8 파일 인코딩을 지원하지 않습니다. bcp.exe 사용 시 파일에 ASCII 파일 또는 UTF-16 인코딩 파일을 사용하세요.
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

다음 결과를 반환해야 합니다.

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
Azure SQL 데이터 웨어하우스는 자동 만들기 또는 통계 자동 업데이트를 아직 지원하지 않습니다. 쿼리에서 최상의 성능을 얻으려면, 데이터를 처음 로드하거나 데이터에 상당한 변화가 발생한 후에 모든 테이블의 모든 열에서 통계가 만들어지는 것이 중요합니다. 통계에 대한 자세한 설명은 개발 항목 그룹의 [통계][Statistics] 항목을 참조하세요. 다음은 이 예제에 로드한 테이블에 대한 통계를 만드는 방법을 간략히 보여주는 예입니다.

sqlcmd 프롬프트에서 다음 CREATE STATISTICS 문을 실행합니다.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>SQL 데이터 웨어하우스에서 데이터 내보내기
이 자습서에서는 SQL 데이터 웨어하우스의 테이블에서 데이터 파일이 만들어집니다. 위에서 만든 데이터를 DimDate2_export.txt라는 새 데이터 파일로 내보냅니다.

### <a name="step-1-export-the-data"></a>1단계: 데이터 내보내기
bcp 유틸리티를 사용하여, 값을 적절하게 대체하는 다음 명령을 사용하여 연결하고 데이터를 내보낼 수 있습니다.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
새 파일을 열어 데이터를 올바르게 내보냈는지 확인할 수 있습니다. 파일의 데이터는 아래 텍스트와 일치해야 합니다.

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
> 분산된 시스템의 특성상 데이터 순서는 SQL 데이터 웨어하우스 데이터베이스에서 동일하지 않을 수 있습니다. 또 다른 옵션은 전체 테이블을 내보내는 것이 아니라 쿼리 추출을 작성하는 bcp의 **queryout** 함수를 사용하는 것입니다.
> 
> 

## <a name="next-steps"></a>다음 단계
로드 개요는 [SQL Data Warehouse로 데이터 로드][Load data into SQL Data Warehouse]를 참조하세요.
더 많은 개발 팁은 [SQL Data Warehouse 개발 개요][SQL Data Warehouse development overview]를 참조하세요.

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

