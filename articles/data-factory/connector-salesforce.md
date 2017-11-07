---
title: "Azure Data Factory를 사용하여 Salesforce에서 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory 파이프라인의 복사 작업을 사용하여 Salesforce에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: c819b3e3e715427632e793ce77d31554914d248e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Salesforce에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-salesforce-connector.md)
> * [버전 2 - 미리 보기](connector-salesforce.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Salesforce 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Salesforce 커넥터](v1/data-factory-salesforce-connector.md)를 참조하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

Salesforce 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, 이 Salesforce 커넥터는 Salesforce **Developer Edition, Professional Edition, Enterprise Edition 또는 Unlimited Edition**을 지원합니다. 그리고 Salesforce **프로덕션, 샌드박스 및 사용자 지정 도메인**에서 데이터 복사를 지원합니다.

## <a name="prerequisites"></a>필수 조건

* Salesforce에서 API 권한을 사용하도록 설정해야 합니다. [권한 집합에 따라 Salesforce에서 API 액세스를 사용하도록 설정하는 방법](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce 요청 제한

Salesforce에는 총 API 요청 수와 동시 API 요청 수에 대한 제한이 있습니다. 다음 사항에 유의하세요.

- 동시 요청 수가 이 제한을 초과하면 제한이 발생하며 임의 오류가 표시됩니다.
- 총 요청 수가 이 제한을 초과하면 Salesforce 계정은 24시간 동안 차단됩니다.

두 시나리오 모두에서 "REQUEST_LIMIT_EXCEEDED" 오류가 나타날 수도 있습니다. 자세한 내용은 [Salesforce 개발자 제한](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) 문서의 “API 요청 제한” 섹션을 참조하세요.

## <a name="getting-started"></a>시작
.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 Salesforce 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Salesforce 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **Salesforce**로 설정되어야 합니다. |예 |
| environmentUrl | Salesforce 인스턴스의 URL을 지정합니다. <br><br> - 기본값은 `"https://login.salesforce.com"`입니다. <br> - 샌드박스에서 데이터를 복사하려면 `"https://test.salesforce.com"`을 지정합니다. <br> - 사용자 지정 도메인에서 데이터를 복사하려면 예를 들어 `"https://[domain].my.salesforce.com"`을 지정합니다. |아니요 |
| username |사용자 계정의 사용자 이름을 지정합니다. |예 |
| password |사용자 계정으로 password를 지정합니다. |예 |
| securityToken |사용자 계정에 대한 보안 토큰을 지정합니다. 보안 토큰을 재설정하거나 가져오는 방법에 대한 자세한 내용은 [보안 토큰 가져오기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) 를 참조하세요. 일반적인 보안 토큰에 대해 자세히 알아보려면 [보안 및 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)를 참조하세요. |예 |

**예제:**

```json
{
    "properties": {
        "type": "Salesforce",
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
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Salesforce 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Salesforce에서 데이터를 복사하려면 데이터 집합의 type 속성을 **RelationalTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성을 **RelationalTable**로 설정해야 합니다. | 예 |
| tableName | Salesforce 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 API 이름에 "__c" 부분이 필요합니다.

![Data Factory - Salesforce 연결 - API 이름](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**예제:**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Salesforce 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="salesforce-as-source"></a>Salesforce를 원본으로

Salesforce에서 데이터를 복사하려면 복사 작업의 원본 형식을 **RelationalSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **RelationalSource**로 설정해야 합니다. | 예 |
| 쿼리 |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. SQL-92 쿼리 또는 [SOQL(Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 쿼리를 사용할 수 있습니다. 예: `select * from MyTable__c` | 아니요(데이터 집합의 "tableName"이 지정된 경우) |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 API 이름에 "__c" 부분이 필요합니다.

![Data Factory - Salesforce 연결 - API 이름](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>쿼리 팁

### <a name="retrieving-data-from-salesforce-report"></a>Salesforce 보고서에서 데이터 검색

`{call "<report name>"}. Example: `"query": "{call \"TestReport\"}"`와 같이 쿼리를 지정하여 Salesforce 보고서에서 데이터를 검색할 수 있습니다.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Salesforce 휴지통에서 삭제된 레코드 검색

임시 삭제된 쿼리를 Salesforce 휴지통에서 쿼리하려면 쿼리에 **"IsDeleted = 1"**을 지정하면 됩니다. 예를 들면 다음과 같습니다.

* 삭제된 레코드만 쿼리하려면 "select * from MyTable__c **where IsDeleted= 1**"을 지정합니다.
* 기존 레코드와 삭제된 레코드를 포함하여 모든 레코드를 쿼리하려면 "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"을 지정합니다.

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>DateTime 열에서 where 절을 사용하여 데이터 검색

SOQL 또는 SQL 쿼리를 지정할 때 DateTime 형식 차이에 주의해야 합니다. 예:

* **SOQL 샘플**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **SQL 샘플**: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="data-type-mapping-for-salesforce"></a>Salesforce에 대한 데이터 형식 매핑

Salesforce에서 데이터를 복사할 경우 Salesforce 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Salesforce 데이터 형식 | 데이터 팩터리 중간 데이터 형식 |
|:--- |:--- |
| 자동 번호 |문자열 |
| 확인란 |Boolean |
| 통화 |Double |
| Date |DateTime |
| 날짜/시간 |DateTime |
| Email |문자열 |
| Id |문자열 |
| 관계 조회 |문자열 |
| 다중 선택 선택 목록 |문자열 |
| Number |Double |
| 백분율 |Double |
| Phone |문자열 |
| 선택 목록 |문자열 |
| 텍스트 |문자열 |
| 텍스트 영역 |문자열 |
| 텍스트 영역(Long) |문자열 |
| 텍스트 영역(Rich) |문자열 |
| 텍스트(암호화됨) |문자열 |
| URL |문자열 |


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.