---
title: ETL 대신, ELT 디자인
description: Azure Synapse Analytics 내에서 SQL Analytics에 대 한 유연한 데이터 로드 전략 구현
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bd356fba557d61f083e811c8763b4e7cf9805fbb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199940"
---
# <a name="data-loading-strategies-for-data-warehousing"></a>데이터 웨어하우징에 대 한 데이터 로드 전략

기존 SMP 데이터 웨어하우스는 데이터를 로드 하기 위한 ETL (추출, 변환 및 로드) 프로세스를 사용 합니다. Azure Synapse Analytics의 SQL 풀은 계산 및 저장소 리소스의 확장성과 유연성을 활용 하는 MPP (대규모 parallel processing) 아키텍처를 사용 합니다. 이 아키텍처에서는 ELT(추출, 로드 및 변환) 프로세스를 통해 MPP를 활용할 수 있으며, 데이터를 로드하기 전에 리소스를 사용하여 변환할 필요가 없습니다. SQL 풀은 BCP 및 SQL 대량 복사 API와 같은 널리 사용 되는 SQL Server 옵션을 비롯 한 여러 로드 방법을 지원 하지만, 데이터를 로드 하는 가장 빠르고 확장성 있는 방법은 PolyBase 외부 테이블 및 [COPY 문](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (미리 보기)을 사용 하는 것입니다. PolyBase와 COPY 문을 사용 하 여 Azure Blob storage에 저장 된 외부 데이터 나 T-sql 언어를 통해 Azure Data Lake Store에 액세스할 수 있습니다. 로드 시 유연성을 극대화 하려면 COPY 문을 사용 하는 것이 좋습니다.

> [!NOTE]  
> COPY 문은 현재 공개 미리 보기로 제공됩니다. 사용자 의견을 제공 하려면 다음 메일 그룹에 전자 메일을 보내세요. sqldwcopypreview@service.microsoft.com.


> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>ELT란 무엇인가요?

ELT (추출, 로드 및 변환)는 데이터를 원본 시스템에서 추출 하 여 데이터 웨어하우스로 로드 한 다음 변환 하는 프로세스입니다. 

ELT를 구현 하는 기본 단계는 다음과 같습니다.

1. 원본 데이터를 텍스트 파일로 추출합니다.
2. Azure Blob Storage 또는 Azure Data Lake Store에 데이터를 둡니다.
3. 로드할 데이터를 준비합니다.
4. PolyBase 또는 복사 명령을 사용 하 여 준비 테이블에 데이터를 로드 합니다. 
5. 데이터를 변환합니다.
6. 프로덕션 테이블에 데이터를 삽입합니다.


PolyBase 로드 자습서는 [polybase를 사용 하 여 Azure blob storage에서 데이터 로드를](load-data-from-azure-blob-storage-using-polybase.md)참조 하세요.

자세한 정보는 [로드 패턴 블로그](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)를 참조하세요. 


## <a name="1-extract-the-source-data-into-text-files"></a>1. 텍스트 파일로 원본 데이터 추출

원본 시스템에서 데이터를 추출하는 방식은 스토리지 위치에 따라 달라집니다.  목표는 데이터를 PolyBase로 이동 하는 것이 고, 지원 되는 구분 된 텍스트 또는 CSV 파일을 복사 하는 것입니다. 

### <a name="polybase-and-copy-external-file-formats"></a>PolyBase 및 외부 파일 형식 복사

PolyBase와 COPY 문을 사용 하면 u t f-8과 u t f-16으로 인코딩된 분리 된 텍스트 또는 CSV 파일에서 데이터를 로드할 수 있습니다. 구분 기호로 분리 된 텍스트 또는 CSV 파일 외에도 ORC 및 Parquet와 같은 Hadoop 파일 형식에서 로드 됩니다. PolyBase와 COPY 문은 Gzip 및 Snappy 압축 파일에서 데이터를 로드할 수도 있습니다. 확장 ASCII, 고정 폭 형식 및 WinZip 또는 XML과 같은 중첩 형식은 지원 되지 않습니다. SQL Server에서 내보내는 경우 [bcp 명령줄 도구](/sql/tools/bcp-utility?view=azure-sqldw-latest) 를 사용 하 여 데이터를 구분 된 텍스트 파일로 내보낼 수 있습니다. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. 데이터를 Azure Blob storage 또는 Azure Data Lake Store로 전환

Azure storage에 데이터를 배치 하려면 [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md) 또는 [Azure Data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md)로 이동할 수 있습니다. 어느 위치에서든 데이터를 텍스트 파일에 저장해야 합니다. PolyBase와 COPY 문은 어느 위치에서 나 로드할 수 있습니다.

데이터를 Azure Storage로 이동하는 데 사용할 수 있는 도구와 서비스는 다음과 같습니다.

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 서비스는 네트워크 처리량, 성능 및 예측 가능성을 개선합니다. ExpressRoute는 Azure에 대한 전용 프라이빗 연결을 통해 데이터를 라우팅하는 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 데이터를 라우팅하지 않습니다. 이 연결은 공용 인터넷을 통한 일반적인 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.
- [AZCopy 유틸리티](../storage/common/storage-moving-data.md)는 공용 인터넷을 통해 Azure Storage로 데이터를 이동합니다. 이는 데이터 크기가 10TB 미만인 경우에 작동합니다. AZCopy를 사용하여 정기적으로 로드를 수행하려면 네트워크 속도를 테스트하여 가능한지 확인하세요. 
- [ADF(Azure Data Factory)](../data-factory/introduction.md)에는 로컬 서버에 설치할 수 있는 게이트웨이가 있습니다. 그런 다음 파이프라인을 만들어 로컬 서버에서 Azure Storage로 데이터를 이동할 수 있습니다. SQL Analytics에서 Data Factory를 사용 하려면 [Sql analytics에 대 한 데이터 로드](/azure/data-factory/load-azure-sql-data-warehouse)를 참조 하세요.


## <a name="3-prepare-the-data-for-loading"></a>3. 로드 하기 위해 데이터 준비

로드 하기 전에 저장소 계정에서 데이터를 준비 하 고 정리 해야 할 수 있습니다. 데이터가 원본에 있는 동안, 데이터를 텍스트 파일로 내보낼 때 또는 Azure Storage로 이동한 후 데이터 준비를 수행할 수 있습니다.  가능한 경우 프로세스의 초기에 데이터로 작업하는 것이 가장 쉽습니다.  

### <a name="define-external-tables"></a>외부 테이블 정의

PolyBase를 사용 하는 경우 로드 하기 전에 데이터 웨어하우스에서 외부 테이블을 정의 해야 합니다. COPY 문에는 외부 테이블이 필요 하지 않습니다. PolyBase는 외부 테이블을 사용하여 Azure Storage의 데이터를 정의하고 액세스합니다. 데이터베이스 뷰와 비슷한 외부 테이블은 테이블 스키마를 포함하며 데이터 웨어하우스 외부에 저장된 데이터를 가리킵니다. 

외부 테이블 정의에는 데이터 원본, 텍스트 파일 형식 및 테이블 정의를 지정하는 것이 포함됩니다. 필요한 t-sql 구문 항목은 다음과 같습니다.
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

Parquet를 로드 하는 경우 SQL 데이터 형식 매핑은 다음과 같습니다.

| **Parquet 데이터 형식** | **SQL 데이터 형식** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       SMALLINT        |     SMALLINT      |
|          int          |        int        |
|        bigint         |      bigint       |
|        boolean        |        bit        |
|        double         |       float       |
|         float         |       real        |
|        double         |       money       |
|        double         |    smallmoney     |
|        string         |       nchar       |
|        string         |     nvarchar      |
|        string         |       char        |
|        string         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|       timestamp       |       date        |
|       timestamp       |   smalldatetime   |
|       timestamp       |     datetime2     |
|       timestamp       |     datetime      |
|       timestamp       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

외부 개체를 만드는 예는 로드 자습서의 [외부 테이블 만들기](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) 단계를 참조하세요.

### <a name="format-text-files"></a>텍스트 파일 형식 지정

PolyBase를 사용 하는 경우 정의 된 외부 개체는 외부 테이블 및 파일 형식 정의를 사용 하 여 텍스트 파일의 행을 정렬 해야 합니다. 텍스트 파일의 각 행에 있는 데이터는 테이블 정의와 정렬되어야 합니다.
텍스트 파일의 형식을 지정하려면:

- 데이터가 비관계형 원본에서 오는 경우 행과 열로 변환해야 합니다. 데이터가 관계형 원본에서 오건 비관계형 원본에서 오건, 데이터를 로드하려고 하는 테이블에 대한 열 정의와 정렬되도록 데이터를 변환해야 합니다. 
- 대상 테이블의 열과 데이터 형식에 맞게 텍스트 파일의 데이터 형식을 지정 합니다. 외부 텍스트 파일의 데이터 형식과 데이터 웨어하우스 테이블의 데이터 형식이 정렬되지 않으면 로드 중에 행이 거부됩니다.
- 종결자로 텍스트 파일의 필드를 구분합니다.  원본 데이터에서 찾을 수 없는 문자 또는 문자 시퀀스를 사용해야 합니다. [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)으로 지정한 종결자를 사용하세요.


## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. PolyBase 또는 COPY 문을 사용 하 여 데이터 로드

데이터는 준비 테이블로 로드하는 것이 가장 좋습니다. 스테이징 테이블을 사용하면 프로덕션 테이블에 영향을 주지 않고 오류를 처리할 수 있습니다. 또한 준비 테이블을 사용 하면 데이터를 프로덕션 테이블에 삽입 하기 전에 데이터 변환을 위해 SQL 풀 MPP를 사용할 수 있습니다. COPY를 사용 하 여 준비 테이블에 로드 하는 경우이 테이블을 미리 만들어야 합니다.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>PolyBase 및 COPY 문으로 로드 하기 위한 옵션

PolyBase를 사용하여 데이터를 로드하려는 경우 다음 로드 옵션 중 하나를 사용할 수 있습니다.

- [T-SQL을 이용한 PolyBase](load-data-from-azure-blob-storage-using-polybase.md): Azure Blob Storage 또는 Azure Data Lake Store에 데이터가 있을 경우 효과적입니다. 로드 프로세스를 가장 잘 제어할 수 있지만, 외부 데이터 개체를 정의해야 합니다. 다른 방법에서는 원본 테이블을 대상 테이블에 매핑할 때 배후에서 이러한 개체를 정의합니다.  T-SQL 로드를 조정하려면 Azure Data Factory, SSIS 또는 Azure 함수를 사용할 수 있습니다. 
- [SSIS를 이용한 PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse): 원본 데이터가 SQL Server(SQL Server 온-프레미스 또는 클라우드)에 있을 경우 효과적입니다. SSIS는 대상 테이블 매핑에 대해 원본을 정의하고 로드를 조정합니다. 이미 SSIS 패키지가 있는 경우 새 데이터 웨어하우스 대상으로 작업하도록 패키지를 수정할 수 있습니다. 
- [PolyBase와 COPY 문은 Azure Data Factory (ADF)를 사용 하 여](sql-data-warehouse-load-with-data-factory.md) 다른 오케스트레이션 도구입니다.  파이프라인을 정의하고 작업을 예약합니다. 
- [Azure Databricks polybase](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) 는 테이블에서 Databricks 데이터 프레임로 데이터를 전송 하 고 polybase를 사용 하 여 Databricks 데이터 프레임의 테이블에 데이터를 기록 합니다.

### <a name="other-loading-options"></a>기타 로드 옵션

PolyBase와 COPY 문 외에도 [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) 또는 [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)를 사용할 수 있습니다. bcp는 Azure Blob storage를 거치지 않고 데이터베이스에 직접 로드 되며, 작은 로드에만 사용 됩니다. 이러한 옵션의 로드 성능은 PolyBase와 COPY 문 보다 속도가 느립니다. 


## <a name="5-transform-the-data"></a>5. 데이터 변환

데이터가 스테이징 테이블에 있는 동안, 워크로드에 필요한 변환을 수행하세요 그런 다음 데이터를 프로덕션 테이블로 이동하세요.


## <a name="6-insert-the-data-into-production-tables"></a>6. 프로덕션 테이블에 데이터 삽입

INSERT INTO ... SELECT 문은 준비 테이블에서 영구 테이블로 데이터를 이동 합니다. 

ETL 프로세스를 설계할 때 작은 테스트 샘플에서 프로세스를 실행해 보세요. 테이블에서 파일로 1000개 행을 추출하여 Azure로 이동한 다음 스테이징 테이블에 로드해 보세요. 


## <a name="partner-loading-solutions"></a>파트너 로드 솔루션

상당수의 파트너에게는 로드 솔루션이 있습니다. 자세한 내용을 알아보려면 [솔루션 파트너](sql-data-warehouse-partner-business-intelligence.md) 목록을 참조하세요. 


## <a name="next-steps"></a>다음 단계

설명서를 로드하려면 [데이터를 로드하기 위한 지침](guidance-for-loading-data.md)을 참조하세요.
