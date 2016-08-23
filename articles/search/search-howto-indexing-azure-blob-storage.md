<properties
pageTitle="Azure 검색으로 Azure Blob 저장소 인덱싱"
description="Azure Blob 저장소를 인덱싱하고 Azure 검색을 사용하여 문서에서 텍스트를 추출하는 방법에 대해 알아보세요."
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/08/2016"
ms.author="eugenesh" />

# Azure 검색으로 Azure Blob 저장소에서 문서 인덱싱

이 문서에서는 Azure 검색을 사용하여 Azure Blob 저장소에 저장된 문서(예: PDF, Office 파일 및 다양한 기타 일반적인 형식)를 인덱싱하는 방법을 보여줍니다. 새로운 Azure 검색 Blob 인덱서로 이 과정을 신속하게 원활하게 수행할 수 있습니다.

> [AZURE.IMPORTANT] 이 기능은 현재 미리 보기 상태입니다. **2015-02-28-Preview** 버전을 사용하여 REST API로만 제공됩니다. 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않는다는 점을 유념하세요.

## 지원되는 문서 형식

BLOB 인덱서는 다음과 같은 문서 형식에서 텍스트를 추출할 수 있습니다.

- PDF
- Microsoft Office 형식: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG(Outlook 전자 메일)
- HTML
- XML
- ZIP
- EML
- 일반 텍스트 파일
- JSON(자세한 내용은 [JSON BLOB 인덱싱](search-howto-index-json-blobs.md) 참조)
- CSV (자세한 내용은 [CSV BLOB 인덱싱](search-howto-index-csv-blobs.md) 참조)

## BLOB 인덱싱 설정

Azure Blob 저장소 인덱서를 설정 및 구성하려면 [이 문서](https://msdn.microsoft.com/library/azure/dn946891.aspx)에 설명된 대로 Azure 검색 REST API를 사용하여 **인덱서** 및 **데이터 원본**을 만들고 관리할 수 있습니다. 향후에는 BLOB 인덱싱에 대한 지원이 Azure 검색 .NET SDK 및 Azure 포털에 추가될 예정입니다.

인덱서를 설정하려면, 데이터 원본 만들기, 인덱스 만들기, 인덱서 구성의 세 단계를 수행합니다.

### 1단계: 데이터 소스 만들기

데이터 원본은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명, Azure 검색에서 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 데이터 원본을 동일한 구독의 여러 인덱서에서 사용할 수 있습니다.

데이터 원본에는 BLOB 인덱싱을 위한 다음과 같은 필수 속성이 있어야 합니다.

- **이름**은 검색 서비스 내 데이터 원본의 고유 이름입니다.

- **형식**은 `azureblob`여야 합니다.

- **자격 증명**은 저장소 계정 연결 문자열을 `credentials.connectionString` 매개 변수로 제공합니다. Azure 포털에서 연결 문자열을 가져올 수 있습니다. 이를 위해 원하는 저장소 계정 블레이드 > **설정** > **키** 로 이동하고 "기본 연결 문자열" 또는 "보조 연결 문자열" 값을 사용합니다. 연결 문자열이 저장소 계정에 바인딩되어 있으므로 연결 문자열을 지정하는 것은 데이터를 제공하는 저장소 계정을 암시적으로 밝히는 것과 같습니다.

- **컨테이너**는 저장소 계정에 있는 컨테이너를 지정합니다. 기본적으로 컨테이너 내의 모든 BLOB은 검색 가능합니다. 특정 가상 디렉터리의 BLOB만 인덱싱하려면 선택 사항인 **쿼리** 매개 변수를 사용하여 해당 디렉터리를 지정할 수 있습니다,

다음 예제에서는 데이터 원본 정의을 설명합니다.

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
	}   

데이터 원본 만들기 API에 대한 자세한 내용은 [데이터 원본 만들기](search-api-indexers-2015-02-28-preview.md#create-data-source)를 참조하세요.

### 2단계: 인덱스 만들기 

인덱스는 문서의 필드, 특성, 및 검색 경험을 형성하는 기타 항목을 지정합니다.

BLOB 인덱싱에 대해 인덱스에 BLOB을 저장하기 위한 검색 가능한 `content` 필드가 있는지 확인합니다.

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
  		]
	}

인덱스 만들기 API에 대한 자세한 내용은 [인덱스 만들기](https://msdn.microsoft.com/library/dn798941.aspx) 참조

### 3단계: 인덱서 만들기 

인덱서는 데이터 새로 고침을 자동화할 수 있게 데이터 원본을 대상 검색 인덱스에 연결하고 일정 정보를 제공합니다. 인덱스 및 데이터 원본이 만들어지면 데이터 원본 및 대상 인덱스를 참조하는 인덱서를 만드는 것은 비교적 간단합니다. 예:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : "blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](search-api-indexers-2015-02-28-preview.md#create-indexer)를 확인하세요.


## 문서 추출 프로세스

Azure 검색은 각 문서(BLOB)를 다음과 같이 인덱싱합니다.

- 문서의 전체 텍스트 내용이 `content`라는 문자열 필드로 추출됩니다. 현재는 단일 BLOB에서 여러 문서를 추출하는 것은 지원되지 않습니다.
	- 예를 들어, CSV 파일은 단일 문서로 인덱싱됩니다. CSV의 각 라인을 별도의 문서로 취급해야 하는 경우 [이 UserVoice 제안](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate)에 투표하세요.
	- 복합 또는 포함된 문서(예: ZIP 보관 파일 또는 PDF 첨부 파일이 있는 Outlook 메일이 포함된 Word 문서)도 단일 문서로 인덱싱됩니다.

- BLOB에 있는 사용자 지정 메타데이터 속성은 그대로 추출됩니다(있는 경우). 메타데이터 속성은 문서 추출 프로세스의 특정 부분을 제어하는 데 사용할 수 있습니다. 자세한 내용은 [사용자 지정 메타데이터를 사용하여 문서 추출 제어](#CustomMetadataControl)를 참조하세요.

- 표준 BLOB 메타데이터 속성이 다음 필드로 추출됩니다.

	- **metadata\_storage\_name**(Edm.String) - BLOB의 파일 이름. 예를 들어 blob /my-container/my-folder/subfolder/resume.pdf를 포함하는 경우 이 필드의 값은 `resume.pdf`입니다.

	- **metadata\_storage\_path**(Edm.String) - 저장소 계정을 포함한 BLOB의 전체 URI. 예: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

	- **metadata\_storage\_content\_type**(Edm.String) - BLOB를 업로드하기 위해 사용한 코드에 지정된 콘텐츠 형식. 예: `application/octet-stream`

	- **metadata\_storage\_last\_modified**(Edm.DateTimeOffset) - BLOB에 대해 마지막으로 수정된 타임스탬프. Azure 검색은 이 타임스탬프로 변경된 BLOB를 식별하여 초기 인덱싱 후 모든 항목을 다시 인덱싱하지 않도록 합니다.

	- **metadata\_storage\_size**(Edm.Int64) - BLOB 크기(바이트).

	- **metadata\_storage\_content\_md5**(Edm.String) - BLOB 콘텐츠의 MD5 해시(사용 가능한 경우).

- 각 문서 형식과 관련된 메타데이터 속성이 [여기](#ContentSpecificMetadata) 나열된 필드로 추출됩니다.

검색 인덱스에서 위의 모든 속성에 대한 필드를 정의하지 않아도 되는 경우 응용 프로그램에 필요한 속성만 캡처합니다.

> [AZURE.NOTE] 기존 인덱스의 필드 이름이 문서 추출 중에 생성된 필드 이름과 달라지는 경우가 있습니다. **필드 매핑**을 사용하여 Azure 검색에서 제공한 속성 이름을 검색 인덱스의 필드 이름에 매핑할 수 있습니다. 아래에 필드 매핑 사용 예제가 있습니다.

## 문서 키 필드 선택 및 다른 필드 이름 처리

Azure 검색에서는 문서 키가 문서를 고유하게 식별합니다. 모든 검색 인덱스는 Edm.String 형식의 키 필드를 정확히 하나만 포함해야 합니다. 인덱스에 추가할 각 문서에는 키 필드가 필요합니다(이 필드는 실제로 유일한 필수 필드임).
   
어떤 추출된 필드를 인덱스에 대한 키 필드에 매핑할지 신중하게 고려해야 합니다. 후보는 다음과 같습니다.

- **metadata\_storage\_name** - 편리한 후보일 수 있으나 1) 다른 폴더에 같은 이름을 가진 BLOB를 포함할 수 있으므로 이름이 고유하지 않을 수 있으며 2) 이름에 대시와 같은 문서 키로 유효하지 않은 문자가 포함될 수 있습니다. 인덱서 속성에서 `base64EncodeKeys` 옵션을 사용하여 유효하지 않은 문자를 처리할 수 있습니다. 이렇게 하면 Lookup과 같은 API 호출에 전달할 때 문서 키를 인코딩해야 합니다. (예를 들어, .NET에서 이러한 용도로 [UrlTokenEncode 메서드](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) 를 사용할 수 있습니다).

- **metadata\_storage\_path** - 전체 경로를 사용하여 고유성을 보장할 수 있지만 해당 경로에 [문서 키로 유효하지 않은](https://msdn.microsoft.com/library/azure/dn857353.aspx) `/` 문자가 분명히 포함됩니다. 위와 같이 `base64EncodeKeys` 옵션을 사용하여 키를 인코딩하는 옵션이 제공됩니다.

- 위의 옵션이 작동하지 않는 경우 BLOB에 사용자 지정 메타데이터 속성을 추가하는 뛰어난 유연성이 제공됩니다. 그러나 이 옵션에는 해당 메타데이터 속성을 모든 BLOB에 추가하는 BLOB 업로드 프로세스가 필요합니다. 키는 필수 속성이므로 해당 속성이 없는 모든 BLOB는 인덱싱에 실패합니다.

> [AZURE.IMPORTANT] 인덱스의 키 필드에 대한 명시적인 매핑이 없는 경우 Azure 검색은 키로 `metadata_storage_path`(위의 두 번째 옵션)를 자동으로 사용하고 키의 base-64 인코딩을 사용합니다.

이 예제에서는 문서 키로 `metadata_storage_name` 필드를 선택하겠습니다. 또한 인덱스에 `key` 키 필드와 문서 크기를 저장하는 `fileSize` 필드가 있다고 가정해보겠습니다. 원하는 대로 연결하려면 인덱서를 만들거나 업데이트할 때 다음 필드 매핑을 지정합니다.

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

이를 모두 함께 연결하기 위해 필드 매핑을 추가하고 기존 인덱서에 대한 키의 base-64 인코딩을 사용하도록 설정하는 방법은 다음과 같습니다.

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

> [AZURE.NOTE] 필드 매핑에 대한 자세한 내용은 [이 문서](search-indexer-field-mappings.md)를 참조하세요.

## 증분 인덱싱 및 삭제 감지

일정에 따라 실행할 BLOB 인덱서가 일정에 따라 실행되도록 설정하는 경우 BLOB의 `LastModified` 타임스탬프에 지정된 대로 변경된 BLOB만 다시 인덱싱합니다.

> [AZURE.NOTE] 변경 감지 정책을 지정하지 않아도 됩니다. 증분 인덱싱이 자동으로 사용됩니다.

인덱스에서 특정 문서를 제거해야 함을 나타내려면 소프트 삭제 전략을 사용합니다. 해당 BLOB를 삭제하는 대신, 삭제됨을 나타내는 사용자 지정 메타데이터 속성을 추가하고 데이터 원본에 대한 소프트 삭제 감지 정책을 설정합니다.

> [AZURE.WARNING] 삭제 감지 정책을 사용하는 대신 BLOB를 삭제하려면 해당 문서가 검색 인덱스에서 제거되지 않습니다.

예를 들어 아래에 표시된 정책은 `true` 값의 메타데이터 속성 `IsDeleted`가 있는 경우 BLOB이 삭제됨을 고려합니다.

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
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

<a name="ContentSpecificMetadata"></a>
## 콘텐츠 형식별 메타데이터 속성

다음 표에서는 각 문서 형식에 대해 수행된 처리를 요약하고 Azure 검색에서 추출한 메타데이터 속성에 대해 설명합니다.

문서 형식/콘텐츠 형식 | 콘텐츠 형식별 메타데이터 속성 | 처리 세부 정보
-------------------------------|-------------------------------------------|-------------------
HTML(`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | HTML 태그를 제거하고 텍스트 추출
PDF(`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| 포함된 문서를 비롯한 텍스트 추출(이미지 제외)
DOCX(application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | 포함된 문서를 비롯한 텍스트 추출
DOC(application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | 포함된 문서를 비롯한 텍스트 추출
XLSX(application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | 포함된 문서를 비롯한 텍스트 추출
XLS(application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | 포함된 문서를 비롯한 텍스트 추출
PPTX(application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | 포함된 문서를 비롯한 텍스트 추출
PPT(application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | 포함된 문서를 비롯한 텍스트 추출
MSG(application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | 첨부 파일을 비롯한 텍스트 추출
ZIP(application/zip) | `metadata_content_type` | 보관 파일의 모든 문서에서 텍스트 추출
XML(application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | XML 태그를 제거하고 텍스트 추출
JSON(application/json) | `metadata_content_type`</br>`metadata_content_encoding` | 텍스트 추출<br/>참고: JSON BLOB에서 여러 문서 필드를 추출해야 하는 경우 자세한 내용은 [JSON BLOB 인덱싱](search-howto-index-json-blobs.md)을 참조하세요.
EML(메시지/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | 첨부 파일을 비롯한 텍스트 추출
일반 텍스트(text/plain) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## 사용자 지정 메타데이터를 사용하여 문서 추출 제어

BLOB에 BLOB 인덱싱 및 문서 추출 프로세스의 특정 측면을 제어하는 메타데이터 속성을 추가할 수 있습니다. 현재는 다음과 같은 속성이 지원됩니다.

속성 이름 | 속성 값 | 설명
--------------|----------------|------------
AzureSearch\_Skip | "true" | BLOB 인덱서에 BLOB를 완전히 건너뛰도록 지시합니다. 메타데이터와 콘텐츠 추출을 시도하지 않습니다. 특정 콘텐츠 형식을 건너뛰거나 특정 BLOB가 반복적으로 실패하고 인덱싱 프로세스를 중단하는 경우 유용합니다.
AzureSearch\_SkipContent | "true" | Blob 인덱서에게 메타데이터만 인덱싱하고 Blob의 콘텐츠를 압축 해제하는 과정을 건너뛰도록 지시합니다. Blob 콘텐츠에는 관심이 없지만 Blob에 연결된 메타데이터는 인덱싱하려는 경우 유용합니다.

<a name="IndexerParametersConfigurationControl"></a>
## 인덱서 매개 변수를 사용하여 문서 추출 제어

인덱싱될 Blob 및 Blob의 콘텐츠 및 메타데이터 부분을 제어하는 데 여러 인덱서 구성 매개 변수를 사용할 수 있습니다.

### 특정 파일 확장명을 가진 Blob만 인덱싱

`indexedFileNameExtensions` 인덱서 구성 매개 변수를 사용하여 지정한 파일 이름 확장명을 가진 Blob만 인덱싱할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PDF 및 .DOCX Blob만을 인덱싱하려면 다음을 수행합니다.

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
	}

### 인덱싱에서 특정 파일 확장명으로 Blob 제외

`excludedFileNameExtensions` 구성 매개 변수를 사용하여 인덱싱에서 특정 파일 이름 확장명으로 Blob를 제외할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PNG 및 .JPEG 확장명을 가진 Blob을 제외한 모든 Blob을 인덱싱하려면 다음을 수행합니다.

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
	}

`indexedFileNameExtensions` 및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 Azure 검색은 먼저 `indexedFileNameExtensions`를 확인한 후 `excludedFileNameExtensions`를 찾습니다. 동일한 파일 확장명이 두 목록 모두에 있는 경우 인덱싱에서 제외되는 것을 의미합니다.

### 저장소 메타데이터만 인덱싱

`indexStorageMetadataOnly` 구성 속성을 사용하여 저장소 메타데이터만을 인덱싱하고 문서 추출 프로세스를 완전히 건너뛸 수 있습니다. 문서 콘텐츠 또는 콘텐츠 형식별 메타데이터 속성이 필요하지 않은 경우에 유용합니다. 이렇게 하려면 `true`에 `indexStorageMetadataOnly` 속성을 설정합니다.

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
	}

### 저장소와 콘텐츠 형식 메타데이터를 인덱싱하지만 콘텐츠 추출은 건너뜀

모든 메타데이터를 추출하려 하지만 모든 Blob에 대한 콘텐츠 추출은 건너뛰어야 하는 경우 각 Blob에 개별적으로 `AzureSearch_SkipContent` 메타데이터를 추가하지 않고도 인덱서 구성을 사용하여 이 동작을 요청할 수 있습니다. 이렇게 하려면 `true`에 `skipContent` 인덱서 구성 속성을 설정합니다.

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "skipContent" : true } }
	}

## Azure 검색 개선 지원

기능 요청 또는 개선에 대한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 연락해 주세요.

<!---HONumber=AcomDC_0810_2016-->