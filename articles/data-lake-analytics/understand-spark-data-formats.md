---
title: Azure Data Lake Analytics U-SQL 개발자를 위한 Apache Spark 데이터 서식을 이해합니다.
description: 이 문서에서는 U-SQL 개발자가 U-SQL과 Spark 데이터 서식 간의 차이점을 이해하는 데 도움이 되는 Apache Spark 개념을 설명합니다.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221114"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>U-SQL과 Spark 데이터 형식 간의 차이점 이해

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 또는 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)를 사용하려면 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)에서 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)로 데이터를 마이그레이션하는 것이 좋습니다.

파일을 이동하는 것 외에도 U-SQL 테이블에 저장된 데이터를 Spark에서 액세스할 수 있도록 만들려고 합니다.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Azure Data Lake Storage Gen1에 저장된 데이터 이동

파일에 저장된 데이터는 다음과 같은 여러 가지 방법으로 이동할 수 있습니다.

- [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 계정에서 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정으로 데이터를 복사하는 [Azure Data Factory](../data-factory/introduction.md) 파이프라인을 작성합니다.
- [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 계정에서 데이터를 읽어 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정에 쓰는 Spark 작업을 작성합니다. 원래 파일 형식을 유지할 필요가 없는 경우 사용 사례에 따라 Parquet과 같은 다른 형식으로 작성하는 것이 좋습니다.

[빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) 문서를 검토하는 것이 좋습니다.

## <a name="move-data-stored-in-u-sql-tables"></a>U-SQL 테이블에 저장된 데이터 이동

U-SQL 테이블은 Spark에서 인식되지 않습니다. SQL 테이블에 저장된 데이터가 있는 경우 테이블 데이터를 추출하고 Spark에서 인식하는 형식으로 저장하는 U-SQL 작업을 실행합니다. 가장 적절한 형식은 Hive 메타스토어의 폴더 레이아웃에 따라 Parquet 파일 세트를 만드는 것입니다.

출력은 기본 제공 Parquet 출력자에서 U-SQL로 얻을 수 있으며 파일 세트와 함께 동적 출력 분할을 사용하여 파티션 폴더를 만듭니다. [Parquet을 사용하여 이전보다 많은 파일 처리](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics)에서는 이러한 Spark 사용 가능 데이터를 만드는 방법을 예를 들어 설명합니다.

변환 후에는 [Azure Data Lake Storage Gen1 파일에 저장된 데이터 이동](#move-data-stored-in-azure-data-lake-storage-gen1-files) 장에서 설명하는 대로 데이터를 복사합니다.

## <a name="caveats"></a>제한 사항

- 데이터 의미 체계. 파일을 복사할 때 복사본은 바이트 수준에서 수행됩니다. 따라서 동일한 데이터가 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정에 표시되어야 합니다. 그러나 Spark는 일부 문자를 다르게 해석할 수 있습니다. 예를 들어 CSV 파일의 행 구분 기호에 대해 다른 기본값을 사용할 수 있습니다.
    또한 테이블에서 형식화된 데이터를 복사하는 경우 Parquet과 Spark는 형식화된 값(예: 부동) 중 일부에 대해 다른 전체 자릿수와 소수 자릿수를 가질 수 있으며 null 값을 다르게 처리할 수 있습니다. 예를 들어, U-SQL은 null 값에 대해 C# 의미 체계를 사용하지만 Spark는 null 값에 대해 값이 3개인 논리를 사용합니다.

- 데이터 구성(분할). U-SQL 테이블은 두 가지 수준 분할을 제공합니다. 외부 수준(`PARTITIONED BY`)은 값을 기준으로 하며 대부분은 폴더 계층 구조를 사용하여 Hive/Spark 파티션 구성표로 매핑됩니다. null 값이 올바른 폴더에 매핑되는지 확인해야 합니다. U-SQL의 내부 수준(`DISTRIBUTED BY`)은 라운드 로빈, 범위, 해시, 직접 해시의 4가지 배포 스키마를 제공합니다.
    Hive/Spark 테이블은 U-SQL과 다른 해시 함수를 사용하여 값 분할 또는 해시 분할만 지원합니다. U-SQL 테이블 데이터를 출력하는 경우 Spark의 값 분할에만 매핑할 수 있으며 최종 Spark 쿼리에 따라 데이터 레이아웃을 추가로 조정해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [U-SQL 개발자를 위한 Spark 코드 개념 이해](understand-spark-code-concepts.md)
- [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Apache Spark용 .NET](/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory에서 Hadoop Hive 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight에서 Apache Spark란](../hdinsight/spark/apache-spark-overview.md)