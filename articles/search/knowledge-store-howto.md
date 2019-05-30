---
title: 지식 저장소 시작 방법(미리 보기) - Azure Search
description: Azure Search에서 AI 인덱싱 파이프라인을 통해 만들어진 강화된 문서를 Azure 스토리지 계정의 지식 저장소에 보내는 단계에 대해 알아봅니다. 여기서는 Azure Search 및 다른 애플리케이션에서 강화된 문서를 보고, 개조하고, 사용할 수 있습니다.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: heidist
ms.openlocfilehash: e7be2dfc811caa087726339846a1de2516f1e2b2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540719"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Azure Search에서 지식 저장소 시작 방법

> [!Note]
> 지식 저장소는 미리 보기로 제공되며 프로덕션 용도로는 제공되지 않습니다. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 .NET SDK 지원이 없습니다.
>

[기술 저장소](knowledge-store-concept-intro.md)는 인덱싱 중에 생성된 AI 보강을 다른 앱의 다운스트림 지식 마이닝을 위해 Azure 스토리지 계정에 저장합니다. 또한 저장된 강화를 사용하여 Azure Search 인덱싱 파이프라인을 이해하고 구체화할 수 있습니다.

지식 저장소는 기술 집합으로 정의됩니다. 일반적인 Azure Search 전체 텍스트 검색 시나리오의 경우 기술 집합의 목적은 콘텐츠를 더 쉽게 검색할 수 있도록 AI 강화를 제공하는 것입니다. 기술 마이닝 시나리오의 경우 기술 집합의 역할은 다른 앱 및 프로세스에서 분석하거나 모델링하기 위해 여러 데이터 구조를 만들, 채우고, 저장하는 것입니다.

이 연습에서는 샘플 데이터, 서비스 및 도구로 시작하여 기술 집합을 정의하는 데 집중하면서 첫 번째 지식 저장소를 만들고 사용하는 기본 워크플로에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 사용되는 서비스, 도구 및 데이터는 다음과 같습니다. 

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 자습서에서는 체험 서비스를 사용할 수 있습니다. 

+ 샘플 데이터를 저장하기 위한 [Azure 스토리지 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 지식 저장소는 Azure 스토리지에 있습니다. 

+ AI 강화에서 사용되는 모든 기술에 광범위하게 액세스할 수 있도록 S0 종량제 계층에서 [Cognitive Services 리소스를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). 이 리소스와 Azure Search 서비스는 동일한 지역에 있어야 합니다.

+ [Postman 데스크톱 앱](https://www.getpostman.com/)은 요청을 Azure Search에 보내는 데 사용됩니다.

+ [Postman 컬렉션](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Caselaw)에는 데이터 원본, 인덱스, 기술 집합 및 인덱서를 만들기 위해 준비된 요청이 포함되어 있습니다. 몇 가지 개체 정의는 너무 길어 이 문서에 포함될 수 없습니다. 인덱스 및 기술 집합 정의 전체를 보려면 이 컬렉션을 가져와야 합니다.

+ [Caselaw 샘플 데이터](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw)는 [Caselaw Access Project](https://case.law/bulk/download/) 공용 대량 데이터 다운로드 페이지에서 가져온 것입니다. 특히 이 연습에서는 첫 번째 다운로드(Arkansas)의 처음 10개 문서를 사용합니다. 이 연습을 위해 10개의 문서 샘플을 GitHub에 업로드했습니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

    ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-fiddler/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다.

## <a name="prepare-sample-data"></a>샘플 데이터 준비

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Storage 계정으로 이동한 후 **Blobs**를 클릭하고 **+ 컨테이너**를 클릭합니다.

1. 샘플 데이터가 포함되도록 [Blob 컨테이너를 만듭니다](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). "caselaw-test"라는 컨테이너 이름을 사용합니다. 유효한 값에 대한 공용 액세스 수준을 설정할 수 있습니다.

1. 컨테이너를 만들었으면 연 다음, 명령 모음에서 **업로드**를 선택합니다.

   ![명령 모음에서 업로드](media/search-semi-structured-data/upload-command-bar.png "명령 모음에서 업로드")

1. **caselaw-sample.json** 샘플 파일이 있는 폴더로 이동합니다. 파일을 선택한 다음, **업로드**를 클릭합니다.


## <a name="set-up-postman"></a>Postman 설정

Postman을 시작하고 Caselaw Postman 컬렉션을 가져옵니다. 또는 일련의 HTTP 요청을 설정합니다. 이 도구가 생소한 경우 [Postman을 사용하여 Azure Search REST API 살펴보기](search-fiddler.md)를 참조하세요.

+ 이 연습의 모든 호출에 대한 요청 메서드는 **PUT** 또는 **POST**입니다.
+ 요청 헤더(2)에는 "application/json"으로 설정된 Content-type, "관리자 키"(검색 기본 키의 자리 표시자)로 설정된 "api-key"가 각각 포함됩니다. 
+ 요청 본문은 호출의 실제 콘텐츠를 배치하는 영역입니다. 

  ![반구조화된 검색](media/search-semi-structured-data/postmanoverview.png)

Postman을 사용하여 검색 서비스에 대한 4개의 API 호출을 수행하여 데이터 원본, 인덱스, 기술 집합 및 인덱서를 이 순서대로 만듭니다. 데이터 원본에는 Azure 스토리지 계정 및 JSON 데이터에 대한 포인터가 포함됩니다. 검색 서비스는 데이터를 가져올 때 연결을 만듭니다.

[기술 집합 만들기](#create-skillset)는 이 연습의 중점 사항으로, 보강 단계와 지식 저장소에서 데이터가 유지되는 방법을 지정합니다.

URL 엔드포인트는 api-version을 지정해야 하며, 각 호출에서 **201 만들어짐**을 반환해야 합니다. 지식 저장소 지원이 있는 기술 집합을 만드는 미리 보기 api-version은 `2019-05-06-Preview`(대/소문자 구분)입니다.

REST 클라이언트에서 다음 API 호출을 실행합니다.

## <a name="create-a-data-source"></a>데이터 소스 만들기

[데이터 원본 만들기 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)는 인덱싱할 데이터를 지정하는 Azure Search 개체를 만듭니다.

이 호출의 엔드포인트는 `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview`입니다. 

1. `[service name]`을 검색 서비스의 이름으로 바꿉니다. 

2. 이 호출의 경우 요청 본문에 스토리지 계정 연결 문자열 및 Blob 컨테이너 이름이 포함되어야 합니다. 연결은 Azure Portal에 있는 스토리지 계정의 **액세스 키** 내에서 찾을 수 있습니다. 

   호출을 실행하기 전에 요청 본문에 있는 연결 문자열과 Blob 컨테이너 이름을 바꿔야 합니다.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. 요청을 보냅니다. 응답은 **201**이어야 하며, 응답 본문은 제공한 요청 페이로드와 거의 동일해야 합니다.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>인덱스 만들기
    
두 번째 호출은 [인덱스 API 만들기](https://docs.microsoft.com/rest/api/searchservice/create-data-source)이며 검색 가능한 모든 데이터를 저장하는 Azure Search 인덱스를 만듭니다. 인덱스는 모든 필드 및 특성을 지정합니다.

기술 마이닝에 대한 인덱스가 반드시 필요한 것은 아니지만, 인덱스가 제공되지 않으면 인덱서가 실행되지 않습니다. 

이 호출에 대한 URL은 `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`입니다.

1. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

2. Postman 컬렉션의 인덱스 만들기 요청의 인덱스 정의를 요청 본문에 복사합니다. 인덱스 정의는 수백 줄로 너무 길어 여기에 인쇄할 수가 없습니다. 

   인덱스의 외부 셸을 구성하는 요소는 다음과 같습니다. 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. `fields` 컬렉션은 대량의 인덱스 정의를 포함합니다. 여기에는 단순 필드, 중첩된 하부 구조가 있는 [복합 필드](search-howto-complex-data-types.md) 및 컬렉션이 포함됩니다.

   302-384번 줄의 `casebody`에 대한 필드 정의를 검토합니다. 계층적 표현이 필요한 경우 복합 필드에 다른 복합 필드가 포함될 수 있습니다.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. 요청을 보냅니다. 

   응답은 **201**이어야 하며, 처음 몇 개의 필드를 보여 주는 다음 예제와 비슷합니다.

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>기술 집합 및 지식 저장소 만들기

[기술 집합 만들기 API](https://docs.microsoft.com/rest/api/searchservice/create-skillset)는 호출할 인지 기술, 기술을 함께 연결하는 방법 및 이 연습에서 가장 중요한 부분인 지식 저장소를 지정하는 방법을 지정하는 Azure Search 개체를 만듭니다.

이 호출의 엔드포인트는 `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`입니다.

1. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

2. Postman 컬렉션의 기술 집합 만들기 요청의 기술 집합 정의를 요청 본문에 복사합니다. 기술 집합 정의는 수백 줄로 너무 길어 여기에 인쇄할 수가 없지만, 이 연습에서 중요한 부분입니다.

   기술 집합의 외부 셸을 구성하는 요소는 다음과 같습니다. `skills` 컬렉션은 메모리 내 강화를 정의하지만, `knowledgeStore` 정의는 출력을 저장하는 방법을 지정합니다. `cognitiveServices` 정의는 AI 강화 엔진에 대한 연결입니다.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. 먼저 `cognitiveServices` 및 `knowledgeStore` 키와 연결 문자열을 설정합니다. 다음 예제에서 이러한 문자열은 기술 집합 정의 뒤에 나오는 요청 본문의 끝부분에 있습니다. Azure Search와 동일한 지역에 있는 S0 계층에서 프로비저닝되는 Cognitive Services 리소스를 사용합니다.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

3. 기술 컬렉션, 특히 85번 줄 및 170번 줄의 쉐이퍼 기술을 각각 검토합니다. 쉐이퍼 기술은 기술 마이닝에 필요한 데이터 구조를 어셈블하므로 중요합니다. 기술 집합을 실행하는 동안 이러한 구조는 메모리 내에만 있지만, 다음 단계로 이동하면 추가 검색을 위해 이 출력을 지식 저장소에 저장하는 방법을 확인할 수 있습니다.

   다음 코드 조각은 217번 줄에서 가져온 것입니다. 

    ```json
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
            ]
        }
    ]
   . . .
   ```

3. 262번 줄에서 시작하는 `knowledgeStore`의 `projections` 요소를 검토합니다. 프로젝션은 지식 저장소 구성을 지정합니다. 프로젝션이 테이블-개체 쌍으로 지정되지만 현재는 한 번에 하나씩만 지정됩니다. 첫 번째 프로젝션에서 알 수 있듯이 `tables`가 지정되었지만 `objects`는 지정되지 않았습니다. 두 번째 프로젝션의 경우 반대입니다.

   Azure 스토리지의 경우 생성한 각 테이블에 대한 Table 스토리지에 테이블이 만들어지고, 각 개체에서 컨테이너를 Blob 스토리지에 가져옵니다.

   Blob 개체에는 일반적으로 농축의 전체 식이 포함됩니다. 테이블에는 일반적으로 특정 용도로 배열하는 부분 강화가 포함됩니다. 다음 예제에서는 Cases 테이블과 Opinions 테이블을 표시하지만, Entities, Attorneys, Judges 및 Parties와 같은 다른 테이블은 표시하지 않습니다.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

5. 요청을 보냅니다. 응답은 **201**이어야 하며, 응답의 처음 부분을 보여 주는 다음 예제와 비슷합니다.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>인덱서 만들기 및 실행

[인덱서 만들기 API](https://docs.microsoft.com/rest/api/searchservice/create-indexer)는 인덱서를 만들고 즉시 실행합니다. 지금까지 만든 모든 정의는 이 단계에서 작동합니다. 인덱서는 서비스에 없으므로 즉시 실행됩니다. 인덱서가 있는 경우 기존 인덱서에 대한 POST 호출은 업데이트 작업입니다.

이 호출의 엔드포인트는 `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`입니다.

1. `[service name]`을 검색 서비스의 이름으로 바꿉니다. 

2. 이 호출의 경우 요청 본문은 인덱서 이름을 지정합니다. 인덱서에는 데이터 원본과 인덱스가 필요합니다. 기술 집합은 인덱서에서 선택 항목이지만 AI 강화에서는 필수 항목입니다.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. 요청을 보냅니다. 응답은 **201**이어야 하며, 응답 본문은 제공한 요청 페이로드와 거의 동일해야 합니다(간결하게 잘랐음).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>지식 저장소 검색

첫 번째 문서를 가져오는 즉시 검색을 시작할 수 있습니다. 이 작업을 수행하려면 포털에서 [**Storage 탐색기**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)를 사용합니다.

지식 저장소가 Azure Search에서 완전히 분리되어 있음을 알고 있어야 합니다. Azure Search 인덱스와 지식 저장소는 모두 데이터 표현과 콘텐츠를 포함하지만, 여기에는 몇 가지 방법만 있습니다. 전체 텍스트 검색, 필터링된 검색 및 Azure Search에서 지원되는 모든 시나리오에는 인덱스를 사용합니다. 또는 다른 도구를 연결하여 콘텐츠를 분석하는 지식 저장소만 사용하여 진행합니다.

## <a name="takeaways"></a>핵심 내용

이제 첫 번째 지식 저장소를 Azure 스토리지에 만들고, Storage 탐색기를 사용하여 강화를 확인했습니다. 이는 저장된 강화를 사용하기 위한 기본적인 환경입니다. 

## <a name="next-steps"></a>다음 단계

쉐이퍼 기술은 새 세이프에 결합될 수 있는 세분화된 데이터 양식을 만드는 데 많은 어려움을 겪습니다. 다음 단계로, 사용하는 방법에 대한 자세한 내용은 이 기술에 대한 참조 페이지를 검토하세요.

> [!div class="nextstepaction"]
> [쉐이퍼 기술 참조](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->