---
title: Azure Databricks Delta Lake 간 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용 하 여 Azure Databricks Delta Lake 간에 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/28/2020
ms.openlocfilehash: 4ff1a793b3e8c4fe642aa304f1aa59bd8edefb8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405623"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Databricks Delta Lake 간 데이터 복사

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 델타 Lake Azure Databricks 간에 데이터를 복사 하는 방법을 설명 합니다. 복사 작업에 대 한 일반적인 개요를 제공 하는 [Azure Data Factory 아티클의 복사 작업](copy-activity-overview.md) 을 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Databricks Delta Lake connector는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 행렬](copy-activity-overview.md) 테이블이 포함 된 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

일반적으로 Azure Data Factory는 다양 한 요구 사항을 충족 하는 다음과 같은 기능으로 델타 Lake를 지원 합니다.

- 복사 작업은 지원 되는 모든 원본 데이터 저장소의 데이터를 Azure Databricks 델타 lake 테이블로 복사 하 고 델타 lake 테이블에서 지원 되는 모든 싱크 데이터 저장소로 데이터를 복사 하는 Azure Databricks Delta Lake 커넥터를 지원 합니다. Databricks 클러스터를 활용 하 여 데이터 이동을 수행 하 고, [필수 구성 요소 섹션](#prerequisites)의 세부 정보를 참조 하세요.
- [매핑 데이터 흐름](concepts-data-flow-overview.md) 은 코드 없는 ETL의 델타 파일을 읽고 쓰기 위한 원본 및 싱크로 Azure Storage의 일반 [델타 형식을](format-delta.md) 지원 하 고 관리 되는 Azure Integration Runtime에서 실행 됩니다.
- [Databricks 활동](transform-data-databricks-notebook.md) 은 델타 lake 위에서 코드 중심 ETL 또는 기계 학습 워크 로드를 오케스트레이션 지원 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 Azure Databricks 델타 Lake connector를 사용 하려면 Azure Databricks에서 클러스터를 설정 해야 합니다.

- 델타 lake에 데이터를 복사 하기 위해 복사 작업은 Azure Databricks 클러스터를 호출 하 여 Azure Storage에서 데이터를 읽습니다. 즉, 원본 원본 또는 준비 영역인 Data Factory는 먼저 기본 제공 준비 복사본을 통해 원본 데이터를 기록 합니다. [델타 lake에서 원본으로](#delta-lake-as-source)자세히 알아보세요.
- 마찬가지로, 델타 lake에서 데이터를 복사 하기 위해 복사 작업은 Azure Databricks 클러스터를 호출 하 여 Azure Storage에 데이터를 씁니다 .이는 원본 싱크 이거나 기본 제공 되는 준비 복사본을 통해 최종 싱크로 데이터를 계속 해 서 Data Factory 하는 준비 영역입니다. [델타 lake에서 싱크로](#delta-lake-as-sink)자세히 알아보세요.

Databricks 클러스터는 Azure Blob 또는 Azure Data Lake Storage Gen2 계정에 대 한 액세스 권한이 있어야 합니다. 여기에는 원본/싱크/스테이징에 사용 되는 저장소 컨테이너/파일 시스템과 델타 Lake 테이블을 작성 하려는 컨테이너/파일 시스템이 모두 있어야 합니다.

- **Azure Data Lake Storage Gen2**를 사용 하려면 Apache Spark 구성의 일부로 Databricks 클러스터에서 **서비스 주체** 또는 **저장소 계정 액세스 키** 를 구성할 수 있습니다. [서비스 사용자로 직접 액세스](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) 또는 [저장소 계정 액세스 키를 사용 하 여 직접 액세스](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key)의 단계를 수행 합니다.

- **Azure Blob 저장소**를 사용 하려면 Apache Spark 구성의 일부로 Databricks 클러스터에서 **저장소 계정 액세스 키** 또는 **SAS 토큰** 을 구성할 수 있습니다. [RDD API를 사용 하 여 Azure Blob Storage 액세스](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api)의 단계를 따릅니다.

복사 작업을 실행 하는 동안 구성한 클러스터가 종료 되 면 Data Factory에서 자동으로 시작 합니다. Data Factory authoring UI를 사용 하 여 파이프라인을 작성 하는 경우 데이터 미리 보기 등의 작업을 위해 라이브 클러스터가 필요 Data Factory 사용자를 대신 하 여 클러스터를 시작할 수 없습니다.

#### <a name="specify-the-cluster-configuration"></a>클러스터 구성 지정

1. **클러스터 모드** 드롭다운에서 **표준**을 선택 합니다.

2. **Databricks Runtime 버전** 드롭다운에서 Databricks 런타임 버전을 선택 합니다.

3. [Spark 구성](https://docs.microsoft.com/azure/databricks/clusters/configure#spark-config)에 다음 속성을 추가 하 여 [자동 최적화](https://docs.microsoft.com/azure/databricks/delta/optimizations/auto-optimize) 를 설정 합니다.

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. 통합 및 확장 요구 사항에 따라 클러스터를 구성 합니다.

클러스터 구성에 대 한 자세한 내용은 [클러스터 구성](https://docs.microsoft.com/azure/databricks/clusters/configure)을 참조 하세요.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Databricks Delta Lake connector에 한정 된 Data Factory 엔터티를 정의 하는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Azure Databricks Delta Lake 연결 된 서비스에 대해 지원 되는 속성입니다.

| 속성    | 설명                                                  | 필수 |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | Type 속성은 **AzureDatabricksDeltaLake**로 설정 해야 합니다. | 예      |
| 도메인      | Azure Databricks 작업 영역 URL (예:)을 지정 `https://adb-xxxxxxxxx.xx.azuredatabricks.net` 합니다. |          |
| clusterId   | 기존 클러스터의 클러스터 ID를 지정 합니다. 이미 생성 된 대화형 클러스터 여야 합니다. <br>Databricks 작업 영역 -> 대화형 클러스터 이름 -> 구성 -> 태그에서 대화형 클러스터의 클러스터 ID를 찾을 수 있습니다. [자세히 알아봅니다](https://docs.microsoft.com/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | 데이터 팩터리가 Azure Databricks에서 인증을 받으려면 액세스 토큰이 필요합니다. 액세스 토큰은 Databricks 작업 영역에서 생성해야 합니다. 액세스 토큰을 찾기 위한 자세한 단계는 [여기](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#generate-token)에서 찾을 수 있습니다. |          |
| connectVia  | 데이터 저장소에 연결 하는 데 사용 되는 [통합 런타임](concepts-integration-runtime.md) 입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 지정 하지 않으면 기본 Azure integration runtime을 사용 합니다. | 예       |

**예:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

Azure Databricks Delta Lake 데이터 집합에 대해 지원 되는 속성은 다음과 같습니다.

| 속성  | 설명                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 집합의 type 속성은 **AzureDatabricksDeltaLakeDataset**로 설정 해야 합니다. | 예                         |
| 데이터베이스 | 데이터베이스의 이름입니다. |원본에 대해 아니요, 싱크에 대해 예  |
| 테이블 | 델타 테이블의 이름입니다. |원본에 대해 아니요, 싱크에 대해 예  |

**예:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Databricks Delta Lake 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="delta-lake-as-source"></a>원본으로 서의 델타 lake

Azure Databricks Delta Lake에서 데이터를 복사 하기 위해 복사 작업 **원본** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성                     | 설명                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 작업 원본의 type 속성은 **AzureDatabricksDeltaLakeSource**로 설정 해야 합니다. | 예      |
| Query          | 데이터를 읽을 SQL 쿼리를 지정 합니다. 시간 이동 컨트롤의 경우 다음 패턴을 따릅니다.<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | 아니요       |
| exportSettings | 델타 테이블에서 데이터를 검색 하는 데 사용 되는 고급 설정입니다. | 아니요       |
| ***에서 `exportSettings` 다음을 수행 합니다.*** |  |  |
| type | **AzureDatabricksDeltaLakeExportCommand**로 설정 된 내보내기 명령의 유형입니다. | 예 |
| dateFormat | 날짜 형식을 날짜 형식의 문자열로 지정 합니다. 사용자 지정 날짜 형식은 [날짜/시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정 하지 않으면 기본값을 사용 `yyyy-MM-dd` 합니다. | 아니요 |
| timestampFormat | 타임 스탬프 형식을 타임 스탬프 형식으로 문자열 형식으로 지정 합니다. 사용자 지정 날짜 형식은 [날짜/시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정 하지 않으면 기본값을 사용 `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` 합니다. | 아니요 |

#### <a name="direct-copy-from-delta-lake"></a>델타 lake에서 직접 복사

싱크 데이터 저장소 및 형식이이 섹션에 설명 된 조건을 충족 하는 경우 복사 작업을 사용 하 Azure Databricks 델타 테이블에서 싱크로 직접 복사할 수 있습니다. Data Factory는 설정을 확인 하 고 다음 조건이 충족 되지 않으면 복사 작업 실행에 실패 합니다.

- **싱크 연결 된 서비스** 는 [Azure Blob storage](connector-azure-blob-storage.md) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)입니다. 계정 자격 증명은 Azure Databricks 클러스터 구성에서 미리 구성 해야 합니다. [필수 구성 요소](#prerequisites)에서 자세한 정보를 알아보세요.

- **싱크 데이터 형식은** 다음과 같은 구성 **으로 Parquet**, **구분 된 텍스트**또는 **Avro** 이며 파일 대신 폴더를 가리킵니다.

    - **Parquet** 형식의 경우 압축 코덱은 **none**, **snappy**또는 **gzip**입니다.
    - **구분 기호로 분리 된 텍스트** 형식:
        - `rowDelimiter` 임의의 단일 문자입니다.
        - `compression`**none**, **bzip2**, **gzip**일 수 있습니다.
        - `encodingName` U t f-7은 지원 되지 않습니다.
    - **Avro** 형식의 경우 압축 코덱은 **none**, **deflate**또는 **snappy**입니다.

- 복사 작업 원본에서 `additionalColumns` 가 지정 되지 않은 경우
- 복사 작업 싱크에서 데이터를 분리 된 텍스트로 복사 하는 경우 `fileExtension` ".csv" 여야 합니다.
- 복사 활동 매핑에서 유형 변환이 사용 되지 않습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>델타 lake에서 준비 된 복사

싱크 데이터 저장소 또는 형식이 직접 복사 조건과 일치 하지 않는 경우 마지막 섹션에 설명 된 대로 중간 Azure 저장소 인스턴스를 사용 하 여 기본 제공 준비 된 복사본을 사용 하도록 설정 합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. Data Factory는 Azure Databricks 델타 Lake에서 준비 저장소로 데이터를 내보낸 다음 데이터를 싱크에 복사 하 고 마지막으로 준비 저장소에서 임시 데이터를 정리 합니다. 준비를 사용 하 여 데이터 복사에 대 한 자세한 내용은 [준비 된 복사](copy-activity-performance-features.md#staged-copy) 를 참조 하세요.

이 기능을 사용 하려면 [Azure Blob storage 연결 된 서비스](connector-azure-blob-storage.md#linked-service-properties) 또는 저장소 계정을 임시 준비로 참조 하는 [연결 된 서비스 Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 만듭니다. 그런 다음 `enableStaging` `stagingSettings` 복사 작업에서 및 속성을 지정 합니다.

>[!NOTE]
>Azure Databricks 클러스터 구성에서 준비 저장소 계정 자격 증명을 미리 구성 해야 합니다. [필수 구성 요소](#prerequisites)를 자세히 알아보세요.

**예:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>싱크로 lake lake

Azure Databricks Delta Lake에 데이터를 복사 하기 위해 복사 작업 **싱크에서** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | 복사 작업 싱크의 type 속성은 **AzureDatabricksDeltaLakeSink**로 설정 됩니다. | 예      |
| preCopyScript | 각 실행에서 Databricks 델타 테이블에 데이터를 쓰기 전에 실행할 복사 작업에 대 한 SQL 쿼리를 지정 합니다. 이 속성을 사용 하 여 미리 로드 된 데이터를 정리 하거나 truncate table 또는 진공 문을 추가할 수 있습니다. | 아니요       |
| importSettings | 델타 테이블에 데이터를 쓰는 데 사용 되는 고급 설정입니다. | 아니요 |
| ***에서 `importSettings` 다음을 수행 합니다.*** |                                                              |  |
| type | **AzureDatabricksDeltaLakeImportCommand**로 설정 된 가져오기 명령의 유형입니다. | 예 |
| dateFormat | 날짜 형식의 날짜 형식에 대 한 형식 문자열입니다. 사용자 지정 날짜 형식은 [날짜/시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정 하지 않으면 기본값을 사용 `yyyy-MM-dd` 합니다. | 아니요 |
| timestampFormat | 타임 스탬프 형식에 대 한 형식 문자열을 타임 스탬프 형식으로 바꿉니다. 사용자 지정 날짜 형식은 [날짜/시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정 하지 않으면 기본값을 사용 `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` 합니다. | 아니요 |

#### <a name="direct-copy-to-delta-lake"></a>델타 lake로 직접 복사

원본 데이터 저장소 및 형식이이 섹션에 설명 된 조건을 충족 하는 경우 복사 작업을 사용 하 여 원본에서 Azure Databricks Delta Lake로 직접 복사할 수 있습니다. Azure Data Factory는 설정을 확인 하 고 다음 조건이 충족 되지 않으면 복사 작업 실행에 실패 합니다.

- **원본 연결 된 서비스** 는 [Azure Blob storage](connector-azure-blob-storage.md) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)입니다. 계정 자격 증명은 Azure Databricks 클러스터 구성에서 미리 구성 해야 합니다. [필수 구성 요소](#prerequisites)에서 자세한 정보를 알아보세요.

- **원본 데이터 형식은** 다음과 같은 구성 **으로 Parquet**, **구분 된 텍스트**또는 **Avro** 이며 파일 대신 폴더를 가리킵니다.

    - **Parquet** 형식의 경우 압축 코덱은 **none**, **snappy**또는 **gzip**입니다.
    - **구분 기호로 분리 된 텍스트** 형식:
        - `rowDelimiter` 는 기본값 또는 모든 단일 문자입니다.
        - `compression`**none**, **bzip2**, **gzip**일 수 있습니다.
        - `encodingName` U t f-7은 지원 되지 않습니다.
    - **Avro** 형식의 경우 압축 코덱은 **none**, **deflate**또는 **snappy**입니다.

- 복사 작업 원본에서: 

    - `wildcardFileName` 와일드 카드만 포함 하 `*` `?` 고 `wildcardFolderName` 는 지정 하지 않습니다.
    - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` 및 `enablePartitionDiscovery`는 지정되지 않습니다.
    - `additionalColumns`을(를) 지정하지 않았습니다.

- 복사 활동 매핑에서 유형 변환이 사용 되지 않습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>델타 lake로 준비 된 복사

마지막 섹션에서 설명한 것 처럼 원본 데이터 저장소 또는 형식이 직접 복사 조건과 일치 하지 않는 경우 중간 Azure 저장소 인스턴스를 사용 하 여 기본 제공 준비 된 복사본을 사용 하도록 설정 합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. Data Factory 데이터 형식 요구 사항을 충족 하도록 데이터를 자동으로 변환 하 고 나 서 해당 데이터 형식을 델타 lake로 로드 합니다. 마지막으로 저장소에서 임시 데이터를 정리 합니다. 준비를 사용 하 여 데이터 복사에 대 한 자세한 내용은 [준비 된 복사](copy-activity-performance-features.md#staged-copy) 를 참조 하세요.

이 기능을 사용 하려면 [Azure Blob storage 연결 된 서비스](connector-azure-blob-storage.md#linked-service-properties) 또는 저장소 계정을 임시 준비로 참조 하는 [연결 된 서비스 Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 만듭니다. 그런 다음 `enableStaging` `stagingSettings` 복사 작업에서 및 속성을 지정 합니다.

>[!NOTE]
>Azure Databricks 클러스터 구성에서 준비 저장소 계정 자격 증명을 미리 구성 해야 합니다. [필수 구성 요소](#prerequisites)를 자세히 알아보세요.

**예:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>모니터링

Azure Data Factory는 다른 커넥터와 동일한 [복사 작업 모니터링 환경을](copy-activity-monitoring.md) 제공 합니다. 또한 Azure Databricks 클러스터에서 델타 lake로 데이터를 로드 하는 것이 실행 되므로 [자세한 클러스터 로그를 확인](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--view-cluster-logs) 하 고 [성능을 모니터링할](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--monitor-performance)수 있습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대 한 자세한 내용은 [조회 작업](control-flow-lookup-activity.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Data Factory 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조 하세요.
