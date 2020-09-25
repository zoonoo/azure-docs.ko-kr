---
title: Apache Spark에 대 한 하이퍼스페이스 인덱스
description: 하이퍼스페이스 인덱스를 사용하여 Apache Spark에 대한 성능 최적화
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/12/2020
ms.author: euang
ms.reviewer: euang
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: f25aae64e117452cd689b68c5478e7431d1a21bf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249368"
---
# <a name="hyperspace-an-indexing-subsystem-for-apache-spark"></a>하이퍼스페이스: Apache Spark에 대 한 인덱싱 하위 시스템

하이퍼스페이스는 Apache Spark 사용자가 CSV, JSON, Parquet 등의 데이터 집합에 인덱스를 만들어 잠재적 쿼리 및 워크 로드 가속에 사용할 수 있는 기능을 도입 합니다.

이 문서에서는 하이퍼스페이스의 기본 사항을 강조 하 고, 간단한 설명을 강조 표시 하 고, 누구에 게 나 사용할 수 있는 방법을 보여 줍니다.

부인: 하이퍼스페이스는 다음과 같은 두 가지 상황에서 작업 또는 쿼리를 가속화 합니다.

* 쿼리에는 높은 선택이 있는 조건자에 대 한 필터가 포함 되어 있습니다. 예를 들어 백만 개 행에서 일치 하는 행 100 개를 선택할 수 있습니다.
* 쿼리에는 높은 섞 습니다가 필요한 조인이 포함 되어 있습니다. 예를 들어 10gb 데이터 집합을 사용 하 여 100 GB 데이터 집합을 조인할 수 있습니다.

작업을 신중 하 게 모니터링 하 고 인덱싱이 사례를 어떻게 지원 하는지 확인 하는 것이 좋습니다.

이 문서는 [Python](https://github.com/microsoft/hyperspace/blob/master/notebooks/python/Hitchhikers%20Guide%20to%20Hyperspace.ipynb), [c #](https://github.com/microsoft/hyperspace/blob/master/notebooks/csharp/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)및 [Scala](https://github.com/microsoft/hyperspace/blob/master/notebooks/scala/Hitchhikers%20Guide%20to%20Hyperspace.ipynb) 에 대 한 노트북 양식 에서도 사용할 수 있습니다.

## <a name="setup"></a>설치

먼저 새 Spark 세션을 시작 합니다. 이 문서는 하이퍼스페이스가 제공할 수 있는 기능을 보여 주기 위한 자습서 이므로 작은 데이터 집합에서 수행 하는 작업을 강조 하는 데 사용할 수 있는 구성 변경 작업을 수행 합니다. 

기본적으로 Spark는 한 쪽 조인의 데이터 크기가 작은 경우 (이 자습서에서 사용 하는 샘플 데이터의 경우) 브로드캐스트 조인을 사용 하 여 조인 쿼리를 최적화 합니다. 따라서 나중에 조인 쿼리를 실행할 때 Spark에서 정렬 병합 조인을 사용 하도록 브로드캐스트 조인을 사용 하지 않도록 설정 합니다. 이는 일반적으로 조인 쿼리를 가속화 하는 데 하이퍼스페이스 인덱스를 사용 하는 방법을 보여 주기 위한 것입니다.

다음 셀 실행의 출력에서는 성공적으로 생성 된 Spark 세션에 대 한 참조를 표시 하 고 수정 된 조인 구성에 대 한 값으로 '-1 '을 출력 하 여 브로드캐스트 조인이 성공적으로 비활성화 되었음을 나타냅니다.

:::zone pivot = "programming-language-scala"

```scala
// Start your Spark session
spark

// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

// Verify that BroadcastHashJoin is set correctly
println(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))

```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Start your Spark session.
spark

# Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

# Verify that BroadcastHashJoin is set correctly 
print(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.Conf().Set("spark.sql.autoBroadcastJoinThreshold", -1);

// Verify that BroadcastHashJoin is set correctly.
Console.WriteLine(spark.Conf().Get("spark.sql.autoBroadcastJoinThreshold"));
```

::: zone-end

결과:

```console
res3: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@297e957d
-1
```

## <a name="data-preparation"></a>데이터 준비

환경을 준비 하려면 샘플 데이터 레코드를 만들고 Parquet 데이터 파일로 저장 합니다. Parquet는 그림에 사용 되지만 CSV와 같은 다른 형식을 사용할 수도 있습니다. 후속 셀에서이 샘플 데이터 집합에 몇 개의 하이퍼스페이스 인덱스를 만들고 쿼리를 실행할 때 Spark에서 사용 하도록 설정 하는 방법을 확인할 수 있습니다.

예제 레코드는 부서 및 직원의 두 데이터 집합에 해당 합니다. 저장소 계정에서 생성 된 데이터 파일을 저장 하기 위해 원하는 위치를 가리키도록 "empLocation" 및 "deptLocation" 경로를 구성 해야 합니다.

다음 셀 실행의 출력은 데이터 집합의 내용을 triplets의 목록으로 표시 한 다음, 각 데이터 집합의 콘텐츠를 기본 위치에 저장 하기 위해 생성 된 데이터 프레임에 대 한 참조를 표시 합니다.

:::zone pivot = "programming-language-scala"

```scala
import org.apache.spark.sql.DataFrame

// Sample department records
val departments = Seq(
      (10, "Accounting", "New York"),
      (20, "Research", "Dallas"),
      (30, "Sales", "Chicago"),
      (40, "Operations", "Boston"))

// Sample employee records
val employees = Seq(
      (7369, "SMITH", 20),
      (7499, "ALLEN", 30),
      (7521, "WARD", 30),
      (7566, "JONES", 20),
      (7698, "BLAKE", 30),
      (7782, "CLARK", 10),
      (7788, "SCOTT", 20),
      (7839, "KING", 10),
      (7844, "TURNER", 30),
      (7876, "ADAMS", 20),
      (7900, "JAMES", 30),
      (7934, "MILLER", 10),
      (7902, "FORD", 20),
      (7654, "MARTIN", 30))

// Save sample data in the Parquet format
import spark.implicits._
val empData: DataFrame = employees.toDF("empId", "empName", "deptId")
val deptData: DataFrame = departments.toDF("deptId", "deptName", "location")

val empLocation: String = "/<yourpath>/employees.parquet"       //TODO ** customize this location path **
val deptLocation: String = "/<yourpath>/departments.parquet"     //TODO ** customize this location path **
empData.write.mode("overwrite").parquet(empLocation)
deptData.write.mode("overwrite").parquet(deptLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql.types import StructField, StructType, StringType, IntegerType

# Sample department records
departments = [(10, "Accounting", "New York"), (20, "Research", "Dallas"), (30, "Sales", "Chicago"), (40, "Operations", "Boston")]

# Sample employee records
employees = [(7369, "SMITH", 20), (7499, "ALLEN", 30), (7521, "WARD", 30), (7566, "JONES", 20), (7698, "BLAKE", 30)]

# Create a schema for the dataframe
dept_schema = StructType([StructField('deptId', IntegerType(), True), StructField('deptName', StringType(), True), StructField('location', StringType(), True)])
emp_schema = StructType([StructField('empId', IntegerType(), True), StructField('empName', StringType(), True), StructField('deptId', IntegerType(), True)])

departments_df = spark.createDataFrame(departments, dept_schema)
employees_df = spark.createDataFrame(employees, emp_schema)

#TODO ** customize this location path **
emp_Location = "/<yourpath>/employees.parquet"
dept_Location = "/<yourpath>/departments.parquet"

employees_df.write.mode("overwrite").parquet(emp_Location)
departments_df.write.mode("overwrite").parquet(dept_Location)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Sql.Types;

// Sample department records
var departments = new List<GenericRow>()
{
    new GenericRow(new object[] {10, "Accounting", "New York"}),
    new GenericRow(new object[] {20, "Research", "Dallas"}),
    new GenericRow(new object[] {30, "Sales", "Chicago"}),
    new GenericRow(new object[] {40, "Operations", "Boston"})
};

// Sample employee records
var employees = new List<GenericRow>() {
      new GenericRow(new object[] {7369, "SMITH", 20}),
      new GenericRow(new object[] {7499, "ALLEN", 30}),
      new GenericRow(new object[] {7521, "WARD", 30}),
      new GenericRow(new object[] {7566, "JONES", 20}),
      new GenericRow(new object[] {7698, "BLAKE", 30}),
      new GenericRow(new object[] {7782, "CLARK", 10}),
      new GenericRow(new object[] {7788, "SCOTT", 20}),
      new GenericRow(new object[] {7839, "KING", 10}),
      new GenericRow(new object[] {7844, "TURNER", 30}),
      new GenericRow(new object[] {7876, "ADAMS", 20}),
      new GenericRow(new object[] {7900, "JAMES", 30}),
      new GenericRow(new object[] {7934, "MILLER", 10}),
      new GenericRow(new object[] {7902, "FORD", 20}),
      new GenericRow(new object[] {7654, "MARTIN", 30})
};

// Save sample data in the Parquet format
var departmentSchema = new StructType(new List<StructField>()
{
    new StructField("deptId", new IntegerType()),
    new StructField("deptName", new StringType()),
    new StructField("location", new StringType())
});
var employeeSchema = new StructType(new List<StructField>()
{
    new StructField("empId", new IntegerType()),
    new StructField("empName", new StringType()),
    new StructField("deptId", new IntegerType())
});

DataFrame empData = spark.CreateDataFrame(employees, employeeSchema); 
DataFrame deptData = spark.CreateDataFrame(departments, departmentSchema); 

string empLocation = "/<yourpath>/employees.parquet";       //TODO ** customize this location path **
string deptLocation = "/<yourpath>/departments.parquet";     //TODO ** customize this location path **
empData.Write().Mode("overwrite").Parquet(empLocation);
deptData.Write().Mode("overwrite").Parquet(deptLocation);

```

::: zone-end

결과:

```console
import org.apache.spark.sql.DataFrame  
departments: Seq[(Int, String, String)] = List((10,Accounting,New York), (20,Research,Dallas), (30,Sales,Chicago), (40,Operations,Boston))  
employees: Seq[(Int, String, Int)] = List((7369,SMITH,20), (7499,ALLEN,30), (7521,WARD,30), (7566,JONES,20), (7698,BLAKE,30), (7782,CLARK,10), (7788,SCOTT,20), (7839,KING,10), (7844,TURNER,30), (7876,ADAMS,20), (7900,JAMES,30), (7934,MILLER,10), (7902,FORD,20), (7654,MARTIN,30))  
import spark.implicits._  
empData: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
empLocation: String = /your-path/employees.parquet  
deptLocation: String = /your-path/departments.parquet  
```

만든 Parquet 파일의 내용을 확인 하 여 올바른 형식으로 예상 레코드가 포함 되어 있는지 확인 합니다. 나중에 이러한 데이터 파일을 사용 하 여 하이퍼스페이스 인덱스를 만들고 샘플 쿼리를 실행 합니다.

다음 셀을 실행 하면 employee 및 학과 데이터 프레임의 행을 테이블 형식으로 표시 하는 및 출력이 생성 됩니다. 사용자가 이전 셀에서 만든 triplets 중 하 나와 일치 하는 14 명의 직원과 4 개의 부서가 있어야 합니다.

:::zone pivot = "programming-language-scala"

```scala
// empLocation and deptLocation are the user defined locations above to save parquet files
val empDF: DataFrame = spark.read.parquet(empLocation)
val deptDF: DataFrame = spark.read.parquet(deptLocation)

// Verify the data is available and correct
empDF.show()
deptDF.show()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# emp_Location and dept_Location are the user-defined locations above to save parquet files
emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

# Verify the data is available and correct
emp_DF.show()
dept_DF.show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// empLocation and deptLocation are the user-defined locations above to save parquet files
DataFrame empDF = spark.Read().Parquet(empLocation);
DataFrame deptDF = spark.Read().Parquet(deptLocation);

// Verify the data is available and correct
empDF.Show();
deptDF.Show();

```

::: zone-end

결과:

```console
empDF: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDF: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

```console
|EmpId|EmpName|DeptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
| 7900|  JAMES|    30|
| 7934| MILLER|    10|
| 7839|   KING|    10|
| 7566|  JONES|    20|
| 7698|  BLAKE|    30|
| 7782|  CLARK|    10|
| 7788|  SCOTT|    20|
| 7902|   FORD|    20|
| 7654| MARTIN|    30|  
```

&nbsp; &nbsp;

```console
|DeptId|  DeptName|Location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="indexes"></a>인덱스

하이퍼스페이스를 사용 하면 지속형 데이터 파일에서 검색 된 레코드에 대해 인덱스를 만들 수 있습니다. 성공적으로 생성 된 후에는 인덱스에 해당 하는 항목이 하이퍼스페이스의 메타 데이터에 추가 됩니다. 이 메타 데이터는 나중에 쿼리를 처리 하는 동안 Apache Spark의 최적화 프로그램에서 적절 한 인덱스를 찾고 사용 하는 데 사용 됩니다.

인덱스를 만든 후에는 다음과 같은 몇 가지 작업을 수행할 수 있습니다.

* **기본 데이터가 변경 되 면 새로 고칩니다.** 기존 인덱스를 새로 고쳐 변경 내용을 캡처할 수 있습니다.
* **인덱스가 필요 하지 않은 경우 삭제 합니다.** 일시 삭제를 수행할 수 있습니다. 즉, 인덱스는 실제로 삭제 되지 않지만 작업에서 더 이상 사용 되지 않도록 "삭제 됨"으로 표시 됩니다.
* **인덱스가 더 이상 필요 하지 않은 경우에는입니다.** 인덱스를 진공 할 수 있으며,이 경우에는 하이퍼스페이스의 메타 데이터에서 인덱스 내용 및 연결 된 메타 데이터를 완전히 실제로 삭제 합니다.

새로 고침 기본 데이터가 변경 되 면이를 캡처하기 위해 기존 인덱스를 새로 고칠 수 있습니다.
삭제 인덱스가 필요 하지 않은 경우에는 삭제를 수행할 수 있습니다. 즉, 인덱스는 실제로 삭제 되지 않고 ' 삭제 됨 '으로 표시 되므로 작업에서 더 이상 사용 되지 않습니다.

다음 섹션에서는 하이퍼스페이스에서 이러한 인덱스 관리 작업을 수행 하는 방법을 보여 줍니다.

먼저 필요한 라이브러리를 가져오고 하이퍼스페이스의 인스턴스를 만들어야 합니다. 나중에이 인스턴스를 사용 하 여 샘플 데이터에 대 한 인덱스를 만들고 해당 인덱스를 수정 하는 다양 한 하이퍼스페이스 Api를 호출 합니다.

다음 셀 실행의 출력에는 하이퍼스페이스의 생성 된 인스턴스에 대 한 참조가 표시 됩니다.

:::zone pivot = "programming-language-scala"

```scala
// Create an instance of Hyperspace
import com.microsoft.hyperspace._

val hyperspace: Hyperspace = Hyperspace()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create an instance of Hyperspace
hyperspace = Hyperspace(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create an instance of Hyperspace
using Microsoft.Spark.Extensions.Hyperspace;

Hyperspace hyperspace = new Hyperspace(spark);

```

::: zone-end

결과:

```console
import com.microsoft.hyperspace._  
hyperspace: com.microsoft.hyperspace.Hyperspace = com.microsoft.hyperspace.Hyperspace@1432f740
```

## <a name="create-indexes"></a>인덱스 만들기

하이퍼스페이스 인덱스를 만들려면 다음 두 가지 정보를 제공 해야 합니다.

* 인덱싱할 데이터를 참조 하는 Spark 데이터 프레임.
* 인덱스 구성 개체인 IndexConfig는 인덱스 이름 및 인덱스의 인덱싱된 열과 포괄 열을 지정 합니다.

먼저 샘플 데이터에 세 개의 하이퍼스페이스 인덱스 ("deptIndex1" 및 "deptIndex2" 라는 부서 데이터 집합의 두 인덱스 및 "empIndex" 라는 직원 데이터 집합에 대 한 인덱스)를 만듭니다. 각 인덱스에 대해 인덱싱된 열 및 포괄 열에 대해 열 목록과 함께 이름을 캡처하기 위해 해당 하는 IndexConfig가 필요 합니다. 다음 셀을 실행 하면 이러한 IndexConfigs 만들어지고 해당 출력에 나열 됩니다.

> [!Note]
> 인덱스 열은 필터 또는 조인 조건에 표시 되는 열입니다. 포괄 열은 선택/프로젝트에 표시 되는 열입니다.

예를 들어 다음 쿼리에서는

```sql
SELECT X
FROM T
WHERE Y = 2
```

X는 인덱스 열이 될 수 있고 Y는 포괄 열이 될 수 있습니다.

:::zone pivot = "programming-language-scala"

```scala
// Create index configurations
import com.microsoft.hyperspace.index.IndexConfig

val empIndexConfig: IndexConfig = IndexConfig("empIndex", Seq("deptId"), Seq("empName"))
val deptIndexConfig1: IndexConfig = IndexConfig("deptIndex1", Seq("deptId"), Seq("deptName"))
val deptIndexConfig2: IndexConfig = IndexConfig("deptIndex2", Seq("location"), Seq("deptName"))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create index configurations

emp_IndexConfig = IndexConfig("empIndex1", ["deptId"], ["empName"])
dept_IndexConfig1 = IndexConfig("deptIndex1", ["deptId"], ["deptName"])
dept_IndexConfig2 = IndexConfig("deptIndex2", ["location"], ["deptName"])

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Extensions.Hyperspace.Index;

var empIndexConfig = new IndexConfig("empIndex", new string[] {"deptId"}, new string[] {"empName"});
var deptIndexConfig1 = new IndexConfig("deptIndex1", new string[] {"deptId"}, new string[] {"deptName"});
var deptIndexConfig2 = new IndexConfig("deptIndex2", new string[] {"location"}, new string[] {"deptName"});

```

::: zone-end

결과:

```console
import com.microsoft.hyperspace.index.IndexConfig  
empIndexConfig: com.microsoft.hyperspace.index.IndexConfig = [indexName: empIndex; indexedColumns: deptid; includedColumns: empname]  
deptIndexConfig1: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex1; indexedColumns: deptid; includedColumns: deptname]  
deptIndexConfig2: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex2; indexedColumns: location; includedColumns: deptname]  
```
이제 인덱스 구성을 사용 하 여 세 개의 인덱스를 만듭니다. 이러한 목적을 위해 하이퍼스페이스 인스턴스에서 "createIndex" 명령을 호출 합니다. 이 명령에는 인덱스 구성 및 인덱싱할 행이 포함 된 데이터 프레임가 필요 합니다. 다음 셀을 실행 하면 3 개의 인덱스가 생성 됩니다.

:::zone pivot = "programming-language-scala"

```scala
// Create indexes from configurations
import com.microsoft.hyperspace.index.Index

hyperspace.createIndex(empDF, empIndexConfig)
hyperspace.createIndex(deptDF, deptIndexConfig1)
hyperspace.createIndex(deptDF, deptIndexConfig2)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create indexes from configurations

hyperspace.createIndex(emp_DF, emp_IndexConfig)
hyperspace.createIndex(dept_DF, dept_IndexConfig1)
hyperspace.createIndex(dept_DF, dept_IndexConfig2)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create indexes from configurations
hyperspace.CreateIndex(empDF, empIndexConfig);
hyperspace.CreateIndex(deptDF, deptIndexConfig1);
hyperspace.CreateIndex(deptDF, deptIndexConfig2);

```

::: zone-end

결과:

```console
import com.microsoft.hyperspace.index.Index
```

## <a name="list-indexes"></a>인덱스 나열

다음 코드는 하이퍼스페이스 인스턴스에서 사용 가능한 모든 인덱스를 나열 하는 방법을 보여 줍니다. 추가 작업을 수행할 수 있도록 기존 인덱스에 대 한 정보를 Spark 데이터 프레임로 반환 하는 "인덱스" API를 사용 합니다. 

예를 들어, 해당 콘텐츠를 확인 하거나 추가로 분석할 수 있도록이 데이터 프레임에 대 한 유효한 작업을 호출할 수 있습니다. 예를 들어 특정 인덱스를 필터링 하거나 원하는 속성에 따라 그룹화 하는 등의 작업을 수행할 수 있습니다.

다음 셀에서는 데이터 프레임의 ' show ' 작업을 사용 하 여 행을 완전히 인쇄 하 고 인덱스의 세부 정보를 테이블 형식으로 표시 합니다. 각 인덱스에 대해 하이퍼스페이스가 메타 데이터에 저장 한 모든 정보를 볼 수 있습니다. 다음에 대 한 알림이 즉시 표시 됩니다.

* indexName, config.xml, includedColumns 및 status입니다. status는 사용자가 일반적으로 참조 하는 필드입니다.
* dfSignature는 하이퍼스페이스에 의해 자동으로 생성 되며 각 인덱스에 대해 고유 합니다. 하이퍼스페이스는 내부적으로이 서명을 사용 하 여 인덱스를 유지 관리 하 고 쿼리 시이를 활용 합니다.


다음 출력에서 세 인덱스는 모두 "활성" 상태이 고 해당 이름, 인덱싱된 열 및 포괄 열은 위의 인덱스 구성에서 정의한 내용과 일치 해야 합니다.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.Indexes().Show();

```

::: zone-end

결과:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="delete-indexes"></a>인덱스 삭제

"DeleteIndex" API를 사용 하 고 인덱스 이름을 제공 하 여 기존 인덱스를 삭제할 수 있습니다. 인덱스 삭제는 일시 삭제를 수행 합니다 .이는 주로 하이퍼스페이스 메타 데이터의 인덱스 상태를 "활성"에서 "삭제 됨"으로 업데이트 합니다. 이렇게 하면 이후 쿼리 최적화에서 삭제 된 인덱스가 제외 되 고 하이퍼스페이스는 더 이상 쿼리에 대해 해당 인덱스를 선택 하지 않습니다. 

그러나 삭제 된 인덱스에 대 한 인덱스 파일은 일시 삭제 되기 때문에 계속 사용할 수 있으므로 사용자가 요청 하는 경우 인덱스를 복원할 수 있습니다.

다음 셀은 이름이 "deptIndex2" 인 인덱스를 삭제 하 고 그 후에 하이퍼스페이스 메타 데이터를 나열 합니다. 출력은 "deptIndex2"를 제외 하 고 "인덱스 나열"의 위 셀과 유사 해야 합니다 .이 경우 이제 상태가 "삭제 됨"으로 변경 되어야 합니다.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
hyperspace.DeleteIndex("deptIndex2");

hyperspace.Indexes().Show();

```

::: zone-end

결과:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="restore-indexes"></a>인덱스 복원

"RestoreIndex" API를 사용 하 여 삭제 된 인덱스를 복원할 수 있습니다. 이렇게 하면 최신 버전의 인덱스를 활성 상태로 되돌려 쿼리에 대해 다시 사용할 수 있습니다. 다음 셀은 "restoreIndex" 사용의 예를 보여 줍니다. "DeptIndex1"를 삭제 하 고 복원 합니다. "DeleteIndex" 명령을 호출 하 고 "restoreIndex"를 호출한 후 "활성" 상태로 다시 전환한 후 "deptIndex1"가 "삭제 됨" 상태로 표시 됩니다.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex1")

hyperspace.indexes.show

hyperspace.restoreIndex("deptIndex1")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex1")
hyperspace.indexes().show()
hyperspace.restoreIndex("deptIndex1")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.DeleteIndex("deptIndex1");
hyperspace.Indexes().Show();
hyperspace.RestoreIndex("deptIndex1");
hyperspace.Indexes().Show();

```

::: zone-end

결과:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.indexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

&nbsp; &nbsp;

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="vacuum-indexes"></a>인덱스

하드 삭제를 수행할 수 있습니다. 즉, **vacuumIndex** 명령을 사용 하 여 파일을 완전히 제거 하 고 삭제 된 인덱스에 대 한 메타 데이터 항목을 수행할 수 있습니다. 이 작업은 되돌릴 수 없습니다. 모든 인덱스 파일을 물리적으로 삭제 하므로 하드 삭제가 됩니다.

다음 셀은 "deptIndex2" 인덱스를 vacuums 베 큐 밍 후에 하이퍼스페이스 메타 데이터를 표시 합니다. "DeptIndex1" 및 "empIndex"의 두 인덱스에 대 한 메타 데이터 항목이 "활성" 상태이 고 "deptIndex2"에 대 한 항목이 없다는 것을 볼 수 있습니다.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.vacuumIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.vacuumIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.VacuumIndex("deptIndex2");
hyperspace.Indexes().Show();

```

::: zone-end

결과:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="enable-or-disable-hyperspace"></a>하이퍼스페이스 사용 또는 사용 안 함

하이퍼스페이스는 Spark를 사용 하 여 인덱스 사용을 설정 하거나 해제 하는 Api를 제공 합니다.

* **Enablehyperspace** 명령을 사용 하면 하이퍼스페이스 최적화 규칙이 Spark 최적화 프로그램에 표시 되 고 기존 하이퍼스페이스 인덱스를 악용 하 여 사용자 쿼리를 최적화할 수 있습니다.
* **Disablehyperspace** 명령을 사용 하면 쿼리 최적화 중에 하이퍼스페이스 규칙이 더 이상 적용 되지 않습니다. 하이퍼스페이스를 사용 하지 않도록 설정 해도 생성 된 인덱스는 그대로 유지 되므로 생성 된 인덱스에 영향을 미치지 않습니다

다음 셀에서는 이러한 명령을 사용 하 여 하이퍼스페이스를 사용 하거나 사용 하지 않도록 설정 하는 방법을 보여 줍니다. 출력은 구성이 업데이트 되는 기존 Spark 세션에 대 한 참조를 표시 합니다.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

// Disable Hyperspace
spark.disableHyperspace
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

# Disable Hyperspace
Hyperspace.disable(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

// Disable Hyperspace
spark.DisableHyperspace();

```

::: zone-end

결과:

```console
res48: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb  
res51: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb
```

## <a name="index-usage"></a>인덱스 사용

Spark에서 쿼리를 처리 하는 동안 하이퍼스페이스 인덱스를 사용 하도록 하려면 하이퍼스페이스가 사용 하도록 설정 되어 있는지 확인 해야 합니다.

다음 셀은 하이퍼스페이스를 사용 하도록 설정 하 고 예제 쿼리를 실행 하는 데 사용 하는 예제 데이터 레코드를 포함 하는 두 개의 데이터 프레임를 만듭니다. 각 데이터 프레임에 대해 몇 가지 샘플 행이 인쇄 됩니다.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

val empDFrame: DataFrame = spark.read.parquet(empLocation)
val deptDFrame: DataFrame = spark.read.parquet(deptLocation)

empDFrame.show(5)
deptDFrame.show(5)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

emp_DF.show(5)
dept_DF.show(5)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

DataFrame empDFrame = spark.Read().Parquet(empLocation);
DataFrame deptDFrame = spark.Read().Parquet(deptLocation);

empDFrame.Show(5);
deptDFrame.Show(5);

```

::: zone-end

결과:

```console
res53: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.Spark™Session@39fe1ddb  
empDFrame: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDFrame: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
```

&nbsp; &nbsp;

```console
|empId|empName|deptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
```

&nbsp;&nbsp;상위 5 개 행 &nbsp; 만 표시 됩니다.&nbsp;

```console
|deptId|  deptName|location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="index-types"></a>인덱스 형식

현재, 하이퍼스페이스에는 두 가지 쿼리 그룹에 대 한 인덱스를 활용 하는 규칙이 있습니다.

* 조회 또는 범위 선택 필터링 조건자를 사용 하 여 선택 쿼리
* 같음 조인 조건자 (즉, 동등 조인)를 사용 하 여 쿼리를 조인 합니다.

## <a name="indexes-for-accelerating-filters"></a>필터 가속화를 위한 인덱스

첫 번째 예제 쿼리에서는 다음 셀에 표시 된 것 처럼 부서 레코드를 조회 합니다. SQL에서이 쿼리는 다음 예제와 같습니다.

```sql
SELECT deptName
FROM departments
WHERE deptId = 20
```

다음 셀을 실행 하면 출력이 표시 됩니다.

* 단일 부서 이름인 쿼리 결과
* Spark에서 쿼리를 실행 하는 데 사용 하는 쿼리 계획입니다.

쿼리 계획의 맨 아래에 있는 **Filescan** 연산자는 레코드를 읽은 데이터 원본을 보여 줍니다. 이 파일의 위치는 "deptIndex1" 인덱스의 최신 버전에 대 한 경로를 나타냅니다. 이 정보는 쿼리 및 하이퍼스페이스 최적화 규칙 사용에 따라 Spark에서 런타임에 적절 한 인덱스를 활용 하기로 결정 했습니다.

:::zone pivot = "programming-language-scala"

```scala
// Filter with equality predicate

val eqFilter: DataFrame = deptDFrame.filter("deptId = 20").select("deptName")
eqFilter.show()

eqFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with equality predicate

eqFilter = dept_DF.filter("""deptId = 20""").select("""deptName""")
eqFilter.show()

eqFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame eqFilter = deptDFrame.Filter("deptId = 20").Select("deptName");
eqFilter.Show();

eqFilter.Explain(true);

```

::: zone-end

결과:

```console
eqFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|DeptName|
|--------|
|Research|
```

&nbsp; &nbsp;

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), EqualTo(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

두 번째 예는 부서 레코드에 대 한 범위 선택 쿼리입니다. SQL에서이 쿼리는 다음 예제와 같습니다.

```sql
SELECT deptName
FROM departments
WHERE deptId > 20
```

첫 번째 예제와 마찬가지로 다음 셀의 출력에서는 쿼리 결과 (두 부서의 이름)와 쿼리 계획을 보여 줍니다. **Filescan** 연산자에서 데이터 파일의 위치는 "deptIndex1"를 사용 하 여 쿼리를 실행 하는 것을 보여 줍니다.

:::zone pivot = "programming-language-scala"

```scala
// Filter with range selection predicate

val rangeFilter: DataFrame = deptDFrame.filter("deptId > 20").select("deptName")
rangeFilter.show()

rangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with range selection predicate

rangeFilter = dept_DF.filter("""deptId > 20""").select("deptName")
rangeFilter.show()

rangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Filter with range selection predicate
DataFrame rangeFilter = deptDFrame.Filter("deptId > 20").Select("deptName");
rangeFilter.Show();

rangeFilter.Explain(true);

```

::: zone-end

결과:

```console
rangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|  DeptName|
|----------|
|Operations|
|     Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```
세 번째 예는 부서 ID에서 부서 및 직원 레코드를 조인 하는 쿼리입니다. 해당 하는 SQL 문은 다음과 같이 표시 됩니다.

```sql
SELECT employees.deptId, empName, departments.deptId, deptName
FROM   employees, departments
WHERE  employees.deptId = departments.deptId
```
다음 셀 실행의 출력은 쿼리 결과를 보여 줍니다. 여기에는 14 명의 직원 이름과 각 직원의 근무 부서 이름이 나와 있습니다. 또한 쿼리 계획은 출력에 포함 됩니다. 두 **Filescan** 연산자의 파일 위치는 Spark에서 "empIndex" 및 "deptIndex1" 인덱스를 사용 하 여 쿼리를 실행 하는 방법을 보여 줍니다.

:::zone pivot = "programming-language-scala"

```scala
// Join

val eqJoin: DataFrame =
      empDFrame.
      join(deptDFrame, empDFrame("deptId") === deptDFrame("deptId")).
      select(empDFrame("empName"), deptDFrame("deptName"))

eqJoin.show()

eqJoin.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Join

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

eqJoin.show()

eqJoin.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Join
DataFrame eqJoin =
      empDFrame
      .Join(deptDFrame, empDFrame.Col("deptId") == deptDFrame.Col("deptId"))
      .Select(empDFrame.Col("empName"), deptDFrame.Col("deptName"));

eqJoin.Show();

eqJoin.Explain(true);

```

::: zone-end

결과:

```console
eqJoin: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534] parquet

== Physical Plan ==
*(3) Project [empName#528, deptName#534]
+- *(3) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>, SelectedBucketsCount: 200 out of 200
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>, SelectedBucketsCount: 200 out of 200
```

## <a name="support-for-sql-semantics"></a>SQL 의미 체계 지원

인덱스 사용은 데이터 프레임 API 또는 Spark SQL을 사용 하는지 여부에 대해 투명 합니다. 다음 예에서는 해당 하는 경우 인덱스 사용을 보여 주는 SQL 형식의 동일한 조인 예제를 보여 줍니다.

:::zone pivot = "programming-language-scala"

```scala
empDFrame.createOrReplaceTempView("EMP")
deptDFrame.createOrReplaceTempView("DEPT")

val joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql import SparkSession

emp_DF.createOrReplaceTempView("EMP")
dept_DF.createOrReplaceTempView("DEPT")

joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

empDFrame.CreateOrReplaceTempView("EMP");
deptDFrame.CreateOrReplaceTempView("DEPT");

var joinQuery = spark.Sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId");

joinQuery.Show();
joinQuery.Explain(true);

```

::: zone-end

결과:

```console

joinQuery: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
'Project ['EMP.empName, 'DEPT.deptName]
+- 'Filter ('EMP.deptId = 'DEPT.deptId)
   +- 'Join Inner
      :- 'UnresolvedRelation `EMP`
      +- 'UnresolvedRelation `DEPT`

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Filter (deptId#529 = deptId#533)
   +- Join Inner
      :- SubqueryAlias `emp`
      :  +- Relation[empId#527,empName#528,deptId#529] parquet
      +- SubqueryAlias `dept`
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empId#527,empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(5) Project [empName#528, deptName#534]
+- *(5) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="explain-api"></a>API 설명

인덱스는 유용 하지만 사용 여부를 어떻게 알 수 있나요? 하이퍼스페이스를 통해 사용자는 쿼리를 실행 하기 전에 원래 계획과 업데이트 된 인덱스 종속 계획을 비교할 수 있습니다. 명령 출력을 표시 하려면 HTML, 일반 텍스트 또는 콘솔 모드에서 선택할 수 있습니다.

다음 셀에서는 HTML을 사용 하는 예제를 보여 줍니다. 강조 표시 된 섹션은 사용 중인 인덱스와 원본 및 업데이트 계획의 차이를 나타냅니다.

:::zone pivot = "programming-language-scala"

```scala
spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin) { displayHTML }
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin, True, displayHTML)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

spark.Conf().Set("spark.hyperspace.explain.displayMode", "html");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.beginTag", "<b style=\"background:LightGreen\">");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.endTag", "</b>");

hyperspace.Explain(eqJoin, false, input => DisplayHTML(input));

```

::: zone-end

결과:

### <a name="plan-with-indexes"></a>인덱스를 사용 하 여 계획

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="plan-without-indexes"></a>인덱스 없이 계획

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [empName#528,deptId#529] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/employees.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="indexes-used"></a>사용 되는 인덱스

```console
deptIndex1:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/deptIndex1/v__=0
empIndex:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/empIndex/v__=0
```

## <a name="refresh-indexes"></a>인덱스 새로 고침

인덱스가 생성 된 원본 데이터가 변경 되 면 인덱스는 더 이상 데이터의 최신 상태를 캡처하지 않습니다. **Refreshindex** 명령을 사용 하 여 오래 된 인덱스를 새로 고칠 수 있습니다. 이 명령은 인덱스를 완전히 다시 작성 하 고 최신 데이터 레코드에 따라 업데이트 합니다. 다른 노트북에서 인덱스를 증분 방식으로 새로 고치는 방법을 보여 드리겠습니다.

다음 두 셀은이 시나리오에 대 한 예를 보여 줍니다.

* 첫 번째 셀은 원본 부서 데이터에 두 개 이상의 부서를 추가 합니다. 부서 목록을 읽고 인쇄 하 여 새 부서가 올바르게 추가 되었는지 확인 합니다. 출력에는 총 6 개 부서 (이전 4 개 및 두 개의 새)가 표시 됩니다. **Refreshindex** 를 호출 하면 인덱스가 새 부서를 캡처하도록 "deptIndex1"가 업데이트 됩니다.
* 두 번째 셀은 범위 선택 쿼리 예제를 실행 합니다. 이제 결과에는 위의 쿼리를 실행 했을 때 표시 되는 두 개의 학과가 포함 됩니다. 두 개의 부서는 추가 된 새로운 부서입니다.

### <a name="specific-index-refresh"></a>특정 인덱스 새로 고침

:::zone pivot = "programming-language-scala"

```scala
val extraDepartments = Seq(
      (50, "Inovation", "Seattle"),
      (60, "Human Resources", "San Francisco"))

val extraDeptData: DataFrame = extraDepartments.toDF("deptId", "deptName", "location")
extraDeptData.write.mode("Append").parquet(deptLocation)

val deptDFrameUpdated: DataFrame = spark.read.parquet(deptLocation)

deptDFrameUpdated.show(10)

hyperspace.refreshIndex("deptIndex1")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

extra_Departments = [(50, "Inovation", "Seattle"), (60, "Human Resources", "San Francisco")]

extra_departments_df = spark.createDataFrame(extra_Departments, dept_schema)
extra_departments_df.write.mode("Append").parquet(dept_Location)


dept_DFrame_Updated = spark.read.parquet(dept_Location)

dept_DFrame_Updated.show(10)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

var extraDepartments = new List<GenericRow>()
{
    new GenericRow(new object[] {50, "Inovation", "Seattle"}),
    new GenericRow(new object[] {60, "Human Resources", "San Francisco"})
};
    
DataFrame extraDeptData = spark.CreateDataFrame(extraDepartments, departmentSchema);
extraDeptData.Write().Mode("Append").Parquet(deptLocation);

DataFrame deptDFrameUpdated = spark.Read().Parquet(deptLocation);

deptDFrameUpdated.Show(10);

hyperspace.RefreshIndex("deptIndex1");

```

::: zone-end

결과:

```console
extraDepartments: Seq[(Int, String, String)] = List((50,Inovation,Seattle), (60,Human Resources,San Francisco))  
extraDeptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
deptDFrameUpdated: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

&nbsp; &nbsp;

```console  
|deptId|       deptName|     location|
|------|---------------|-------------|
|    60|Human Resources|San Francisco|
|    10|     Accounting|     New York|
|    50|      Inovation|      Seattle|
|    40|     Operations|       Boston|
|    20|       Research|       Dallas|
|    30|          Sales|      Chicago|
```

### <a name="range-selection"></a>범위 선택

:::zone pivot = "programming-language-scala"

```scala
val newRangeFilter: DataFrame = deptDFrameUpdated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

newRangeFilter = dept_DFrame_Updated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame newRangeFilter = deptDFrameUpdated.Filter("deptId > 20").Select("deptName");
newRangeFilter.Show();

newRangeFilter.Explain(true);

```

::: zone-end

결과:

```console
newRangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|       DeptName|
|---------------|
|Human Resources|
|      Inovation|
|     Operations|
|          Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#675]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Optimized Logical Plan ==
Project [deptName#675]
+- Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Physical Plan ==
*(1) Project [deptName#675]
+- *(1) Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- *(1) FileScan parquet [deptId#674,deptName#675] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="next-steps"></a>다음 단계

* [프로젝트 하이퍼스페이스](https://microsoft.github.io/hyperspace/)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
