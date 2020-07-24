---
title: 배열 및 중첩 된 구조로 스키마 분석
description: Apache Spark 및 SQL을 사용 하 여 배열 및 중첩 구조를 분석 하는 방법
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ad6761466cc958235557609e929e641a0311ee43
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999189"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 복잡 한 데이터 형식 분석

이 문서는 [Azure Cosmos DB에 대 한 Synapse 링크](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)의 Parquet 파일 및 컨테이너와 관련이 있습니다. 사용자가 Spark 또는 SQL을 사용 하 여 배열 또는 중첩 된 구조와 같은 복잡 한 스키마로 데이터를 읽거나 변환 하는 방법을 설명 합니다. 다음 예제는 단일 문서로 완료 되지만 Spark 또는 SQL을 사용 하 여 수십억 개의 문서로 쉽게 확장할 수 있습니다. 이 문서에 포함 된 코드는 PySpark (Python)를 사용 합니다.

## <a name="use-case"></a>사용 사례

복합 데이터 형식은 점점 일반적 이며, 중첩 된 스키마를 분석 하는 데이터 엔지니어의 과제를 나타내며, 배열은 시간이 많이 걸리고 복잡 한 SQL 쿼리를 포함 하는 경향이 있습니다. 또한 중첩 열 데이터 형식에 대 한 이름을 바꾸거나 캐스트 하는 것은 어려울 수 있습니다. 또한 깊게 중첩 된 개체로 작업 하는 경우 성능 문제가 발생 합니다.

데이터 엔지니어는 복잡 한 데이터 형식을 효율적으로 처리 하 고 누구나 쉽게 액세스할 수 있도록 하는 방법을 이해 해야 합니다.

다음 예에서는 Synapse Spark를 사용 하 여 개체를 데이터 프레임을 통해 플랫 구조체로 읽고 변환 합니다. Synapse SQL server를 사용 하지 않는 경우 이러한 개체를 직접 쿼리 하 고 해당 결과를 일반 테이블로 반환 합니다.

## <a name="what-are-arrays-and-nested-structures"></a>배열 및 중첩 구조체 란?

다음 개체는 [앱](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)정보에서 제공 됩니다. 이 개체에는 중첩 된 구조체와 중첩 된 구조체를 포함 하는 배열이 있습니다.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>배열 및 중첩 구조체의 스키마 예제
명령을 사용 하 여 개체의 데이터 프레임 ( **df**)의 스키마를 인쇄 하는 경우 `df.printschema` 다음 표현이 표시 됩니다.

* 노란색은 중첩 된 구조를 나타냄
* 녹색은 두 개의 요소가 포함 된 배열을 나타냅니다.

[![스키마 원본](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

문서를 Azure Cosmos DB 트랜잭션 저장소로 수집 **_rid**, **_ts**및 **_etag** 가 시스템에 추가 되었습니다.

위의 데이터 프레임은 5 개의 열과 1 개의 행만 계산 합니다. 변환 후 큐 레이트 데이터 프레임에는 13 개의 열과 2 개의 행이 테이블 형식으로 포함 됩니다.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Apache Spark를 사용 하 여 중첩 된 구조체를 평면화 하 고 배열 분해

Synapse Spark를 사용 하면 중첩 구조를 열과 배열 요소로 여러 행으로 쉽게 변환할 수 있습니다. 구현에는 다음 단계를 사용할 수 있습니다.

[![Spark 변환 단계](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**1 단계**: 중첩 된 스키마를 평면화 하는 함수를 정의 합니다. 이 함수는 변경 하지 않고 사용할 수 있습니다. [PySpark 노트북](quickstart-apache-spark-notebook.md) 에서 다음 함수를 사용 하 여 셀을 만듭니다.

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**2 단계**: 함수를 사용 하 여 데이터 프레임 (**df**)의 중첩 된 스키마를 새 데이터 프레임으로 평면화 합니다 `df_flat` .

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

표시 함수는 10 개의 열과 1 개의 행을 반환 해야 합니다. 배열 및 중첩 된 요소도 여전히 있습니다.

**3 단계**: `context_custom_dimensions` 데이터 프레임의 배열을 `df_flat` 새 데이터 프레임 변환 `df_flat_explode` 합니다. 다음 코드에서는 선택할 열도 정의 합니다.

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

표시 함수는 10 개의 열과 2 개의 행을 반환 해야 합니다. 다음 단계는 1 단계에서 정의한 함수를 사용 하 여 중첩 된 스키마를 평면화 하는 것입니다.

**4 단계**: 함수를 사용 하 여 데이터 프레임의 중첩 된 스키마를 `df_flat_explode` 새 데이터 프레임으로 평면화 합니다 `df_flat_explode_flat` .
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

표시 함수에는 13 개의 열과 2 개의 행이 표시 됩니다.

`printSchema`데이터 프레임의 함수는 `df_flat_explode_flat` 다음 결과를 반환 합니다.

[![스키마 최종](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>SQL server 서버를 사용 하 여 배열 및 중첩 구조를 직접 읽습니다.

SQL server 서버를 사용 하면 이러한 개체에 대 한 뷰 및 테이블을 쿼리하고 만들 수 있습니다.

먼저 데이터가 저장 되는 방식에 따라 사용자는 다음 분류를 사용 해야 합니다. 대문자로 표시 된 모든 항목은 사용 사례에 따라 다릅니다.

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = ACCOUNTNAME; database = DATABASENAME; collection = COLLECTIONNAME; region = 지역이 TOQUERY, SECRET = ' 해당 암호 ' |' CosmosDB ' (Synapse Link)|


> [!NOTE]
> SQL server 서버를 사용 하지 않는 경우 Azure Cosmos 및 AAD 통과에 대 한 연결 된 서비스 Synapse 링크가 지원 됩니다. 이 기능은 현재 Synapse 용 제어 미리 보기 링크에 있습니다.

각 필드를 다음과 같이 바꿉니다.
* ' 대량 초과 ' = 연결 하는 데이터 원본의 연결 문자열입니다.
* ' 위의 형식 ' = 원본에 연결 하는 데 사용 하는 형식입니다.

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

작업에는 다음과 같은 두 가지 유형이 있습니다.

첫 번째 작업 형식은 `contextdataeventTime` 중첩 된 요소인 eventTime를 참조 하는 이라는 열을 정의 하는 다음 코드 줄에 표시 됩니다. 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

이 줄에서는 중첩 요소를 참조 하는 contextdataeventTime 라는 열을 정의 합니다. Context>Data>eventTime

두 번째 작업 형식은 `cross apply` 를 사용 하 여 배열의 각 요소에 대 한 새 행을 만든 다음를 사용 하 여 첫 번째 글머리 기호 지점과 비슷한 각 중첩 된 개체를 정의 합니다. 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

배열에 4 개의 중첩 된 구조체가 있는 5 개의 요소가 있는 경우 SQL server를 사용 하지 않는 경우 5 개의 행과 4 개의 열이 반환 됩니다. 서버를 사용 하지 않는 SQL server는 내부에서 쿼리를 수행 하 고, 배열을 2 개의 행으로 매핑하고, 중첩 된 모든 구조를 열로 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Spark를 사용 하 여 Azure Cosmos DB에 대 한 Synapse 링크를 쿼리 하는 방법 알아보기](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet 중첩 형식 쿼리](./sql/query-parquet-nested-types.md) 