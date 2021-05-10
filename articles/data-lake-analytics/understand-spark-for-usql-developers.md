---
title: Azure Data Lake Analytics U-SQL 개발자용 Apache Spark를 이해합니다.
description: 이 문서에서는 U-SQL 개발자 간의 차이를 파악하는 데 도움이 되는 Apache Spark 개념을 설명합니다.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221097"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>U-SQL 개발자를 위한 Apache Spark 이해

Microsoft는 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 및 [Azure HDInsight](../hdinsight/hdinsight-overview.md)와 같은 여러 분석 서비스와 Azure Data Lake Analytics를 지원합니다. 개발자는 분석 파이프라인을 빌드할 때 오픈 소스 솔루션을 확실히 선호한다고 합니다. U-SQL 개발자가 Apache Spark를 이해하고 U-SQL 스크립트를 Apache Spark로 변환하는 방법을 이해하도록 돕기 위해 이 지침을 만들었습니다.

여기에는 수행할 수 있는 몇 가지 단계와 여러 대안이 나와 있습니다.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>U-SQL을 Apache Spark로 변환하는 단계

1. 작업 오케스트레이션 파이프라인을 변환합니다.

   [Azure Data Factory](../data-factory/introduction.md)를 사용하여 Azure Data Lake Analytics 스크립트를 오케스트레이션하는 경우 새 Spark 프로그램을 오케스트레이션하도록 해당 스크립트를 조정해야 합니다.
2. U-SQL과 Spark의 데이터 관리 방법의 차이점을 이해합니다.

   [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)에서 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)로 데이터를 이동하려는 경우 파일 데이터와 카탈로그에서 유지 관리되는 데이터를 둘 다 복사해야 합니다. Azure Data Lake Analytics는 Azure Data Lake Storage Gen1만 지원합니다. [Apache Spark 데이터 서식 이해](understand-spark-data-formats.md)를 참조하세요.
3. U-SQL 스크립트를 Spark로 변환합니다.

   U-SQL 스크립트를 변환하기 전에 분석 서비스를 선택해야 합니다. 사용할 수 있는 컴퓨팅 서비스 중 일부는 다음과 같습니다.
      - [Azure Data Factory DataFlow](../data-factory/concepts-data-flow-overview.md) 매핑 데이터 흐름은 데이터 엔지니어가 코드를 작성하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있도록 하는 시각적으로 디자인된 데이터 변환입니다. 복잡한 사용자 코드를 실행하는 데 적합하지는 않지만 SQL과 유사한 기존 데이터 흐름 변환을 쉽게 나타낼 수 있습니다.
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) HDInsight의 Apache Hive는 ETL(추출, 변환 및 로드) 작업에 적합합니다. 즉, U-SQL 스크립트를 Apache Hive로 변환합니다.
      - Apache Spark 엔진(예: [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) 또는 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)) 즉, U-SQL 스크립트를 Spark로 변환합니다. 자세한 내용은 [Spark 데이터 폼 이해](understand-spark-data-formats.md)를 참조하세요.

> [!CAUTION]
> [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 및 [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)는 둘 다 클러스터 서비스이며 Azure Data Lake Analytics와 같은 서버리스 작업이 아닙니다. 적절한 비용/성능 비율을 얻기 위해 클러스터를 프로비저닝하는 방법과 비용을 최소화하기 위해 수명을 관리하는 방법을 고려해야 합니다. 이러한 서비스는 .NET으로 작성된 사용자 코드와 성능 특성이 다르므로 래퍼를 작성하거나 지원되는 언어로 코드를 다시 작성해야 합니다. 자세한 내용은 [Spark 데이터 서식 이해](understand-spark-data-formats.md), [U-SQL 개발자를 위한 Apache Spark 코드 개념 이해](understand-spark-code-concepts.md), [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [U-SQL 개발자를 위한 Spark 데이터 서식 이해](understand-spark-data-formats.md)
- [U-SQL 개발자를 위한 Spark 코드 개념 이해](understand-spark-code-concepts.md)
- [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Apache Spark용 .NET](/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory에서 Hadoop Hive 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure HDInsight에서 Apache Spark란](../hdinsight/spark/apache-spark-overview.md)