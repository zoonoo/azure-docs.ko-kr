---
title: '자습서: Azure Data Lake Storage에서 데이터 로드'
description: COPY 문을 사용 하 여 Synapse SQL에 대 한 Azure Data Lake Storage에서 데이터를 로드 합니다.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/07/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 73d19df546f2ff0e9e9180c94567bd334b44bedd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482811"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>Synapse SQL용 Azure Data Lake Storage에서 데이터 로드

이 가이드에서는 [COPY 문을](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 사용 하 여 Azure Data Lake Storage에서 데이터를 로드 하는 방법을 설명 합니다. 모든 인증 방법에서 COPY 문을 사용 하는 방법에 대 한 빠른 예제를 보려면 [SYNAPSE SQL을 사용 하 여 데이터를 안전 하 게 로드](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)설명서를 참조 하세요.

> [!NOTE]  
> COPY 문에 사용자 의견을 제공 하거나 문제를 보고 하려면 메일 그룹에 전자 메일을 보냅니다 sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Azure Data Lake Storage에서 데이터를 로드 하는 대상 테이블을 만듭니다.
> * 데이터 웨어하우스로 데이터를 로드 하는 COPY 문을 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 시작하기 전에 최신 버전의 SSMS([SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest))를 다운로드하여 설치합니다.

이 자습서를 실행하려면 다음이 필요합니다.

* SQL 풀. [SQL 풀 만들기 및 데이터 쿼리](create-data-warehouse-portal.md)를 참조하세요.
* Data Lake Storage 계정. [Azure Data Lake Storage 시작](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조하세요. 이 저장소 계정에 대해 저장소 계정 키, SAS (공유 액세스 서명) 키, Azure Directory 응용 프로그램 사용자 또는 저장소 계정에 대 한 적절 한 Azure 역할이 있는 AAD 사용자를 로드 하려면 다음 자격 증명 중 하나를 구성 하거나 지정 해야 합니다.

## <a name="create-the-target-table"></a>대상 테이블 만들기

SQL 풀에 연결 하 고 로드할 대상 테이블을 만듭니다. 이 예에서는 product 차원 테이블을 만듭니다.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>COPY 문 만들기

SQL 풀에 연결 하 고 COPY 문을 실행 합니다. 예제에 대 한 전체 목록을 보려면 [SYNAPSE SQL을 사용 하 여 데이터를 안전 하 게 로드](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)설명서를 참조 하세요.

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Columnstore 압축을 최적화합니다.

기본적으로 테이블은 클러스터형 columnstore 인덱스로 정의됩니다. 로드를 완료한 후 데이터 행 일부는 columnstore로 압축되지 않을 수 있습니다.  여기에는 다양한 이유가 있습니다. 자세한 내용은 [Columnstore 인덱스 관리](sql-data-warehouse-tables-index.md)를 참조하세요.

로드 후 쿼리 성능과 columnstore 압축을 최적화하려면 모든 행을 압축하기 위해 columnstore 인덱스를 강제 적용할 테이블을 다시 빌드합니다.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>통계를 최적화합니다.

로드 직후 단일 열 통계를 만드는 것이 가장 좋습니다. 통계에 대한 몇 가지 선택 사항이 있습니다. 예를 들어 모든 열에 단일 열 통계를 만드는 경우 모든 통계를 다시 작성하는 데 시간이 오래 걸릴 수 있습니다. 쿼리 조건자에 위치하지 않을 특정 열에 대해 알고 있다면 이들 열에 대한 통계 생성 과정은 건너뛸 수 있습니다.

단일 열 통계를 모든 테이블의 모든 열에 대해 만들기로 결정한 경우 [통계](sql-data-warehouse-tables-statistics.md) 문서에 저장된 프로시저 코드 샘플 `prc_sqldw_create_stats`를 사용할 수 있습니다.

다음 예제는 통계를 만들기 위한 좋은 출발점이 됩니다. 차원 테이블의 각 열과 팩트 테이블의 각 조인 열의 단일 열 통계를 생성합니다. 이후 언제라도 다른 팩트 테이블 열에 단일 또는 여러 열 통계를 추가할 수 있습니다.

## <a name="achievement-unlocked"></a>목표를 달성했습니다!

데이터 웨어하우스에 데이터를 성공적으로 로드했습니다. 잘 하셨습니다!

## <a name="next-steps"></a>다음 단계
데이터 로드는 Azure Synapse Analytics를 사용하여 데이터 웨어하우스 솔루션을 개발하는 첫 번째 단계입니다. 개발 리소스를 확인하세요.

> [!div class="nextstepaction"]
> [데이터 웨어하우징용 테이블을 개발하는 방법 알아보기](sql-data-warehouse-tables-overview.md)

예제 및 참조를 로드 하는 방법에 대 한 자세한 내용은 다음 설명서를 참조 하세요.
- [COPY 문 참조 설명서](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [각 인증 방법에 대 한 복사 예제](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [단일 테이블에 대 한 빠른 시작 복사](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
