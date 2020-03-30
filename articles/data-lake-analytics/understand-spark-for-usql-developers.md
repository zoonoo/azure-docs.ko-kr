---
title: Azure 데이터 레이크 분석 U-SQL 개발자를 위한 아파치 스파크를 이해합니다.
description: 이 문서에서는 U-SQL 개발자 간의 차이점을 설명하는 아파치 스파크 개념에 대해 설명합니다.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648429"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>U-SQL 개발자를 위한 Apache Spark 이해

Microsoft는 [Azure 데이터브릭](../azure-databricks/what-is-azure-databricks.md) 및 [Azure HDInsight와](../hdinsight/hdinsight-overview.md) 같은 여러 분석 서비스뿐만 아니라 Azure 데이터 레이크 애널리틱스를 지원합니다. 개발자들은 분석 파이프라인을 구축할 때 오픈 소스 솔루션에 대한 명확한 선호도를 가지고 있다는 것을 개발자들로부터 듣습니다. U-SQL 개발자가 아파치 스파크를 이해하고 U-SQL 스크립트를 아파치 스파크로 변환하는 방법을 이해하기 위해 이 지침을 만들었습니다.

여기에는 수행할 수 있는 여러 단계와 몇 가지 대안이 포함되어 있습니다.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>U-SQL을 아파치 스파크로 변환하는 단계

1. 작업 오케스트레이션 파이프라인을 변환합니다.

   [Azure 데이터 팩터리에서 Azure Data](../data-factory/introduction.md) Lake Analytics 스크립트를 오케스트레이션하는 경우 새 Spark 프로그램을 오케스트레이션하도록 조정해야 합니다.
2. U-SQL과 Spark가 데이터를 관리하는 방법의 차이점 이해

   Azure Data Lake Storage [Gen1에서](../data-lake-store/data-lake-store-overview.md) [Azure Data Lake Storage Gen2로](../storage/blobs/data-lake-storage-introduction.md)데이터를 이동하려면 파일 데이터와 카탈로그 유지 관리 데이터를 모두 복사해야 합니다. Azure 데이터 레이크 분석은 Azure 데이터 레이크 저장소 Gen1만 지원합니다. [스파크 데이터 형식 이해](understand-spark-data-formats.md) 보기
3. U-SQL 스크립트를 스파크로 변환

   U-SQL 스크립트를 변환하기 전에 분석 서비스를 선택해야 합니다. 사용 가능한 계산 서비스 중 일부는 다음과 같습니다.
      - [Azure 데이터 팩토리 데이터 흐름](../data-factory/concepts-data-flow-overview.md) 데이터 흐름 매핑은 데이터 엔지니어가 코드를 작성하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있도록 시각적으로 설계된 데이터 변환입니다. 복잡한 사용자 코드를 실행하는 데는 적합하지 않지만 기존의 SQL과 같은 데이터 흐름 변환을 쉽게 나타낼 수 있습니다.
      - [푸른 HD인사이트 하이브](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) HDInsight의 아파치 하이브는 ETL(추출, 변환 및 부하) 작업에 적합합니다. 즉, U-SQL 스크립트를 아파치 하이브로 번역할 것입니다.
      - 아파치 스파크 엔진 같은 [Azure HDInsight 스파크](../hdinsight/spark/apache-spark-overview.md) 또는 [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) 즉, U-SQL 스크립트를 스파크로 변환합니다. 자세한 내용은 [스파크 데이터 형식 이해하기를](understand-spark-data-formats.md) 참조하십시오.

> [!CAUTION]
> [Azure 데이터 브릭과](../azure-databricks/what-is-azure-databricks.md) [Azure HDInsight 스파크는](../hdinsight/spark/apache-spark-overview.md) 모두 클러스터 서비스이며 Azure 데이터 레이크 분석과 같은 서버없는 작업이 아닙니다. 적절한 비용/성능 비율을 얻기 위해 클러스터를 프로비전하는 방법과 비용을 최소화하기 위해 클러스터의 수명을 관리하는 방법을 고려해야 합니다. 이러한 서비스는 .NET으로 작성된 사용자 코드와 성능 특성이 다르므로 래퍼를 작성하거나 지원되는 언어로 코드를 다시 작성해야 합니다. 자세한 내용은 [스파크 데이터 형식 이해,](understand-spark-data-formats.md) [U-SQL 개발자를 위한 아파치 스파크 코드 개념 이해,](understand-spark-code-concepts.md) [아파치 스파크용 .Net](https://dotnet.microsoft.com/apps/data/spark) 을 참조하십시오.

## <a name="next-steps"></a>다음 단계

- [U-SQL 개발자를 위한 스파크 데이터 형식 이해](understand-spark-data-formats.md)
- [U-SQL 개발자를 위한 스파크 코드 개념 이해](understand-spark-code-concepts.md)
- [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)
- [Apache Spark용 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory에서 Hadoop Hive 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure HDInsight에서 Apache Spark란](../hdinsight/spark/apache-spark-overview.md)
