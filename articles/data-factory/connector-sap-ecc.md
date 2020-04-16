---
title: SAP ECC에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 활동을 사용하여 데이터를 SAP ECC에서 지원되는 싱크 데이터 저장소로 복사하는 방법을 알아봅니다.
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
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413792"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 SAP ECC의 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure 데이터 팩터리에서 복사 활동을 사용하여 ECC(SAP 엔터프라이즈 중앙 구성 요소)의 데이터를 복사하는 방법을 설명합니다. 자세한 내용은 [활동 복사 개요를](copy-activity-overview.md)참조하십시오.

>[!TIP]
>SAP 데이터 통합 시나리오에 대한 ADF의 전반적인 지원에 대해 알아보려면 자세한 소개, 비교 및 지침이 있는 [Azure Data Factory 백서를 사용하는 SAP 데이터 통합을](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP ECC 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

SAP ECC에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 활동에 의해 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하십시오.

특히 이 SAP ECC 커넥터는 다음을 지원합니다.

- SAP NetWeaver 버전 7.0 이상에서 SAP ECC의 데이터 복사.
- SAP ECC OData 서비스에서 노출한 모든 개체의 데이터 복사( 예:

  - SAP 테이블 또는 보기.
  - 비즈니스 응용 프로그램 프로그래밍 인터페이스 [BAPI] 개체입니다.
  - 데이터 추출기.
  - 상대 어댑터를 통해 OData로 받을 수 있는 SAP 프로세스 통합(PI)으로 전송된 데이터 또는 중간 문서(IDOCs)입니다.

- 기본 인증을 사용하여 데이터를 복사합니다.

>[!TIP]
>SAP 테이블 또는 보기를 통해 SAP ECC의 데이터를 복사하려면 더 빠르고 확장성이 뛰어난 [SAP 테이블](connector-sap-table.md) 커넥터를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

일반적으로 SAP ECC는 SAP 게이트웨이를 통해 OData 서비스를 통해 엔터티를 공개합니다. 이 SAP ECC 커넥터를 사용하려면 다음을 수행해야 합니다.

- **SAP 게이트웨이 설정**. 7.4 이상 SAP NetWeaver 버전이 있는 서버의 경우 SAP 게이트웨이가 이미 설치되어 있습니다. 이전 버전의 경우 OData 서비스를 통해 SAP ECC 데이터를 노출하기 전에 임베디드 SAP 게이트웨이 또는 SAP 게이트웨이 허브 시스템을 설치해야 합니다. SAP 게이트웨이를 설정하려면 [설치 가이드를](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)참조하십시오.

- **SAP OData 서비스를 활성화하고 구성합니다.** 몇 초 만에 TCODE SICF를 통해 OData 서비스를 활성화할 수 있습니다. 노출해야 하는 개체를 구성할 수도 있습니다. 자세한 내용은 [단계별 지침을](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP ECC 커넥터와 관련된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대한 세부 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP ECC 연결 서비스에 는 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapEcc`합니다. | 예 |
| `url` | SAP ECC OData 서비스의 URL입니다. | 예 |
| `username` | SAP ECC에 연결하는 데 사용되는 사용자 이름입니다. | 예 |
| `password` | SAP ECC에 연결하는 데 사용되는 일반 텍스트 암호입니다. | 예 |
| `connectVia` | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 런타임을 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. | 예 |

### <a name="example"></a>예제

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조하십시오. 다음 섹션에서는 SAP ECC 데이터 집합에서 지원하는 속성 목록을 제공합니다.

SAP ECC에서 데이터를 복사하려면 `type` 데이터 집합의 `SapEccResource`속성을 로 설정합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `path` | SAP ECC OData 엔터티의 경로입니다. | 예 |

### <a name="example"></a>예제

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

활동을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 을](concepts-pipelines-activities.md)참조하십시오. 다음 섹션에서는 SAP ECC 소스에서 지원하는 속성 목록을 제공합니다.

### <a name="sap-ecc-as-a-source"></a>소스로서의 SAP ECC

SAP ECC에서 데이터를 복사하려면 `type` 복사 `source` 활동 섹션의 속성을 `SapEccSource`로 설정합니다.

다음 속성은 복사 활동 `source` 섹션에서 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `type` | 복사 `type` 활동 섹션의 `source` 속성을 로 `SapEccSource`설정해야 합니다. | 예 |
| `query` | 데이터를 필터링하는 OData 쿼리 옵션입니다. 다음은 그 예입니다.<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 커넥터는 결합된 URL에서 데이터를 복사합니다.<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>자세한 내용은 [OData URL 구성 요소](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)를 참조하세요. | 예 |

### <a name="example"></a>예제

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC용 데이터 유형 매핑

SAP ECC에서 데이터를 복사할 때 SAP ECC 데이터에 대한 OData 데이터 형식에서 Azure Data Factory 중간 데이터 형식에 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| OData 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> 복잡한 데이터 형식은 현재 지원되지 않습니다.

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 활동에 의해 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소를](copy-activity-overview.md#supported-data-stores-and-formats)참조하십시오.
