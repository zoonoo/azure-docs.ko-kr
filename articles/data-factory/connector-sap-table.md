---
title: Azure Data Factory를 사용 하 여 SAP 테이블에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용 하 여 SAP 테이블에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법에 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: 4dee0e994c9e7be9677a8f1051481850990998e9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247171"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 SAP 테이블에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 SAP 테이블에서 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SAP 테이블에서 모든 지원 되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히이 SAP 테이블 커넥터를 지원합니다.

- SAP 테이블에서 데이터 복사 **7.01 이상 버전을 사용 하 여 SAP Business Suite** (최근 SAP 지원 패키지 스택을 2015 년 이후에 출시)에서 또는 **S/4HANA**합니다.
- 둘 다에서 데이터를 복사 **투명 한 SAP 테이블** 하 고 **보기**합니다.
- 사용 하 여 데이터 복사 **기본 인증** 하거나 **SNC** (보안 네트워크 통신) SNC 구성 된 경우.
- 연결할 **응용 프로그램 서버** 하거나 **메시지 서버**합니다.

## <a name="prerequisites"></a>필수 조건

이 SAP 테이블 커넥터를 사용 하려면를 지정 해야 합니다.

- 3.17 버전 이상에 자체 호스팅 Integration Runtime을 설정 합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.

- SAP 웹 사이트에서 **64비트 [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** 을 다운로드하여 자체 호스팅 IR 컴퓨터에 설치합니다. 경우 선택적인 설정 단계 창에서 설치를 선택 해야 합니다 **GAC에 어셈블리 설치** 옵션입니다. 

    ![SAP .NET Connector 설치](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory SAP 테이블 커넥터에 사용 되는 SAP 사용자는 다음과 같은 권한이 있어야 해야 합니다. 

    - RFC에 대 한 권한입니다. 
    - 권한 부여 개체 "S_SDSAUTH"의 "실행" 작업에 대 한 권한입니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP 테이블 커넥터에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP Business Warehouse Open Hub 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성을 다음으로 설정해야 합니다. **SapTable** | 예 |
| 서버 | SAP 인스턴스가 상주 하는 서버의 이름입니다.<br/>에 연결 하려는 경우 해당 **SAP 응용 프로그램 서버**합니다. | 아닙니다. |
| systemNumber | SAP 시스템의 시스템 번호입니다.<br/>에 연결 하려는 경우 해당 **SAP 응용 프로그램 서버**합니다.<br/>허용되는 값: 문자열로 표현되는 두 자리 10진수 | 아닙니다. |
| messageServer | SAP 메시지 서버의 호스트 이름입니다.<br/>에 연결 하려는 경우 해당 **SAP 메시지 서버**합니다. | 아닙니다. |
| messageServerService | 서비스 이름 또는 포트 메시지 서버 수입니다.<br/>에 연결 하려는 경우 해당 **SAP 메시지 서버**합니다. | 아닙니다. |
| systemId | 테이블이 위치 하는 SAP 시스템의 SystemID 합니다.<br/>에 연결 하려는 경우 해당 **SAP 메시지 서버**합니다. | 아닙니다. |
| logonGroup | SAP 시스템에 대 한 로그온 그룹입니다.<br/>에 연결 하려는 경우 해당 **SAP 메시지 서버**합니다. | 아닙니다. |
| clientId | SAP 시스템에서 클라이언트의 클라이언트 ID입니다.<br/>허용되는 값: 문자열로 표현되는 세 자리 10진수 | 예 |
| language | SAP 시스템에서 사용하는 언어입니다. | No(기본값: **EN**)|
| userName | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | 예. |
| password | 사용자에 대한 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| sncMode | SNC 테이블 위치한 SAP 서버에 액세스 하려면 활성화 표시기입니다.<br/>SNC 사용 하 여 SAP 서버에 연결 하려는 경우에 적용할 수 있습니다.<br/>허용되는 값은 다음과 같습니다. **0** (해제, 기본값) 또는 **1** (설정). | 아닙니다. |
| sncMyName | 초기자의 테이블 위치한 SAP 서버에 액세스할 SNC 이름입니다.<br/>적용 가능한 경우 `sncMode` 켜져 있습니다. | 아닙니다. |
| sncPartnerName | 테이블 위치한 SAP 서버에 액세스 하려면 통신 파트너의 SNC 이름입니다.<br/>적용 가능한 경우 `sncMode` 켜져 있습니다. | 아닙니다. |
| sncLibraryPath | SAP 서버에 액세스 하려면 외부 보안 제품의 라이브러리를 테이블 위치한입니다.<br/>적용 가능한 경우 `sncMode` 켜져 있습니다. | 아닙니다. |
| sncQop | SNC 품질 보호입니다.<br/>적용 가능한 경우 `sncMode` 켜져 있습니다. <br/>허용되는 값은 다음과 같습니다. **1** (인증) **2** (무결성) **3** (개인) **8** (기본값) **9** (최대값). | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예 1: SAP 응용 프로그램 서버에 연결**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예 2: SAP Message Server에 연결**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 3: SNC를 사용 하 여 연결**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 SAP 테이블 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

SAP BW Open Hub에 데이터를 복사 하는 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성으로 설정 되어 있어야 **SapTableResource**합니다. | 예 |
| tableName | 데이터를 복사 하려면 SAP 테이블의 이름입니다. | 예 |

**예제:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SAP 테이블 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="sap-table-as-source"></a>SAP 테이블 원본으로

SAP 테이블에서 데이터를 복사할에 다음 속성이 지원 됩니다.

| 자산                         | 설명                                                  | 필수 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| 형식                             | 형식 속성으로 설정 되어 있어야 **SapTableSource**합니다.       | 예      |
| rowCount                         | 검색할 행 수입니다.                              | 아닙니다.       |
| rfcTableFields                   | SAP 테이블에서 복사할 필드입니다. 예: `column0, column1`. | 아닙니다.       |
| rfcTableOptions                  | SAP 테이블에 행을 필터링 할 옵션입니다. 예: `COLUMN0 EQ 'SOMEVALUE'`. | 아닙니다.       |
| customRfcReadTableFunctionModule | SAP 테이블에서 데이터를 읽을 수 있는 사용자 지정 RFC 함수 모듈 | 아닙니다.       |
| partitionOption                  | SAP 테이블에서 읽을 파티션 메커니즘입니다. 지원 되는 옵션은 다음과 같습니다. <br/>- **없음**<br/>- **PartitionOnInt** (정수 또는 정수 값 0 패딩 0000012345 같은 왼쪽)<br/>- **PartitionOnCalendarYear** ("YYYY" 형식으로 4 자리 숫자)<br/>- **PartitionOnCalendarMonth** ("YYYYMM" 형식으로 6 자리 숫자)<br/>- **PartitionOnCalendarDate** ("YYYYMMDD" 형식의 8 자리 숫자) | 아닙니다.       |
| partitionColumnName              | 데이터를 분할 하는 열의 이름입니다. | 아닙니다.       |
| partitionUpperBound              | 에 지정 된 열의 최대값 `partitionColumnName` 는 계속 분할에 사용 됩니다. | 아닙니다.       |
| partitionLowerBound              | 에 지정 된 열의 최소값 `partitionColumnName` 는 계속 분할에 사용 됩니다. | 아닙니다.       |
| maxPartitionsNumber              | 데이터를 분할 하는 파티션 최대 수입니다. | 아닙니다.       |

>[!TIP]
>- SAP 테이블에 많은 양의 여러 수십억 개의 행과 같은 데이터를 사용 하 여 `partitionOption` 고 `partitionSetting` 작은 파티션으로 데이터를 분할 하려면 하나의 하 여 SAP 서버에서 검색 되는 쿼리에서 데이터를 읽은 파티션과 각 데이터 파티션에 RFC 호출입니다.<br/>
>- 수행 `partitionOption` 으로 `partitionOnInt` 예를 들어 각 파티션의 행 수에서 계산 됩니다 (총 행 사이 속하는 *partitionUpperBound* 하 고 *partitionLowerBound*) /*maxPartitionsNumber*합니다.<br/>
>- 추가 파티션을 복사 속도를 병렬로 실행 하려는 경우이 강력 하 게 만드는 것이 좋습니다 `maxPartitionsNumber` 값의 배수 `parallelCopies` (자세한 내용은 [병렬 복사](copy-activity-performance.md#parallel-copy)).

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-table"></a>SAP 테이블에 대 한 데이터 형식 매핑

SAP 테이블에서 데이터를 복사 하면 Azure Data Factory 중간 데이터 형식에 SAP 테이블 데이터 형식에서 다음 매핑이 사용 됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP ABAP 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| C(문자열) | String |
| 합니까 (Integer) | Int32 |
| F(부동) | Double |
| D(날짜) | 문자열 |
| T(시간) | 문자열 |
| P(BCD 압축, 통화, 10진수, 수량) | Decimal |
| N (숫자) | String |
| X(이진 및 원시) | 문자열 |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
