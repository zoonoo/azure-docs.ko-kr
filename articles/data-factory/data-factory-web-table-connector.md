<properties 
    pageTitle="웹 테이블에서 데이터 이동 | Azure Data Factory" 
    description="Azure Data Factory를 사용하여 온-프레미스 웹 페이지의 테이블로 데이터를 이동하는 방법에 대해 알아봅니다." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>


# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Azure Data Factory를 사용하여 웹 테이블 원본에서 데이터 이동
이 문서에서는 Azure Data Factory에서 복사 작업을 사용하여 웹 페이지의 테이블에서 다른 데이터 저장소로 데이터를 복사하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 Data Factory는 웹 테이블에서 다른 데이터 저장소로 데이터 이동이 아닌 다른 데이터 저장소에서 웹 테이블 대상으로 데이터 이동만을 지원합니다.

> [AZURE.NOTE] 이 웹 커넥터는 현재 HTML 페이지에서 테이블 콘텐츠를 추출하도록 지원합니다.

## <a name="sample:-copy-data-from-web-table-to-azure-blob"></a>샘플: 웹 테이블의 데이터를 Azure Blob으로 복사

아래 샘플은 다음을 보여줍니다.

1.  [웹](#web-linked-service-properties) 형식의 연결된 서비스입니다.
2.  [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.  [WebTable](#WebTable-dataset-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.  [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4.  [WebSource](#websource-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 웹 테이블의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다. 

다음 샘플에서는 웹 테이블의 데이터를 Azure Blob으로 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서에 설명한 싱크로 직접 데이터를 복사할 수 있습니다. 

**웹 연결된 서비스** 이 예제에서는 익명 인증으로 웹 연결된 서비스를 사용합니다. 사용할 수 있는 다른 유형의 인증은 [웹 연결된 서비스](#web-linked-service-properties) 섹션을 참조하세요. 

    {
        "name": "WebLinkedService",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "url" : "https://en.wikipedia.org/wiki/"
            }
        }
    }


**Azure 저장소 연결된 서비스**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**WebTable 입력 데이터 집합** **external**을 **true**로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

> [AZURE.NOTE] HTML 페이지에서 테이블의 인덱스를 가져오는 단계는 [HTML 페이지에서 테이블의 인덱스 가져오기](#get-index-of-a-table-in-an-html-page) 섹션을 참조하세요.  

    
    {
        "name": "WebTableInput",
        "properties": {
            "type": "WebTable",
            "linkedServiceName": "WebLinkedService",
            "typeProperties": {
                "index": 1,
                "path": "AFI's_100_Years...100_Movies"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }



**Azure Blob 출력 데이터 집합**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/Movies"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }




**복사 작업을 포함하는 파이프라인**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **WebSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. 

WebSource에서 지원하는 속성 목록은 [WebSource 형식 속성](#websource-copy-activity-type-properties)을 참조하세요. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [
              {
                "name": "WebTableInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "WebSource"
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


## <a name="web-linked-service-properties"></a>웹 연결된 서비스 속성

다음 테이블은 웹 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- | 
| type | 형식 속성은 **웹** | 예 | 
| Url | 웹 원본에 대한 URL입니다. | 예 |
| authenticationType | 익명 또는 기본입니다. | 예 |
| userName | 기본 인증을 위한 사용자 이름입니다. | 예(기본 인증의 경우)
| password | 기본 인증을 위한 암호입니다. | 예(기본 인증의 경우)

### <a name="using-anonymous-authentication"></a>익명 인증 사용

    {
        "name": "web",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "url" : "https://en.wikipedia.org/wiki/"
            }
        }
    }


### <a name="using-basic-authentication"></a>기본 인증 사용
    
    {
        "name": "web",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "basic",
                "url" : "http://myit.mycompany.com/",
                "userName": "Administrator",
                "password": "password"
            }
        }
    }


## <a name="webtable-dataset-properties"></a>WebTable 데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **WebTable** 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

속성 | 설명 | 필수
:-------- | :----------- | :--------
type    | 데이터 집합의 형식입니다.  **데이터 집합** | 예
path | 테이블을 포함하는 리소스에 대한 상대 URL입니다. | 아니요. 경로를 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. 
index | 리소스에 있는 테이블의 인덱스입니다. HTML 페이지에서 테이블의 인덱스를 가져오는 단계는 [HTML 페이지에서 테이블의 인덱스 가져오기](#get-index-of-a-table-in-an-html-page) 섹션을 참조하세요. | 예


**예제:**

    {
        "name": "WebTableInput",
        "properties": {
            "type": "WebTable",
            "linkedServiceName": "WebLinkedService",
            "typeProperties": {
                "index": 1,
                "path": "AFI's_100_Years...100_Movies"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }

## <a name="websource---copy-activity-type-properties"></a>WebSource - 복사 작업 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다. 

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

현재 복사 작업의 원본이 **WebSource**형식인 경우 추가 속성이 지원되지 않습니다. 

## <a name="get-index-of-a-table-in-an-html-page"></a>HTML 페이지에서 테이블의 인덱스 가져오기

1. **Excel 2016**을 시작하고 **데이터** 탭으로 전환합니다.  
2. 도구 모음에서 **새 쿼리**를 클릭하고 **기타 원본에서**를 가리킨 다음 **웹에서**를 클릭합니다.
    
    ![파워 쿼리 메뉴](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. **웹에서** 대화 상자에서 연결된 서비스 JSON에 사용할 **URL**(예: https://en.wikipedia.org/wiki/)과 데이터 집합에 대해 지정할 경로(예: AFI %27s_100_Years... 100_Movies)를 입력하고 **확인**을 클릭합니다. 

    ![웹 대화 상자](./media/data-factory-web-table-connector/FromWeb-DialogBox.png) 

    이 예제에 사용된 URL: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  **웹 콘텐츠 액세스** 대화 상자가 표시된 경우 오른쪽 **URL**, **인증**을 선택하고 **연결**을 클릭합니다. 

    ![웹 콘텐츠 액세스 대화 상자](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  트리 뷰에서 **테이블** 항목을 클릭하여 테이블에서 콘텐츠를 표시한 다음 아래쪽의 **편집** 단추를 클릭합니다.  

    ![탐색기 대화 상자](./media/data-factory-web-table-connector/Navigator-DialogBox.png) 

5. **쿼리 편집기** 창에서 도구 모음의 **고급 편집기** 단추를 클릭합니다.

    ![고급 편집기 단추](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. 고급 편집기 대화 상자에서 "원본" 옆에 있는 숫자가 인덱스입니다.

    ![고급 편집기 - 인덱스](./media/data-factory-web-table-connector/AdvancedEditor-Index.png) 


Excel 2013을 사용하는 경우 [Excel용 Microsoft 파워 쿼리](https://www.microsoft.com/download/details.aspx?id=39379) 를 사용하여 인덱스를 가져옵니다. 자세한 내용은 [웹 페이지에 연결](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) 문서를 참조하세요. [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)을 사용하는 경우에도 작업 단계는 비슷합니다. 

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.



<!--HONumber=Oct16_HO2-->


