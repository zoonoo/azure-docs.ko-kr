---
title: Azure 데이터 팩터리를 사용하여 Teradata Vantage의 데이터 복사
description: 데이터 팩터리 서비스의 Teradata 커넥터를 사용하면 테라데이터 유리한 데이터에서 데이터 팩터리에서 지원하는 데이터 저장소에 데이터를 싱크로 복사할 수 있습니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 1e1d7cc4bb7762d3ebd29e349467f3e33c0887f9
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421231"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Teradata Vantage의 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> * [버전 1](v1/data-factory-onprem-teradata-connector.md)
> * [현재 버전](connector-teradata.md)

이 문서에서는 Azure Data Factory의 복사 활동을 사용하여 Teradata Vantage의 데이터를 복사하는 방법을 설명합니다. [복사 활동 개요를](copy-activity-overview.md)기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Teradata 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

Teradata Vantage의 데이터를 지원되는 싱크 데이터 저장소에 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Teradata 커넥터는 다음을 지원합니다.

- 테라 데이터 **버전 14.10, 15.0, 15.10, 16.0, 16.10 및 16.20**.
- **기본** 또는 **Windows** 인증을 사용하여 데이터를 복사합니다.
- Teradata 원본에서 병렬 복사. 자세한 내용은 [Teradata의 병렬 복사본을](#parallel-copy-from-teradata) 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

자체 호스팅 통합 런타임을 사용하는 경우 버전 3.18부터 기본 제공 Teradata 드라이버를 제공합니다. 드라이버를 수동으로 설치할 필요가 없습니다. 드라이버는 자체 호스팅 통합 런타임 컴퓨터에서 "Visual C++ 재배포 가능 2012 업데이트 4"가 필요합니다. 아직 설치하지 않은 경우 [여기에서](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)다운로드하십시오.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Teradata 커넥터와 관련된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대한 세부 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Teradata 연결 서비스는 다음 속성을 지원합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Teradata로**설정해야 합니다. | 예 |
| connectionString | Teradata 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 다음 샘플을 참조하세요.<br/>Azure Key Vault에 암호를 입력하고 연결 `password` 문자열에서 구성을 가져올 수도 있습니다. 자세한 내용은 [Azure 키 자격 증명 모음의 자격 증명 저장을](store-credentials-in-key-vault.md) 참조하십시오. | 예 |
| 사용자 이름 | Teradata에 연결할 사용자 이름을 지정합니다. Windows 인증을 사용하는 경우에 적용됩니다. | 예 |
| password | 사용자 이름에 대해 지정한 사용자 계정에 대한 암호를 지정합니다. [Azure 키 자격 증명 모음에 저장된 비밀을 참조하도록](store-credentials-in-key-vault.md)선택할 수도 있습니다. <br>Windows 인증을 사용하거나 기본 인증을 위해 Key Vault에서 암호를 참조하는 경우에 적용됩니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임입니다.](concepts-integration-runtime.md) [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

케이스당 연결 문자열에서 설정할 수 있는 연결 속성이 더 많아요.

| 속성 | Description | 기본값 |
|:--- |:--- |:--- |
| CharacterSet | 세션에 사용할 문자 집합입니다. 예를 들어, `CharacterSet=UTF16`.<br><br/>이 값은 사용자 정의 문자 집합이거나 다음 미리 정의된 문자 집합 중 하나일 수 있습니다. <br/>- 아시이<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- 시프트 지스 (윈도우, DOS 호환, KANJISJIS_0S)<br/>- EUC (유닉스 호환, KANJIEC_0U)<br/>- IBM 메인프레임 (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- 네트워크한국어 (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | 기본값은 `ASCII`입니다. |
| 맥스레스프사이즈 |SQL 요청에 대한 응답 버퍼의 최대 크기(KB)입니다. 예를 들어, `MaxRespSize=‭10485760‬`.<br/><br/>Teradata 데이터베이스 버전 16.00 이상에서 최대값은 7361536입니다. 이전 버전을 사용하는 연결의 경우 최대값은 1048576입니다. | 기본값은 `65536`입니다. |

**기본 인증 사용 예**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Windows 인증 사용 예**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 다음 페이로드는 계속 지원됩니다. 그러나 앞으로는 새 것을 사용해야 합니다.

**이전 페이로드:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 Teradata 데이터 집합에서 지원하는 속성 목록을 제공합니다. 데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조하십시오.

Teradata의 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성을 로 `TeradataTable`설정해야 합니다. | 예 |
| 데이터베이스 | 테라데이타 인스턴스의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |
| 테이블 | Teradata 인스턴스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`형식 데이터 집합은 여전히 지원됩니다. 그러나 새 데이터 집합을 사용하는 것이 좋습니다.

**이전 페이로드:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

이 섹션에서는 Teradata 원본에서 지원하는 속성의 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="teradata-as-source"></a>Teradata를 원본으로

>[!TIP]
>데이터 분할을 사용하여 Teradata의 데이터를 효율적으로 로드하려면 [Teradata](#parallel-copy-from-teradata) 섹션의 병렬 복사본에서 자세히 알아봅니다.

Teradata의 데이터를 복사하려면 복사 활동 **소스** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 원본의 형식 속성을 로 `TeradataSource`설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `"SELECT * FROM MyTable"`입니다.<br>분할된 로드를 사용하도록 설정하면 쿼리에 해당 기본 제공 파티션 매개 변수를 연결해야 합니다. 예를 들어 [Teradata](#parallel-copy-from-teradata) 의 병렬 복사본 섹션을 참조하십시오. | 아니오(데이터 집합의 테이블이 지정된 경우) |
| 파티션옵션 | Teradata에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용 값은 **None** 없음(기본값), **해시** 및 **DynamicRange**입니다.<br>파티션 옵션이 활성화되면(즉, `None`그렇지 않음) Teradata에서 데이터를 동시에 로드하는 병렬 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 처리 정도는 복사 활동의 설정에 의해 제어됩니다. | 예 |
| 파티션설정 | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 아닌 `None`경우 적용합니다. | 예 |
| 파티션열이름 | 병렬 복사본에 대한 범위 파티션 또는 해시 파티션에서 사용할 소스 열의 이름을 지정합니다. 지정하지 않으면 테이블의 기본 인덱스가 자동으로 감지되어 파티션 열로 사용됩니다. <br>파티션 옵션이 `Hash` 있는 `DynamicRange`경우 적용하거나 . 쿼리를 사용하여 원본 데이터, 후크 `?AdfHashPartitionCondition` 또는 `?AdfRangePartitionColumnName` WHERE 절을 검색하는 경우. Teradata 섹션의 [병렬 복사본에서 예제를](#parallel-copy-from-teradata) 참조하십시오. | 예 |
| 파티션어바운드 | 데이터를 복사할 파티션 열의 최대값입니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용합니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절을 연결합니다. `?AdfRangePartitionUpbound` 예를 들어 [Teradata의 병렬 복사본](#parallel-copy-from-teradata) 섹션을 참조하십시오. | 예 |
| 파티션로바운드 | 데이터를 복사할 파티션 열의 최소값입니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfRangePartitionLowbound` 경우 WHERE 절을 연결합니다. 예를 들어 [Teradata의 병렬 복사본](#parallel-copy-from-teradata) 섹션을 참조하십시오. | 예 |

> [!NOTE]
>
> `RelationalSource`형식 복사 소스는 여전히 지원되지만 Teradata(파티션 옵션)의 새 기본 제공 병렬 로드를 지원하지 않습니다. 그러나 새 데이터 집합을 사용하는 것이 좋습니다.

**예: 파티션 없이 기본 쿼리를 사용하여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>테라데이타에서 병렬 복사

데이터 팩터리 Teradata 커넥터는 Teradata의 데이터를 병렬로 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 활동의 **소스** 테이블에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-teradata/connector-teradata-partition-options.png)

분할된 복사본을 사용하도록 설정하면 Data Factory는 Teradata 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 평행 정도는 복사 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 활동의 설정에 의해 제어됩니다. 예를 들어 4로 `parallelCopies` 설정하면 Data Factory는 지정된 파티션 옵션 및 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며 각 쿼리는 Teradata에서 데이터의 일부를 검색합니다.

특히 Teradata에서 많은 양의 데이터를 로드할 때 데이터 분할과 병렬 복사본을 사용하도록 설정하는 것이 좋습니다. 다음은 다양한 시나리오에 대한 권장 구성입니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 여러 파일(폴더 이름만 지정)으로 폴더에 쓰도록 명령되며, 이 경우 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

| 시나리오                                                     | 권장 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 큰 테이블에서 전체 로드.                                   | **파티션 옵션**: 해시. <br><br/>실행 하는 동안 Data Factory 는 PK 열을 자동으로 감지하고 해시를 적용하며 파티션별로 데이터를 복사합니다. |
| 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다.                 | **파티션 옵션**: 해시.<br>**쿼리** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`: .<br>**파티션 열**: 해시 파티션 적용에 사용되는 열을 지정합니다. 지정하지 않으면 데이터 팩터리는 Teradata 데이터 집합에 지정한 테이블의 PK 열을 자동으로 검색합니다.<br><br>실행 하는 동안 데이터 `?AdfHashPartitionCondition` 팩터리는 해시 파티션 논리로 대체 하 고 Teradata에 보냅니다. |
| 범위 분할에 대해 균등하게 분산된 값을 가진 정수 열을 갖는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 동적 범위 파티션.<br>**쿼리** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**분할 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 데이터 형식을 사용하여 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한**: 파티션 열에 대해 필터링하여 하위 범위와 상위 범위 간에만 데이터를 검색할지 지정합니다.<br><br>실행 하는 동안 Data `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` 는 에서 를 대체하고 각 파티션의 실제 열 이름 및 값 범위로 Teradata로 보냅니다. <br>예를 들어, 파티션 열 "ID"가 하한을 1로 설정하고 상한을 80으로 설정하고 병렬 복사본을 4로 설정한 경우 Data Factory는 4개의 파티션으로 데이터를 검색합니다. 그들의 아이디는 각각 [1,20], [21, 40], [41, 60], [61, 80] 사이입니다. |

**예: 해시 파티션이 있는 쿼리**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata에 대한 데이터 형식 매핑

Teradata에서 데이터를 복사하면 다음 매핑이 적용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Teradata 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| 정수 |Int32 |
| Interval Day |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Day To Hour |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Day To Minute |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Day To Second |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Hour |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Hour To Minute |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Hour To Second |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Minute |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Minute To Second |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Month |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Second |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Year |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Interval Year To Month |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Number |Double |
| 기간(날짜) |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| 기간(시간) |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| 기간(표준 시간대가 있는 시간) |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| Period (Timestamp) |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| 기간(표준 시간대가 있는 타임스탬프) |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| 타임스탬프 |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |
| xml |지원되지 않습니다. 소스 쿼리에 명시적 캐스트를 적용합니다. |


## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.


## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
