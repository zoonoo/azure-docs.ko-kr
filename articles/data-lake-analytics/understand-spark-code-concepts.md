---
title: Azure Data Lake Analytics U-SQL 개발자를 위한 Apache Spark 코드 개념을 이해합니다.
description: 이 문서에서는 U-SQL 개발자가 Spark 코드 개념을 이해하는 데 유용한 Apache Spark 개념을 설명합니다.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 4faa48a9edb5ea157fde67e4a4f3008864342075
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639764"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>U-SQL 개발자를 위한 Apache Spark 코드 이해

이 섹션에서는 Apache Spark로 U-SQL 스크립트를 변환하는 방법에 대한 대략적인 참고 자료를 제공합니다.

- [두 언어의 처리 패러다임 비교](#understand-the-u-sql-and-spark-language-and-processing-paradigms)로 시작합니다.
- 다음 방법에 대한 팁을 제공합니다.
   - U-SQL의 [행 집합 식](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)을 포함하는 [스크립트 변환](#transform-u-sql-scripts)
   - [.NET 코드](#transform-net-code)
   - [데이터 형식](#transform-typed-values)
   - [카탈로그 개체](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>U-SQL 및 Spark 언어와 처리 패러다임 이해

Azure Data Lake Analytics의 U-SQL 스크립트를 Spark로 마이그레이션하기 전에 두 시스템의 일반적인 언어 및 처리 방법을 이해하면 유용합니다.

U-SQL은 데이터 흐름 패러다임을 사용하며 .NET으로 작성된 사용자 코드(예: C#), Python 및 R을 쉽게 포함하고 스케일 아웃하는 데 사용할 수 있는, SQL과 유사한 선언적 쿼리 언어입니다. 사용자 확장은 단순한 식이나 사용자 정의 함수를 구현할 수 있지만, 사용자에게 소위 사용자 정의 연산자를 구현해서 사용자 지정 연산자가 행 집합 수준의 변환, 추출 및 작성 출력을 구현할 수 있게 하는 기능도 제공할 수 있습니다.

Spark는 Scala, Java, Python, .NET 등의 여러 언어 바인딩을 제공하는 스케일 아웃 프레임워크입니다. 이러한 언어 중 하나로 코드를 주로 작성하는 경우에는 RDD(탄력적 분산 데이터 세트), 데이터 프레임 및 데이터 세트라는 데이터 추상화를 만든 다음 LINQ와 유사한 DSL(Domain-Specific Language)을 사용해 변환합니다. 또한 데이터 프레임 및 데이터 세트 추상화에 선언적 하위 언어로 SparkSQL을 제공합니다. DSL은 변환 및 동작이라는 두 가지 범주의 작업이 가능합니다. 데이터 추상화에 변환을 적용하면 변환이 실행되지 않습니다. 대신 작업을 사용하여 평가용으로 제출할 실행 계획을 빌드합니다(예: 결과를 임시 테이블 또는 파일에 기록하거나 결과 인쇄).

따라서 U-SQL 스크립트를 Spark 프로그램으로 변환하는 경우 최소한 데이터 프레임 추상화(현재 가장 자주 사용되는 데이터 추상화)를 생성하는 데 사용할 언어를 결정하고 DSL 또는 SparkSQL을 사용하여 선언적 데이터 흐름 변환을 쓸지 여부를 결정해야 합니다. 일부 복잡한 경우에는 U-SQL 스크립트를 Azure Batch 또는 Azure Functions를 사용하여 구현된 Spark 시퀀스 및 기타 단계로 분할해야 할 수 있습니다.

또한 Azure Data Lake Analytics는 서버리스 작업 서비스 환경에서 U-SQL을 제공하지만 Azure Databricks와 Azure HDInsight는 모두 클러스터 서비스의 형식으로 Spark를 제공합니다. 애플리케이션을 변환할 때 이제는 클러스터 만들기, 크기 조정, 스케일링 및 해제의 영향을 고려해야 합니다.

## <a name="transform-u-sql-scripts"></a>U-SQL 스크립트 변환

U-SQL 스크립트는 다음 처리 패턴을 따릅니다.

1. 데이터는 `EXTRACT` 명령문, 위치 혹은 파일 집합 사양, 기본 제공 혹은 사용자 정의된 추출기와 원하는 스키마, 혹은 U-SQL 테이블(관리형 혹은 외부 테이블)을 사용해 구조화되지 않은 파일에서 읽습니다. 데이터는 행 집합으로 표시됩니다.
2. 행 집합은 행 집합에 U-SQL 식을 적용하고 새 행 집합을 생성하는 여러 U-SQL 명령문으로 변환됩니다.
3. 마지막으로 결과 행 집합은 위치와 기본 제공 또는 사용자 정의 출력자를 지정하는 `OUTPUT` 명령문을 사용하는 파일 또는 U-SQL 테이블로 출력됩니다.

스크립트는 지연 평가되므로 각 추출 및 변환 단계가 식 트리로 구성되고 전체적으로 평가됩니다(데이터 흐름).

Spark 프로그램은 Spark 커넥터를 사용하여 데이터를 읽고 데이터 프레임을 만든 다음, LINQ와 유사한 DSL 또는 SparkSQL을 사용하여 데이터 프레임에 변환을 적용하고, 결과를 파일, 임시 Spark 테이블, 일부 프로그래밍 언어 형식 또는 콘솔에 쓴다는 면에서 유사합니다.

## <a name="transform-net-code"></a>.NET 코드 변환

U-SQL의 식 언어는 C#이며 사용자 지정 .NET 코드를 스케일 아웃하는 다양한 방법을 제공합니다.

Spark는 현재 .NET 코드 실행을 기본적으로 지원하지 않으므로 식을 해당 Spark, Scala, Java 또는 Python 식으로 다시 작성하거나 .NET 코드를 호출하는 방법을 찾아야 합니다. 스크립트에서 .NET 라이브러리를 사용하는 경우 다음과 같은 옵션을 사용할 수 있습니다.

- .NET 코드를 Scala 또는 Python으로 변환합니다.
- Azure Batch 프로세스를 사용하여 .NET 변환을 적용하는 여러 단계로 U-SQL 스크립트 분할(적합한 규모를 가져올 수 있는 경우)
- Moebius라는 오픈 소스에서 사용할 수 있는 .NET 언어 바인딩을 사용합니다. 이 프로젝트는 지원되는 상태가 아닙니다.

어떤 경우든 U-SQL 스크립트에 대량의 .NET 논리가 있는 경우 Microsoft 계정 담당자에게 문의하여 추가 참고 자료를 확인하세요.

다음은 U-SQL 스크립트의 .NET 및 C# 사용량에 대한 다양한 사례에 대한 세부 정보입니다.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>스칼라 인라인 U-SQL C# 식 변환

U-SQL의 식 언어는 C#입니다. 대부분의 스칼라 인라인 U-SQL 식은 성능 향상을 위해 기본적으로 구현되지만, 더 복잡한 식은 .NET 프레임워크 호출로 실행할 수 있습니다.

Spark에는 DSL 또는 SparkSQL의 일부로 고유한 스칼라 식 언어가 있으므로 호스팅 언어로 작성된 사용자 정의 함수를 호출할 수 있습니다.

U-SQL에 스칼라 식이 있는 경우 먼저 기본적으로 해석되는 가장 적합한 Spark 스칼라 식을 찾아 최대 성능을 확보한 후 다른 식을 선택한 Spark 호스팅 언어의 사용자 정의 함수로 매핑합니다.

.NET과 C#에는 Spark 호스팅 언어와 Spark의 DSL과는 다른 형식 의미 체계가 있다는 점을 유의하세요. 형식 시스템 차이점에 대한 자세한 내용은 [아래](#transform-typed-values)를 참조하세요.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>사용자 정의 스칼라 .NET 함수 및 사용자 정의 집계자 변환

U-SQL은 임의의 스칼라 .NET 함수를 호출하고 .NET으로 작성된 사용자 정의 집계자를 호출하는 방법을 제공합니다.

또한 Spark는 Spark의 DSL 및 SparkSQL에서 호출할 수 있는 대부분의 호스팅 언어로 작성된 사용자 정의 함수 및 사용자 정의 집계자에 대한 지원도 제공합니다.

### <a name="transform-user-defined-operators-udos"></a>UDO(사용자 정의 연산자) 변환

U-SQL은 .NET(또 일부 익스텐트의 경우는 Python과 R)으로 작성할 수 있는 추출기, 출력자, Reducer, 프로세서, 적용자 및 결합자와 같은 다양한 범주의 UDO(사용자 정의 연산자)를 제공합니다.

Spark는 연산자에 대해 동일한 확장성 모델을 제공하지 않지만 일부에 대해서는 동일한 기능을 제공합니다.

추출기 및 출력자에 해당하는 Spark는 Spark 커넥터입니다. 많은 U-SQL 추출기는 Spark 커뮤니티에서 거기에 대응하는 커넥터를 찾을 수 있습니다. 다른 사용자는 사용자 지정 커넥터를 작성해야 합니다. U-SQL 추출기가 복잡하고 여러 .NET 라이브러리를 사용하는 경우 interop을 사용하여 .NET 라이브러리를 호출해 데이터의 실제 처리를 수행하는 Scala에서 커넥터를 빌드하는 쪽이 더 적합할 수 있습니다. 이 경우에는 .NET Core 런타임을 Spark 클러스터에 배포하고 참조된 .NET 라이브러리가 .NET Standard 2.0 규격인지 확인해야 합니다.

다른 형식의 U-SQL UDO는 사용자 정의 함수와 집계자 및 의미상 적절한 Spark DLS 또는 SparkSQL 식을 사용하여 다시 작성해야 합니다. 예를 들어 데이터 프레임을 인수로 사용하고 데이터 프레임을 반환하는 함수로 패키지된 다양한 UDF 호출의 SELECT에 프로세서를 매핑할 수 있습니다.

### <a name="transform-u-sqls-optional-libraries"></a>U-SQL의 선택적 라이브러리 변환

U-SQL은 [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, AVRO 지원](https://github.com/Azure/usql/tree/master/Examples/DataFormats) 및 일부 [Cognitive Services 기능](data-lake-analytics-u-sql-cognitive.md)을 제공하는 선택적 및 데모 라이브러리 집합을 제공합니다.

Spark는 자체적인 Python 및 R 통합, pySpark 및 SparkR을 각각 제공하고, JSON, XML 및 AVRO를 읽고 쓰는 커넥터도 제공합니다.

Cognitive Services 라이브러리를 참조하는 스크립트를 변환해야 하는 경우 Microsoft 계정 담당자에게 문의하기를 권합니다.

## <a name="transform-typed-values"></a>입력된 값 변환

U-SQL의 형식 시스템은 .NET 형식 시스템이 기반이고 Spark에는 호스트 언어 바인딩의 영향을 받는 자체 형식 시스템이 있으므로, 작동하고 있는 형식들이 가까운지 확인해야 하며, 특정 형식의 경우 형식 범위, 정밀도 및/또는 규모가 약간 다를 수 있습니다. 또한 U-SQL과 Spark는 `null` 값을 다르게 처리합니다.

### <a name="data-types"></a>데이터 형식

다음 표에서는 지정된 U-SQL 형식에 대해 Spark, Scala 및 PySpark의 동등한 형식을 설명합니다.

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

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL 및 데이터 프레임 형식](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Scala 값 형식](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/2.3.1/api/python/_modules/pyspark/sql/types.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL 값 처리

Spark에서 기본 형식에는 NULL 값을 허용하는 반면 U-SQL에서는 비개체 스칼라를 null 허용으로 표시합니다. Spark를 사용하면 열을 null 허용하지 않음으로 정의할 수 있지만 제약 조건을 적용하지 않으면 [결과가 잘못될 수 있습니다](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

Spark에서 NULL은 값을 알 수 없다는 의미입니다. Spark NULL 값은 그 자체를 포함해서 다른 모든 값과 다릅니다. 두 Spark NULL 값을 비교하거나 NULL 값과 다른 값을 비교하면 각 NULL 값을 알 수 없기 때문에 알 수 없음을 반환합니다.  

이 동작은 `null` 값이 다른 값과는 다르지만 자신과는 동일한 C# 의미 체계를 따르는 U-SQL에서와는 다릅니다.  

따라서 SparkSQL `SELECT` 명령문이 `WHERE column_name = NULL`를 사용하면 `column_name`에 NULL 값이 있어도 반환하는 행이 없는 반면 U-SQL에서는 `column_name`가 `null`로 설정되어 있어서 행을 반환합니다. 마찬가지로 Spark `SELECT` 명령문이 `WHERE column_name != NULL`를 사용하면 `column_name`에 Null이 아닌 값이 있어도 반환하는 행이 없는 반면 U-SQL에서는 Null이 아닌 값을 가진 행을 반환합니다. 따라서 U-SQL Null 검사 의미 체계가 필요한 경우에는 각각 [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) 및 [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull)을 사용해야 합니다(또는 그에 상응하는 DSL).

## <a name="transform-u-sql-catalog-objects"></a>U-SQL 카탈로그 개체 변환

한 가지 중요한 차이점은 U-SQL 스크립트가 스크립트 자신의 카탈로그 개체를 사용할 수 있다는 것입니다. 이 중 상당수에는 직접적으로 상응하는 Spark가 없습니다.

Spark는 주로 데이터베이스 및 테이블인 Hive 메타 저장소 개념에 대한 지원을 제공하여 이를 통해 U-SQL 데이터베이스 및 스키마를 Hive 데이터베이스에 매핑할 수 있으며, U-SQL 테이블을 Spark 테이블에 매핑할 수 있습니다([U-SQL 테이블에 저장된 데이터 이동](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables) 참조). 그러나 보기, TVF(테이블 반환 함수), 저장된 프로시저, U-SQL 어셈블리, 외부 데이터 원본 등은 지원되지 않습니다.

보기, TVF, 저장된 프로시저, 어셈블리 등의 U-SQL 코드 개체는 Spark의 코드 함수 및 라이브러리를 통해 모델링되고 호스트 언어의 함수 및 절차적 추상화 메커니즘을 사용하여 참조될 수 있습니다. 예를 들어 Python 모듈을 가져오거나 Scala 함수를 참조할 수 있습니다.

U-SQL 카탈로그가 프로젝트와 팀에서 데이터 및 코드 개체를 공유하는 데 사용되는 경우 공유에 동등한 메커니즘(예: 코드 개체를 공유하기 위한 Maven)을 사용해야 합니다.

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>U-SQL 행 집합 식 및 SQL 기반 스칼라 식 변환

U-SQL의 코어 언어는 행 집합을 변환하고 있으며 SQL을 기반으로 합니다. 다음은 U-SQL에서 제공되는 가장 일반적인 행 집합 식의 비 한정적 목록입니다.

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+집계+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI` `JOIN` 식
- `CROSS`/`OUTER` `APPLY` 식
- `PIVOT`/`UNPIVOT` 식
- `VALUES` 행 집합 생성자

- 집합 식 `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

또한 U-SQL은 다음과 같은 다양한 SQL 기반 스칼라 식을 제공합니다

- `OVER` 창 작업 식
- 다양한 기본 제공 집계자 및 순위 지정 함수(`SUM`, `FIRST` 등)
- 가장 익숙한 SQL 스칼라 식 중 일부는 `CASE`, `LIKE`, (`NOT`) `IN`, `AND`, `OR` 등입니다.

Spark는 이러한 대부분의 식에 대해 DSL 및 SparkSQL 형식의 동일한 식을 제공합니다. Spark에서 기본적으로 지원되지 않는 일부 식은 기본 Spark 식과 의미상 동등한 패턴의 조합을 사용하여 다시 작성해야 합니다. 예를 들어, `OUTER UNION`은 프로젝션 및 공용 구조체에 상응하는 조합으로 변환되어야 합니다.

NULL 값을 다르게 처리하기 때문에 U-SQL 조인은 대조되는 두 열에 모두 NULL 값이 포함된 경우에는 항상 행과 일치하는 반면, Spark의 조인은 명시적 NULL 검사를 추가하지 않으면 이러한 열과 일치하지 않습니다.

## <a name="transform-other-u-sql-concepts"></a>다른 U-SQL 개념 변환

또한 U-SQL은 SQL Server 데이터베이스, 매개 변수, 스칼라 및 람다 식 변수, 시스템 변수, `OPTION` 힌트에 대한 페더레이션된 쿼리와 같은 다양한 다른 기능 및 개념을 제공합니다.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>SQL Server 데이터베이스/외부 테이블에 페더레이션된 쿼리

U-SQL은 데이터 원본 및 외부 테이블뿐만 아니라 Azure SQL Database에 대한 직접적인 쿼리를 제공합니다. Spark는 동일한 개체 추상화를 제공하지 않지만 SQL 데이터베이스를 쿼리하는 데 사용할 수 있는 [Azure SQL Database용 Spark 커넥터](../azure-sql/database/spark-connector.md)를 제공합니다.

### <a name="u-sql-parameters-and-variables"></a>U-SQL 매개 변수 및 변수

매개 변수 및 사용자 변수의 개념은 Spark 및 그 호스팅 언어와 동일합니다.

예를 들어 스칼라에서 `var` 키워드를 사용하여 변수를 정의할 수 있습니다.

```
var x = 2 * 3;
println(x)
```

U-SQL의 시스템 변수(변수는 `@@`로 시작함)는 다음과 같은 두 가지 범주로 나눌 수 있습니다.

- 스크립트 동작에 영향을 주는 특정 값으로 설정할 수 있는 설정 가능한 시스템 변수
- 시스템 및 작업 수준 정보를 조회하는 정보 제공 시스템 변수

설정 가능한 시스템 변수의 대부분은 Spark에 직접 대응되지 않습니다. 일부 정보 제공 시스템 변수는 작업 실행 중에 인수로 정보를 전달 하여 모델링할 수 있으며, 다른 변수는 Spark의 호스팅 언어에서 동일한 함수를 사용할 수 있습니다.

### <a name="u-sql-hints"></a>U-SQL 힌트

U-SQL은 쿼리 최적화 프로그램 및 실행 엔진에 힌트를 제공하는 여러 구문 방법을 제공합니다.  

- U-SQL 시스템 변수 설정
- 데이터 또는 계획 힌트를 제공하기 위해 행 집합 식과 연결된 `OPTION` 절입니다.
- 조인 식의 구문에 있는 조인 힌트(예: `BROADCASTLEFT`)

Spark의 비용 기반 쿼리 최적화 프로그램은 힌트를 제공하고 쿼리 성능을 조정하는 자체 기능을 제공합니다. 해당 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [U-SQL 개발자를 위한 Spark 데이터 형식 이해](understand-spark-data-formats.md)
- [Apache Spark용 .NET](/dotnet/spark/what-is-apache-spark-dotnet)
- [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory에서 Hadoop Hive 작업을 사용하여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight에서 Apache Spark란](../hdinsight/spark/apache-spark-overview.md)
