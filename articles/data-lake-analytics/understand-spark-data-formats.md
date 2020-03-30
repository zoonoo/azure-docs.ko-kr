---
title: Azure 데이터 레이크 분석 U-SQL 개발자를 위한 아파치 스파크 데이터 형식을 이해합니다.
description: 이 문서에서는 U_SQL 개발자가 U-SQL과 Spark 데이터 형식 간의 차이점을 이해하는 데 도움이 되는 아파치 스파크 개념에 대해 설명합니다.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648442"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>U-SQL 과 스파크 데이터 형식 간의 차이점 이해

[Azure Databricks](../azure-databricks/what-is-azure-databricks.md) 또는 [Azure HDInsight 스파크를](../hdinsight/spark/apache-spark-overview.md)사용하려면 Azure Data Lake [Storage Gen1에서 Azure Data](../data-lake-store/data-lake-store-overview.md) Lake Storage [Gen2로](../storage/blobs/data-lake-storage-introduction.md)데이터를 마이그레이션하는 것이 좋습니다.

파일을 이동하는 것 외에도 U-SQL 테이블에 저장된 데이터를 Spark에 액세스할 수 있도록 하는 것도 좋습니다.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Azure 데이터 레이크 저장소 Gen1 파일에 저장된 데이터 이동

파일에 저장된 데이터는 다음과 같은 다양한 방법으로 이동할 수 있습니다.

- Azure [Data Factory](../data-factory/introduction.md) 파이프라인을 작성하여 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 계정의 데이터를 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정으로 복사합니다.
- [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) 계정의 데이터를 읽고 Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정에 기록하는 Spark 작업을 작성합니다. 사용 사례에 따라 원본 파일 형식을 보존할 필요가 없는 경우 Parquet와 같은 다른 형식으로 작성할 수 있습니다.

이 문서를 검토하는 것이 [좋습니다Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 빅 데이터 분석 솔루션 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>U-SQL 테이블에 저장된 데이터 이동

U-SQL 테이블은 Spark에서 이해하지 않습니다. U-SQL 테이블에 저장된 데이터가 있는 경우 테이블 데이터를 추출하고 Spark가 이해하는 형식으로 저장하는 U-SQL 작업을 실행합니다. 가장 적절한 형식은 Hive 메타스토어의 폴더 레이아웃에 따라 마루 파일 집합을 만드는 것입니다.

출력은 내장 마루 출력기와 파티션 폴더를 만들기 위해 파일 세트와 동적 출력 분할을 사용하여 U-SQL에서 달성 될 수있다. [그 어느 때보 다 더 많은 파일을 처리하고 Parquet를 사용하여](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) 이러한 Spark 소모품 데이터를 만드는 방법의 예를 제공합니다.

이 변환 후 [Azure Data Lake Storage Gen1 파일에 저장된 데이터 이동](#move-data-stored-in-azure-data-lake-storage-gen1-files)장에 설명된 대로 데이터를 복사합니다.

## <a name="caveats"></a>제한 사항

- 데이터 의미 체계 파일을 복사할 때 복사는 바이트 수준에서 발생합니다. 따라서 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 계정에 동일한 데이터가 표시되어야 합니다. 그러나 Spark는 일부 문자를 다르게 해석할 수 있습니다. 예를 들어 CSV 파일의 행 구분 기호에 대해 다른 기본값을 사용할 수 있습니다.
    또한 형식데이터를 테이블에서 복사하는 경우 Parquet와 Spark는 형식된 일부 값(예: float)에 대해 정밀도와 배율이 다를 수 있으며 null 값을 다르게 처리할 수 있습니다. 예를 들어 U-SQL에는 null 값에 대한 C# 의미 체계가 있는 반면 Spark에는 null 값에 대한 세 가지 값 논리가 있습니다.

- 데이터 구성(분할) U-SQL 테이블은 두 가지 수준의 분할을 제공합니다. 외부 수준`PARTITIONED BY`() 값에 의해 이며 폴더 계층을 사용 하 여 하이 브/스파크 분할 구성표에 주로 매핑 됩니다. null 값이 올바른 폴더에 매핑되었는지 확인해야 합니다. U-SQL의`DISTRIBUTED BY`내부 수준() 은 라운드 로빈, 범위, 해시 및 직접 해시의 4가지 배포 체계를 제공합니다.
    Hive/Spark 테이블은 U-SQL과 다른 해시 함수를 사용하여 값 분할 또는 해시 분할만 지원합니다. U-SQL 테이블 데이터를 출력할 때 Spark의 값 분할에만 매핑할 수 있으며 최종 Spark 쿼리에 따라 데이터 레이아웃을 추가로 조정해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [U-SQL 개발자를 위한 스파크 코드 개념 이해](understand-spark-code-concepts.md)
- [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)
- [Apache Spark용 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory에서 Hadoop Hive 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight에서 Apache Spark란](../hdinsight/spark/apache-spark-overview.md)
