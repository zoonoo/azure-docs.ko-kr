---
title: SQL 주문형(미리 보기)을 사용하여 Spark 테이블 쿼리
description: SQL 주문형(미리 보기)을 사용하여 Spark 테이블 쿼리하는 방법 개요
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420077"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>SQL 주문형(미리 보기)을 사용하여 Azure Synapse Analytics로 Spark 테이블 쿼리

SQL 주문형(미리 보기)은 Synapse 작업 영역(미리 보기) 내의 Spark 풀에서 메타데이터를 자동으로 동기화할 수 있습니다. Spark 풀(미리 보기)에 있는 각 데이터베이스에 대해 SQL 주문형 데이터베이스가 생성됩니다. Parquet 또는 CSV를 기반으로 하는 각 Spark 테이블에 대해 SQL 주문형 데이터베이스에 외부 테이블이 생성됩니다. 따라서 Spark 풀을 종료하고 SQL 주문형에서 Spark 테이블을 계속 쿼리할 수 있습니다.

테이블이 Spark에서 분할되면 스토리지의 파일은 폴더별로 구성됩니다. SQL 주문형은 파티션 메타데이터를 활용하고 쿼리에 관련된 폴더와 파일만 대상으로 합니다.

Azure Synapse 작업 영역에서 프로비저닝된 각 Spark 풀에 대해 메타데이터 동기화가 자동으로 구성됩니다. Spark 테이블을 즉시 쿼리할 수 있습니다.

각 Spark 테이블은 SQL 주문형 데이터베이스에 해당하는 dbo 스키마의 외부 테이블로 표시됩니다. Spark 테이블 쿼리의 경우 외부 [spark_table]을 대상으로 하는 쿼리를 실행합니다. 아래 예제를 실행하기 전에 파일이 있는 [스토리지 계정에 대한 액세스 권한](develop-storage-files-storage-access-control.md)이 올바른지 확인합니다.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>SQL 데이터 형식을 Spark 데이터 형식으로 매핑

| Spark 데이터 형식 | SQL 데이터 형식               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (JSON으로)** |
| MapType         | varchar(max)* (JSON으로)** |
| StructType      | varchar(max)* (JSON으로)** |

\* 사용되는 데이터 정렬은 Latin1_General_100_BIN2_UTF8입니다.

** ArrayType, MapType 및 StructType은 JSON으로 표시됩니다.



## <a name="next-steps"></a>다음 단계

스토리지 액세스 제어에 대해 자세히 알아보려면 [Storage Access Control](develop-storage-files-storage-access-control.md) 문서로 이동합니다.
