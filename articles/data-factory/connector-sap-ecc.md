---
title: Azure Data Factory를 사용 하 여 SAP ECC에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 활동을 사용하여 데이터를 SAP ECC에서 지원되는 싱크 데이터 저장소로 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827777"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 SAP ECC에서 데이터 복사

이 문서에서 SAP Enterprise Central 구성 요소 (ECC) 데이터를 복사 하려면 Azure Data Factory의 복사 작업을 사용 하는 방법을 설명 합니다. 자세한 내용은 [복사 작업 개요](copy-activity-overview.md)합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SAP ECC에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SAP ECC 커넥터는 다음을 지원합니다.

- SAP NetWeaver 7.0 이상 버전에서 SAP ECC에서 데이터를 복사합니다.
- 와 같은 SAP ECC OData 서비스에서 노출 하는 모든 개체에서 데이터를 복사 합니다.

  - SAP 테이블 또는 뷰를 제공 합니다.
  - 비즈니스 응용 프로그램 프로그래밍 인터페이스 [BAPI] 개체입니다.
  - 데이터 추출기입니다.
  - 데이터 또는 중간 문서 (Idoc) 상대 어댑터를 통해 OData로 받을 수 있는 SAP 프로세스 통합 (PI)를 전송 합니다.

- 기본 인증을 사용 하 여 데이터를 복사 합니다.

>[!TIP]
>SAP 테이블 또는 뷰를 통해 SAP ECC에서 데이터를 복사 하려면 사용 합니다 [SAP 테이블](connector-sap-table.md) 커넥터를 빠르고 확장성 가능 합니다.

## <a name="prerequisites"></a>필수 구성 요소

일반적으로 SAP ECC는 SAP 게이트웨이를 통해 OData 서비스를 통해 엔터티를 공개합니다. 이 SAP ECC 커넥터를 사용하려면 다음을 수행해야 합니다.

- **SAP 게이트웨이 설정**. SAP NetWeaver 버전 7.4 이상 서버에 대 한 SAP 게이트웨이가 이미 설치 되어 있습니다. 이전 버전에서는 포함 된 SAP 게이트웨이 또는 SAP 게이트웨이 허브 시스템 OData 서비스를 통해 SAP ECC 데이터를 공개 하기 전에 설치 해야 합니다. SAP 게이트웨이 설정 하려면 참조는 [설치 가이드](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)합니다.

- **활성화 하 고 SAP OData 서비스를 구성**합니다. 시간 (초)에 TCODE SICF를 통해 OData 서비스를 활성화할 수 있습니다. 개체를 노출 해야 합니다. 구성할 수 있습니다. 자세한 내용은 참조는 [단계별 지침](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)합니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP ECC 커넥터에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP ECC 연결 된 서비스에 대 한 다음 속성이 지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapEcc`합니다. | 예 |
| `url` | SAP ECC OData 서비스의 URL입니다. | 예 |
| `username` | SAP ECC에 연결 하는 데 사용자 이름입니다. | 아니요 |
| `password` | SAP ECC에 연결 하는 데 일반 텍스트 암호입니다. | 아니요 |
| `connectVia` | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. (데이터 저장소에 공개적으로 액세스할 수 있는 경우) Azure integration runtime 또는 자체 호스팅된 integration runtime을 사용할 수 있습니다. 런타임 지정 하지 않으면 `connectVia` 기본 Azure integration runtime을 사용 합니다. | 아니요 |

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

섹션 및 데이터 집합 정의에 사용할 수 있는 속성의 전체 목록을 참조 하세요 [데이터 집합](concepts-datasets-linked-services.md)합니다. 다음 섹션에서는 SAP ECC 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

SAP ECC에서 데이터를 복사 하려면 합니다 `type` 데이터 집합의 속성 `SapEccResource`합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
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
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

섹션 및 작업 정의에 사용할 수 있는 속성의 전체 목록을 참조 하세요 [파이프라인](concepts-pipelines-activities.md)합니다. 다음 섹션에서는 SAP ECC 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="sap-ecc-as-a-source"></a>SAP ECC를 원본으로

SAP ECC에서 데이터를 복사 하려면 합니다 `type` 속성에는 `source` 복사 작업의 섹션 `SapEccSource`합니다.

복사 작업에서 다음 속성이 지원 됩니다 `source` 섹션:

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 복사 활동의 속성 `source` 섹션으로 설정 되어 있어야 `SapEccSource`합니다. | 예 |
| `query` | 데이터를 필터링 할 OData 쿼리 옵션입니다. 예를 들어:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 커넥터는 결합 된 URL에서 데이터를 복사합니다.<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>자세한 내용은 [OData URL 구성 요소](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)를 참조하세요. | 아니요 |

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

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC에 대 한 데이터 형식 매핑

SAP ECC에서 데이터를 복사 하는, 다음 매핑이 Azure Data Factory 중간 데이터 형식으로 SAP ECC 데이터에 대 한 OData 데이터 형식에서 사용 됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

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
> 복합 데이터 형식은 현재 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업은 원본 및 싱크로 지원 되는 데이터 저장소 목록은 참조 하세요 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)합니다.
