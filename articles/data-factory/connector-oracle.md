---
title: Azure 데이터 팩터리를 사용하여 오라클과 데이터를 복사합니다.
description: Data Factory를 사용하여 지원되는 원본 저장소에서 Oracle 데이터베이스또는 오라클에서 지원되는 싱크 저장소로 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 874c685491774e2a318ae0a8b7394945a51b2f7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244512"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Oracle 간 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-onprem-oracle-connector.md)
> * [현재 버전](connector-oracle.md)

이 문서에서는 Azure Data Factory에서 복사 활동을 사용하여 Oracle 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. [복사 활동 개요를](copy-activity-overview.md)기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Oracle 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

Oracle 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Oracle에 복사할 수도 있습니다. 복사 활동에 의해 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하십시오.

특히 이 오라클 커넥터는 다음을 지원합니다.

- Oracle 데이터베이스의 다음 버전:
    - 오라클 18c R1(18.1) 이상
    - 오라클 12c R1(12.1) 이상
    - 오라클 11g R1(11.1) 이상
    - 오라클 10g R1(10.1) 이상
    - 오라클 9i R2(9.2) 이상
    - 오라클 8i R3(8.1.7) 이상
    - 오라클 데이터베이스 클라우드 엑사데이 서비스
- Oracle 소스에서 병렬 복사. 자세한 내용은 [Oracle의 병렬 복사본을](#parallel-copy-from-oracle) 참조하십시오.

> [!Note]
> Oracle 프록시 서버는 지원되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

통합 런타임은 기본 제공 Oracle 드라이버를 제공합니다. 따라서 Oracle 데이터 복사 작업에는 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Oracle 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Oracle 연결 서비스는 다음 속성을 지원합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Oracle**로 설정해야 합니다. | yes |
| connectionString | Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. <br/>Azure Key Vault에 암호를 입력하고 연결 `password` 문자열에서 구성을 가져올 수도 있습니다. 자세한 내용은 Azure Key Vault의 다음 샘플 및 [스토어 자격 증명을](store-credentials-in-key-vault.md) 참조하십시오. <br><br>**지원되는 연결 유형**: 데이터베이스를 식별하기 위해 **Oracle SID** 또는 **Oracle 서비스 이름**을 사용할 수 있습니다.<br>- SID를 사용하는 경우: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 서비스 이름을 사용하는 경우: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>고급 Oracle 기본 연결 옵션의 경우 TNSNAMES에 항목을 추가하도록 선택할 수 [있습니다. ](http://www.orafaq.com/wiki/Tnsnames.ora)오라클 서버및 ADF 오라클 연결 서비스에서 ORA 파일은 오라클 서비스 이름 연결 유형을 사용하고 해당 서비스 이름을 구성하도록 선택합니다. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |예 |

>[!TIP]
>"ORA-01025: UPI 매개 변수 범위 외"라는 오류가 발생했으며 Oracle 버전이 `WireProtocolMode=1` 8i인 경우 연결 문자열에 추가합니다. 그런 다음, 다시 시도하세요.

케이스당 연결 문자열에서 설정할 수 있는 연결 속성이 더 많아요.

| 속성 | 설명 | 허용되는 값 |
|:--- |:--- |:--- |
| ArraySize |커넥터가 단일 네트워크 왕복에서 가져올 수 있는 바이트 수입니다. 예를 들어, `ArraySize=‭10485760‬`.<br/><br/>값이 클수록 네트워크를 통해 데이터를 가져오는 횟수를 줄여 처리량이 증가합니다. 값이 작을수록 서버가 데이터를 전송할 때까지 기다리는 지연이 적기 때문에 응답 시간이 늘어나오게 됩니다. | 1에서 4294967296(4GB)의 정수입니다. 기본값은 `60000`입니다. 값 1은 바이트 수를 정의하지 않지만 정확히 한 행의 데이터에 대한 공간 할당을 나타냅니다. |

Oracle 연결에서 암호화를 사용하도록 설정하려면 다음 두 가지 옵션이 있습니다.

-   오라클 서버 측에서 **트리플-DES 암호화(3DES) 및 고급 암호화 표준(AES)을**사용하려면 오라클 고급 보안(OAS)으로 이동하여 암호화 설정을 구성하십시오. 자세한 내용은 이 [Oracle 설명서를](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)참조하십시오. 오라클 응용 프로그램 개발 프레임워크(ADF) 커넥터는 오라클에 대한 연결을 설정할 때 OAS에서 구성한 암호화 방법을 사용하도록 암호화 방법을 자동으로 협상합니다.

-   **SSL을**사용하려면 :

    1.  SSL 인증서 정보를 가져옵니다. SSL 인증서의 DER(고유 인코딩 규칙) 인코딩 인증서 정보를 얻고 인증서 시작 시 출력(----- 저장합니다... End Certificate -----)을 텍스트 파일로 저장합니다.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **예:** DERcert.cer에서 인증서 정보를 추출한 다음 출력을 cert.txt에 저장합니다.

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
    
    2.  또는 `truststore` `keystore` 를 빌드합니다. 다음 명령은 PKCS-12 형식으로 암호 유무에 관계없이 `truststore` 파일을 만듭니다.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **예:** 암호로 MyTrustStoreFile이라는 PKCS12 `truststore` 파일을 만듭니다.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  자체 `truststore` 호스팅 IR 컴퓨터에 파일을 배치합니다. 예를 들어 C:\MyTrustStoreFile에 파일을 배치합니다.
    4.  Azure 데이터 팩터리에서 Oracle 연결 `EncryptionMethod=1` 문자열과 `TrustStore` / `TrustStorePassword`해당 값을 구성합니다. `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`)을 입력합니다.

**예제:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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

이 섹션에는 Oracle 데이터 세트에서 지원하는 속성의 목록을 제공합니다. 데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조하십시오. 

Oracle에서 데이터를 복사하려면 데이터 집합의 형식 속성을 `OracleTable`로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성을 로 `OracleTable`설정해야 합니다. | yes |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 있는 테이블/보기의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 `schema` 경우 `table`및 를 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예제:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

이 섹션에서는 Oracle 원본 및 싱크에서 지원하는 속성 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="oracle-as-source"></a>Oracle을 원본으로

>[!TIP]
>데이터 분할을 사용하여 오라클의 데이터를 효율적으로 로드하려면 [Oracle의 병렬 복사본에서](#parallel-copy-from-oracle)자세히 알아보십시오.

Oracle에서 데이터를 복사하려면 복사 활동의 소스 유형을 `OracleSource`로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 원본의 형식 속성을 로 `OracleSource`설정해야 합니다. | yes |
| oracleReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `"SELECT * FROM MyTable"`입니다.<br>분할된 로드를 사용하도록 설정하면 쿼리에 해당 기본 제공 파티션 매개 변수를 연결해야 합니다. 예를 들어 Oracle [섹션의 병렬 복사본을](#parallel-copy-from-oracle) 참조하십시오. | 예 |
| 파티션옵션 | Oracle에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용된 값은 없음(기본값), **물리적파티션OfTable** 및 **DynamicRange**입니다. **None**<br>파티션 옵션이 활성화되면(즉, `None`그렇지 않음) Oracle 데이터베이스에서 데이터를 동시에 로드하는 병렬 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 처리 정도는 복사 활동의 설정에 의해 제어됩니다. | 예 |
| 파티션설정 | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 없는 `None`경우 적용됩니다. | 예 |
| 파티션 이름 | 복사해야 하는 실제 파티션 목록입니다. <br>파티션 옵션이 `PhysicalPartitionsOfTable`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfTabularPartitionName` 경우 WHERE 절을 연결합니다. 예를 들어 [Oracle의 병렬 복사본](#parallel-copy-from-oracle) 섹션을 참조하십시오. | 예 |
| 파티션열이름 | 병렬 복사본에 대한 범위 분할에서 사용할 **정수 형식의** 원본 열 이름을 지정합니다. 지정하지 않으면 테이블의 기본 키가 자동으로 감지되어 파티션 열로 사용됩니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfRangePartitionColumnName` 경우 WHERE 절을 연결합니다. 예를 들어 [Oracle의 병렬 복사본](#parallel-copy-from-oracle) 섹션을 참조하십시오. | 예 |
| 파티션어바운드 | 데이터를 복사할 파티션 열의 최대값입니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfRangePartitionUpbound` 경우 WHERE 절을 연결합니다. 예를 들어 [Oracle의 병렬 복사본](#parallel-copy-from-oracle) 섹션을 참조하십시오. | 예 |
| 파티션로바운드 | 데이터를 복사할 파티션 열의 최소값입니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfRangePartitionLowbound` 경우 WHERE 절을 연결합니다. 예를 들어 [Oracle의 병렬 복사본](#parallel-copy-from-oracle) 섹션을 참조하십시오. | 예 |

**예: 파티션 없이 기본 쿼리를 사용하여 데이터 복사**

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

### <a name="oracle-as-sink"></a>Oracle을 싱크로

데이터를 Oracle에 복사하려면 복사 활동의 싱크 유형을 `OracleSink`로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 싱크의 형식 속성을 로 `OracleSink`설정해야 합니다. | yes |
| writeBatchSize | 버퍼 크기에 도달하면 `writeBatchSize`SQL 테이블에 데이터를 삽입합니다.<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)입니다. | 예 |
| preCopyScript | 각 실행에서 Oracle에 데이터를 쓰기 전에 실행할 복사 활동에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 예 |

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

## <a name="parallel-copy-from-oracle"></a>오라클의 병렬 사본

데이터 팩터리 오라클 커넥터는 오라클의 데이터를 병렬로 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 활동의 **소스** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-oracle/connector-oracle-partition-options.png)

분할된 복사본을 사용하도록 설정하면 Data Factory는 Oracle 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 평행 정도는 복사 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 활동의 설정에 의해 제어됩니다. 예를 들어 4로 `parallelCopies` 설정하면 Data Factory는 지정된 파티션 옵션 및 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며 각 쿼리는 Oracle 데이터베이스에서 데이터의 일부를 검색합니다.

특히 Oracle 데이터베이스에서 많은 양의 데이터를 로드할 때 데이터 분할과 병렬 복사본을 사용하도록 설정하는 것이 좋습니다. 다음은 다양한 시나리오에 대한 권장 구성입니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 여러 파일(폴더 이름만 지정)으로 폴더에 쓰도록 명령되며, 이 경우 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

| 시나리오                                                     | 권장 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션이 있는 큰 테이블의 전체 로드입니다.          | **파티션 옵션**: 테이블의 실제 파티션입니다. <br><br/>실행 하는 동안 Data Factory는 자동으로 실제 파티션을 감지 하 고 파티션으로 데이터를 복사 합니다. |
| 데이터 분할을 위한 정수 열이 있는 동안 물리적 파티션없이 큰 테이블에서 전체 로드됩니다. | **파티션 옵션**: 동적 범위 파티션.<br>**분할 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 기본 키 열이 사용됩니다. |
| 실제 파티션을 사용하여 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 테이블의 실제 파티션입니다.<br>**쿼리** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`: .<br>**파티션 이름**: 데이터를 복사할 파티션 이름을 지정합니다. 지정하지 않으면 데이터 팩터리는 Oracle 데이터 집합에 지정한 테이블의 실제 파티션을 자동으로 검색합니다.<br><br>실행 하는 동안 데이터 `?AdfTabularPartitionName` 팩터리는 실제 파티션 이름으로 대체 하 고 오라클에 보냅니다. |
| 데이터 분할을 위한 정수 열을 사용하여 실제 파티션 없이 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 동적 범위 파티션.<br>**쿼리** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**분할 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 데이터 형식을 사용하여 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한**: 파티션 열에 대해 필터링하여 하위 범위와 상위 범위 간에만 데이터를 검색할지 지정합니다.<br><br>실행 하는 동안 Data `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` 는 에서 를 대체하고 각 파티션의 실제 열 이름 및 값 범위로 오라클에 보냅니다. <br>예를 들어 파티션 열 "ID"가 하한을 1로 설정하고 상한을 80으로 설정하고 병렬 복사본을 4로 설정한 경우 Data Factory는 4개의 파티션으로 데이터를 검색합니다. 그들의 아이디는 각각 [1,20], [21, 40], [41, 60], [61, 80] 사이입니다. |

**예: 실제 파티션이 있는 쿼리**

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

**예: 동적 범위 파티션이 있는 쿼리**

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

오라클에서 데이터를 복사하면 다음 매핑이 적용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

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
| timestamp |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 데이터 형식 INTERVAL YEAR TO MONTH 및 INTERVAL DAY TO SECOND는 지원되지 않습니다.

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
