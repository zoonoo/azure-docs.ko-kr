---
title: Azure Data Factory를 사용하여 Oracle 간 데이터 복사 | Microsoft Docs
description: 데이터 팩터리를 사용하여 지원되는 원본 저장소에서 Oracle 데이터베이스로 (또는) Oracle에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 04f623a889a87c325b1f53e3b39656ca4b703961
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509240"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Oracle 간 데이터 복사
> [!div class="op_single_selector" title1="사용 하는 Data Factory 서비스 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-onprem-oracle-connector.md)
> * [현재 버전](connector-oracle.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Oracle 데이터베이스 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Oracle 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Oracle에 복사할 수도 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히,이 Oracle 커넥터를 지원합니다.

- Oracle 데이터베이스의 다음 버전:
  - Oracle 12c R1(12.1)
  - Oracle 11g R1, R2(11.1, 11.2)
  - Oracle 10g R1, R2(10.1, 10.2)
  - Oracle 9i R1, R2(9.0.1, 9.2)
  - Oracle 8i R3(8.1.7)
- 사용 하 여 데이터 복사 **기본적인** 하거나 **OID** 인증 합니다.
- Oracle 원본에서 병렬 복사본입니다. 참조 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 세부 정보가 포함 된 섹션입니다.

> [!Note]
> Oracle 프록시 서버는 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 Oracle 데이터베이스 간에 데이터를 복사하려면 자체 호스팅 통합 런타임을 설정해야 합니다. 통합 런타임에 대한 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md)을 참조하세요. 통합 런타임은 기본 제공 Oracle 드라이버를 제공합니다. 따라서 Oracle 데이터 복사 작업에는 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Oracle 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Oracle 연결 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Oracle**로 설정해야 합니다. | 예. |
| connectionString | Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. Azure Key Vault에 암호를 넣고, 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. <br><br>**지원되지 않는 연결 유형**: 데이터베이스를 식별하기 위해 **Oracle SID** 또는 **Oracle 서비스 이름**을 사용할 수 있습니다.<br>- SID를 사용하는 경우: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 서비스 이름을 사용하는 경우: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | 예. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

>[!TIP]
>"ORA-01025: UPI parameter out of range”(ORA-01025: UPI 매개 변수가 범위를 벗어남) 오류가 표시되고 Oracle이 8i 버전인 경우 연결 문자열에 `WireProtocolMode=1`을 추가하고 다시 시도합니다.

**Oracle 연결에서 암호화를 사용하도록 설정하려면** 다음 두 가지 옵션이 있습니다.

1.  **3DES(Triple-DES Encryption) 및 AES(Advanced Encryption Standard)** 를 사용하려면 Oracle 서버 쪽에서 OAS(Oracle Advanced Security)로 이동하여 암호화 설정을 구성합니다. 자세한 내용은 [여기](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)를 참조하세요. ADF Oracle 커넥터는 Oracle에 대한 연결을 설정할 때 OAS에서 구성한 암호화 방법을 사용하도록 해당 암호화 방법을 자동으로 협상합니다.

2.  **SSL**을 사용하려면 다음 단계를 수행합니다.

    1.  SSL 인증서 정보를 가져옵니다. SSL 인증서의 DER로 인코딩된 인증서 정보를 가져오고 출력(----- Begin Certificate … End Certificate -----)을 텍스트 파일로 저장합니다.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **예:** DERcert.cer에서 인증서 정보를 추출한 다음, 출력을 cert.txt로 저장합니다.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  키 저장소 또는 trustsotre를 빌드합니다. 다음 명령은 PKCS-12 형식의 암호를 사용하거나 사용하지 않는 trustsotre 파일을 만듭니다.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **예:** 암호로 MyTrustStoreFile 라는 PKCS12 truststore 파일을 만듭니다

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  예를 들어 C:\MyTrustStoreFile과 같은 자체 호스팅 IR 머신에 truststore 파일을 배치합니다.
    4.  ADF에서 `EncryptionMethod=1` 및 해당 `TrustStore`/`TrustStorePassword` 값(예: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`)을 사용하여 Oracle 연결 문자열을 구성합니다.

**예제:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 암호 저장**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 Oracle 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Oracle 간에 데이터를 복사하려면 데이터 세트의 형식 속성을 **OracleTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **OracleTable**로 설정해야 합니다. | 예. |
| tableName |연결된 서비스가 참조하는 Oracle 데이터베이스에 있는 테이블의 이름입니다. | 예. |

**예제:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Oracle 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="oracle-as-a-source-type"></a>원본 유형인 Oracle

> [!TIP]
>
> 자세히 알아보세요 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 효율적으로 데이터 분할을 사용 하 여 Oracle에서 데이터를 로드 하는 방법에 대 한 섹션입니다.

Oracle에서 데이터를 복사하려면 복사 작업의 원본 형식을 **OracleSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **OracleSource**로 설정해야 합니다. | 예 |
| oracleReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예는 `"SELECT * FROM MyTable"`입니다.<br>분할 된 부하를 사용 하도록 설정 하면 쿼리에서 해당 기본 제공 파티션 매개 변수를 연결 해야 합니다. 예제를 참조 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 섹션입니다. | 아닙니다. |
| partitionOptions | 데이터 분할 Oracle에서 데이터를 로드 하는 데 사용 하는 옵션을 지정 합니다. <br>값은 허용: **None** (기본값) 이면 **PhysicalPartitionsOfTable** 하 고 **DynamicRange**합니다.<br>파티션 옵션을 사용 하는 경우 (없습니다 ' None'), 또한 구성 하십시오 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 4 예를 들어 복사 활동에서 설정을 결정 하는 동시에 Oracle에서 데이터를 로드 하는 병렬 처리 수준이 데이터베이스입니다. | 아닙니다. |
| partitionSettings | 데이터 분할에 대 한 설정 그룹을 지정 합니다. <br>파티션 옵션 없는 경우 적용 `None`합니다. | 아닙니다. |
| partitionNames | 복사 해야 하는 실제 파티션 목록입니다. <br>파티션 옵션을 적용 `PhysicalPartitionsOfTable`합니다. 원본 데이터를 검색할 쿼리를 사용 하는 경우 연결 `?AdfTabularPartitionName` WHERE 절에 있습니다. 예제를 참조 하세요 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 섹션입니다. | 아닙니다. |
| partitionColumnName | 원본 열의 이름을 지정 **정수 형식의** 병렬 복사본에 대 한 범위를 분할 하 여 사용 되는 합니다. 지정 하지 않으면 테이블의 기본 키를 자동으로 검색 하 고 파티션 열으로 사용 됩니다. <br>파티션 옵션을 적용 `DynamicRange`합니다. 원본 데이터를 검색할 쿼리를 사용 하는 경우 연결 `?AdfRangePartitionColumnName` WHERE 절에 있습니다. 예제를 참조 하세요 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 섹션입니다. | 아닙니다. |
| partitionUpperBound | 파티션 열 복사할 데이터의 최대값입니다. <br>파티션 옵션을 적용 `DynamicRange`합니다. 원본 데이터를 검색할 쿼리를 사용 하는 경우 연결 `?AdfRangePartitionUpbound` WHERE 절에 있습니다. 예제를 참조 하세요 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 섹션입니다. | 아닙니다. |
| PartitionLowerBound | 파티션 열 복사할 데이터의 최소값입니다. <br>파티션 옵션을 적용 `DynamicRange`합니다. 원본 데이터를 검색할 쿼리를 사용 하는 경우 연결 `?AdfRangePartitionLowbound` WHERE 절에 있습니다. 예제를 참조 하세요 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 섹션입니다. | 아닙니다. |

**예제: 파티션 없이 기본 쿼리를 사용 하 여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

더 많은 예제를 참조 하세요 [병렬 복사 Oracle에서](#parallel-copy-from-oracle) 섹션입니다.

### <a name="oracle-as-a-sink-type"></a>싱크 형식인 Oracle

Oracle에 데이터를 복사하려면 복사 작업의 싱크 형식을 **OracleSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 **OracleSink**로 설정해야 합니다. | 예. |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)입니다. | 아닙니다. |
| preCopyScript | 각 실행 시 Oracle에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 아닙니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Oracle에서 병렬 복사

데이터 팩터리 Oracle 커넥터는 Oracle에서 뛰어난 성능으로 병렬로 데이터를 복사 하는 분할과 기본 제공 데이터를 제공 합니다. 복사 작업에서 데이터 분할 옵션에는 Oracle 원본->를 찾을 수 있습니다.

![파티션 옵션](./media/connector-oracle/connector-oracle-partition-options.png)

분할 된 복사본을 사용 하면 data factory는 파티션에서 데이터를 로드 하 여 Oracle 원본에 대해 병렬 쿼리를 실행 합니다. 병렬 처리 수준이 구성 하 고 제어를 통해 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 복사 활동에 설정 합니다. 예를 들어 설정한 `parallelCopies` 4로 동시에 데이터 팩터리를 생성 하 고 Oracle 데이터베이스에서에서 데이터를 검색 하는 동안 각 부분 지정한 파티션 옵션 및 설정에 따라 4 개의 쿼리를 실행 합니다.

Oracle 데이터베이스에서 많은 양의 데이터를 로드 하는 경우에 특히 분할 데이터를 사용 하 여 병렬 복사를 사용 하려면 제안 됩니다. 다음은 다양 한 시나리오에 대 한 권장된 구성입니다.

| 시나리오                                                     | 제안 된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션 사용 하 여 큰 테이블에서 전체 로드          | **파티션 옵션**: 테이블의 물리적 파티션에 합니다. <br><br/>자동으로 실행 하는 동안 데이터 팩터리는 실제 파티션을 검색 하 고 파티션에서 데이터를 복사. |
| 데이터 분할에 대 한 정수 열이 있는 동안 실제 파티션 하지 않고 큰 테이블에서 전체 로드 | **파티션 옵션**: 동적 범위 분할 합니다.<br>**파티션 열**: 데이터를 분할 하는 데 사용 하는 열을 지정 합니다. 그렇지 않으면 지정 된 기본 키 열이 사용 됩니다. |
| 많은 양의 아래 실제 파티션을 사용 하 여 사용자 지정 쿼리를 사용 하 여 데이터 로드 | **파티션 옵션**: 테이블의 물리적 파티션에 합니다.<br>**쿼리**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`합니다.<br>**파티션 이름**: 데이터를 복사 하려면 파티션 이름을 지정 합니다. 지정 하지 않으면 ADF Oracle 데이터 집합에서 지정한 테이블에 있는 실제 파티션이 자동으로 검색 됩니다.<br><br>데이터 팩터리 교체를 실행 하는 동안 `?AdfTabularPartitionName` 실제 파티션 이름 및 Oracle에는 송신 합니다. |
| 많은 양의 데이터 분할에 대 한 아래 정수 열이 있는 동안 실제 파티션을 하지 않고 사용자 지정 쿼리를 사용 하는 데이터를 로드 합니다. | **파티션 옵션**: 동적 범위 분할 합니다.<br>**쿼리**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`합니다.<br>**파티션 열**: 데이터를 분할 하는 데 사용 하는 열을 지정 합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 하 고 **파티션 하한값**: 파티션 열만 데이터를 검색 하려면 한 및 상한 범위 사이 대해 필터링 할 것인지를 지정 합니다.<br><br>데이터 팩터리 교체를 실행 하는 동안 `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, 및 `?AdfRangePartitionLowbound` 실제 열 이름 및 값을 사용 하 여 각 범위를 분할 및 Oracle에 보냅니다. <br>예를 들어, 1 및 4에서 병렬 복사 집합과 80으로 상한 하한값으로 설정 하 고 파티션 열 "ID" ADF [21, 40] [1, 20] 간에 ID 사용 하 여 4 개의 파티션 기준으로 데이터 검색 [41, 60] 및 [61, 80]. |

**실제 파티션 쿼리 예제:**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**동적 범위 분할을 사용 하 여 쿼리 예제:**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Oracle에 대한 데이터 형식 매핑

Oracle 간에 데이터를 복사할 경우 Oracle 데이터 형식에서 데이터 팩터리 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Oracle 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Oracle 10g 이상에서만 지원됨) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| INTEGER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 데이터 형식 INTERVAL YEAR TO MONTH 및 INTERVAL DAY TO SECOND는 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
