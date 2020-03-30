---
title: SAP 테이블에서 데이터 복사
description: Azure Data Factory 파이프라인에서 복사 작업을 사용하여 SAP 테이블에서 지원되는 싱크 데이터 저장소에 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: e98ff7fd914bb86cae256bb1bf6c19086758d463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371556"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 SAP 테이블의 데이터 복사

이 문서에서는 Azure Data Factory의 복사 활동을 사용하여 SAP 테이블에서 데이터를 복사하는 방법을 설명합니다. 자세한 내용은 [활동 복사 개요를](copy-activity-overview.md)참조하십시오.

>[!TIP]
>SAP 데이터 통합 시나리오에 대한 ADF의 전반적인 지원에 대해 알아보려면 자세한 소개, 비교 및 지침이 있는 [Azure Data Factory 백서를 사용하는 SAP 데이터 통합을](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP 테이블 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

SAP 테이블에서 지원되는 모든 싱크 데이터 저장소에 데이터를 복사할 수 있습니다. 복사 활동에 의해 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하십시오.

특히 이 SAP 테이블 커넥터는 다음을 지원합니다.

- SAP 테이블에서 데이터를 복사합니다.

  - SAP ERP 중앙 구성 요소(SAP ECC) 버전 7.01 이상(2015년 이후에 릴리스된 최근 SAP 지원 패키지 스택).
  - SAP 비즈니스 웨어하우스(SAP BW) 버전 7.01 이상(2015년 이후에 릴리스된 최근 SAP 지원 패키지 스택).
  - SAP S/4HANA.
  - SAP 비즈니스 Suite 버전 7.01 이상(2015년 이후에 릴리스된 최근 SAP 지원 패키지 스택)의 다른 제품입니다.

- SAP 투명 테이블, 풀링된 테이블, 클러스터된 테이블 및 뷰모두에서 데이터를 복사합니다.
- SNC가 구성된 경우 기본 인증 또는 보안 네트워크 통신(SNC)을 사용하여 데이터를 복사합니다.
- SAP 응용 프로그램 서버 또는 SAP 메시지 서버에 연결합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 SAP 테이블 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 통합 런타임(버전 3.17 이상)을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성을](create-self-hosted-integration-runtime.md)참조하십시오.

- SAP 웹 사이트에서 [Microsoft .NET 3.0용](https://support.sap.com/en/product/connectors/msnet.html) 64비트 SAP 커넥터를 다운로드하여 자체 호스팅 통합 런타임 컴퓨터에 설치합니다. 설치하는 동안 **선택적 설치 단계** 창에서 **GAC에 어셈블리 설치** 옵션을 선택해야 합니다.

  ![.NET용 SAP 커넥터 설치](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 데이터 팩터리 SAP 테이블 커넥터에서 사용 중인 SAP 사용자에게는 다음 권한이 있어야 합니다.

  - RFC(원격 함수 호출) 대상 사용에 대한 권한 부여.
  - S_SDSAUTH 권한 부여 개체의 실행 활동에 대한 사용 권한입니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP 테이블 커넥터와 관련된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대한 세부 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP BW 오픈 허브 연결 서비스에 대해 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapTable`합니다. | yes |
| `server` | SAP 인스턴스가 있는 서버의 이름입니다.<br/>SAP 응용 프로그램 서버에 연결하는 데 사용합니다. | 예 |
| `systemNumber` | SAP 시스템의 시스템 번호입니다.<br/>SAP 응용 프로그램 서버에 연결하는 데 사용합니다.<br/>허용된 값: 문자열로 표시되는 두 자리 소수 자릿수입니다. | 예 |
| `messageServer` | SAP 메시지 서버의 호스트 이름입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `messageServerService` | 메시지 서버의 서비스 이름 또는 포트 번호입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `systemId` | 테이블이 있는 SAP 시스템의 ID입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `logonGroup` | SAP 시스템의 로그온 그룹입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `clientId` | SAP 시스템의 클라이언트 ID입니다.<br/>허용된 값: 문자열로 표시되는 세 자리 소수 자릿수입니다. | yes |
| `language` | SAP 시스템에서 사용하는 언어입니다.<br/>기본값은 `EN`입니다.| 예 |
| `userName` | SAP 서버에 액세스할 수 있는 사용자의 이름입니다. | yes |
| `password` | 사용자의 암호입니다. 이 필드를 데이터 `SecureString` 팩터리에 안전하게 저장할 유형으로 표시하거나 [Azure Key Vault에 저장된 비밀을 참조합니다.](store-credentials-in-key-vault.md) | yes |
| `sncMode` | 테이블이 있는 SAP 서버에 액세스하는 SNC 활성화 표시기입니다.<br/>SNC를 사용하여 SAP 서버에 연결하려는 경우 사용합니다.<br/>허용된 `0` 값은 (꺼져, `1` 기본값) 또는 (켜기)입니다. | 예 |
| `sncMyName` | 테이블이 있는 SAP 서버에 액세스하기 위한 이니시에이터의 SNC 이름입니다.<br/>사용 `sncMode` 중일 때 적용됩니다. | 예 |
| `sncPartnerName` | 테이블이 있는 SAP 서버에 액세스하려면 통신 파트너의 SNC 이름입니다.<br/>사용 `sncMode` 중일 때 적용됩니다. | 예 |
| `sncLibraryPath` | 테이블이 있는 SAP 서버에 액세스하려면 외부 보안 제품의 라이브러리입니다.<br/>사용 `sncMode` 중일 때 적용됩니다. | 예 |
| `sncQop` | 적용 할 보호 수준의 SNC 품질.<br/>켜기 `sncMode` 때 적용됩니다. <br/>허용된 `1` 값은 `2` (인증), `3` (무결성), (개인 정보 보호), `8` (기본값), `9` (최대). | 예 |
| `connectVia` | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 조건의](#prerequisites)앞에서 설명한 것처럼 자체 호스팅 통합 런타임이 필요합니다. |yes |

**예 1: SAP 응용 프로그램 서버에 연결**

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

### <a name="example-3-connect-by-using-snc"></a>예 3: SNC를 사용하여 연결

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

데이터 집합을 정의하기 위한 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조하십시오. 다음 섹션에서는 SAP 테이블 데이터 집합에서 지원하는 속성 목록을 제공합니다.

SAP BW Open Hub 연결 서비스에서 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapTableResource`합니다. | yes |
| `tableName` | 데이터를 복사할 SAP 테이블의 이름입니다. | yes |

### <a name="example"></a>예제

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

활동 정의를 위한 섹션 및 속성의 전체 목록은 [파이프라인 을](concepts-pipelines-activities.md)참조하십시오. 다음 섹션에서는 SAP 테이블 소스에서 지원하는 속성 목록을 제공합니다.

### <a name="sap-table-as-source"></a>소스로 SAP 테이블

SAP 테이블에서 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성                         | 설명                                                  | 필수 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | 합니다 `type` 속성으로 설정 되어 있어야 `SapTableSource`합니다.         | yes      |
| `rowCount`                         | 검색할 행 수입니다.                              | 예       |
| `rfcTableFields`                   | SAP 테이블에서 복사할 필드(열)입니다. `column0, column1`)을 입력합니다. | 예       |
| `rfcTableOptions`                  | SAP 테이블의 행을 필터링하는 옵션입니다. `COLUMN0 EQ 'SOMEVALUE'`)을 입력합니다. 이 문서의 후반부에서 SAP 쿼리 연산자 테이블도 참조하십시오. | 예       |
| `customRfcReadTableFunctionModule` | SAP 테이블에서 데이터를 읽는 데 사용할 수 있는 사용자 지정 RFC 함수 모듈입니다.<br>사용자 지정 RFC 함수 모듈을 사용하여 SAP 시스템에서 데이터를 검색하고 데이터 팩터리로 반환하는 방법을 정의할 수 있습니다. 사용자 지정 함수 모듈에는 Data Factory에서 사용하는 기본 인터페이스와 `/SAPDS/RFC_READ_TABLE2`유사한 인터페이스(가져오기, 내보내기, 테이블)가 구현되어 있어야 합니다. | 예       |
| `partitionOption`                  | SAP 테이블에서 읽을 파티션 메커니즘입니다. 지원되는 옵션은 다음과 같습니다. <ul><li>`None`</li><li>`PartitionOnInt`(왼쪽에 패딩이 0인 일반 정수 또는 정수 `0000012345`값(예:)</li><li>`PartitionOnCalendarYear`("YYYY"형식의 4자리)</li><li>`PartitionOnCalendarMonth`("YYYYMM" 형식의 6자리)</li><li>`PartitionOnCalendarDate`("YYYYMMDD"형식의 8자리)</li></ul> | 예       |
| `partitionColumnName`              | 데이터를 분할하는 데 사용되는 열의 이름입니다.                | 예       |
| `partitionUpperBound`              | 분할을 계속하는 데 `partitionColumnName` 사용되는 열의 최대값입니다. | 예       |
| `partitionLowerBound`              | 분할을 계속하는 데 `partitionColumnName` 사용되는 열의 최소값입니다. (참고: `partitionLowerBound` 파티션 옵션이 있는 경우 `PartitionOnInt`"0"이 될 수 없습니다) | 예       |
| `maxPartitionsNumber`              | 데이터를 분할할 최대 파티션 수입니다.     | 예       |

>[!TIP]
>SAP 테이블에 수십억 개의 행과 같은 대용량의 데이터가 `partitionOption` `partitionSetting` 있는 경우 데이터를 더 작은 파티션으로 사용하고 분할합니다. 이 경우 데이터는 파티션당 읽게 되며 각 데이터 파티션은 단일 RFC 호출을 통해 SAP 서버에서 검색됩니다.<br/>
<br/>
>`partitionOption` `partitionUpperBound` `partitionLowerBound``maxPartitionsNumber`예를 들어, 각 파티션의 행 수는 이 수식으로 계산됩니다. `partitionOnInt`<br/>
<br/>
>복사 속도를 높이기 위해 데이터 파티션을 병렬로 로드하려면 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 복사 활동의 설정에 의해 병렬 정도가 제어됩니다. 예를 들어 4로 `parallelCopies` 설정하면 Data Factory는 지정된 파티션 옵션 및 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며 각 쿼리는 SAP 테이블에서 데이터의 일부를 검색합니다. 속성 값의 `maxPartitionsNumber` 배수를 만드는 것이 좋습니다. `parallelCopies` 파일 기반 데이터 저장소에 데이터를 복사할 때 여러 파일(폴더 이름만 지정)으로 폴더에 쓰도록 명령되며, 이 경우 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

에서 `rfcTableOptions`다음과 같은 일반적인 SAP 쿼리 연산자에서 행을 필터링할 수 있습니다.

| 연산자 | 설명 |
| :------- | :------- |
| `EQ` | 같음 |
| `NE` | 같지 않음 |
| `LT` | 보다 작음 |
| `LE` | 작거나 같음 |
| `GT` | 초과 |
| `GE` | 크거나 같음 |
| `LIKE` | 에서와 같이`LIKE 'Emma%'` |

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
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 테이블에 대한 데이터 형식 매핑

SAP 테이블에서 데이터를 복사할 때 SAP 테이블 데이터 형식에서 Azure Data Factory 임시 데이터 형식에 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP ABAP 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| `C`(문자열) | `String` |
| `I`(정수) | `Int32` |
| `F`(플로트) | `Double` |
| `D`(날짜) | `String` |
| `T`(시간) | `String` |
| `P`(BCD 포장, 통화, 소수점, Qty) | `Decimal` |
| `N`(숫자) | `String` |
| `X`(바이너리 및 원시) | `String` |

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.


## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 활동에 의해 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소를](copy-activity-overview.md#supported-data-stores-and-formats)참조하십시오.
