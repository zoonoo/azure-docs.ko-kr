---
title: "Data Factory를 사용하여 Azure Table Storage 간에 데이터 복사 | Microsoft Docs"
description: "지원되는 원본 저장소에서 Azure Table Storage로 (또는) Table Storage에서 지원되는 싱크 저장소로 Data Factory를 사용하여 데이터를 복사하는 방법에 대해 알아봅니다."
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
ms.date: 11/07/2017
ms.author: jingwang
ms.openlocfilehash: d7da4c3c4aa902cf9ccb97ebd13b7d16940f2c32
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure 테이블 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-azure-table-connector.md)
> * [버전 2 - 미리 보기](connector-azure-table-storage.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure 테이블 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Azure Table Storage 커넥터](v1/data-factory-azure-table-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

모든 지원되는 원본 데이터 저장소에서 Azure 테이블로 데이터를 복사하거나 Azure 테이블에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure Table 커넥터는 **계정 키** 및 **서비스 SAS**(공유 액세스 서명) 인증을 모두 사용하여 데이터를 복사할 수 있습니다.

## <a name="get-started"></a>시작
.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 Azure Table Storage에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

### <a name="using-account-key"></a>계정 키 사용

계정 키를 사용하여 Azure Storage에 대한 전역 액세스로 데이터 팩터리를 제공하는 Azure Storage 연결된 서비스를 만들 수 있습니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureStorage** |예 |
| connectionString | connectionString 속성에 대한 Azure 저장소에 연결하는 데 필요한 정보를 지정합니다. 이 필드를 SecureString으로 표시합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

**예제:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>서비스 SAS 인증 사용

또한 SAS(공유 액세스 서명)를 사용하여 Azure Storage 연결된 서비스를 만들 수 있습니다. 이 서비스는 저장소의 모든/특정 리소스에 대해 제한된/시간 제한 액세스를 데이터 팩터리에 제공합니다.

SAS(공유 액세스 서명)는 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 액세스 키를 공유할 필요 없이 지정된 권한 집합을 사용하여 지정된 기간 동안 클라이언트에 저장소 계정의 개체에 대한 제한된 권한을 부여할 수 있습니다. SAS는 저장소 리소스에 인증된 액세스를 수행하는 데 필요한 모든 정보가 쿼리 매개 변수에 있는 URI입니다. SAS를 사용하여 저장소 리소스에 액세스하려는 클라이언트는 SAS를 적절한 생성자 또는 메서드에 전달하면 됩니다. SAS에 대한 자세한 내용은 [공유 액세스 서명: SAS 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory는 이제 **서비스 SAS**만 지원하며 계정 SAS는 지원하지 않습니다. 이러한 두 가지 형식과 생성 방법에 대한 자세한 내용은 [공유 액세스 서명 형식](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) 을 참조하세요. Azure Portal 또는 Storage 탐색기에서 생성할 수 있는 SAS URL는 지원되지 않는 계정 SAS입니다.
>

서비스 SAS 인증을 사용하기 위해 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureStorage** |예 |
| sasUri | BLOB, 컨테이너, 테이블 등의 Azure Storage 리소스에 공유 액세스 서명 URI를 지정합니다. 이 필드를 SecureString으로 표시합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

**예제:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**SAS URI**를 만들 때 다음 사항을 고려하세요.

- 데이터 팩터리에서 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방식에 따라 개체에 적절한 읽기/쓰기 **권한**을 설정합니다.
- **만료 시간**을 적절하게 설정합니다. Azure Storage 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않아야 합니다.
- 필요에 따라 올바른 테이블 수준에서 URI가 만들어져야 합니다.

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Azure Table 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Azure Table 간에 데이터를 복사하려면 데이터 집합의 type 속성을 **AzureTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성을 **AzureTable**로 설정해야 합니다. |예 |
| tableName |연결된 서비스가 참조하는 Azure 테이블 데이터베이스 인스턴스에서 테이블의 이름입니다. |예 |

**예제:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory에서의 스키마

Azure 테이블과 같은 스키마 없는 데이터 저장소의 경우 Data Factory 서비스는 다음 방법 중 하나로 스키마를 유추합니다.

1. 데이터 집합 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하는 경우 Data Factory 서비스는 이 구조를 스키마로 인식합니다. 이 경우 행에 열의 값이 포함되어 있지 않으면 null 값이 제공됩니다.
2. 데이터 집합 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하지 않는 경우 Data Factory는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 이 경우 첫 번째 행에 전체 스키마가 포함되어 있지 않으면 일부 열이 복사 작업의 결과에서 누락됩니다.

따라서 스키마 없는 데이터 원본에 대한 모범 사례는 **구조** 속성을 사용하여 데이터의 구조를 지정하는 것입니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Table 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-table-as-source"></a>Azure Table을 원본으로

Azure Table에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AzureTableSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **AzureTableSource**로 설정해야 합니다. |예 |
| AzureTableSourceQuery |사용자 지정 Azure Table 쿼리를 사용하여 데이터를 읽습니다. 다음 섹션의 예제를 참조하세요. |아니요 |
| azureTableSourceIgnoreTableNotFound |존재하지 않는 테이블의 예외를 받아들이는지를 나타냅니다.<br/>허용되는 값은 **True** 및 **False**(기본값)입니다. |아니요 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 예제

Azure 테이블 열이 문자열 형식인 경우:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Azure 테이블 열이 날짜/시간 형식인 경우:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>Azure Table을 싱크로

Azure Table에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AzureTableSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **AzureTableSink**로 설정해야 합니다. |예 |
| azureTableDefaultPartitionKeyValue |싱크에서 사용할 수 있는 기본 파티션 키 값입니다. |아니요 |
| azureTablePartitionKeyName |해당 값이 파티션 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 "AzureTableDefaultPartitionKeyValue"가 파티션 키로 사용됩니다. |아니요 |
| azureTableRowKeyName |해당 열 값이 행 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 각 행에 GUID를 사용합니다. |아니요 |
| azureTableInsertType |Azure 테이블에 데이터를 삽입하는 모드입니다. 이 속성은 출력 테이블에서 파티션 및 행 키가 일치하는 기존 행의 값을 바꿀지 또는 병합할지 제어합니다. <br/><br/>허용되는 값은 **merge**(기본값) 및 **replace**입니다. <br/><br> 이 설정은 테이블 수준이 아니라 행 수준에서 적용되며, 두 옵션 모두 출력 테이블에서 입력에 존재하지 않는 행을 삭제하지 않습니다. 이러한 설정(병합 및 바꾸기)이 작동하는 방법을 알아보려면 [엔터티 삽입 또는 병합](https://msdn.microsoft.com/library/azure/hh452241.aspx) 및 [엔터티 삽입 또는 바꾸기](https://msdn.microsoft.com/library/azure/hh452242.aspx)를 참조하세요. |아니요 |
| writeBatchSize |WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다.<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10000) |
| writeBatchTimeout |WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다.<br/>허용되는 값은 시간 범위입니다. 예: "00:20:00"(20분) |아니요(기본값: 90초 - 저장소 클라이언트의 기본 시간 제한) |

**예제:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

대상 열을 azureTablePartitionKeyName으로 사용할 수 있기 전에 "translator" 속성을 사용하여 원본 열을 대상 열에 매핑합니다.

다음 예제에서 원본 열 DivisionID은 대상 열 DivisionID에 매핑됩니다.

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID"는 파티션 키로 지정됩니다.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure 테이블에 대한 데이터 형식 매핑

Azure Table 간에 데이터를 복사하는 경우 Azure Table 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

Azure 테이블에서 데이터를 이동하는 경우 다음 [Azure Table service에서 정의된 매핑](https://msdn.microsoft.com/library/azure/dd179338.aspx)은 Azure 테이블 OData 형식에서 .NET 유형에 또는 그 반대로 사용됩니다.

| Azure Table 데이터 형식 | 데이터 팩터리 중간 데이터 형식 | 세부 정보 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |바이트 배열은 최대 64KB입니다. |
| Edm.Boolean |bool |부울 값입니다. |
| Edm.DateTime |DateTime |UTC(협정 세계시)로 표현되는 64비트 값입니다. 지원되는 DateTime 범위는 서기 1601년 1월 1일 자정 12시부터 시작합니다. (서기), UTC입니다. 범위는 9999년 12월 31일에 끝납니다. |
| Edm.Double |double |64비트 부동 소수점 값입니다. |
| Edm.Guid |Guid |전역적으로 고유한 128 비트 식별자입니다. |
| Edm.Int32 |Int32 |32비트 정수입니다. |
| Edm.Int64 |Int64 |64비트 정수입니다. |
| Edm.String |문자열 |UTF-16으로 인코딩된 값입니다. 문자열 값은 최대 64KB입니다. |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.