---
title: Azure Blob storage 콘텐츠를 검색 합니다.
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search를 사용 하 여 문서에서 텍스트를 인덱싱하고 Azure Blob Storage 하는 방법에 대해 알아봅니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 1c2bac06f2526260fb290b63e5aa559a1e2337b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379561"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Azure Cognitive Search를 사용 하 여 Azure Blob Storage에서 문서를 인덱싱하는 방법

이 문서에서는 azure Cognitive Search를 사용 하 여 Azure Blob storage에 저장 된 문서 (예: Pdf, Microsoft Office 문서 및 다른 몇 가지 일반적인 형식)를 인덱싱하는 방법을 보여 줍니다. 먼저 blob 인덱서 설정 및 구성에 대 한 기본 사항을 설명 합니다. 그런 다음 발생할 수 있는 동작 및 시나리오에 대 한 심층 탐색을 제공 합니다.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>지원 되는 문서 형식
Blob 인덱서는 다음 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Blob 인덱싱 설정
다음을 사용 하 여 Azure Blob Storage 인덱서를 설정할 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> 일부 기능 (예: 필드 매핑)은 포털에서 아직 사용할 수 없으며 프로그래밍 방식으로 사용 해야 합니다.
>

여기서는 REST API을 사용 하 여 흐름을 보여 줍니다.

### <a name="step-1-create-a-data-source"></a>1 단계: 데이터 원본 만들기
데이터 원본은 인덱싱할 데이터, 데이터에 액세스 하는 데 필요한 자격 증명 및 데이터 변경 내용 (새로운, 수정 또는 삭제 된 행)을 효율적으로 식별 하는 정책을 지정 합니다. 동일한 검색 서비스의 여러 인덱서에 데이터 원본을 사용할 수 있습니다.

Blob 인덱싱의 경우 데이터 원본에 다음과 같은 필수 속성이 있어야 합니다.

* **name** 은 검색 서비스 내에서 데이터 원본의 고유 이름입니다.
* **형식은** `azureblob`이어야 합니다.
* **자격 증명** 은 `credentials.connectionString` 매개 변수로 저장소 계정 연결 문자열을 제공 합니다. 자세한 내용은 아래의 [자격 증명을 지정 하는 방법을](#Credentials) 참조 하세요.
* **컨테이너** 는 저장소 계정의 컨테이너를 지정 합니다. 기본적으로 컨테이너 내의 모든 blob은 검색할 수 있습니다. 특정 가상 디렉터리의 blob만 인덱싱하고 싶으면 선택적 **쿼리** 매개 변수를 사용 하 여 해당 디렉터리를 지정할 수 있습니다.

데이터 원본을 만들려면 다음을 수행 합니다.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

데이터 원본 만들기 API에 대 한 자세한 내용은 [데이터 원본 만들기](https://docs.microsoft.com/rest/api/searchservice/create-data-source)를 참조 하세요.

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>자격 증명을 지정 하는 방법 ####

다음 방법 중 하나로 blob 컨테이너에 대 한 자격 증명을 제공할 수 있습니다.

- **전체 액세스 저장소 계정 연결 문자열**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` 저장소 계정 블레이드 > 설정 > 키 (클래식 저장소 계정) 또는 설정 > 액세스 키 (Azure Resource Manager 저장소 계정)로 이동 하 여 Azure Portal에서 연결 문자열을 가져올 수 있습니다.
- **저장소 계정 sas (공유 액세스 서명** ) 연결 문자열: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` sas에 컨테이너 및 개체 (이 경우 blob)에 대 한 읽기 권한 및 목록이 있어야 합니다.
-  **컨테이너 공유 액세스 서명**: SAS에 컨테이너에 대 한 읽기 권한 및 목록이 있어야 `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` 합니다.

저장소 공유 액세스 서명에 대 한 자세한 내용은 [공유 액세스 서명 사용](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조 하세요.

> [!NOTE]
> SAS 자격 증명을 사용 하는 경우 만료를 방지 하기 위해 갱신 된 서명을 사용 하 여 정기적으로 데이터 원본 자격 증명을 업데이트 해야 합니다. SAS 자격 증명이 만료 되 면 인덱서가 실패 하 고 `Credentials provided in the connection string are invalid or have expired.`와 유사한 오류 메시지가 나타납니다.  

### <a name="step-2-create-an-index"></a>2 단계: 인덱스 만들기
인덱스는 문서의 필드, 특성 및 검색 환경을 구성 하는 기타 구문을 지정 합니다.

Blob에서 추출 된 텍스트를 저장 하기 위해 검색 가능한 `content` 필드로 인덱스를 만드는 방법은 다음과 같습니다.   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

인덱스 만들기에 대 한 자세한 내용은 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 를 참조 하세요.

### <a name="step-3-create-an-indexer"></a>3 단계: 인덱서 만들기
인덱서는 데이터 원본을 대상 검색 인덱스와 연결 하 고 데이터 새로 고침을 자동화 하는 일정을 제공 합니다.

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

이 인덱서는 2 시간 마다 실행 됩니다 (일정 간격이 "PT2H"로 설정 됨). 30 분 마다 인덱서를 실행 하려면 간격을 "에서는 PT30M으로"로 설정 합니다. 지원 되는 가장 짧은 간격은 5 분입니다. 일정은 선택 사항입니다. 생략 하는 경우 인덱서는 만들어질 때 한 번만 실행 됩니다. 그러나 언제 든 지 요청 시 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대 한 자세한 내용은 [인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-indexer)를 참조 하세요.

인덱서 일정을 정의 하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search의 인덱서를 예약 하는 방법을](search-howto-schedule-indexers.md)참조 하세요.

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Azure Cognitive Search 인덱스 blob 방법

[인덱서 구성](#PartsOfBlobToIndex)에 따라 blob 인덱서는 저장소 메타 데이터만 인덱싱할 수 있습니다 (메타 데이터에 대해서만 관심이 있고 blob의 콘텐츠를 인덱싱할 필요가 없는 경우에 유용), 저장소 및 콘텐츠 메타 데이터, 메타 데이터 및 텍스트 콘텐츠 모두를 인덱싱할 수 있습니다. 기본적으로 인덱서는 메타 데이터와 콘텐츠를 모두 추출 합니다.

> [!NOTE]
> 기본적으로 JSON 또는 CSV와 같이 구조화 된 콘텐츠를 포함 하는 blob은 단일 텍스트 청크로 인덱싱됩니다. 구조화 된 방식으로 JSON 및 CSV blob을 인덱싱하는 방법에 대 한 자세한 내용은 [json Blob 인덱싱](search-howto-index-json-blobs.md) 및 [csv blob 인덱싱](search-howto-index-csv-blobs.md) 을 참조 하세요.
>
> 복합 또는 포함 된 문서 (예: ZIP 보관 파일 또는 첨부 파일을 포함 하는 포함 된 Outlook 전자 메일을 포함 하는 Word 문서)도 단일 문서로 인덱싱됩니다.

* 문서의 텍스트 내용이 `content`라는 문자열 필드로 추출 됩니다.

> [!NOTE]
> Azure Cognitive Search는 가격 책정 계층에 따라 추출 되는 텍스트의 양을 제한 합니다. 무료 계층의 경우 32000 자, 400만 Basic의 경우 64000, standard S2의 경우 800만, 표준 S3의 경우 1600만입니다. 잘린 문서에 대 한 인덱서 상태 응답에 경고가 포함 됩니다.  

* Blob에 있는 사용자 지정 메타 데이터 속성 (있는 경우)은 약어를 사용 하 여 추출 됩니다. 이렇게 하려면 blob의 메타 데이터 키와 동일한 이름의 인덱스에 필드가 정의 되어 있어야 합니다. 예를 들어 blob의 메타 데이터 키가 값 `High`인 `Sensitivity` 경우 검색 인덱스에 `Sensitivity` 이라는 필드를 정의 해야 하며 `High`값으로 채워집니다.
* 표준 blob 메타 데이터 속성은 다음 필드로 추출 됩니다.

  * **메타 데이터\_저장소\_이름** (Edm. 문자열)-blob의 파일 이름입니다. 예를 들어 blob/my-container/my-folder/subfolder/resume.pdf 있는 경우이 필드의 값은 `resume.pdf`입니다.
  * **메타 데이터\_저장소\_경로** (Edm. 문자열)-저장소 계정을 포함 하 여 blob의 전체 URI입니다. 예를 들어 `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * blob을 업로드 하는 데 사용한 코드에 지정 된 대로 **메타 데이터\_저장소\_콘텐츠\_형식** (Edm. 문자열)-콘텐츠 형식입니다. 예를 들어 `application/octet-stream`합니다.
  * **메타 데이터\_저장소\_마지막\_수정** (Edm. DateTimeOffset)-blob의 마지막으로 수정 된 타임 스탬프입니다. Azure Cognitive Search는이 타임 스탬프를 사용 하 여 초기 인덱싱 후 모든 항목을 다시 인덱싱하도록 방지 하기 위해 변경 된 blob를 식별 합니다.
  * **메타 데이터\_저장소\_크기** (Edm. Int64)-blob 크기 (바이트)입니다.
  * **메타 데이터\_저장소\_콘텐츠\_md5** (Edm. 문자열)-BLOB 콘텐츠의 md5 해시 (있는 경우)입니다.
  * **메타 데이터\_저장소\_sas\_토큰** (Edm. 문자열)-blob에 대 한 액세스 권한을 얻기 위해 [사용자 지정 기술](cognitive-search-custom-skill-interface.md) 에서 사용할 수 있는 임시 sas 토큰입니다. 이 토큰은 만료 될 수 있으므로 나중에 사용할 수 있도록 저장 해서는 안 됩니다.

* 각 문서 형식과 관련 된 메타 데이터 속성은 [여기](#ContentSpecificMetadata)에 나열 된 필드로 추출 됩니다.

검색 인덱스에서 위의 모든 속성에 대해 필드를 정의할 필요는 없습니다. 응용 프로그램에 필요한 속성만 캡처합니다.

> [!NOTE]
> 기존 인덱스의 필드 이름은 문서 추출 중에 생성 되는 필드 이름과 다를 수 있습니다. **필드 매핑을** 사용 하 여 Azure Cognitive Search에서 제공 하는 속성 이름을 검색 인덱스의 필드 이름에 매핑할 수 있습니다. 아래의 필드 매핑 사용 예를 볼 수 있습니다.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>문서 키 및 필드 매핑 정의
Azure Cognitive Search에서 문서 키는 문서를 고유 하 게 식별 합니다. 모든 검색 인덱스에는 정확히 하나의 Edm. String 형식의 키 필드가 있어야 합니다. 키 필드는 인덱스에 추가 되는 각 문서에 필요 합니다 (실제로 유일 하 게 필요한 필드).  

인덱스의 키 필드에 매핑할 추출 된 필드를 신중 하 게 고려해 야 합니다. 후보는 다음과 같습니다.

* **메타 데이터\_저장소\_이름** -이는 편리한 후보가 될 수 있지만 1) 이름은 고유 하지 않을 수 있습니다. 즉, 다른 폴더에 동일한 이름의 blob이 있을 수 있습니다. 2) 이름에 대시와 같은 문서 키에 사용할 수 없는 문자가 포함 될 수 있습니다. `base64Encode` [필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction) 를 사용 하 여 잘못 된 문자를 처리할 수 있습니다. 이렇게 하면 조회와 같은 API 호출에서 문서 키를 전달할 때 문서 키를 인코딩해야 합니다. 예를 들어 .NET에서는 해당 목적으로 [Urltokenencode 메서드](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) 를 사용할 수 있습니다.
* **메타 데이터\_저장소\_경로** -전체 경로를 사용 하면 고유성이 보장 되지만 [문서 키에 유효 하지](https://docs.microsoft.com/rest/api/searchservice/naming-rules)않은 `/` 문자를 경로에 포함 합니다.  위에서 설명한 것 처럼 `base64Encode` [함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용 하 여 키를 인코딩할 수 있습니다.
* 위의 옵션을 사용할 수 없는 경우 blob에 사용자 지정 메타 데이터 속성을 추가할 수 있습니다. 그러나이 옵션을 선택 하는 경우에는 blob 업로드 프로세스가 모든 blob에 메타 데이터 속성을 추가 해야 합니다. 키가 필수 속성 이므로 해당 속성이 없는 모든 blob는 인덱싱되지 않습니다.

> [!IMPORTANT]
> 인덱스의 키 필드에 대 한 명시적 매핑이 없는 경우 Azure Cognitive Search는 자동으로 `metadata_storage_path`를 키로 사용 하 고 64을 사용 하 여 키 값을 인코딩합니다 (위의 두 번째 옵션).
>
>

이 예에서는 `metadata_storage_name` 필드를 문서 키로 선택 하겠습니다. 인덱스에 `key` 라는 키 필드가 있고 문서 크기를 저장 하는 데 사용할 수 있는 `fileSize` 필드가 있다고 가정 합니다. 원하는 대로 연결 하려면 인덱서를 만들거나 업데이트할 때 다음 필드 매핑을 지정 합니다.

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

이를 모두 함께 사용 하려면 다음과 같이 필드 매핑을 추가 하 고 기존 인덱서에 키의 base-64 인코딩을 사용 하도록 설정 합니다.

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> 필드 매핑에 대해 자세히 알아보려면 [이 문서](search-indexer-field-mappings.md)를 참조 하세요.
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>인덱싱되는 blob 제어
인덱싱되는 blob 및 건너뛰는 blob을 제어할 수 있습니다.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>특정 파일 확장명을 가진 blob만 인덱싱합니다.
`indexedFileNameExtensions` 인덱서 구성 매개 변수를 사용 하 여 지정한 파일 이름 확장명을 가진 blob만 인덱싱할 수 있습니다. 값은 쉼표로 구분 된 파일 확장명 목록 (선행 점 포함)을 포함 하는 문자열입니다. 예를 들어를 인덱싱합니다. PDF 및 .DOCX blob, 다음을 수행 합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>특정 파일 확장명을 가진 blob 제외
`excludedFileNameExtensions` 구성 매개 변수를 사용 하 여 인덱싱에서 특정 파일 이름 확장명을 가진 blob을 제외할 수 있습니다. 값은 쉼표로 구분 된 파일 확장명 목록 (선행 점 포함)을 포함 하는 문자열입니다. 예를 들어를 사용 하 여 모든 blob을 인덱싱하는 경우입니다. PNG 및 JPEG 확장,이 작업을 수행 합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

`indexedFileNameExtensions` 및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 Azure Cognitive Search 먼저 `indexedFileNameExtensions`를 확인 한 다음 `excludedFileNameExtensions`합니다. 즉, 두 목록에 동일한 파일 확장명이 있는 경우 인덱싱에서 제외 됩니다.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>인덱싱할 blob 부분 제어

`dataToExtract` 구성 매개 변수를 사용 하 여 인덱싱할 blob의 부분을 제어할 수 있습니다. 다음 값을 사용할 수 있습니다.

* `storageMetadata`- [표준 blob 속성 및 사용자 지정 메타](../storage/blobs/storage-properties-metadata.md) 데이터만 인덱싱됩니다.
* `allMetadata`-저장소 메타 데이터 및 blob 콘텐츠에서 추출 된 [content-type 특정 메타 데이터가](#ContentSpecificMetadata) 인덱싱되는 것을 지정 합니다.
* `contentAndMetadata`-blob에서 추출 된 모든 메타 데이터 및 텍스트 콘텐츠가 인덱싱되는 것을 지정 합니다. 이 값은 기본값입니다.

예를 들어 저장소 메타 데이터만 인덱싱 하려면 다음을 사용 합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Blob 메타 데이터를 사용 하 여 blob 인덱싱 방법 제어

위에 설명 된 구성 매개 변수는 모든 blob에 적용 됩니다. 경우에 따라 *개별 blob* 이 인덱싱되는 방식을 제어 하는 것이 좋습니다. 다음 blob 메타 데이터 속성 및 값을 추가 하 여이 작업을 수행할 수 있습니다.

| 속성 이름 | 속성 값 | 보고 |
| --- | --- | --- |
| AzureSearch_Skip |true |Blob을 완전히 건너뛰도록 blob 인덱서를 지시 합니다. 메타 데이터와 콘텐츠 추출을 모두 시도 하지 않습니다. 특정 blob이 반복적으로 실패 하 고 인덱싱 프로세스를 중단 하는 경우에 유용 합니다. |
| AzureSearch_SkipContent |true |이는 [위에서](#PartsOfBlobToIndex) 설명한 대로 특정 blob으로 범위가 지정 된 `"dataToExtract" : "allMetadata"` 설정에 해당 합니다. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>오류 처리

기본적으로 blob 인덱서는 지원 되지 않는 콘텐츠 형식 (예: 이미지)의 blob을 발견 하는 즉시 중지 됩니다. 물론 `excludedFileNameExtensions` 매개 변수를 사용 하 여 특정 내용 유형을 건너뛸 수 있습니다. 그러나 가능한 모든 콘텐츠 형식을 미리 알지 못해도 blob을 인덱싱하는 경우도 있습니다. 지원 되지 않는 콘텐츠 형식이 발견 될 때 인덱싱을 계속 하려면 `failOnUnsupportedContentType` 구성 매개 변수를 `false`으로 설정 합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

일부 blob의 경우 Azure Cognitive Search에서 콘텐츠 형식을 확인할 수 없거나 지원 되지 않는 콘텐츠 형식의 문서를 처리할 수 없습니다. 이 오류 모드를 무시 하려면 `failOnUnprocessableDocument` 구성 매개 변수를 false로 설정 합니다.

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Cognitive Search은 인덱싱되는 blob의 크기를 제한 합니다. 이러한 한도는 [Azure Cognitive Search의 서비스 제한](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)사항에 설명 되어 있습니다. 크기가 큰 blob은 기본적으로 오류로 처리 됩니다. 그러나 `indexStorageMetadataOnlyForOversizedDocuments` 구성 매개 변수를 true로 설정한 경우에도 크기가 큰 blob의 저장소 메타 데이터를 인덱싱할 수 있습니다. 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Blob을 구문 분석 하거나 인덱스에 문서를 추가 하는 동안 처리 중에 오류가 발생 하는 경우에도 인덱싱을 계속할 수 있습니다. 특정 오류 수를 무시 하려면 `maxFailedItems` 및 `maxFailedItemsPerBatch` 구성 매개 변수를 원하는 값으로 설정 합니다. 예를 들어:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>증분 인덱싱 및 삭제 검색
일정에 따라 실행 되도록 blob 인덱서를 설정 하는 경우 blob의 `LastModified` 타임 스탬프에 따라 결정 된 대로 변경 된 blob만 다시 인덱싱합니다.

> [!NOTE]
> 변경 검색 정책을 지정할 필요가 없습니다. 증분 인덱싱이 자동으로 사용 됩니다.

문서 삭제를 지원 하려면 "일시 삭제" 방법을 사용 합니다. Blob을 완전히 삭제 하는 경우 해당 문서는 검색 인덱스에서 제거 되지 않습니다. 대신 다음 단계를 사용 합니다.  

1. Blob에 사용자 지정 메타 데이터 속성을 추가 하 여 논리적으로 삭제 됨을 Azure Cognitive Search에 표시 합니다.
2. 데이터 원본에 대 한 일시 삭제 검색 정책 구성
3. 인덱서가 blob을 처리 한 후 (인덱서 상태 API에 표시 됨) blob을 물리적으로 삭제할 수 있습니다.

예를 들어 다음 정책은 `true`값이 `IsDeleted` 메타 데이터 속성이 있는 경우 blob를 삭제 된 것으로 간주 합니다.

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>대량 데이터 집합 인덱싱

인덱싱 blob은 시간이 많이 걸리는 프로세스 일 수 있습니다. 인덱스를 만들 수백만 개의 blob이 있는 경우 데이터를 분할 하 고 여러 인덱서를 사용 하 여 데이터를 병렬로 처리 함으로써 인덱싱 속도를 높일 수 있습니다. 이를 설정 하는 방법은 다음과 같습니다.

- 데이터를 여러 blob 컨테이너 또는 가상 폴더로 분할
- 컨테이너 또는 폴더 마다 하나씩 여러 Azure Cognitive Search 데이터 원본을 설정 합니다. Blob 폴더를 가리키도록 `query` 매개 변수를 사용 합니다.

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- 각 데이터 원본에 해당 하는 인덱서를 만듭니다. 모든 인덱서는 동일한 대상 검색 인덱스를 가리킬 수 있습니다.  

- 서비스의 한 검색 단위는 지정 된 시간에 하나의 인덱서를 실행할 수 있습니다. 위에서 설명한 대로 여러 인덱서를 만드는 것은 실제로 병렬로 실행 되는 경우에만 유용 합니다. 여러 인덱서를 병렬로 실행 하려면 적절 한 수의 파티션 및 복제본을 만들어 검색 서비스를 확장 합니다. 예를 들어 search 서비스에 6 개의 검색 단위 (예: 2 개의 파티션 x 3 개)가 있는 경우 6 개의 인덱서가 동시에 실행 될 수 있으며,이로 인해 인덱싱 처리량이 6 배 늘어납니다. 크기 조정 및 용량 계획에 대 한 자세한 내용은 [Azure Cognitive Search의 쿼리 및 인덱싱 작업을 위한 리소스 수준 확장](search-capacity-planning.md)을 참조 하세요.

## <a name="indexing-documents-along-with-related-data"></a>관련 된 데이터와 함께 문서 인덱싱

인덱스의 여러 소스에서 문서를 "조합" 할 수 있습니다. 예를 들어 Cosmos DB에 저장 된 다른 메타 데이터와 blob의 텍스트를 병합 하는 것이 좋습니다. 다양 한 인덱서와 함께 푸시 인덱싱 API를 사용 하 여 여러 부분에서 검색 문서를 빌드할 수도 있습니다. 

이 작업을 수행 하려면 모든 인덱서 및 기타 구성 요소가 문서 키에 동의 해야 합니다. 이 항목에 대 한 자세한 내용은 [여러 Azure 데이터 원본 인덱싱](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources)을 참조 하세요. 자세한 연습을 보려면이 외부 문서: [Azure Cognitive Search의 다른 데이터와 문서 결합](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)을 참조 하세요.

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>일반 텍스트 인덱싱 

모든 blob에 동일한 인코딩의 일반 텍스트가 포함 된 경우 **텍스트 구문 분석 모드**를 사용 하 여 인덱싱 성능을 크게 향상 시킬 수 있습니다. 텍스트 구문 분석 모드를 사용 하려면 `parsingMode` 구성 속성을 `text`로 설정 합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

기본적으로 `UTF-8` 인코딩은로 가정 됩니다. 다른 인코딩을 지정 하려면 `encoding` 구성 속성을 사용 합니다. 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>콘텐츠 형식별 메타 데이터 속성
다음 표에서는 각 문서 형식에 대 한 처리 작업을 요약 하 고 Azure Cognitive Search에서 추출 된 메타 데이터 속성을 설명 합니다.

| 문서 형식/내용 유형 | Content-type 별 메타 데이터 속성 | 처리 세부 정보 |
| --- | --- | --- |
| HTML (텍스트/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML 태그를 제거 하 고 텍스트 추출 |
| PDF (응용 프로그램/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |포함 된 문서를 포함 하 여 텍스트 추출 (이미지 제외) |
| .DOCX (application/vnd. wordprocessingml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함 된 문서를 포함 하 여 텍스트 추출 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함 된 문서를 포함 하 여 텍스트 추출 |
| .DOCM (application/vnd. macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함 된 문서를 포함 하 여 텍스트 추출 |
| WORD XML (application/vnd word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML 태그를 제거 하 고 텍스트 추출 |
| WORD 2003 XML (application/vnd. ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML 태그를 제거 하 고 텍스트 추출 |
| .XLSX (application/vnd. vnd.openxmlformats-officedocument.spreadsheetml.sheet. vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함 된 문서를 포함 하 여 텍스트 추출 |
| XLS (application/vnd. vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함 된 문서를 포함 하 여 텍스트 추출 |
| .XLSM (application/vnd. vnd.ms-excel. macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함 된 문서를 포함 하 여 텍스트 추출 |
| .PPTX (application/vnd. presentationml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함 된 문서를 포함 하 여 텍스트 추출 |
| PPT (application/vnd. ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함 된 문서를 포함 하 여 텍스트 추출 |
| PPTM (application/vnd. ms-powerpoint macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함 된 문서를 포함 하 여 텍스트 추출 |
| MSG (application/vnd. ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |첨부 파일을 포함 하는 텍스트를 추출 합니다. `metadata_message_to_email`, `metadata_message_cc_email` 및 `metadata_message_bcc_email`는 문자열 컬렉션입니다. 나머지 필드는 문자열입니다.|
| ODT (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함 된 문서를 포함 하 여 텍스트 추출 |
| ODS (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함 된 문서를 포함 하 여 텍스트 추출 |
| ODP (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |포함 된 문서를 포함 하 여 텍스트 추출 |
| ZIP (응용 프로그램/zip) |`metadata_content_type` |보관 파일에 있는 모든 문서에서 텍스트 추출 |
| RELEASE.TAR.GZ (응용 프로그램/gzip) |`metadata_content_type` |보관 파일에 있는 모든 문서에서 텍스트 추출 |
| EPUB (application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |보관 파일에 있는 모든 문서에서 텍스트 추출 |
| XML (응용 프로그램/x m l) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML 태그를 제거 하 고 텍스트 추출 |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |텍스트 추출<br/>참고: JSON blob에서 여러 문서 필드를 추출 해야 하는 경우 자세한 내용은 [json Blob 인덱싱](search-howto-index-json-blobs.md) 을 참조 하세요. |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |첨부 파일을 포함 한 텍스트 추출 |
| RTF (application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 텍스트 추출|
| 일반 텍스트 (텍스트/일반) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 텍스트 추출|


## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search 향상에 도움을 주세요.
기능 요청 또는 개선에 대 한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 알려주세요.
