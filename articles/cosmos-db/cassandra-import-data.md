---
title: Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
description: CQL Copy 명령 및 Spark를 사용하여 Apache Cassandra에서 Azure Cosmos DB Cassandra API로 데이터를 복사하는 방법을 알아봅니다.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 56fc07c6d775ee8015ce244acb7782607bda802a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739790"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>자습서: Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션

이 자습서는 Apache Cassandra 데이터를 Azure Cosmos DB Cassandra API로 마이그레이션하는 방법에 대한 지침을 제공합니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 마이그레이션 계획
> * 마이그레이션을 위한 필수 조건
> * cqlsh COPY 명령을 사용하여 데이터 마이그레이션
> * Spark를 사용하여 데이터 마이그레이션 

## <a name="plan-for-migration"></a>마이그레이션 계획

데이터를 Azure Cosmos DB Cassandra API로 마이그레이션하기 전에 워크로드에 대한 처리량 수요를 예측해야 합니다. 일반적으로 CRUD 작업에 필요한 평균 처리량부터 시작한 다음, ETL(추출, 변환 및 로드) 또는 급증하는 작업에 필요한 추가 처리량을 포함하는 것이 좋습니다. 마이그레이션을 계획하려면 다음과 같은 세부 정보가 필요합니다. 

* **기존 데이터 크기 또는 예상 데이터 크기:** 최소 데이터베이스 크기 및 처리량 요구 사항을 정의합니다. 새 응용 프로그램의 데이터 크기를 예측할 경우 데이터가 행 전체에 균일하게 분포되어 있다고 가정하고 데이터 크기를 곱하여 값을 추정하면 됩니다. 

* **필요한 처리량:** 대략적인 읽기(쿼리/가져 오기) 및 쓰기(업데이트/삭제/삽입) 처리량 속도. 이 값은 안정적인 상태 데이터 크기와 함께 필요한 요청 단위를 계산하는 데 필요합니다.  

* **스키마를 가져오기:** cqlsh를 통해 기존 Cassandra 클러스터에 연결하고 Cassandra의 스키마를 내보냅니다. 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

기존 워크로드의 요구 사항을 확인한 후에는 수집한 처리량 요구 사항에 따라 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만들어야 합니다.  

* **작업에 대한 RU 요금 확인:** 원하는 Azure Cosmos DB Cassandra API SDK를 사용하여 RU를 확인할 수 있습니다. 다음 예제는 RU 비용 가져오기의 .NET 버전을 보여 줍니다.

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **필요한 처리량 할당:** Azure Cosmos DB는 요구 사항이 증가함에 따라 스토리지 및 처리량을 자동으로 조정할 수 있습니다. [Azure Cosmos DB 요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 사용하여 처리량 수요를 예측할 수 있습니다. 

## <a name="prerequisites-for-migration"></a>마이그레이션을 위한 필수 조건

* **Azure Cosmos DB Cassandra API 계정에 테이블 만들기:** 데이터 마이그레이션을 시작하기 전에 Azure Portal이나 cqlsh를 통해 모든 테이블을 미리 작성합니다. 데이터베이스 수준 처리량이 있는 Azure Cosmos DB 계정으로 마이그레이션하는 경우에는 Azure Cosmos DB 컨테이너를 만들 때 파티션 키를 제공해야 합니다.

* **처리량 늘리기:** 데이터 마이그레이션 기간은 Azure Cosmos DB의 테이블에 대해 프로비전한 처리량에 따라 다릅니다. 마이그레이션 기간에 대한 처리량을 늘립니다. 처리량이 높을수록 속도 제한을 피하고 마이그레이션 시간을 단축할 수 있습니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요. 처리량 증가에 대한 자세한 내용은 Azure Cosmos DB 컨테이너에 대한 [처리량 설정](set-throughput.md)을 참조하세요. Azure Cosmos DB 계정은 원본 데이터베이스와 같은 지역에 두는 것이 좋습니다. 

* **SSL 사용:** Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 있습니다. 계정과 상호 작용하는 경우 SSL을 사용해야 합니다. SSH와 함께 CQL을 사용하는 경우 SSL 정보를 제공할 수 있는 옵션이 있습니다.

## <a name="options-to-migrate-data"></a>데이터 마이그레이션 옵션

다음 옵션을 사용하여 기존 Cassandra 워크로드에서 Azure Cosmos DB로 데이터를 옮길 수 있습니다.

* [cqlsh COPY 명령 사용](#using-cqlsh-copy-command)  
* [Spark 사용](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>cqlsh COPY 명령을 사용하여 데이터 마이그레이션

[CQL COPY 명령](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)은 로컬 데이터를 Azure Cosmos DB Cassandra API 계정에 복사하는 데 사용됩니다. 다음 단계에 따라 데이터를 복사합니다.

1. Cassandra API 계정의 연결 문자열 정보를 확보합니다.

   * [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Cosmos DB 계정으로 이동합니다.

   * cqlsh를 통해 Cassandra API 계정에 연결하는 데 필요한 모든 정보가 들어있는 **연결 문자열** 창을 엽니다.

2. 포털의 연결 정보를 사용하여 cqhsh에 로그인합니다.

3. CQL COPY 명령을 사용하여 로컬 데이터를 Cassandra API 계정에 복사합니다.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Spark를 사용하여 데이터 마이그레이션 

다음 단계에 따라 Spark를 사용하여 데이터를 Azure Cosmos DB Cassandra API로 마이그레이션합니다.

- [Azure Databricks](cassandra-spark-databricks.md) 또는 [HDInsight 클러스터](cassandra-spark-hdinsight.md) 프로비전 

- [테이블 복사 작업](cassandra-spark-table-copy-ops.md)을 사용하여 데이터를 Cassandra API 엔드포인트로 이동합니다. 

Azure 가상 머신이나 다른 클라우드의 기존 클러스터에 데이터가 있는 경우 Spark 작업을 사용하여 데이터를 마이그레이션하는 것이 좋습니다. 이 옵션을 사용하려면 Spark를 한 번 또는 정기적으로 중개자로 설정해야 합니다. 온-프레미스와 Azure 사이에 Express 경로 연결을 사용하면 마이그레이션 속도를 높일 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB Cassandra API 계정으로 데이터를 마이그레이션하는 방법을 알아보았습니다. 이제 개념 섹션으로 진행하여 Azure Cosmos DB에 대한 자세한 정보를 확인할 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준](../cosmos-db/consistency-levels.md)


