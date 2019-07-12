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
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827763"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 SAP 테이블에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 SAP 테이블에서 데이터를 복사 하는 방법을 설명 합니다. 자세한 내용은 [복사 작업 개요](copy-activity-overview.md)합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SAP 테이블에서 모든 지원 되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 목록을 복사 활동에서 원본 또는 싱크로 지원 되는 데이터 저장소에 대 한 참조를 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블입니다.

특히이 SAP 테이블 커넥터를 지원합니다.

- SAP 테이블에서 데이터를 복사 합니다.

  - SAP ERP 중앙 구성 요소 (SAP ECC) 7.01 이상 (최근 SAP 지원 패키지 스택을 2015 년 이후에 출시) 버전입니다.
  - SAP Business Warehouse (SAP BW) 7.01 이상 버전.
  - SAP S/4HANA.
  - SAP Business Suite 7.01 이상 버전의에서 다른 제품입니다.

- SAP 투명 한 테이블, 풀링된 테이블, 클러스터형된 테이블 및 뷰를 모두에서 데이터를 복사합니다.
- SNC 구성 된 경우에 기본 인증 또는 보안 네트워크 통신 SNC ()를 사용 하 여 데이터를 복사 합니다.
- SAP 응용 프로그램 서버 또는 SAP 메시지 서버에 연결 합니다.

## <a name="prerequisites"></a>전제 조건

이 SAP 테이블 커넥터를 사용 하려면를 지정 해야 합니다.

- 자체 호스팅된 통합 런타임 (3.17 이상 버전)을 설정 합니다. 자세한 내용은 [만들기 및 자체 호스팅된 통합 런타임을 구성](create-self-hosted-integration-runtime.md)합니다.

- 64 비트를 다운로드 [3.0 Microsoft.NET 용 SAP Connector](https://support.sap.com/en/product/connectors/msnet.html) SAP의 웹 사이트에서 자체 호스팅된 통합 런타임 컴퓨터에 설치 합니다. 설치 하는 동안 선택 해야 합니다 **GAC에 어셈블리 설치** 옵션을 **선택적인 설정 단계** 창입니다.

  ![.NET 용 SAP Connector를 설치 합니다.](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 데이터 팩터리 SAP 테이블 커넥터에 사용 되는 SAP 사용자는 다음 권한이 있어야 합니다.

  - 원격 함수 호출 (RFC) 대상을 사용 하 여에 대 한 권한입니다.
  - S_SDSAUTH 권한 부여 개체의 실행 작업에 권한입니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP 테이블 커넥터에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP BW 오픈 허브 연결 된 서비스에 대 한 다음 속성이 지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapTable`합니다. | 예 |
| `server` | SAP 인스턴스가 있는 서버의 이름입니다.<br/>SAP 응용 프로그램 서버에 연결 하려면 사용 합니다. | 아니요 |
| `systemNumber` | SAP 시스템의 시스템 번호입니다.<br/>SAP 응용 프로그램 서버에 연결 하려면 사용 합니다.<br/>허용 되는 값: 문자열로 표현 되는 두 자리 10 진수. | 아니요 |
| `messageServer` | SAP 메시지 서버의 호스트 이름입니다.<br/>SAP 메시지 서버에 연결 하려면 사용 합니다. | 아니요 |
| `messageServerService` | 서비스 이름 또는 포트 메시지 서버 수입니다.<br/>SAP 메시지 서버에 연결 하려면 사용 합니다. | 아니요 |
| `systemId` | 테이블이 위치 하는 SAP 시스템의 ID입니다.<br/>SAP 메시지 서버에 연결 하려면 사용 합니다. | 아니요 |
| `logonGroup` | SAP 시스템에 대 한 로그온 그룹입니다.<br/>SAP 메시지 서버에 연결 하려면 사용 합니다. | 아니요 |
| `clientId` | SAP 시스템에서 클라이언트의 ID입니다.<br/>허용 되는 값: 문자열로 표현 되는 세 자리 10 진수. | 예 |
| `language` | SAP 시스템을 사용 하는 언어입니다.<br/>기본값은 `EN`여야 합니다.| 아니요 |
| `userName` | SAP 서버에 대 한 액세스 권한이 있는 사용자의 이름입니다. | 예 |
| `password` | 사용자에 대 한 암호입니다. 이 필드를 표시 합니다 `SecureString` Data Factory에 안전 하 게 저장 하는 형식 또는 [Azure Key Vault에 저장 된 비밀을 참조할](store-credentials-in-key-vault.md)합니다. | 예 |
| `sncMode` | 테이블 위치한 SAP 서버에 액세스 하 SNC 활성화 표시기입니다.<br/>SNC 사용 하 여 SAP 서버에 연결 하려는 경우 사용 합니다.<br/>허용 되는 값은 `0` (해제, 기본값) 또는 `1` (설정). | 아니요 |
| `sncMyName` | 테이블 위치한 SAP 서버에 액세스 하 여 개시자의 SNC 이름입니다.<br/>경우에 적용 됩니다 `sncMode` 켜져 있습니다. | 아니요 |
| `sncPartnerName` | 테이블 위치한 SAP 서버에 액세스 하는 통신 파트너의 SNC 이름입니다.<br/>경우에 적용 됩니다 `sncMode` 켜져 있습니다. | 아니요 |
| `sncLibraryPath` | SAP 서버에 액세스 하려면 외부 보안 제품의 라이브러리를 테이블 위치한입니다.<br/>경우에 적용 됩니다 `sncMode` 켜져 있습니다. | 아니요 |
| `sncQop` | 적용할 보호 품질이 SNC 수준입니다.<br/>경우에 적용 됩니다 `sncMode` 켜져 있습니다. <br/>허용 되는 값은 `1` (인증) `2` (무결성) `3` (개인) `8` (기본값) `9` (최대값). | 아니요 |
| `connectVia` | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 자체 호스팅된 integration runtime을 반드시 앞에서 언급 한 대로 [필수 구성 요소](#prerequisites)합니다. |예 |

**예제 1: SAP 응용 프로그램 서버에 연결**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
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

### <a name="example-2-connect-to-an-sap-message-server"></a>예제 2: SAP 메시지 서버에 연결

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>예제 3: SNC 사용 하 여 연결

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

섹션 및 데이터 집합 정의 대 한 속성의 전체 목록을 참조 하세요 [데이터 집합](concepts-datasets-linked-services.md)합니다. 다음 섹션에서는 SAP 테이블 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

SAP BW 오픈 허브 연결 된 서비스에 데이터를 복사 하는 다음 속성이 지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapTableResource`합니다. | 예 |
| `tableName` | 데이터를 복사 하려면 SAP 테이블의 이름입니다. | 예 |

### <a name="example"></a>예제

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

섹션 및 활동 정의 대 한 속성의 전체 목록을 참조 하세요 [파이프라인](concepts-pipelines-activities.md)합니다. 다음 섹션에서는 SAP 테이블 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="sap-table-as-a-source"></a>SAP 테이블 원본으로

SAP 테이블에서 데이터를 복사할에 다음 속성이 지원 됩니다.

| 속성                         | 설명                                                  | 필수 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | 합니다 `type` 속성으로 설정 되어 있어야 `SapTableSource`합니다.         | 예      |
| `rowCount`                         | 검색할 행의 수입니다.                              | 아니요       |
| `rfcTableFields`                   | SAP 테이블에서 복사할 필드 (열)입니다. `column0, column1` )을 입력합니다. | 아니요       |
| `rfcTableOptions`                  | SAP 테이블에 행을 필터링 하는 옵션입니다. `COLUMN0 EQ 'SOMEVALUE'` )을 입력합니다. 이 문서의 뒷부분에 SAP 쿼리 연산자 표를 참조 하세요. | 아니요       |
| `customRfcReadTableFunctionModule` | 사용자 지정 RFC 함수 모듈입니다 SAP 테이블에서 데이터를 읽는 데 사용할 수 있습니다.<br>데이터를 SAP 시스템에서 검색 하 고 데이터 팩터리를 반환 하는 방법을 정의 하는 사용자 지정 RFC 함수 모듈을 사용할 수 있습니다. 사용자 지정 함수 모듈 인터페이스 구현 (가져오기, 내보내기, 테이블)와 비슷한 있어야 `/SAPDS/RFC_READ_TABLE2`, 데이터 팩터리에서 사용 되는 기본 인터페이스는 합니다. | 아니요       |
| `partitionOption`                  | SAP 테이블에서 읽을 파티션 메커니즘입니다. 지원 되는 옵션은 다음과 같습니다. <ul><li>`None`</li><li>`PartitionOnInt` (정수 또는 정수 값 0의 안쪽 여백을 왼쪽 같은 `0000012345`)</li><li>`PartitionOnCalendarYear` ("YYYY" 형식으로 4 자리 숫자)</li><li>`PartitionOnCalendarMonth` ("YYYYMM" 형식으로 6 자리 숫자)</li><li>`PartitionOnCalendarDate` ("YYYYMMDD" 형식의 8 자리 숫자)</li></ul> | 아니요       |
| `partitionColumnName`              | 데이터를 분할 하는 데 사용 된 열의 이름입니다.                | 아니요       |
| `partitionUpperBound`              | 에 지정 된 열의 최대값 `partitionColumnName` 분할 진행 하는 데 수 있습니다. | 아니요       |
| `partitionLowerBound`              | 에 지정 된 열의 최소값 `partitionColumnName` 분할 진행 하는 데 수 있습니다. | 아니요       |
| `maxPartitionsNumber`              | 데이터를 분할 하는 파티션 최대 수입니다.     | 아니요       |

>[!TIP]
>SAP 테이블에 많은 양의 데이터를 여러 십억 행과 같은 경우 사용 하 여 `partitionOption` 고 `partitionSetting` 작은 파티션으로 데이터를 분할 합니다. 이 경우 데이터를 파티션당 읽기 및 각 데이터 파티션에 대 한 단일 RFC 호출 하 여 SAP 서버에서 검색 됩니다.<br/>
<br/>
>수행 `partitionOption` 으로 `partitionOnInt` 각 파티션에 있는 행의 수는 예를 들어이 수식을 사용 하 여 계산 됩니다: (총 행 사이 속하는 `partitionUpperBound` 하 고 `partitionLowerBound`) /`maxPartitionsNumber`합니다.<br/>
<br/>
>파티션을 복사 속도를 병렬로 실행 하려면 좋습니다 making `maxPartitionsNumber` 값의 배수를 `parallelCopies` 속성입니다. 자세한 내용은 [병렬 복사](copy-activity-performance.md#parallel-copy)합니다.

`rfcTableOptions`, 다음 일반적인 SAP 쿼리 연산자를 사용 하 여 행을 필터링 할 수 있습니다.

| 연산자 | Description |
| :------- | :------- |
| `EQ` | 다음과 같음 |
| `NE` | 같지 않음 |
| `LT` | 보다 작음 |
| `LE` | 작거나 같음 |
| `GT` | 보다 큼 |
| `GE` | 크거나 같음 |
| `LIKE` | 와 같이 `LIKE 'Emma%'` |

### <a name="example"></a>예제

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 테이블에 대 한 데이터 형식 매핑

SAP 테이블에서 데이터를 복사 하는 경우 Azure Data Factory 중간 데이터 형식에 SAP 테이블 데이터 형식에서 다음과 같은 매핑이 사용 됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP ABAP 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| `C` (String) | `String` |
| `I` (Integer) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` (시간) | `String` |
| `P` (BCD 압축, 통화, Decimal, Qty) | `Decimal` |
| `N` (숫자) | `String` |
| `X` (이진 및 원시) | `String` |

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업은 원본 및 싱크로 지원 되는 데이터 저장소 목록은 참조 하세요 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)합니다.
