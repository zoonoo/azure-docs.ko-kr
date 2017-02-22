---
title: "Azure Data Lake 저장소 간 데이터 이동 | Microsoft Docs"
description: "Azure 데이터 팩터리를 사용하여 Azure 데이터 레이크 저장소 간 데이터를 이동하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b2d1a740782a20a7c6b7b8cec8335a41f16231f5
ms.openlocfilehash: 5a6a14e5fc8f6915b34f9667c4294a46c8591633


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Azure 데이터 레이크 저장소 간 데이터 이동
이 문서에서는 Azure Data Factory에서 복사 작업을 사용하여 Azure Data Lake Store 및 다른 데이터 저장소 간에 데이터를 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 작성합니다.

> [!NOTE]
> Azure Data Lake Store 간에 데이터를 이동하려면 복사 작업을 사용하여 파이프라인을 만들기 전에 Azure Data Lake Store 계정을 만듭니다. Azure Data Lake Store에 대해 알아보려면 [Azure Data Lake Store 시작](../data-lake-store/data-lake-store-get-started-portal.md)을 참조하세요.
>

## <a name="supported-authentication-types"></a>지원되는 인증 형식
Azure Data Lake Store 커넥터는 **서비스 주체** 인증 및 **사용자 자격 증명** 인증을 지원합니다. 사용자 자격 증명 인증에서의 토큰 만료 문제를 방지하려면 예약된 데이터 복사에 대해 서비스 사용자 인증을 사용하는 것이 좋습니다. 구성 세부 정보와 함께 [Azure Data Lake Store 연결된 서비스 속성](#azure-data-lake-store-linked-service-properties) 섹션을 참조하세요.

## <a name="copy-data-wizard"></a>데이터 복사 마법사
Azure Data Lake Store 간에 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure Data Lake Store 및 Azure Blob 저장소 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 임의의 원본에서 지원되는 싱크로 **직접** 데이터를 복사할 수 있습니다. 자세한 내용은 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md)에서 "지원되는 데이터 저장소 및 형식" 섹션을 참조하세요.  

## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>샘플: Azure Blob에서 Azure 데이터 레이크 저장소로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스
2. [AzureDataLakeStore](#azure-data-lake-linked-service-properties) 형식의 연결된 서비스입니다.
3. [AzureBlob](#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
5. [BlobSource](#azure-blob-copy-activity-type-properties) 및 [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 샘플은 Azure Blob 저장소에서 Azure Data Lake Store로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure 저장소 연결된 서비스:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Azure 데이터 레이크 연결된 서비스:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> 구성 세부 정보와 함께 [Azure Data Lake Store 연결된 서비스 속성](#azure-data-lake-store-linked-service-properties) 섹션의 단계를 참조하세요.
>

**Azure Blob 입력 데이터 집합:**

데이터는 매시간 새 blob에 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로에는 시작 시간의 년/월/일 부분이 사용되고 파일 이름에는 시작 시간의 시간 부분이 사용됩니다. "external": "true"를 설정하면 테이블이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```JSON
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
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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

**Azure 데이터 레이크 출력 데이터 집합:**

이 샘플은 Azure 데이터 레이크 저장소로 데이터를 복사합니다. 새 데이터가 데이터 레이크 저장소로 매시간 복사됩니다.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **BlobSource**로 설정되고 **sink** 형식은 **AzureDataLakeStoreSink**로 설정됩니다.

```JSON
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
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

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>샘플: Azure 데이터 레이크 저장소에서 Azure Blob로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureDataLakeStore](#azure-data-lake-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스
3. [AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [AzureBlob](#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
5. [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) 및 [BlobSink](#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 샘플은 Azure Data Lake Store에서 Azure Blob로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure 데이터 레이크 저장소 연결된 서비스:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> 구성 세부 정보와 함께 [Azure Data Lake Store 연결된 서비스 속성](#azure-data-lake-store-linked-service-properties) 섹션의 단계를 참조하세요.
>

**Azure 저장소 연결된 서비스:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure 데이터 레이크 입력 데이터 집합:**

**"external": true**를 설정하면 테이블이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```JSON
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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
**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```JSON
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

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **AzureDataLakeStoreSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

## <a name="azure-data-lake-store-linked-service-properties"></a>Azure 데이터 레이크 저장소 연결된 서비스 속성
다음 표에서는 Azure Data Lake Store 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공하며, 사용자는 **서비스 주체** 및 **사용자 자격 증명** 인증 중에서 선택할 수 있습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureDataLakeStore** | 예 |
| dataLakeStoreUri | Azure 데이터 레이크 저장소 계정에 대한 정보를 지정합니다. **https://[accountname].azuredatalakestore.net/webhdfs/v1** 또는 **adl://[accountname].azuredatalakestore.net/** 형식입니다. | 예 |
| subscriptionId | Data Lake Store가 속하는 Azure 구독 ID입니다. | 싱크에 필요 |
| resourceGroupName | Data Lake Store가 속하는 Azure 리소스 그룹 이름입니다. | 싱크에 필요 |

### <a name="using-service-principal-authentication-recommended"></a>서비스 주체 인증 사용(권장)
서비스 주체 인증을 사용하려면 AAD(Azure Active Directory)에서 응용 프로그램 엔터티를 등록한 후 Data Lake Store에서 액세스 권한을 부여해야 합니다. 그런 다음 해당 응용 프로그램 ID, 응용 프로그램 키 및 테넌트 정보로 Azure Data Factory에서 아래 속성을 지정하여 Data Lake Store로 데이터를 복사하거나 Data Lake Store에서 다른 위치로 복사할 수 있습니다. 필요한 정보를 설정하고 검색하는 방법은 [서비스 간 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

> [!IMPORTANT]
> 복사 마법사를 사용하는 경우 폴더들 사이에서 성공적으로 탐색하도록 하려면 서비스 주체에게 ADLS 루트("/")에 대해 최소한의 읽기 권한 또는 ADLS 계정에 대한 읽기 역할을 부여하세요. 그렇지 않으면 "The credentials provided are invalid(제공된 자격 증명이 유효하지 않습니다)" 오류가 표시될 수 있습니다.
>
> AAD에서 서비스 주체를 새로 만들면/업데이트하면 실제로 적용하는 데 몇 분이 소요될 수 있습니다. 먼저 서비스 주체와 ADLS ACL 구성을 다시 확인하고, 여전히 "The credentials provided are invalid(제공된 자격 증명이 유효하지 않습니다)"라는 오류가 나타나는 경우 잠시 기다린 후 다시 시도하세요.
>

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | 응용 프로그램의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 응용 프로그램의 키를 지정합니다. | 예 |
| tenant | 응용 프로그램이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 가져가서 검색할 수 있습니다. | 예 |

**예제: 서비스 주체 인증 사용**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>사용자 자격 증명 인증 사용
또는 아래 속성을 지정하여 사용자 자격 증명 인증을 통해 사용하여 Data Lake Store로 데이터를 복사하거나 Data Lake Store에서 다른 위치로 복사할 수 있습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| authorization | **Data Factory 편집기**에서 **권한 부여** 단추를 클릭하고 자격 증명을 입력합니다. 그러면 자동 생성된 authorization URL이 이 속성에 할당됩니다. | 예 |
| sessionid | OAuth 권한 부여 세션에서 가져온 OAuth 세션 ID입니다. 각 세션 ID는 고유하고 한 번만 사용될 수 있으며  이 설정은 Data Factory 편집기를 사용하는 경우 자동으로 생성됩니다. | 예 |

**예제: 사용자 자격 증명 인증 사용**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>토큰 만료
**권한 부여** 단추를 사용하여 생성한 권한 부여 코드는 잠시 후 만료됩니다. 다양한 유형의 사용자 계정에 대한 만료 시간은 다음 표를 참조하세요. 인증 **토큰이 만료**되는 경우 다음과 같은 오류 메시지가 표시될 수 있습니다. "자격 증명 작업 오류: invalid_grant - AADSTS70002: 자격 증명의 유효성 검사 오류 "자격 증명 작업 오류: invalid_grant - AADSTS70002: 자격 증명의 유효성 검사 오류 AADSTS70008: 제공된 액세스 권한 부여가 만료되었거나 해지됩니다. 추적 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 상관관계 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 타임스탬프: 2015-12-15 21-09-31Z".

| 사용자 유형 | 다음 시간 후에 만료 |
|:--- |:--- |
| Azure Active Directory (@hotmail.com, @live.com, 등에서 관리되지 않는 사용자 계정 |12시간 |
| AAD(Azure Active Directory)에서 관리되는 사용자 계정 |마지막 조각이 실행된 후&14;일 <br/><br/>OAuth 기반 연결된 서비스를 기반으로 하는 조각이 14일마다 한 번 이상 실행된 경우 90일 |

토큰 만료 시간 전에 암호를 변경하면, 토큰이 즉시 만료되고 이 섹션에 나와 있는 오류가 표시됩니다.

이 오류를 방지/해결하려면 **토큰이 만료**되면 **권한 부여** 단추를 사용하여 다시 인증하고 연결된 서비스를 다시 배포합니다. 다음 섹션의 코드를 사용하여 프로그래밍 방식으로 **sessionId** 및 **권한 부여** 속성의 값을 생성할 수도 있습니다.

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>프로그래밍 방식으로 sessionId와 권한 부여 값을 생성하려면

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
코드에 사용되는 Data Factory 클래스에 대한 세부 정보는 [AzureDataLakeStoreLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) 및 [AuthorizationSessionGetResponse 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) 항목을 참조하세요. 코드에 사용된 WindowsFormsWebAuthenticationDialog 클래스에 대한 **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll**의 **2.9.10826.1824** 버전에 대한 참조를 추가합니다.

## <a name="azure-data-lake-dataset-type-properties"></a>Azure 데이터 레이크 데이터 집합 형식 속성
데이터 집합 정의에 사용할 수 있는 JSON 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치, 서식 등에 대한 정보를 제공합니다. **AzureDataLakeStore** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| folderPath |Azure 데이터 레이크 저장소의 컨테이너 및 폴더에 대한 경로입니다. |예 |
| fileName |Azure 데이터 레이크 저장소에 있는 파일의 이름입니다. fileName은 선택 사항이며 대/소문자를 구분합니다. <br/><br/>filename을 지정하는 경우 활동(복사 포함)은 특정 파일에서 작동합니다.<br/><br/>fileName을 지정하지 않으면 복사는 입력 데이터 집합에 대한 folderPath에 모든 파일을 포함합니다.<br/><br/>fileName이 출력 데이터 집합에 대해 지정되지 않으면 생성된 파일의 이름은 다음 형식을 사용합니다. Data.<Guid>.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |아니요 |
| partitionedBy |partitionedBy는 선택적 속성입니다. 동적 folderPath 및 시계열 데이터에 대한 filename을 지정하는 데 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대한 매개 변수화됩니다. 자세한 내용과 예제는 [partitionedBy 속성 사용](#using-partitionedby-property) 섹션을 참조하세요. |아니요 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](#specifying-textformat), [Json 형식](#specifying-jsonformat), [Avro 형식](#specifying-avroformat), [Orc 형식](#specifying-orcformat) 및 [Parquet 형식](#specifying-parquetformat) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사**하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다. |아니요 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**이고 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 자세한 내용은 [압축 지정](#specifying-compression) 섹션을 참조하세요. |아니요 |

### <a name="using-partitionedby-property"></a>partitionedBy 속성 사용
**partitionedBy** 섹션, Data Factory 매크로 및 시스템 변수를 사용하여 동적 folderPath 및 시계열 데이터와 파일 이름을 지정할 수 있습니다. 지정된 데이터 조각에 대한 시작 및 종료 시간을 나타내는 SliceStart 및 SliceEnd입니다.

시간 시계열 데이터 집합, 예약 및 조각에 대한 자세한 내용을 이해하려면 [데이터 집합 만들기](data-factory-create-datasets.md) 및 [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

#### <a name="sample-1"></a>샘플 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
이 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 Data Factory 시스템 변수 SliceStart 값으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다

#### <a name="sample-2"></a>샘플 2
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
이 예제에서 SliceStart의 연도, 월, 일 및 시간은 folderPath 및 fileName 속성에서 사용하는 별도 변수로 추출됩니다.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-data-lake-copy-activity-type-properties"></a>Azure 데이터 레이크 복사 작업 형식 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 작업의 경우 원본 및 싱크의 형식에 따라 달라집니다.

**AzureDataLakeStoreSource**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True(기본값), False |아니요 |

**AzureDataLakeStoreSink**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| copyBehavior |복사 동작을 지정합니다. |**PreserveHierarchy:** 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/>**FlattenHierarchy:** 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 만들어집니다. 대상 파일은 자동 생성된 이름으로 만들어집니다.<br/><br/>**MergeFiles:** 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다. |아니요 |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>성능 및 튜닝

초기 데이터 이동이 큰 볼륨의 기록 데이터나 증분 프로덕션 데이터 로드로 계획되었는지 여부에 따라, Azure 데이터 팩터리에는 이러한 작업의 성능을 향상시키는 옵션이 있습니다. 동시성 매개 변수는 **복사 작업**의 일부이며 여러 다른 활동 창이 병렬로 처리됩니다. **parallelCopies** 매개 변수는 단일 작업 실행에 대한 병렬 처리를 정의합니다. 최상의 처리량을 달성하기 위해 Azure 데이터 팩터리를 사용하여 데이터 이동 파이프라인을 디자인할 때 이러한 매개 변수를 사용하는 것이 중요합니다.

Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.



<!--HONumber=Feb17_HO2-->


