---
title: 세일즈포스 서비스 클라우드에서 데이터 복사
description: 데이터 팩터리 파이프라인에서 복사 활동을 사용하여 Salesforce Service Cloud에서 지원되는 싱크 데이터 저장소 또는 지원되는 원본 데이터 저장소에서 Salesforce Service Cloud로 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ec2aa5b1492534908adb55544623110242717609
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416667"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Salesforce 서비스 클라우드에서 데이터를 복사합니다.
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure 데이터 팩터리에서 복사 활동을 사용하여 Salesforce 서비스 클라우드에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Salesforce 서비스 클라우드 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

Salesforce Service Cloud에서 지원되는 싱크 데이터 저장소에 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Salesforce Service Cloud로 복사할 수도 있습니다. 복사 활동에 의해 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하십시오.

특히 이 Salesforce 서비스 클라우드 커넥터는 다음을 지원합니다.

- Salesforce 개발자, Professional, Enterprise 또는 Unlimited Edition.
- Salesforce 프로덕션, 샌드박스 및 사용자 지정 도메인 간에 데이터 복사

Salesforce 커넥터는 Salesforce REST/벌크 API 위에 구축되었습니다. 기본적으로 커넥터는 [v45를](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) 사용하여 Salesforce의 데이터를 복사하고 [v40을](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) 사용하여 Salesforce에 데이터를 복사합니다. 연결된 서비스에서 [ `apiVersion` 속성을](#linked-service-properties) 통해 데이터를 읽고/쓰는 데 사용되는 API 버전을 명시적으로 설정할 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Salesforce에서 API 권한을 사용하도록 설정해야 합니다. 자세한 내용은 [권한 집합에 따라 Salesforce에서 API 액세스를 사용하도록 설정](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)을 참조하세요.

## <a name="salesforce-request-limits"></a>Salesforce 요청 제한

Salesforce에는 총 API 요청 수와 동시 API 요청 수에 대한 제한이 있습니다. 다음 사항에 유의하세요.

- 동시 요청 수가 이 제한을 초과하면 제한이 발생하며 임의 오류가 표시됩니다.
- 총 요청 수가 이 제한을 초과하면 Salesforce 계정은 24시간 동안 차단됩니다.

두 시나리오 모두에서 "REQUEST_LIMIT_EXCEEDED" 오류 메시지가 나타날 수 있습니다. 자세한 내용은 [Salesforce 개발자 제한](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) 문서의 "API 요청 제한" 섹션을 참조하세요.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Salesforce Service Cloud 커넥터와 관련된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대한 세부 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Salesforce 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **SalesforceServiceCloud로**설정해야 합니다. |예 |
| environmentUrl | Salesforce 서비스 클라우드 인스턴스의 URL을 지정합니다. <br> - 기본값은 `"https://login.salesforce.com"`입니다. <br> - 샌드박스에서 데이터를 복사하려면 `"https://test.salesforce.com"`을 지정합니다. <br> - 사용자 지정 도메인에서 데이터를 복사하려면 예를 들어 `"https://[domain].my.salesforce.com"`을 지정합니다. |예 |
| 사용자 이름 |사용자 계정의 사용자 이름을 지정합니다. |예 |
| password |사용자 계정으로 password를 지정합니다.<br/><br/>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| securityToken |사용자 계정에 대한 보안 토큰을 지정합니다. <br/><br/>일반적인 보안 토큰에 대해 자세히 알아보려면 [보안 및 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)를 참조하세요. Salesforce의 [신뢰할 수 있는 IP 주소 목록에](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) 통합 런타임의 IP를 추가하는 경우에만 보안 토큰을 건너뛸 수 있습니다. Azure IR을 사용하는 경우 [Azure 통합 런타임 IP 주소를](azure-integration-runtime-ip-addresses.md)참조하십시오.<br/><br/>보안 토큰을 얻고 재설정하는 방법에 대한 지침은 [보안 토큰 받기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)를 참조하십시오. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| apiVersion | 사용할 Salesforce REST/벌크 API 버전을 지정합니다(예: `48.0`. 기본적으로 커넥터는 [v45를](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) 사용하여 Salesforce의 데이터를 복사하고 [v40을](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) 사용하여 Salesforce에 데이터를 복사합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 원본에 연결된 서비스에 통합 런타임이 없는 경우 원본은 아니요, 싱크는 예입니다. |

>[!IMPORTANT]
>Salesforce Service Cloud에 데이터를 복사하는 경우 기본 Azure 통합 런타임을 사용하여 복사본을 실행할 수 없습니다. 즉, 원본 연결 서비스에 지정된 통합 런타임이 없는 경우 Salesforce Service Cloud 인스턴스 근처의 위치로 [Azure 통합 런타임을](create-azure-integration-runtime.md#create-azure-ir) 명시적으로 만듭니다. 다음 예제와 같이 Salesforce 서비스 클라우드 연결 서비스를 연결합니다.

**예: 데이터 팩터리에 자격 증명 저장**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예: Key Vault에 자격 증명 저장**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Salesforce Service 클라우드 데이터 집합에서 지원하는 속성 목록을 제공합니다.

Salesforce Service Cloud에서 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **SalesforceServiceCloudObject**로 설정해야 합니다.  | 예 |
| objectApiName | 데이터를 검색할 Salesforce 개체 이름입니다. | 원본에는 아니요이고 싱크에는 예입니다 |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 **API 이름**에 "__c" 부분이 필요합니다.

![데이터 팩터리 Salesforce 연결 API 이름](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**예제:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **RelationalTable**로 설정해야 합니다. | 예 |
| tableName | Salesforce 서비스 클라우드의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Salesforce Service 클라우드 소스 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="salesforce-service-cloud-as-a-source-type"></a>소스 유형으로서의 세일즈포스 서비스 클라우드

Salesforce Service Cloud에서 데이터를 복사하려면 복사 활동 **원본** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 원본의 형식 속성은 **SalesforceServiceCloudSource**로 설정해야 합니다. | 예 |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. [SOQL(Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 쿼리 또는 SQL-92 쿼리를 사용할 수 있습니다. [쿼리 팁](#query-tips) 섹션에서 더 많은 팁을 참조하세요. 쿼리를 지정하지 않으면 데이터 집합의 "objectApiName"에 지정된 Salesforce Service Cloud 개체의 모든 데이터가 검색됩니다. | 아니요(데이터 세트의 “objectApiName”이 지정된 경우) |
| readBehavior | 기존 레코드를 쿼리할지, 아니면 삭제된 항목을 포함하여 모든 레코드를 쿼리할지 여부를 나타냅니다. 지정하지 않으면 기본 동작은 전자입니다. <br>허용되는 값: **query**(기본값), **queryAll**입니다.  | 예 |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 **API 이름**에 "__c" 부분이 필요합니다.

![데이터 팩터리 Salesforce 연결 API 이름 목록](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>싱크 유형으로 세일즈포스 서비스 클라우드

데이터를 Salesforce Service Cloud에 복사하려면 복사 활동 **싱크** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 싱크의 형식 속성을 **SalesforceServiceCloud싱크로**설정해야 합니다. | 예 |
| writeBehavior | 작업의 쓰기 동작입니다.<br/>허용되는 값은 **Insert** 및 **Upsert**입니다. | 아니요(기본값: 삽입) |
| externalIdFieldName | Upsert 작업의 외부 ID 필드 이름입니다. 지정된 필드는 Salesforce 서비스 클라우드 개체에서 "외부 ID 필드"로 정의되어야 합니다. 해당하는 입력 데이터에서 NULL 값을 가질 수 없습니다. | "Upsert"에서 예 |
| writeBatchSize | 각 일괄 처리에서 Salesforce 서비스 클라우드에 기록된 데이터의 행 수입니다. | 아니요(기본값: 5,000) |
| ignoreNullValues | 쓰기 작업 중에 입력 데이터에서 NULL 값을 무시할지 여부를 나타냅니다.<br/>허용되는 값은 **true** 및 **false**입니다.<br>- **True**: Upsert나 업데이트 작업을 수행할 때 대상 개체의 데이터를 변경하지 않고 유지합니다. 삽입 작업을 수행할 때 정의된 기본 값을 삽입합니다.<br/>- **False**: Upsert나 업데이트 작업을 수행할 때 대상 개체의 데이터를 NULL로 업데이트합니다. 삽입 작업을 수행할 때 NULL 값을 삽입합니다. | 아니요(기본값: false) |

**예제:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>쿼리 팁

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Salesforce 서비스 클라우드 보고서에서 데이터 검색

쿼리를 로 지정하여 Salesforce Service Cloud 보고서에서 `{call "<report name>"}`데이터를 검색할 수 있습니다. 예제는 `"query": "{call \"TestReport\"}"`입니다.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Salesforce 서비스 클라우드 휴지통에서 삭제된 레코드 검색

Salesforce Service 클라우드 휴지통에서 삭제된 소프트 레코드를 `queryAll`쿼리하려면 을 로 지정할 `readBehavior` 수 있습니다. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>SOQL과 SQL 쿼리 구문의 차이점

Salesforce Service Cloud에서 데이터를 복사할 때 SOQL 쿼리 또는 SQL 쿼리를 사용할 수 있습니다. 이 두 가지 쿼리는 서로 다른 구문과 기능을 지원하므로 혼용하지 마세요. Salesforce Service Cloud에서 기본적으로 지원하는 SOQL 쿼리를 사용하는 것이 좋습니다. 다음 표에는 주요 차이점이 나와 있습니다.

| 구문 | SOQL 모드 | SQL 모드 |
|:--- |:--- |:--- |
| 열 선택 | 쿼리에서 복사할 필드를 등록해야 합니다(예:`SELECT field1, filed2 FROM objectname` | 열 선택 외에도 `SELECT *`이 지원됩니다. |
| 따옴표 | 필드/개체 이름은 따옴표로 묶을 수 없습니다. | 필드/개체 이름은 따옴표로 묶을 수 있습니다. 예: `SELECT "id" FROM "Account"` |
| 날짜/시간 형식 |  자세한 내용은 [여기](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) 및 다음 섹션의 샘플을 참조하세요. | 자세한 내용은 [여기](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) 및 다음 섹션의 샘플을 참조하세요. |
| 부울 값 | `False` 및 `True`로 표시됩니다. 예: `SELECT … WHERE IsDeleted=True` | 0 또는 1로 표시됩니다. 예: `SELECT … WHERE IsDeleted=1` |
| 열 이름 바꾸기 | 지원되지 않습니다. | 지원됨. 예: `SELECT a AS b FROM …` |
| 관계 | 지원됨. 예: `Account_vod__r.nvs_Country__c` | 지원되지 않습니다. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>DateTime 열에서 Where 문을 사용하여 데이터를 검색합니다.

SOQL 또는 SQL 쿼리를 지정할 때 DateTime 형식 차이에 주의해야 합니다. 다음은 그 예입니다.

* **SOQL 샘플**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL 샘플**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>MALFORMED_QUERY 오류:잘린

"MALFORMED_QUERY: 잘린"의 오류가 발생하면 일반적으로 데이터에 JunctionIdList 형식 열이 있기 때문에 Salesforce는 많은 수의 행으로 이러한 데이터를 지원하는 데 제한이 있습니다. 완화하려면 JunctionIdList 열을 제외하거나 복사할 행 수를 제한합니다(여러 복사 활동 실행으로 분할할 수 있음).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>세일즈포스 서비스 클라우드를 위한 데이터 유형 매핑

Salesforce Service Cloud에서 데이터를 복사하면 Salesforce Service 클라우드 데이터 형식에서 데이터 팩터리 중간 데이터 유형으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| 세일즈포스 서비스 클라우드 데이터 유형 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| 자동 번호 |String |
| 확인란 |부울 |
| 통화 |Decimal |
| Date |DateTime |
| 날짜/시간 |DateTime |
| Email |String |
| Id |String |
| 관계 조회 |String |
| 다중 선택 선택 목록 |String |
| Number |Decimal |
| 백분율 |Decimal |
| Phone |String |
| 선택 목록 |String |
| 텍스트 |String |
| 텍스트 영역 |String |
| 텍스트 영역(Long) |String |
| 텍스트 영역(Rich) |String |
| 텍스트(암호화됨) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.


## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
