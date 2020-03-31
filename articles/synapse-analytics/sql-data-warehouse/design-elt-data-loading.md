---
title: ETL 대신 ELT 를 설계합니다.
description: Azure 시냅스 분석 내에서 SQL 분석을 위한 유연한 데이터 로드 전략 구현
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0111f3edc3ab7a681e22153828d1bb430a14e57b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348996"
---
# <a name="data-loading-strategies-for-data-warehousing"></a>데이터 웨어하우징을 위한 데이터 로딩 전략

기존의 SMP 데이터 웨어하우스는 데이터를 로드하기 위해 추출, 변환 및 로드(ETL) 프로세스를 사용합니다. Azure Synapse Analytics의 SQL 풀에는 컴퓨팅 및 저장소 리소스의 확장성과 유연성을 활용하는 MPP(병렬 처리) 아키텍처가 있습니다. 이 아키텍처에서는 ELT(추출, 로드 및 변환) 프로세스를 통해 MPP를 활용할 수 있으며, 데이터를 로드하기 전에 리소스를 사용하여 변환할 필요가 없습니다. SQL 풀은 BCP 및 SQL BulkCopy API와 같은 인기 있는 SQL Server 옵션을 포함한 많은 로딩 방법을 지원하지만 데이터를 로드하는 가장 빠르고 확장 가능한 방법은 PolyBase 외부 테이블과 [COPY 문(미리](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 보기)을 통해 서하는 것입니다. PolyBase 및 COPY 문을 사용하면 T-SQL 언어를 통해 Azure Blob 저장소 또는 Azure Data Lake 스토어에 저장된 외부 데이터에 액세스할 수 있습니다. 로드 할 때 가장 유연하 게 COPY 문을 사용 하는 것이 좋습니다.

> [!NOTE]  
> COPY 문은 현재 공개 미리 보기로 제공됩니다. 피드백을 제공하려면 다음 메일 그룹으로 sqldwcopypreview@service.microsoft.com전자 메일을 보냅니다.


> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>ELT란 무엇인가요?

ELT(추출, 로드 및 변환)는 원본 시스템에서 데이터를 추출하여 데이터 웨어하우스에 로드한 다음 변환하는 프로세스입니다. 

ELT 구현을 위한 기본 단계는 다음과 같습니다.

1. 원본 데이터를 텍스트 파일로 추출합니다.
2. Azure Blob Storage 또는 Azure Data Lake Store에 데이터를 둡니다.
3. 로드할 데이터를 준비합니다.
4. PolyBase 또는 COPY 명령을 사용하여 스테이징 테이블에 데이터를 로드합니다. 
5. 데이터를 변환합니다.
6. 프로덕션 테이블에 데이터를 삽입합니다.


PolyBase 로드 자습서의 경우 [PolyBase 사용을 참조하여 Azure Blob 저장소에서 데이터를 로드합니다.](load-data-from-azure-blob-storage-using-polybase.md)

자세한 정보는 [로드 패턴 블로그](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)를 참조하세요. 


## <a name="1-extract-the-source-data-into-text-files"></a>1. 텍스트 파일로 소스 데이터 추출

원본 시스템에서 데이터를 추출하는 방식은 스토리지 위치에 따라 달라집니다.  목표는 데이터를 PolyBase로 이동하고 COPY가 지원되는 제한된 텍스트 또는 CSV 파일입니다. 

### <a name="polybase-and-copy-external-file-formats"></a>폴리베이스 및 COPY 외부 파일 형식

PolyBase 및 COPY 문을 사용하면 UTF-8 및 UTF-16 인코딩된 구분된 텍스트 또는 CSV 파일에서 데이터를 로드할 수 있습니다. 구분된 텍스트 또는 CSV 파일 외에도 ORC 및 마루와 같은 Hadoop 파일 형식에서 로드됩니다. PolyBase 및 COPY 문은 Gzip 및 Snappy 압축 파일에서 데이터를 로드할 수도 있습니다. 확장 ASCII, 고정 너비 형식 및 WinZip 또는 XML과 같은 중첩 형식은 지원되지 않습니다. SQL Server에서 내보내는 경우 [bcp 명령줄 도구를](/sql/tools/bcp-utility?view=azure-sqldw-latest) 사용하여 데이터를 구분된 텍스트 파일로 내보낼 수 있습니다. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Azure Blob 저장소 또는 Azure 데이터 레이크 저장소에 데이터를 저장합니다.

Azure 저장소에 데이터를 저장하려면 [Azure Blob 저장소](../../storage/blobs/storage-blobs-introduction.md) 또는 [Azure Data Lake 저장소 Gen2로](../../data-lake-store/data-lake-store-overview.md)데이터를 이동할 수 있습니다. 어느 위치에서든 데이터를 텍스트 파일에 저장해야 합니다. PolyBase 및 COPY 문은 어느 위치에서든 로드할 수 있습니다.

데이터를 Azure Storage로 이동하는 데 사용할 수 있는 도구와 서비스는 다음과 같습니다.

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 서비스는 네트워크 처리량, 성능 및 예측 가능성을 개선합니다. ExpressRoute는 Azure에 대한 전용 프라이빗 연결을 통해 데이터를 라우팅하는 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 데이터를 라우팅하지 않습니다. 이 연결은 공용 인터넷을 통한 일반적인 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.
- [AZCopy 유틸리티](../../storage/common/storage-choose-data-transfer-solution.md)는 공용 인터넷을 통해 Azure Storage로 데이터를 이동합니다. 이는 데이터 크기가 10TB 미만인 경우에 작동합니다. AZCopy를 사용하여 정기적으로 로드를 수행하려면 네트워크 속도를 테스트하여 가능한지 확인하세요. 
- [ADF(Azure Data Factory)](../../data-factory/introduction.md)에는 로컬 서버에 설치할 수 있는 게이트웨이가 있습니다. 그런 다음 파이프라인을 만들어 로컬 서버에서 Azure Storage로 데이터를 이동할 수 있습니다. SQL 분석과 함께 데이터 팩터리를 사용하려면 [SQL 분석에 대한 데이터 로드를](../../data-factory/load-azure-sql-data-warehouse.md)참조하십시오.


## <a name="3-prepare-the-data-for-loading"></a>3. 로드를 위한 데이터 준비

로드하기 전에 저장소 계정의 데이터를 준비하고 정리해야 할 수 있습니다. 데이터가 원본에 있는 동안, 데이터를 텍스트 파일로 내보낼 때 또는 Azure Storage로 이동한 후 데이터 준비를 수행할 수 있습니다.  가능한 경우 프로세스의 초기에 데이터로 작업하는 것이 가장 쉽습니다.  

### <a name="define-external-tables"></a>외부 테이블 정의

PolyBase를 사용하는 경우 로드하기 전에 데이터 웨어하우스에서 외부 테이블을 정의해야 합니다. 외부 테이블은 COPY 문에 필요하지 않습니다. PolyBase는 외부 테이블을 사용하여 Azure Storage의 데이터를 정의하고 액세스합니다. 데이터베이스 뷰와 비슷한 외부 테이블은 테이블 스키마를 포함하며 데이터 웨어하우스 외부에 저장된 데이터를 가리킵니다. 

외부 테이블 정의에는 데이터 원본, 텍스트 파일 형식 및 테이블 정의를 지정하는 것이 포함됩니다. 필요한 T-SQL 구문 항목은 다음과 같습니다.
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [외부 파일 형식 만들기](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [외부 테이블 만들기](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

마루를 로드할 때 SQL 데이터 형식 매핑은 다음과 같은 것입니다.

| **마루 데이터 유형** | **SQL 데이터 형식** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        boolean        |        bit        |
|        double         |       float       |
|         float         |       real        |
|        double         |       money       |
|        double         |    smallmoney     |
|        문자열         |       nchar       |
|        문자열         |     nvarchar      |
|        문자열         |       char        |
|        문자열         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|       timestamp       |       date        |
|       timestamp       |   smalldatetime   |
|       timestamp       |     datetime2     |
|       timestamp       |     Datetime      |
|       timestamp       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

외부 개체를 만드는 예는 로드 자습서의 [외부 테이블 만들기](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) 단계를 참조하세요.

### <a name="format-text-files"></a>텍스트 파일 형식 지정

PolyBase를 사용하는 경우 정의된 외부 개체는 텍스트 파일의 행을 외부 테이블 및 파일 형식 정의와 정렬해야 합니다. 텍스트 파일의 각 행에 있는 데이터는 테이블 정의와 정렬되어야 합니다.
텍스트 파일의 형식을 지정하려면:

- 데이터가 비관계형 원본에서 오는 경우 행과 열로 변환해야 합니다. 데이터가 관계형 원본에서 오건 비관계형 원본에서 오건, 데이터를 로드하려고 하는 테이블에 대한 열 정의와 정렬되도록 데이터를 변환해야 합니다. 
- 텍스트 파일의 데이터 서식을 지정하여 대상 테이블의 열 및 데이터 형식에 맞게 정렬합니다. 외부 텍스트 파일의 데이터 형식과 데이터 웨어하우스 테이블의 데이터 형식이 정렬되지 않으면 로드 중에 행이 거부됩니다.
- 종결자로 텍스트 파일의 필드를 구분합니다.  원본 데이터에서 찾을 수 없는 문자 또는 문자 시퀀스를 사용해야 합니다. [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)으로 지정한 종결자를 사용하세요.


## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. PolyBase 또는 COPY 문을 사용하여 데이터를 로드합니다.

데이터는 준비 테이블로 로드하는 것이 가장 좋습니다. 준비 테이블을 사용하면 프로덕션 테이블에 영향을 주지 않고 오류를 처리할 수 있습니다. 스테이징 테이블은 데이터를 프로덕션 테이블에 삽입하기 전에 SQL 풀 MPP를 데이터 변환에 사용할 수 있는 기회도 제공합니다. COPY를 사용하여 스테이징 테이블에 로드할 때 테이블을 미리 만들어야 합니다.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>PolyBase 및 COPY 문으로 로드하는 옵션

PolyBase를 사용하여 데이터를 로드하려는 경우 다음 로드 옵션 중 하나를 사용할 수 있습니다.

- [T-SQL을 이용한 PolyBase](load-data-from-azure-blob-storage-using-polybase.md): Azure Blob Storage 또는 Azure Data Lake Store에 데이터가 있을 경우 효과적입니다. 로드 프로세스를 가장 잘 제어할 수 있지만, 외부 데이터 개체를 정의해야 합니다. 다른 방법에서는 원본 테이블을 대상 테이블에 매핑할 때 배후에서 이러한 개체를 정의합니다.  T-SQL 로드를 조정하려면 Azure Data Factory, SSIS 또는 Azure 함수를 사용할 수 있습니다. 
- [SSIS를 이용한 PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse): 원본 데이터가 SQL Server(SQL Server 온-프레미스 또는 클라우드)에 있을 경우 효과적입니다. SSIS는 대상 테이블 매핑에 대해 원본을 정의하고 로드를 조정합니다. 이미 SSIS 패키지가 있는 경우 새 데이터 웨어하우스 대상으로 작업하도록 패키지를 수정할 수 있습니다. 
- [Azure 데이터 팩터리(ADF)를 사용하여 PolyBase 및 COPY 문은](../../data-factory/load-azure-sql-data-warehouse.md) 또 다른 오케스트레이션 도구입니다.  파이프라인을 정의하고 작업을 예약합니다. 
- [Azure Databricks를 사용하는 PolyBase는](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md) 테이블에서 데이터 브릭 데이터 프레임으로 데이터를 전송하거나 PolyBase를 사용하여 Databricks 데이터 프레임에서 테이블로 데이터를 씁니다.

### <a name="other-loading-options"></a>기타 로딩 옵션

PolyBase 및 COPY 문 외에도 [숨은 숨은](/sql/tools/bcp-utility?view=azure-sqldw-latest) [대/SQLBulkCopy API를](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)사용할 수 있습니다. bcp는 Azure Blob 저장소를 거치지 않고 데이터베이스에 직접 로드되며 작은 부하에만 적합합니다. 이러한 옵션의 로드 성능은 PolyBase 및 COPY 문보다 느립니다. 


## <a name="5-transform-the-data"></a>5. 데이터 변환

데이터가 준비 테이블에 있는 동안, 워크로드에 필요한 변환을 수행하세요. 그런 다음 데이터를 프로덕션 테이블로 이동하세요.


## <a name="6-insert-the-data-into-production-tables"></a>6. 생산 테이블에 데이터를 삽입합니다.

삽입 ... SELECT 문은 스테이징 테이블에서 영구 테이블로 데이터를 이동합니다. 

ETL 프로세스를 설계할 때 작은 테스트 샘플에서 프로세스를 실행해 보세요. 테이블에서 파일로 1000개 행을 추출하여 Azure로 이동한 다음 준비 테이블에 로드해 보세요. 


## <a name="partner-loading-solutions"></a>파트너 로드 솔루션

상당수의 파트너에게는 로드 솔루션이 있습니다. 자세한 내용을 알아보려면 [솔루션 파트너](sql-data-warehouse-partner-business-intelligence.md) 목록을 참조하세요. 


## <a name="next-steps"></a>다음 단계

설명서를 로드하려면 [데이터를 로드하기 위한 지침](guidance-for-loading-data.md)을 참조하세요.
