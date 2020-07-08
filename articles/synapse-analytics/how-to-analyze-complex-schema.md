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
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908028"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Synapse에서 복잡 한 데이터 형식 분석

이 문서는 **Azure Cosmos DB에 대 한 Azure Synapse 링크**의 Parquet 파일 및 컨테이너와 관련이 있습니다. 사용자가 Spark 또는 SQL을 사용 하 여 배열 또는 중첩 된 구조와 같은 복잡 한 스키마로 데이터를 읽거나 변환 하는 방법을 설명 합니다. 아래 예제는 단일 문서로 수행 되지만 Spark 또는 SQL을 사용 하 여 수십억 개의 문서로 쉽게 확장할 수 있습니다. 아래 코드는 PySpark (Python)를 사용 합니다.

## <a name="use-case"></a>사용 사례

최신 데이터 형식을 사용 하는 경우 복잡 한 데이터 형식은 일반적으로 데이터 엔지니어의 과제를 처리 하 고 나타내는 데 주로 사용 됩니다. 중첩 된 스키마와 배열을 분석 하면 몇 가지 과제가 발생 합니다.
* SQL 쿼리 작성에 대 한 복잡 한
* 중첩 열의 이름을 바꾸거나 데이터 형식을 캐스팅 하기 어렵습니다.
* 깊게 중첩 된 개체의 성능 저하 문제

데이터 엔지니어는 이러한 데이터 형식을 효율적으로 처리 하 고 모든 사용자가 쉽게 액세스할 수 있도록 하는 방법을 이해 해야 합니다.

아래 예제에서는 Synapse Spark를 사용 하 여 데이터 프레임을 통해 개체를 읽고 플랫 구조로 변환 합니다. 서버를 사용 하지 않는 Synapse SQL을 사용 하 여 이러한 개체를 직접 쿼리하고 해당 결과를 일반 테이블로 반환 합니다.

## <a name="what-are-arrays-and-nested-structures"></a>배열 및 중첩 구조체 란?

다음 개체는 앱 정보에서 제공 됩니다. 이 개체에는 중첩 된 구조체가 있지만 중첩 된 구조도 포함 하는 배열도 있습니다.

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
**Df. printschema**를 사용 하 여 해당 개체의 데이터 프레임 ( **df**)의 스키마를 인쇄 하는 경우 다음 표현이 표시 됩니다.

* 노란색 색은 중첩 된 구조를 나타냅니다.
* 녹색 색은 두 개의 요소가 있는 배열을 나타냅니다.

[![스키마 원본](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid 문서를 Azure Cosmos DB 트랜잭션 저장소로 수집 _ts 및 _etag가 시스템에 추가 되었습니다.

위의 데이터 프레임은 5 개의 열과 1 개의 행만 계산 합니다. 변환 후 큐 레이트 데이터 프레임에는 13 개의 열과 2 개의 행이 테이블 형식으로 포함 됩니다.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Apache Spark를 사용 하 여 중첩 된 구조체를 평면화 하 고 배열 분해

Synapse Spark를 사용 하 여 중첩 된 구조를 열과 배열 요소로 여러 행으로 변환 하는 것이 쉽습니다. 아래 단계는 모든 사용자가 자체적으로 구현 하는 데 사용할 수 있습니다.

[![Spark 변환 단계](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**1 단계**: 중첩 된 스키마를 평면화 하는 함수를 정의 합니다. 이 함수는 변경 하지 않고 사용할 수 있습니다. 해당 함수를 사용 하 여 Pyspark 노트북에서 셀을 만듭니다.

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

**2 단계**: 함수를 사용 하 여 데이터 프레임 **df** 의 중첩 된 스키마를 새 데이터 프레임 **df_flat**평면화 합니다.

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

표시 함수는 10 개의 열과 1 개의 행을 반환 해야 합니다. 배열 및 중첩 된 요소도 여전히 있습니다.

**3 단계**: 이제 데이터 프레임의 배열 **context_custom_dimensions** **df_flat** 새 데이터 프레임 **df_flat_explode**변환 합니다. 아래 코드에서는 선택 하는 열도 정의 합니다.

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

표시 함수는 10 개의 열과 2 개의 행을 반환 해야 합니다. 다음 단계는 1 단계에서 정의한 함수를 사용 하 여 중첩 된 스키마를 평면화 하는 것입니다.

**4 단계**: 함수를 사용 하 여 데이터 프레임 **df_flat_explode** 의 중첩 된 스키마를 새 데이터 프레임 **df_flat_explode_flat**평면화 합니다.
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

표시 함수는 13 개의 열과 2 개의 행을 표시 합니다.

데이터 프레임의 printSchema 함수 df_flat_explode_flat는 다음 결과를 반환 합니다.

[![스키마 최종](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>SQL server 서버를 사용 하 여 배열 및 중첩 구조를 직접 읽습니다.

SQL server 서버를 사용 하 여 쿼리, 뷰 및 테이블을 만들 수 있습니다.

먼저 데이터를 저장 하는 방법에 따라 사용자는 다음 분류를 사용 해야 합니다. 모든 대문자는 사용 사례와 관련이 있습니다.

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = ACCOUNTNAME; database = DATABASENAME; collection = COLLECTIONNAME; region = 지역이 TOQUERY, SECRET = ' 해당 암호 ' |' CosmosDB ' (Synapse Link)|



서버를 사용 하지 않는 **SQL server** 는 AZURE COSMOS DB 및 AAD 통과를 위한 Azure Synapse 링크 연결 된 서비스를 지원 합니다. 이 기능은 현재 Synapse 용 제어 미리 보기 링크에 있습니다.

아래에서 바꾸기:
* 사용자가 연결 하는 데이터 원본의 연결 문자열에 의해 ' 대량 연결 '
* 원본에 연결 하는 데 사용 하는 형식으로 ' 위의 형식 '

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

수행 되는 작업에는 다음 두 가지 유형이 있습니다.
* 아래 코드 줄에서는 nested 요소를 참조 하는 contextdataeventTime 라는 열을 정의 합니다. eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

이 줄에서는 중첩 요소를 참조 하는 contextdataeventTime 라는 열을 정의 합니다. Context>Data>eventTime

* **cross apply** 는 배열의 각 요소에 대해 새 행을 만드는 데 사용 되며,를 사용 하 여 첫 번째 글머리 기호 지점과 비슷한 각 중첩 된 개체를 정의 합니다. 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

배열에 4 개의 중첩 된 구조체가 있는 5 개의 요소가 있는 경우 SQL server를 사용 하지 않는 경우 5 개의 행과 4 개의 열이 반환 됩니다.

서버를 사용 하지 않는 SQL server는 내부에서 쿼리를 수행 하 고, 배열을 2 개의 행으로 매핑하고, 중첩 된 모든 구조를 열로 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Spark를 사용 하 여 Azure Cosmos DB에 대 한 Azure Synapse 링크를 쿼리 하는 방법 알아보기](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet 중첩 형식 쿼리](./sql/query-parquet-nested-types.md) 