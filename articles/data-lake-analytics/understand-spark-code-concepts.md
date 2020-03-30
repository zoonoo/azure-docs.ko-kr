---
title: Azure 데이터 레이크 분석 U-SQL 개발자를 위한 아파치 스파크 코드 개념을 이해합니다.
description: 이 문서에서는 U-SQL 개발자가 스파크 코드 개념을 이해하는 데 도움이 되는 아파치 스파크 개념에 대해 설명합니다.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424011"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>U-SQL 개발자를 위한 아파치 스파크 코드 이해

이 섹션에서는 U-SQL 스크립트를 아파치 스파크로 변환하는 방법에 대한 고급 지침을 제공합니다.

- 두 [언어의 처리 패러다임을 비교하는 것으로 시작됩니다.](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- 다음 방법에 대한 팁을 제공합니다.
   - U-SQL의 행 집합 [식을](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) 포함한 [스크립트 변환](#transform-u-sql-scripts)
   - [.NET 코드](#transform-net-code)
   - [데이터 형식](#transform-typed-values)
   - [카탈로그 개체](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>U-SQL 및 스파크 언어 및 처리 패러다임 이해

Azure Data Lake Analytics의 U-SQL 스크립트를 Spark로 마이그레이션하기 전에 두 시스템의 일반적인 언어 및 처리 철학을 이해하는 것이 유용합니다.

U-SQL은 데이터 흐름 패러다임을 사용하는 SQL과 같은 선언적 쿼리 언어이며 .NET(예: C#), Python 및 R로 작성된 사용자 코드를 쉽게 포함하고 확장할 수 있습니다. 사용자 확장은 간단한 식 또는 사용자 정의 함수를 구현할 수 있지만 행 집합 수준 변환, 추출 및 쓰기 출력을 수행하기 위해 사용자 정의 연산자를 구현하는 사용자 정의 연산자를 구현하는 기능을 사용자에게 제공할 수도 있습니다.

스파크는 스칼라, 자바, 파이썬, .NET 등에서 여러 언어 바인딩을 제공하는 스케일 아웃 프레임워크로, 주로 이러한 언어 중 하나로 코드를 작성하고, RDD(복원력 있는 분산 데이터 집합), 데이터 프레임 및 데이터 집합 및 데이터 집합이라고 하는 데이터 추상화를 생성합니다. 그런 다음 LINQ와 같은 도메인별 언어(DSL)를 사용하여 변환합니다. 또한 SparkSQL을 데이터 프레임 및 데이터 집합 추상화에 대한 선언적 하위 언어로 제공합니다. DSL은 작업, 변환 및 작업의 두 가지 범주를 제공합니다. 데이터 추상화에 변환을 적용하면 변환이 실행되지 않고 대신 작업을 사용하여 평가를 위해 제출될 실행 계획을 구축합니다(예: 결과를 임시 테이블이나 파일에 작성하거나 인쇄하는 경우) 결과)를 참조하십시오.

따라서 U-SQL 스크립트를 Spark 프로그램으로 변환할 때 최소한 데이터 프레임 추상화(현재 가장 자주 사용되는 데이터 추상화)를 생성하는 데 사용할 언어와 선언적 을 작성할지 여부를 결정해야 합니다. DSL 또는 SparkSQL을 사용하여 데이터 흐름 변환을 제공합니다. 좀 더 복잡한 경우에는 U-SQL 스크립트를 Spark 시퀀스로 분할해야 할 수 있으며 Azure Batch 또는 Azure Functions로 구현된 다른 단계도 있습니다.

또한 Azure Data Lake Analytics는 서버가 없는 작업 서비스 환경에서 U-SQL을 제공하며, Azure Databricks및 Azure HDInsight는 클러스터 서비스 형태로 스파크를 제공합니다. 응용 프로그램을 변환할 때 클러스터를 만들고 크기를 조정하고 크기를 조정하고 해제하는 데 미치는 영향을 고려해야 합니다.

## <a name="transform-u-sql-scripts"></a>U-SQL 스크립트 변환

U-SQL 스크립트는 다음 처리 패턴을 따릅니다.

1. 데이터는 `EXTRACT` 문, 위치 또는 파일 집합 사양, 기본 제공 또는 사용자 정의 추출기 및 원하는 스키마 또는 U-SQL 테이블(관리되는 테이블 또는 외부 테이블)을 사용하여 구조화되지 않은 파일에서 읽습니다. 행 집합으로 표시됩니다.
2. 행 집합은 행 집합에 U-SQL 식을 적용하고 새 행 집합을 생성하는 여러 U-SQL 문에서 변환됩니다.
3. 마지막으로 결과 행 집합은 위치 및 `OUTPUT` 기본 제공 또는 사용자 정의 출력자를 지정하는 문을 사용하여 파일 또는 U-SQL 테이블로 출력됩니다.

스크립트는 느리게 평가되므로 각 추출 및 변환 단계가 식 트리로 구성되고 전역적으로 평가됩니다(데이터 흐름).

스파크 프로그램은 Spark 커넥터를 사용하여 데이터를 읽고 데이터 프레임을 만든 다음 LINQ와 유사한 DSL 또는 SparkSQL을 사용하여 데이터 프레임에 변환을 적용한 다음 결과를 파일, 임시 스파크 테이블에 작성한다는 점에서 유사합니다. 일부 프로그래밍 언어 유형 또는 콘솔을 클릭합니다.

## <a name="transform-net-code"></a>.NET 코드 변환

U-SQL의 표현식 언어는 C#이며 사용자 지정 .NET 코드를 확장하는 다양한 방법을 제공합니다.

Spark는 현재 기본적으로 .NET 코드 실행을 지원하지 않으므로 표현식을 동등한 Spark, Scala, Java 또는 Python 표현식으로 다시 작성하거나 .NET 코드로 호출할 방법을 찾아야 합니다. 스크립트에서 .NET 라이브러리를 사용하는 경우 다음과 같은 옵션이 있습니다.

- .NET 코드를 스칼라 또는 파이썬으로 번역합니다.
- U-SQL 스크립트를 여러 단계로 분할하여 Azure Batch 프로세스를 사용하여 .NET 변환을 적용합니다(허용 가능한 척도를 얻을 수 있는 경우).
- Moebius라는 오픈 소스에서 사용할 수 있는 .NET 언어 바인딩을 사용합니다. 이 프로젝트는 지원되는 상태가 아닙니다.

어쨌든 U-SQL 스크립트에 많은 양의 .NET 논리가 있는 경우 Microsoft 계정 담당자에게 문의하여 자세한 지침을 확인하십시오.

다음 세부 정보는 U-SQL 스크립트에서 .NET 및 C# 사용의 다양한 경우에 대한 것입니다.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>스칼라 인라인 U-SQL C# 표현식 변환

U-SQL의 표현 언어는 C#입니다. 대부분의 스칼라 인라인 U-SQL 식은 성능 향상을 위해 기본적으로 구현되지만 .NET 프레임워크를 호출하여 보다 복잡한 식을 실행할 수 있습니다.

Spark는 자체 스칼라 표현 언어(DSL 또는 SparkSQL의 일부)를 가지며 호스팅 언어로 작성된 사용자 정의 함수로 호출할 수 있습니다.

U-SQL에 스칼라 식이 있는 경우 가장 적절한 기본적으로 이해되는 Spark 스칼라 식을 먼저 찾아 성능을 가장 많이 끌어온 다음 다른 식을 원하는 Spark 호스팅 언어의 사용자 정의 함수에 매핑해야 합니다.

.NET 및 C#에는 스파크 호스팅 언어 및 Spark의 DSL과 다른 형식의 의미 체계가 있습니다. 유형 시스템 차이에 대한 자세한 내용은 [아래를](#transform-typed-values) 참조하십시오.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>사용자 정의 스칼라 .NET 함수 및 사용자 정의 집계기 변환

U-SQL은 임의의 scalar .NET 함수를 호출하고 .NET으로 작성된 사용자 정의 집계를 호출하는 방법을 제공합니다.

또한 Spark는 Spark의 DSL 및 SparkSQL에서 호출할 수 있는 대부분의 호스팅 언어로 작성된 사용자 정의 함수 및 사용자 정의 집계에 대한 지원을 제공합니다.

### <a name="transform-user-defined-operators-udos"></a>사용자 정의 연산자 변환(DSO)

U-SQL은 .NET(및 어느 정도 - 파이썬 및 R)에서 작성할 수 있는 추출기, 출력기, 감속기, 프로세서, 앱리어 및 조합체와 같은 여러 범주의 사용자 정의 연산자(DTO)를 제공합니다.

Spark는 연산자에게 동일한 확장성 모델을 제공하지 않지만 일부 사업자에게는 동등한 기능을 제공합니다.

추출기 및 출력기와 동등한 스파크는 스파크 커넥터입니다. 많은 U-SQL 추출기의 경우 Spark 커뮤니티에서 동등한 커넥터를 찾을 수 있습니다. 다른 사람의 경우 사용자 지정 커넥터를 작성해야 합니다. U-SQL 추출기가 복잡하고 여러 .NET 라이브러리를 사용하는 경우 데이터의 실제 처리를 수행하는 .NET 라이브러리를 호출하기 위해 interop을 사용하는 커넥터를 스칼라에서 빌드하는 것이 좋습니다. 이 경우 .NET Core 런타임을 Spark 클러스터에 배포하고 참조된 .NET 라이브러리가 .NET 표준 2.0을 준수하는지 확인해야 합니다.

U-SQL UTO의 다른 유형은 사용자 정의 함수 및 집계 및 시술적으로 적절한 Spark DLS 또는 SparkSQL 식을 사용하여 다시 작성해야 합니다. 예를 들어 프로세서는 다양한 UDF 호출의 SELECT에 매핑될 수 있으며, 데이터 프레임을 인수로 사용하여 데이터 프레임을 반환하는 함수로 패키징될 수 있습니다.

### <a name="transform-u-sqls-optional-libraries"></a>U-SQL의 선택적 라이브러리 변환

U-SQL은 [파이썬,](data-lake-analytics-u-sql-python-extensions.md) [R,](data-lake-analytics-u-sql-r-extensions.md) [JSON, XML, AVRO 지원](https://github.com/Azure/usql/tree/master/Examples/DataFormats)및 일부 [인지 서비스 기능을](data-lake-analytics-u-sql-cognitive.md)제공하는 선택적 및 데모 라이브러리 집합을 제공합니다.

스파크는 각각 파이썬과 R 통합, 파이스파크와 스파크R을 제공하며, JSON, XML 및 AVRO를 읽고 쓸 수 있는 커넥터를 제공합니다.

코그너티브 서비스 라이브러리를 참조하는 스크립트를 변환해야 하는 경우 Microsoft 계정 담당자를 통해 문의하는 것이 좋습니다.

## <a name="transform-typed-values"></a>입력된 값 변환

U-SQL의 형식 시스템은 .NET 형식 시스템을 기반으로 하며 Spark에는 호스트 언어 바인딩의 영향을 받는 자체 형식 시스템이 있으므로 작업 중인 형식이 가깝고 특정 형식의 경우 형식 범위, 정밀도 및/또는 축척이 약간 다를 수 있습니다. 또한 U-SQL 및 Spark는 값을 다르게 처리합니다. `null`

### <a name="data-types"></a>데이터 형식

다음 표에서는 지정된 U-SQL 형식에 대해 스파크, 스칼라 및 PySpark에 해당하는 형식을 제공합니다.

| U-SQL | Spark |  스칼라 | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

자세한 내용은 다음을 참조하세요.

- [org.apache.spark.sql.type](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [스파크 SQL 및 데이터 프레임 유형](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [스칼라 값 유형](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.type](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL의 치료

Spark에서 기본값당 형식은 U-SQL에서 NULL 값을 허용하며, 스칼라, 비오브젝트를 null로 명시적으로 표시합니다. Spark를 사용하면 열을 null able으로 정의할 수 있지만 제약 조건을 적용하지 않으며 [잘못된 결과가 발생할 수 있습니다.](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)

Spark에서 NULL은 값을 알 수 없음을 나타냅니다. 스파크 NULL 값은 자체를 포함한 모든 값과 다릅니다. 두 Spark NULL 값 또는 NULL 값과 다른 값 간의 비교는 각 NULL의 값을 알 수 없기 때문에 알 수 없음을 반환합니다.  

이 동작은 모든 값과 다르지만 그 자체와 동일한 `null` C# 의미 체계를 따르는 U-SQL과 다릅니다.  

따라서 U-SQL에서 `SELECT` NULL `WHERE column_name = NULL` 값이 `column_name`있는 경우에도 0행을 반환하는 SparkSQL 문은 로 설정된 `column_name` 행을 `null`반환합니다. 마찬가지로 U-SQL에서 null이 아닌 값이 `SELECT` `WHERE column_name != NULL` `column_name`있는 경우에도 0 행을 반환하는 Spark 문은 null이 아닌 행을 반환합니다. 따라서 U-SQL null 검사 의미 체계를 원하는 경우 [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) 및 [isnotnull(또는](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) 해당 DSL 등가)을 각각 사용해야 합니다.

## <a name="transform-u-sql-catalog-objects"></a>U-SQL 카탈로그 개체 변환

한 가지 큰 차이점은 U-SQL 스크립트가 카탈로그 개체를 사용할 수 있다는 것입니다.

Spark는 Hive Meta 저장소 개념(주로 데이터베이스 및 테이블)에 대한 지원을 제공하므로 U-SQL 데이터베이스및 스키마를 Hive 데이터베이스에 매핑하고 U-SQL 테이블을 스파크 테이블에 매핑할 수 있지만 뷰, 테이블 값 [함수(TVF),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)저장 프로시저, U-SQL 어셈블리, 외부 데이터 원본 등에 대한 지원은 없습니다.

뷰, TVF, 저장 프로시저 및 어셈블리와 같은 U-SQL 코드 개체는 Spark의 코드 함수 및 라이브러리를 통해 모델링되고 호스트 언어의 함수 및 절차 추상화 메커니즘(예: 가져오기를 통해 참조)을 사용하여 참조할 수 있습니다. 파이썬 모듈 또는 스칼라 함수 참조).

U-SQL 카탈로그가 프로젝트와 팀 간에 데이터 및 코드 개체를 공유하는 데 사용된 경우 공유에 해당하는 메커니즘을 사용해야 합니다(예: 코드 개체 공유를 위한 Maven).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>U-SQL 행 집합 식 및 SQL 기반 스칼라 식 변환

U-SQL의 핵심 언어는 행 집합을 변환하고 SQL을 기반으로 합니다. 다음은 U-SQL에서 제공하는 가장 일반적인 행 집합 식의 완전하지 않은 목록입니다.

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+집계+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` 식
- `CROSS`/`OUTER``APPLY` 식
- `PIVOT`/`UNPIVOT`식
- `VALUES`행 집합 생성자

- 식 설정`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

또한 U-SQL은 다음과 같은 다양한 SQL 기반 스칼라 식을 제공합니다.

- `OVER`창식
- 내장 된 애그리게이터 및 순위 함수`SUM` `FIRST` (기타)
- 가장 친숙한 SQL 스칼라 표현식 중 `CASE` `LIKE`일부:`NOT` `IN`" `AND` `OR` , "

Spark는 이러한 표현식의 대부분에 대해 DSL 및 SparkSQL 형식 모두에서 동등한 표현식을 제공합니다. Spark에서 기본적으로 지원되지 않는 일부 표현식은 네이티브 스파크 표현식과 시맨티컬에 상응하는 패턴을 조합하여 다시 작성해야 합니다. 예를 들어 `OUTER UNION` 투영과 공용 구조체의 동등한 조합으로 변환해야 합니다.

NULL 값을 서로 다르게 처리하기 때문에 비교중인 두 열이 NULL 값을 포함하는 경우 U-SQL 조인은 항상 행과 일치하지만 Spark의 조인은 명시적 null 검사가 추가되지 않는 한 해당 열과 일치하지 않습니다.

## <a name="transform-other-u-sql-concepts"></a>다른 U-SQL 개념 변환

또한 U-SQL은 SQL Server 데이터베이스에 대한 페더레이션 쿼리, 매개 변수, 스칼라 및 람다 식 변수, 시스템 변수, `OPTION` 힌트 와 같은 다양한 다른 기능과 개념을 제공합니다.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>SQL Server 데이터베이스/외부 테이블에 대한 페더레이션된 쿼리

U-SQL은 Azure SQL Database에 대한 직접 쿼리뿐만 아니라 데이터 원본 및 외부 테이블을 제공합니다. Spark는 동일한 개체 추상화를 제공하지 는 않지만 SQL 데이터베이스를 쿼리하는 데 사용할 수 있는 [Azure SQL Database용 Spark 커넥터를](../sql-database/sql-database-spark-connector.md) 제공합니다.

### <a name="u-sql-parameters-and-variables"></a>U-SQL 매개 변수 및 변수

매개 변수 및 사용자 변수는 Spark 및 해당 호스팅 언어에서 동일한 개념을 갖습니다.

예를 들어 스칼라에서 키워드로 변수를 정의할 `var` 수 있습니다.

```
var x = 2 * 3;
println(x)
```

U-SQL의 시스템 변수(시작하는 `@@`변수)는 다음 두 가지 범주로 나눌 수 있습니다.

- 스크립트 동작에 영향을 주는 특정 값으로 설정할 수 있는 설정 가능한 시스템 변수
- 시스템 및 직무 수준 정보를 조회하는 정보 시스템 변수

설정 가능한 시스템 변수의 대부분은 Spark에서 직접적인 동등하지 않습니다. 일부 정보 시스템 변수는 작업 실행 중에 정보를 인수로 전달하여 모델링할 수 있으며, 다른 변수는 Spark의 호스팅 언어에서 동등한 기능을 가질 수 있습니다.

### <a name="u-sql-hints"></a>U-SQL 힌트

U-SQL은 쿼리 최적화 프로그램 및 실행 엔진에 힌트를 제공하는 몇 가지 구문 방법을 제공합니다.  

- U-SQL 시스템 변수 설정
- 데이터 `OPTION` 또는 계획 힌트를 제공하기 위해 행 집합 식과 연결된 절
- 조인 식구의 조인 힌트(예: `BROADCASTLEFT`)

Spark의 비용 기반 쿼리 최적화 프로그램에는 힌트를 제공하고 쿼리 성능을 조정하는 자체 기능이 있습니다. 해당 문서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

- [U-SQL 개발자를 위한 스파크 데이터 형식 이해](understand-spark-data-formats.md)
- [Apache Spark용 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)
- [Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory에서 Hadoop Hive 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight에서 Apache Spark란](../hdinsight/spark/apache-spark-overview.md)
