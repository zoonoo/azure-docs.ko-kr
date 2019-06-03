---
title: 'C# 자습서: 인덱싱 파이프라인에서 Cognitive Services API 호출 - Azure Search'
description: 이 자습서에서는 데이터 추출 및 변환을 위한 Azure Search 인덱싱의 데이터 추출, 자연어 및 이미지 AI 처리 예제를 단계별로 알아봅니다.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 7aab7f75e6489fcaea1ecafee34823ad546a6b48
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244385"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C# 자습서: Azure Search 인덱싱 파이프라인에서 Cognitive Services API 호출

이 자습서에서는 *인식 기술*을 사용하여 Azure Search에서 데이터 보강을 프로그래밍하는 메커니즘을 알아봅니다. 기술은 Cognitive Services의 NLP(자연어 처리) 및 이미지 분석 기능에 의해 지원됩니다. 기술 세트 컴퍼지션 및 구성을 통해 이미지 또는 스캔한 문서 파일의 텍스트 및 텍스트 표현을 추출할 수 있습니다. 언어, 엔터티, 핵심 문구 등을 검색할 수도 있습니다. 최종적으로 AI 지원 인덱싱 파이프라인을 통해 Azure Search 인덱스에 풍부한 추가 콘텐츠가 생성됩니다.

이 자습서에서는 .NET SDK를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 인덱스로 라우팅되는 샘플 데이터를 보강하는 인덱싱 파이프라인 만들기
> * 기본 제공 기술 적용(광학 문자 인식, 텍스트 병합기, 언어 감지, 텍스트 분할, 엔터티 인식, 핵심 구 추출)
> * 기술 집합에서 입력을 출력에 매핑하여 여러 기술을 연결하는 방법을 알아봅니다
> * 요청을 실행하고 결과 검토
> * 추가 개발을 위해 인덱스 및 인덱서 다시 설정

출력은 Azure Search에서 검색 가능한 전체 텍스트 인덱스입니다. [동의어](search-synonyms.md), [점수 매기기 프로필](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [분석기](search-analyzers.md), [필터](search-filters.md) 등의 다른 표준 기능을 사용하여 인덱스를 향상할 수 있습니다.

이 자습서는 무료 서비스에서 실행되지만 무료 트랜잭션 수는 일일 20개 문서로 제한됩니다. 이 자습서를 동일한 날에 두 번 이상 실행하려면 더 많은 실행에 적합하도록 더 작은 파일 세트를 사용하세요.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때는 청구 가능 Cognitive Services 리소스를 연결해야 합니다. Cognitive Services에서 API를 호출할 때와 Azure Search에서 문서 해독 단계의 일부로 이미지를 추출할 때는 요금이 누적됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Search 가격 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에서 설명하고 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 다음과 같은 서비스, 도구 및 데이터가 사용됩니다. 

+ 샘플 데이터를 저장하기 위한 [Azure 스토리지 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 스토리지 계정이 Azure Search와 동일한 지역에 있는지 확인합니다.

+ [샘플 데이터](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)는 여러 종류의 작은 파일 세트로 구성됩니다. 

+ IDE로 사용할 [Visual Studio를 설치](https://visualstudio.microsoft.com/)합니다.

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 자습서에서는 체험 서비스를 사용할 수 있습니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

Azure Search 서비스와 상호 작용하려면 서비스 URL과 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-fiddler/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="prepare-sample-data"></a>샘플 데이터 준비

보강 파이프라인은 Azure 데이터 원본에서 데이터를 가져옵니다. 원본 데이터는 [Azure Search 인덱서](search-indexer-overview.md)의 지원되는 데이터 원본 유형에서 생성되어야 합니다. Azure Table Storage는 인식 검색을 지원하지 않습니다. 이 연습에서는 Blob Storage를 사용하여 여러 가지 콘텐츠 형식을 보여줍니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Storage 계정으로 이동한 후 **Blobs**를 클릭하고 **+ 컨테이너**를 클릭합니다.

1. 샘플 데이터가 포함되도록 [Blob 컨테이너를 만듭니다](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 유효한 값에 대한 공용 액세스 수준을 설정할 수 있습니다. 이 자습서에서는 컨테이너 이름이 "basic-demo-data-pr"이라고 가정합니다.

1. 컨테이너가 만들어지면 해당 컨테이너를 열고 명령 모음에서 **업로드**를 선택하여 [샘플 데이터](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)를 업로드합니다.

   ![Azure Blob Storage의 원본 파일](./media/cognitive-search-quickstart-blob/sample-data.png)

1. 샘플 파일이 로드되면 Blob Storage에 대한 컨테이너 이름 및 연결 문자열을 가져옵니다. 이 작업은 Azure Portal에서 스토리지 계정으로 이동하고, **액세스 키**를 선택한 다음, **연결 문자열** 필드를 복사하여 수행할 수 있습니다.

   연결 문자열은 다음 예제와 비슷한 URL이어야 합니다.

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

공유 액세스 서명을 제공하는 방법을 포함하여 연결 문자열을 지정하는 여러 방법이 있습니다. 데이터 원본 자격 증명에 대한 자세한 내용은 [Azure Blob Storage 인덱싱](search-howto-indexing-azure-blob-storage.md#Credentials)을 참조하세요.

## <a name="set-up-your-environment"></a>환경 설정

먼저 Visual Studio를 열고 .NET Core에서 실행할 수 있는 새 콘솔 앱 프로젝트를 만듭니다.

### <a name="install-nuget-packages"></a>NuGet 패키지 설치

[Azure Search .NET SDK](https://aka.ms/search-sdk)는 HTTP 및 JSON의 세부 정보를 처리하지 않고도 인덱스, 데이터 원본, 인덱서 및 기술 집합을 관리하고, 문서를 업로드 및 관리하고, 쿼리를 실행할 수 있게 하는 몇 가지 클라이언트 라이브러리로 구성됩니다. 이러한 클라이언트 라이브러리는 모두 NuGet 패키지로 배포됩니다.

이 프로젝트의 경우 `Microsoft.Azure.Search` NuGet 패키지 버전 9와 최신 `Microsoft.Extensions.Configuration.Json` NuGet 패키지를 설치해야 합니다.

Visual Studio의 패키지 관리자 콘솔을 사용하여 `Microsoft.Azure.Search` NuGet 패키지를 설치합니다. 패키지 관리자 콘솔을 열려면 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 차례로 선택합니다. 명령을 실행하려면 [Microsoft.Azure.Search NuGet 패키지 페이지](https://www.nuget.org/packages/Microsoft.Azure.Search)로 이동하여 버전 9를 선택하고 패키지 관리자 명령을 복사합니다. 패키지 관리자 콘솔에서 이 명령을 실행합니다.

`Microsoft.Extensions.Configuration.Json` NuGet 패키지를 Visual Studio에 설치하려면 **도구** > **NuGet 패키지 관리자** > **솔루션용 NuGet 패키지 관리...** 를 차례로 선택합니다. [찾아보기]를 선택하고 `Microsoft.Extensions.Configuration.Json` NuGet 패키지를 검색합니다. 이 NuGet 패키지가 검색되면 해당 패키지를 선택하고, 프로젝트를 선택하고, 안정적인 최신 버전인지 확인한 다음, [설치]를 선택합니다.

## <a name="add-azure-search-service-information"></a>Azure Search 서비스 정보 추가

Azure Search 서비스에 연결하려면 검색 서비스 정보를 프로젝트에 추가해야 합니다. 솔루션 탐색기에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **추가** > **새 항목...** 을 차례로 선택합니다. 파일 이름을 `appsettings.json`으로 지정하고 **추가**를 선택합니다. 

이 파일은 출력 디렉터리에 포함되어야 합니다. 이렇게 하려면 마우스 오른쪽 단추로 `appsettings.json`을 클릭하고 **속성**을 선택합니다. **출력 디렉터리로 복사**의 값을 **최신 복사본**으로 변경합니다.

아래 JSON을 새 JSON 파일에 복사합니다.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

검색 서비스 및 Blob 스토리지 계정 정보를 추가합니다.

검색 서비스 정보는 Azure Portal의 계정 검색 페이지에서 가져올 수 있습니다. 계정 이름은 기본 페이지에 있으며, 키는 **키**를 선택하여 찾을 수 있습니다.

Blob 연결 문자열은 Azure Portal에서 스토리지 계정으로 이동하고, **액세스 키**를 선택한 다음, **연결 문자열** 필드를 복사하여 가져올 수 있습니다.

## <a name="add-namespaces"></a>네임스페이스 추가

이 자습서에서는 다양한 네임스페이스의 여러 가지 형식을 사용합니다. 이러한 형식을 사용하려면 다음을 `Program.cs`에 추가합니다.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>클라이언트 만들기

`SearchServiceClient` 클래스의 인스턴스를 만듭니다.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`는 애플리케이션의 구성 파일(appsettings.json)에 저장된 값을 사용하여 `SearchServiceClient`를 새로 만듭니다.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> `SearchServiceClient` 클래스는 검색 서비스에 대한 연결을 관리합니다. 너무 많은 연결이 열리는 것을 방지하기 위해 되도록 애플리케이션에서 단일 `SearchServiceClient` 인스턴스를 공유합니다. 해당 메서드는 스레드로부터 안전하므로 이러한 공유를 사용할 수 있습니다.
> 
> 

## <a name="create-a-data-source"></a>데이터 소스 만들기

`DataSource.AzureBlobStorage`를 호출하여 새 `DataSource` 인스턴스를 만듭니다. `DataSource.AzureBlobStorage`에서는 데이터 원본 이름, 연결 문자열 및 Blob 컨테이너 이름을 지정해야 합니다.

이 자습서에서 사용되지는 않지만 일시 삭제 열의 값에 따라 삭제된 Blob을 식별하는 데 사용되는 일시 삭제 정책도 정의됩니다. 다음 정책에서는 값이 `true`인 `IsDeleted` 메타데이터 속성이 있는 경우 Blob이 삭제되는 것으로 간주합니다.

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

이제 `DataSource` 개체를 초기화했으므로 데이터 원본을 만듭니다. `SearchServiceClient`에는 `DataSources` 속성이 있습니다. 이 속성은 Azure Search 데이터 원본을 생성, 나열, 업데이트 또는 삭제하는 데 필요한 모든 메서드를 제공합니다.

요청이 성공하면 메서드에서 만들어진 데이터 원본을 반환합니다. 잘못된 매개 변수와 같이 요청에 문제가 있는 경우 메서드에서 예외를 throw합니다.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

이번이 첫 번째 요청이므로 Azure Portal에서 데이터 원본이 Azure Search에 생성되었는지 확인합니다. 검색 서비스 대시보드 페이지에서 데이터 원본 타일에 새 항목이 있는지 확인합니다. 포털 페이지가 새로 고침될 때까지 몇 분 정도 기다려야 할 수도 있습니다.

  ![포털의 데이터 원본 타일](./media/cognitive-search-tutorial-blob/data-source-tile.png "포털의 데이터 원본 타일")

## <a name="create-a-skillset"></a>기술 집합 만들기

이 섹션에서는 데이터에 적용하려는 일단의 보강 단계를 정의합니다. 각 보강 단계는 *기술*이라고 하며, 보강 단계 집합은 *기술 집합*이라고 합니다. 이 자습서에서는 다음과 같은 [미리 정의된 인식 기술](cognitive-search-predefined-skills.md)을 기술 집합에 사용합니다.

+ [광학 문자 인식](cognitive-search-skill-ocr.md): 이미지 파일의 인쇄 및 필기 텍스트를 인식합니다.

+ [텍스트 병합기](cognitive-search-skill-textmerger.md): 필드 컬렉션의 텍스트를 단일 필드로 통합합니다.

+ [언어 감지](cognitive-search-skill-language-detection.md): 콘텐츠의 언어를 식별합니다.

+ [텍스트 나누기](cognitive-search-skill-textsplit.md): 핵심 구 추출 기술과 엔터티 인식 기술을 호출하기 전에 큰 콘텐츠를 작은 청크로 나눕니다. 핵심 구 추출 및 엔터티 인식은 50,000자 이하의 입력을 허용합니다. 일부 샘플 파일은 이 제한에 맞게 분할해야 합니다.

+ [엔터티 인식](cognitive-search-skill-entity-recognition.md): Blob 컨테이너의 콘텐츠에서 조직 이름을 추출합니다.

+ [핵심 구 추출](cognitive-search-skill-keyphrases.md): 상위 핵심 구를 추출합니다.

초기 처리 중에 Azure Search는 각 문서를 분해하여 다른 파일 형식의 콘텐츠를 읽습니다. 원본 파일에서 발생하는 텍스트는 각 문서에 대해 생성되는 ```content``` 필드에 배치됩니다. 따라서 이 텍스트를 사용하려면 입력을 ```"/document/content"```로 설정합니다. 

출력을 인덱스에 매핑할 수도 있고, 다운스트림 기술의 입력으로 사용할 수도 있고, 언어 코드처럼 둘 다 할 수도 있습니다. 인덱스에서 언어 코드는 필터링에 유용합니다. 입력으로써의 언어 코드는 단어 분리에 대한 언어적 규칙을 알려주기 위해 텍스트 분석 기술에 사용됩니다.

기술 집합 기본에 대한 자세한 내용은 [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)을 참조하세요.

### <a name="ocr-skill"></a>OCR 기술

**OCR** 기술은 이미지에서 텍스트를 추출합니다. 이 기술은 normalized_images 필드가 있다고 가정합니다. 이 필드를 생성하기 위해 자습서의 뒷부분에서 인덱서 정의의 ```"imageAction"``` 구성을 ```"generateNormalizedImages"```로 설정합니다.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>병합 기술

이 섹션에서는 문서 콘텐츠 필드를 OCR 기술로 생성된 텍스트와 병합하는 **병합** 기술을 만듭니다.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>언어 감지 기술

**언어 감지** 기술은 입력 텍스트의 언어를 감지하고 요청에 제출된 모든 문서에 대한 단일 언어 코드를 보고합니다. **언어 감지** 기술의 출력을 **텍스트 나누기** 기술에 대한 입력의 일부로 사용합니다.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>텍스트 나누기 기술

아래 **나누기** 기술은 텍스트를 페이지별로 나누고 `String.Length`에서 측정할 때 페이지 길이를 4,000자로 제한합니다. 알고리즘은 텍스트를 최대 `maximumPageLength` 크기의 청크로 나누려고 시도합니다. 이 경우 알고리즘은 문장 경계에서 문장을 나누는 데 최선을 다할 것이므로 청크의 크기는 `maximumPageLength`보다 약간 작을 수 있습니다.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>엔터티 인식 기술

다음 `EntityRecognitionSkill` 인스턴스는 `organization` 범주 유형을 인식하도록 설정되어 있습니다. **엔터티 인식** 기술은 `person` 및 `location` 범주 유형도 인식할 수 있습니다.

"context" 필드는 별표를 포함하여 ```"/document/pages/*"```로 설정되어 있으며, 이는 ```"/document/pages"``` 아래의 각 페이지에 대해 보강 단계가 호출됨을 의미합니다.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>핵심 구 추출 기술

방금 만든 `EntityRecognitionSkill` 인스턴스와 마찬가지로 문서의 각 페이지에 대해 **핵심 구 추출** 기술이 호출됩니다.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "keyPhrases",
    targetName: "keyPhrases"));

KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
    description: "Extract the key phrases",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="build-and-create-the-skillset"></a>기술 집합 빌드 및 만들기

만든 기술을 사용하여 `Skillset`를 빌드합니다.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

검색 서비스에서 기술 집합을 만듭니다.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>인덱스 만들기

이 섹션에서는 검색 가능한 인덱스에 포함할 필드 및 각 필드의 검색 특성을 지정하여 인덱스 스키마를 정의합니다. 필드에는 형식이 있으며 필드가 사용되는 방식(검색 가능, 정렬 가능 등)을 결정하는 특성을 가질 수 있습니다. 인덱스의 필드 이름은 원본의 필드 이름을 동일하게 일치시키는 데 필요하지 않습니다. 이후 단계에서 인덱서의 필드 매핑을 원본-대상 연결 필드에 추가할 것입니다. 이 단계에서는 검색 애플리케이션과 관련된 필드 명명 규칙을 사용하여 인덱스를 정의합니다.

이 연습에서는 다음 필드와 필드 형식을 사용합니다.

| 필드 이름: | `id`       | 콘텐츠   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| 필드 형식: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>DemoIndex 클래스 만들기

이 인덱스에 대한 필드는 모델 클래스를 사용하여 정의됩니다. 모델 클래스의 각 속성에는 해당 인덱스 필드의 검색 관련 동작을 결정하는 특성이 있습니다. 

모델 클래스를 새 C# 파일에 추가합니다. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **추가** > **새 항목...** 을 차례로 선택하고, "클래스"를 선택하고, 파일 이름을 `DemoIndex.cs`로 지정한 다음, **추가**를 선택합니다.

`Microsoft.Azure.Search` 및 `Microsoft.Azure.Search.Models` 네임스페이스의 형식을 사용하도록 지정해야 합니다.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

아래의 모델 클래스 정의를 `DemoIndex.cs`에 추가하고, 인덱스를 만들 동일한 네임스페이스에 이를 포함시킵니다.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
```

이제 모델 클래스를 `Program.cs`에 다시 정의했으므로 인덱스 정의를 매우 쉽게 만들 수 있습니다. 이 인덱스의 이름은 "demoindex"입니다.

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

테스트 중에 인덱스를 두 번 이상 만들려고 시도할 수 있습니다. 이로 인해 인덱스를 만들려고 하기 전에 해당 인덱스가 이미 있는지 확인합니다.

```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```

인덱스 정의에 대한 자세한 내용은 [인덱스 만들기(Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>인덱서 만들기, 필드 매핑 및 변환 실행

지금까지 데이터 원본, 기술 집합 및 인덱스를 만들었습니다. 이러한 세 가지 구성 요소는 각 조각을 서로 연결하여 단일 다단계 작업으로 만드는 [인덱서](search-indexer-overview.md)의 일부가 됩니다. 각 구성 요소를 인덱서에 서로 연결하려면 필드 매핑을 정의해야 합니다.

+ fieldMappings은 기술 세트보다 먼저 처리되며, 데이터 원본의 원본 필드를 인덱스의 대상 필드로 매핑합니다. 필드 이름과 유형이 양쪽 끝에서 동일하면 매핑이 필요 없습니다.

+ outputFieldMappings는 기술 세트 후에 처리되며, 문서 크래킹 또는 보강을 통해 만들기 전에는 존재하지 않는 sourceFieldNames를 참조합니다. targetFieldName은 인덱스의 필드입니다.

입력을 출력에 후크할 뿐만 아니라 필드 매핑을 사용하여 데이터 구조를 평면화할 수도 있습니다. 자세한 내용은 [검색 가능한 인덱스에 보강된 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)을 참조하세요.

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

Indexer indexer = new Indexer(
    name: "demoindexer",
    dataSourceName: dataSource.Name,
    targetIndexName: index.Name,
    description: "Demo Indexer",
    skillsetName: skillSet.Name,
    parameters: new IndexingParameters(
        maxFailedItems: -1,
        maxFailedItemsPerBatch: -1,
        configuration: config),
    fieldMappings: fieldMappings,
    outputFieldMappings: outputMappings);

try
{
    bool exists = serviceClient.Indexers.Exists(indexer.Name);

    if (exists)
    {
        serviceClient.Indexers.Delete(indexer.Name);
    }

    serviceClient.Indexers.Create(indexer);
}
catch (Exception e)
{
    // Handle exception
}
```

인덱서 만드는 데 약간의 시간이 걸릴 것입니다. 데이터 집합이 작아도 분석 기술은 계산을 많이 수행합니다. 이미지 분석 같은 일부 기술은 오래 실행됩니다.

> [!TIP]
> 인덱서를 만들면 파이프라인이 호출됩니다. 데이터 도달, 입력 및 출력 매핑 또는 작업 순서에 문제가 있으면 이 단계에 나타납니다.

### <a name="explore-creating-the-indexer"></a>인덱서 만들기 검색

코드에서 ```"maxFailedItems"```를 -1로 설정하며, 이는 데이터를 가져오는 동안 오류를 무시하도록 인덱싱 엔진에 지시합니다. 데모 데이터 원본에는 문서가 몇 개 없기 때문에 이렇게 하면 도움이 됩니다. 데이터 원본의 크기가 큰 경우에는 0보다 큰 값으로 설정해야 합니다.

또한 ```"dataToExtract"```는 ```"contentAndMetadata"```으로 설정되어 있습니다. 이 명령문은 다른 파일 형식의 콘텐츠 및 각 파일과 관련된 메타데이터를 자동으로 추출하도록 인덱서에 지시합니다.

콘텐츠가 추출되면 데이터 원본에서 찾은 이미지의 텍스트를 추출하도록 `imageAction`을 설정할 수 있습니다. OCR 기술 및 텍스트 병합 기술과 함께 ```"generateNormalizedImages"``` 구성으로 설정된 ```"imageAction"```은 이미지에서 텍스트(예: 트래픽 중지 기호에서 "중지"라는 단어)를 추출하여 콘텐츠 필드의 일부로 포함시키도록 인덱서에 지시합니다. 이 동작은 문서에 포함된 이미지(예: PDF 내 이미지)뿐 아니라 JPG 파일 같은 데이터 원본의 이미지에도 적용됩니다.

## <a name="check-indexer-status"></a>인덱서 상태 확인

인덱서가 정의되면 사용자가 요청을 제출할 때 인덱서가 자동으로 실행됩니다. 사용자가 정의한 인식 기술에 따라 인덱싱이 예상보다 오래 걸릴 수 있습니다. 인덱서가 아직 실행되고 있는지 확인하려면 `GetStatus` 메서드를 사용합니다.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
            break;
        case IndexerStatus.Running:
            Console.WriteLine("Indexer is running");
            break;
        case IndexerStatus.Unknown:
            Console.WriteLine("Indexer status is unknown");
            break;
        default:
            Console.WriteLine("No indexer information");
            break;
    }
}
catch (Exception e)
{
    // Handle exception
}
```

`IndexerExecutionInfo`는 인덱서의 현재 상태 및 실행 기록을 나타냅니다.

경고는 일부 원본 파일 및 기술 조합에서 일반적이며 항상 문제를 나타내는 것은 아닙니다. 이 자습서의 경고는 심각하지 않습니다(예: JPEG 파일의 텍스트 입력이 없음).
 
## <a name="query-your-index"></a>인덱스 쿼리

인덱싱이 완료되면 개별 필드의 콘텐츠를 반환하는 쿼리를 실행할 수 있습니다. 기본적으로 Azure Search는 상위 50개 결과를 반환합니다. 샘플 데이터가 작기 때문에 기본값으로 충분합니다. 그러나 더 큰 데이터 집합으로 작업할 경우 쿼리 문자열에 더 많은 결과를 반환하는 매개 변수를 포함해야 합니다. 자세한 지침은 [Azure Search에서 결과를 페이징하는 방법](search-pagination-page-layout.md)을 참조하세요.

확인 단계로 모든 필드에 대한 인덱스를 쿼리합니다.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient`는 애플리케이션의 구성 파일(appsettings.json)에 저장된 값을 사용하여 `SearchIndexClient`를 새로 만듭니다. 검색 서비스 쿼리 API 키가 사용되며 관리자 키는 사용되지 않습니다.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

출력은 각 필드의 이름, 형식 및 특성이 포함된 인덱스 스키마입니다.

`organizations`처럼 단일 필드의 모든 콘텐츠를 반환하도록 `"*"`에 대한 두 번째 쿼리를 제출합니다.

```csharp
SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

추가 필드 반복: 이 연습의 콘텐츠, 언어 코드, 핵심 구 및 조직. 쉼표로 구분된 목록을 사용하여 `$select`를 통해 여러 필드를 반환할 수 있습니다.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

초기 개발 실험 단계에서 설계 반복에 대한 가장 실용적인 방법은 Azure Search에서 개체를 삭제하고 코드에서 이를 다시 작성하도록 허용하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

이 자습서에서는 기존 인덱서와 인덱스를 확인하고 이미 있는 경우 해당 인덱스를 삭제하여 코드를 다시 실행할 수 있도록 했습니다.

또한 포털을 사용하여 인덱스, 인덱서 및 기술 집합을 삭제할 수도 있습니다.

코드가 완성될수록 다시 빌드 전략을 구체화하는 것이 좋습니다. 자세한 내용은 [인덱스 다시 작성](search-howto-reindex.md)을 참조하세요.

## <a name="takeaways"></a>핵심 내용

이 자습서에서는 데이터 원본, 기술 집합, 인덱스 및 인덱서라는 구성 요소 부분을 만들어서 강화된 인덱싱 파이프라인을 빌드하는 기본 단계를 살펴보았습니다.

기술 집합 정의 및 입/출력을 통해 기술을 서로 연결하는 메커니즘과 함께 [미리 정의된 기술](cognitive-search-predefined-skills.md)을 소개했습니다. 인덱서 정의의 `outputFieldMappings`는 보강된 값을 Azure Search 서비스의 파이프라인에서 검색 가능한 인덱스로 라우팅하는 데 필요하다는 것도 배웠습니다.

마지막으로, 결과를 테스트하고 추가 반복을 위해 시스템을 다시 설정하는 방법을 배웠습니다. 인덱스에 대한 쿼리를 실행하면 보강된 인덱싱 파이프라인에서 만든 출력이 반환된다는 것을 배웠습니다. 인덱서 상태를 확인하는 방법과 파이프라인을 다시 실행하기 전에 어떤 개체를 삭제해야 하는지도 배웠습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 마친 후 정리하는 가장 빠른 방법은 Azure Search 서비스 및 Azure Blob service를 포함하고 있는 리소스 그룹을 삭제하는 것입니다. 두 서비스를 동일한 그룹에 배치한 경우 리소스 그룹을 삭제하면 서비스와 이 자습서에서 만들고 저장한 콘텐츠를 포함하여 리소스 그룹에 들어 있는 모든 것이 영구적으로 삭제됩니다. 포털에서 리소스 그룹 이름은 각 서비스의 개요 페이지에 있습니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 기술을 사용하여 파이프라인을 사용자 지정 또는 확장합니다. 사용자 지정 기술을 만들어서 기술 집합에 추가하면 사용자가 직접 작성한 텍스트 또는 이미지 분석을 온보딩할 수 있습니다.

> [!div class="nextstepaction"]
> [예제: 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)
