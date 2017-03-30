---
title: "데이터 팩터리를 사용하여 Oracle 간 데이터 이동 | Microsoft Docs"
description: "Azure 데이터 팩터리를 사용하여 온-프레미스 Oracle 데이터베이스 간 데이터를 이동하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: a27ec9e1ebfde3493e41c493b85c0dc7f0ada2a0
ms.lasthandoff: 03/18/2017


---
# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 온-프레미스 Oracle 간 데이터 이동
이 문서에서는 데이터 팩터리를 사용하여 Oracle과 다른 데이터 저장소 간에 데이터를 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

Data Factory는 데이터 관리 게이트웨이를 사용하여 온-프레미스 Oracle 원본에 연결하도록 지원합니다. [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하여 데이터 관리 게이트웨이에 대해 알아보고 [온-프레미스에서 클라우드로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하여 데이터를 이동하는 데이터 파이프라인의 게이트웨이 설정에 대 한 단계별 지침에 대해 알아보세요.

> [!NOTE]
> 게이트웨이는 Oracle이 Azure IaaS VM에 호스트되더라도 필요합니다. 게이트웨이를 데이터베이스에 연결할 수 있는 한 데이터 저장소와 동일한 IaaS VM 또는 다른 VM에 게이트웨이를 설치할 수 있습니다.
>

## <a name="supported-versions-and-installation"></a>지원되는 버전 및 설치
Oracle 커넥터는 다음 두 가지 버전의 드라이버를 지원합니다.

- **Oracle용 Microsoft 드라이버**는 버전 2.7부터 데이터 관리 게이트웨이와 번들로 묶여 제공됩니다. 이 드라이버가 **권장**됩니다. Oracle에 연결하기 위해 게이트웨이 외에 다른 어떤 것도 설치할 필요가 없으며 복사 성능이 향상될 수도 있습니다. Oracle Database 버전 10g 릴리스 2 이상을 사용할 수 있습니다.

    > [!NOTE]
    > 현재 Oracle용 Microsoft 드라이버는 Oracle에서 데이터를 복사하는 것만 지원하고 Oracle로 쓰는 것은 지원하지 않습니다. 데이터 관리 게이트웨이 진단 탭의 연결 테스트 기능은 이 드라이버를 지원하지 않습니다. 또는 복사 마법사를 사용하여 연결의 유효성을 검사할 수도 있습니다.
    >

- **Oracle Data Provider for .NET:** 데이터 관리 게이트웨이 버전 2.7 이상은 이 구성 요소를 포함하기 때문에 별도로 설치할 필요가 없습니다. 2.7 미만의 게이트웨이 버전을 사용하는 경우 [여기](https://www.microsoft.com/download/details.aspx?id=39717)에서 최신 버전의 게이트웨이를 설치하는 것이 좋습니다. 게이트웨이 버전은 데이터 관리 게이트웨이 구성 관리자의 도움말 페이지에서 찾을 수 있습니다("데이터 관리 게이트웨이" 검색).

## <a name="copy-data-wizard"></a>데이터 복사 마법사
Oracle 데이터베이스의 데이터를 지원되는 싱크 데이터 저장소 중 하나에 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md) 를 참조하세요.

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 Oracle 데이터베이스와 Azure Blob 저장소 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 에 설명한 싱크로 데이터를 복사할 수 있습니다.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>샘플: Oracle에서 Azure Blob로 데이터 복사
이 샘플은 온-프레미스 Oracle 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 **여기** 에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.  

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1. [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)형식의 연결된 서비스
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)형식의 연결된 서비스
3. [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
5. [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties)를 소스로, [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 싱크로 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 온-프레미스 Oracle 데이터베이스의 테이블에서 blob에 매시간 데이터를 복사합니다. 샘플에 사용되는 다양한 속성에 대한 자세한 내용은 샘플 다음에 나오는 섹션의 문서를 참조하세요.

**Oracle 연결된 서비스:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob 저장소 연결된 서비스:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Oracle 입력 데이터 집합:**

샘플은 Oracle에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn"이라는 열이 포함되어 있다고 가정합니다.

"external": "true"로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {     
            "externalData": {        
                "retryInterval": "00:01:00",    
                "retryTimeout": "00:10:00",       
                "maximumRetry": 3       
            }     
        }
    }
}
```

**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **OracleSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다.  **oracleReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>샘플: Azure Blob에서 Oracle로 데이터 복사
이 샘플은 Azure Blob 저장소에서 온-프레미스 Oracle 데이터베이스로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 **여기** 에 설명한 원본에서 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.  

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1. [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)형식의 연결된 서비스
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)형식의 연결된 서비스
3. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md).
5. [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 소스로, [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties)를 싱크로 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Blob에서 온-프레미스 Oracle 데이터베이스의 테이블로 매시간 데이터를 복사합니다. 샘플에 사용되는 다양한 속성에 대한 자세한 내용은 샘플 다음에 나오는 섹션의 문서를 참조하세요.

**Oracle 연결된 서비스:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob 저장소 연결된 서비스:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Azure Blob 입력 데이터 집합**

데이터는 매시간 새 blob에 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 팩터리 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Oracle 출력 데이터 집합:**

샘플은 Oracle에 "MyTable" 테이블을 만들었다고 가정합니다. Blob CSV 파일을 포함하려 하면 같은 수의 열을 사용하여 Oracle에 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource**로 설정되고 **싱크** 형식은 **OracleSink**로 설정됩니다.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="oracle-linked-service-properties"></a>Oracle 연결된 서비스 속성
다음 표에서는 Oracle 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |type 속성은 **OnPremisesOracle** |예 |
| driverType | Oracle Database로 데이터를 복사하거나 Oracle Database에서 데이터를 복사하는 데 사용할 드라이버를 지정합니다. 허용되는 값은 **Microsoft** 또는 **ODP**(기본값)입니다. 드라이버 세부 정보에 대해서는 [지원되는 버전 및 설치](#supported-versions-and-installation) 섹션을 참조하세요. | 아니요 |
| connectionString | connectionString 속성에 대한 Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. | 예 |
| gatewayName | 온-프레미스 Oracle 서버에 연결하는 데 사용할 게이트웨이 이름입니다. |예 |

온-프레미스 Oracle 데이터 원본의 자격 증명을 설정하는 방법에 대한 자세한 내용은 [데이터 관리 게이트웨이를 사용하여 온-프레미스 원본과 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

**예제: Microsoft 드라이버 사용**
```JSON
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**예제: ODP 드라이버 사용**

허용되는 추가 형식에 대해서는 [이 사이트](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/)를 참조하세요.
```JSON
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="oracle-dataset-type-properties"></a>Oracle 데이터 집합 형식 속성
데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Oracle, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. OracleTable 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Oracle 데이터베이스에 있는 테이블의 이름입니다. |아니요(**OracleSource**의 **oracleReaderQuery**가 지정된 경우) |

## <a name="oracle-copy-activity-type-properties"></a>Oracle 복사 작업 형식 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

> [!NOTE]
> 복사 작업은 하나의 입력을 가지고 하나의 출력을 생성합니다.
>
>

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

### <a name="oraclesource"></a>oracleReaderQuery
원본이 **OracleSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| oracleReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: select *from MyTable <br/><br/>지정하지 않는 경우 실행되는 SQL 문: select* from MyTable |아니요(**데이터 집합**의 **tableName**이 지정된 경우) |

### <a name="oraclesink"></a>파이프라인
**OracleSink** 는 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예: “00:30:00”(30분). |아니요 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 |정수(행 수) |아니요(기본값: 100) |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. |쿼리 문입니다. |아니요 |
| sliceIdentifierColumnName |자동 생성된 조각 식별자를 입력할 복사 활동의 열 이름을 지정합니다. 이 식별자는 복사 활동을 다시 실행할 때 특정 조각의 데이터를 정리하는 데 사용됩니다. |이진(32) 데이터 형식이 있는 열의 열 이름입니다. |아니요 |

## <a name="troubleshooting-tips"></a>문제 해결 팁
### <a name="problem-1-net-framework-data-provider"></a>문제 1: .NET Framework Data Provider

다음 **오류 메시지**가 표시됩니다.

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**가능한 원인:**

1. .NET Framework Data Provider for Oracle이 설치되지 않았습니다.
2. .NET Framework Data Provider for Oracle이 .NET Framework 2.0에 설치되었고 .NET Framework 4.0 폴더에서 찾을 수 없습니다.

**해결 방법**

1. .NET Provider for Oracle을 설치하지 않았으면 [해당 항목을 설치](http://www.oracle.com/technetwork/topics/dotnet/downloads/) 한 후 시나리오를 다시 시도합니다.
2. Provider를 설치한 후에도 오류 메시지가 표시되면 다음 단계를 수행합니다.
   1. <system disk>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config 폴더에서 .NET 2.0 machine config 파일을 엽니다.
   2. **Oracle Data Provider for .NET**을 검색하면 **system.data** -> **DbProviderFactories**에서 “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET과 같은 항목을 찾을 수 있습니다." type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”이라는 제목의 방법 가이드를 참조하세요.
3. 이 항목을 v4.0 폴더 즉, <system disk>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config의 machine.config 파일에 복사하고 버전을 4.xxx.x.x으로 변경합니다.
4. `gacutil /i [provider path]`를 실행하여 전역 어셈블리 캐시(GAC)에 “<ODP.NET 설치된 경로>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll”을 설치합니다.## 문제 해결 팁

### <a name="problem-2-datetime-formatting"></a>문제 2: 날짜/시간 서식

다음 **오류 메시지**가 표시됩니다.

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**해결 방법**

다음 샘플에서처럼 Oracle Database에 날짜가 구성되는 방법에 따라 복사 작업에서 쿼리 문자열을 조정해야 할 수 있습니다(To_date 함수 사용).

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Oracle에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Oracle에서 데이터를 이동하는 경우 Oracle 데이터 형식에서 .NET 형식에 그리고 그 반대로 다음 매핑을 사용합니다.

| Oracle 데이터 형식 | .NET Framework 데이터 형식 |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |문자열 |
| CLOB |문자열 |
| DATE |DateTime |
| FLOAT |10진수 |
| INTEGER |10진수 |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |문자열 |
| LONG RAW |Byte[] |
| NCHAR |문자열 |
| NCLOB |문자열 |
| NUMBER |10진수 |
| NVARCHAR2 |문자열 |
| RAW |Byte[] |
| ROWID |문자열 |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |문자열 |
| XML |String |

> [!NOTE]
> 데이터 형식 **INTERVAL YEAR TO MONTH** 및 **INTERVAL DAY TO SECOND**는 Microsoft 드라이버를 사용할 때 지원되지 않습니다.
>

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

