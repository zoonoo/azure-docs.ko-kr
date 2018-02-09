---
title: "Azure Data Factory(베타)를 사용하여 Google BigQuery에서 데이터 복사 | Microsoft Docs"
description: "데이터 팩터리 파이프라인의 복사 작업을 사용하여 Google BigQuery에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Azure Data Factory(베타)를 사용하여 Google BigQuery에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Google BigQuery에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급 상태인 데이터 팩터리 버전 1 서비스를 사용할 경우 [버전 1의 복사 작업](v1/data-factory-data-movement-activities.md)을 참조하세요.

> [!IMPORTANT]
> 이 커넥터는 현재 베타 버전입니다. 사용해 보고 피드백을 제공할 수 있습니다. 프로덕션 환경에서는 사용하지 마세요.

## <a name="supported-capabilities"></a>지원되는 기능

Google BigQuery에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

 데이터 팩터리는 연결을 허용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하기 위해 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

다음 섹션에서는 Google BigQuery 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Google BigQuery 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **GoogleBigQuery**로 설정해야 합니다. | 예 |
| project | 쿼리할 기본 BigQuery 프로젝트의 프로젝트 ID입니다.  | 예 |
| additionalProjects | 액세스할 공용 BigQuery 프로젝트의 쉼표로 구분된 프로젝트 ID의 목록입니다.  | 아니요 |
| requestGoogleDriveScope | Google Drive에 대한 액세스를 요청할지 여부입니다. Google Drive 액세스를 허용하면 BigQuery 데이터를 Google Drive의 데이터와 결합하는 페더레이션된 테이블을 지원할 수 있습니다. 기본값은 **false**입니다.  | 아니오 |
| authenticationType | 인증에 사용되는 OAuth 2.0 인증 메커니즘입니다. ServiceAuthentication은 자체 호스팅 통합 런타임에서만 사용할 수 있습니다. <br/>허용되는 값은 **ServiceAuthentication**과 **UserAuthentication**입니다. | 예 |
| refreshToken | UserAuthentication을 위한 BigQuery 액세스 권한을 부여하기 위해 Google에서 얻은 새로 고침 토큰입니다. 이 필드를 SecureString으로 표시하여 데이터 팩터리에서 안전하게 저장할 수 있습니다. Azure Key Vault에 암호를 저장하고 데이터 복사를 수행할 때 여기에서 복사 작업을 끌어올 수도 있습니다. 자세히 알아보려면 [Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. | 아니요 |
| email | ServiceAuthentication에 사용되는 서비스 계정 메일 ID입니다. 자체 호스팅 통합 런타임에서만 사용할 수 있습니다.  | 아니요 |
| keyFilePath | 서비스 계정 메일 주소를 인증하는 데 사용되는 .p12 키 파일의 전체 경로입니다. 자체 호스팅 통합 런타임에서만 사용할 수 있습니다.  | 아니오 |
| trustedCertPath | SSL을 통해 연결할 때 서버를 확인하는 데 사용되는 신뢰할 수 있는 CA 인증서를 포함하는 .pem 파일의 전체 경로입니다. 자체 호스팅 Integration Runtime에서 SSL을 사용할 때만 이 속성을 설정할 수 있습니다. 기본값은 통합 런타임과 함께 설치된 cacerts.pem 파일입니다.  | 아니요 |
| useSystemTrustStore | 시스템 신뢰 저장소 또는 지정된 .pem 파일의 CA 인증서를 사용할지 여부를 지정합니다. 기본값은 **false**입니다.  | 아니요 |

**예제:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Google BigQuery 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Google BigQuery에서 데이터를 복사하려면 데이터 집합의 type 속성을 **GoogleBigQueryObject**로 설정합니다. 이 형식의 데이터 집합에는 추가적인 형식별 속성이 없습니다.

**예제**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Google BigQuery 원본 형식에서 지원하는 속성의 목록을 제공합니다.

### <a name="googlebigquerysource-as-a-source-type"></a>원본 형식인 GoogleBigQuerySource

Google BigQuery에서 데이터를 복사하려면 복사 작업의 원본 형식을 **GoogleBigQuerySource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성은 **GoogleBigQuerySource**로 설정해야 합니다. | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예는 `"SELECT * FROM MyTable"`입니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
데이터 팩터리에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
