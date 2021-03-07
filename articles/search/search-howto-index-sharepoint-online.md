---
title: SharePoint Online 인덱서 구성 (미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 문서 라이브러리 콘텐츠의 인덱싱을 자동화 하도록 SharePoint Online 인덱서를 설정 합니다.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 22adccfc4adbb7f8b1c72d8b5705ec8fcdb9a375
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441094"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Cognitive Search에서 SharePoint Online 인덱싱을 구성 하는 방법 (미리 보기)

> [!IMPORTANT] 
> SharePoint Online 지원은 현재 **제어 된 공개 미리 보기** 상태입니다. [이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)작성 하 여 제어 되는 미리 보기에 대 한 액세스를 요청할 수 있습니다.
>
> 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 
> [REST API 버전 2020-06-30-미리 보기](search-api-preview.md) 에서이 기능을 제공 합니다. 현재 포털 또는 SDK를 지원 하지 않습니다.

> [!NOTE]
> SharePoint Online은 문서 수준에서 사용자별 액세스를 결정 하는 세분화 된 권한 부여 모델을 지원 합니다. SharePoint Online 인덱서는 이러한 권한을 검색 인덱스에 가져오지 않으며 Cognitive Search는 문서 수준 권한 부여를 지원 하지 않습니다. 문서가 SharePoint Online에서 검색 서비스로 인덱싱되는 경우 인덱스에 대 한 읽기 액세스 권한이 있는 사용자는 해당 콘텐츠를 사용할 수 있습니다. 문서 수준 사용 권한이 필요한 경우에는 보안 필터를 조사 하 여 권한 없는 콘텐츠의 결과를 트리밍합니다. 자세한 내용은 [보안 트리밍 Active Directory id 사용](search-security-trimming-for-azure-search-with-aad.md)을 참조 하세요.

이 문서에서는 Azure Cognitive Search를 사용 하 여 SharePoint Online 문서 라이브러리에 저장 된 문서 (예: Pdf, Microsoft Office 문서 및 다른 몇 가지 일반적인 형식)를 Azure Cognitive Search 인덱스에 인덱싱하는 방법을 설명 합니다. 먼저 인덱서 설정 및 구성에 대 한 기본 사항을 설명 합니다. 그런 다음, 동작 및 발생할 수 있는 시나리오의 심층적 탐색을 제공합니다.

## <a name="functionality"></a>기능
Azure Cognitive Search의 인덱서는 데이터 원본에서 검색 가능한 데이터 및 메타 데이터를 추출 하는 크롤러입니다. SharePoint Online 인덱서는 SharePoint Online 사이트에 연결 하 고 하나 이상의 문서 라이브러리에서 문서를 인덱싱합니다. 인덱서는 다음과 같은 기능을 제공 합니다.
+ 하나 이상의 SharePoint Online 문서 라이브러리에서 콘텐츠를 인덱싱합니다.
+ Azure Cognitive Search 서비스와 동일한 테 넌 트에 있는 SharePoint Online 문서 라이브러리의 콘텐츠를 인덱싱합니다. 인덱서는 Azure Cognitive Search 서비스와 다른 테 넌 트에 있는 SharePoint 사이트에서 작동 하지 않습니다. 
+ 인덱서는 증분 인덱싱을 지원 합니다 .이는 문서 라이브러리에서 변경 된 콘텐츠를 식별 하 고 이후 인덱싱 실행 시 업데이트 된 내용만 인덱싱하는 것을 의미 합니다. 예를 들어 5 개의 Pdf가 원래 인덱서에 의해 인덱싱되는 경우 1이 업데이트 된 다음 인덱서가 다시 실행 되며 인덱서는 업데이트 된 PDF 1 개만 인덱싱합니다.
+ 텍스트 및 정규화 된 이미지는 기본적으로 인덱싱되는 문서에서 추출 됩니다. 필요에 따라 추가 콘텐츠 보강을 위해 파이프라인에 기술를 추가할 수 있습니다. 기술력과에 대 한 자세한 내용은 [Azure Cognitive Search의 기술 개념](cognitive-search-working-with-skillsets.md)문서에서 찾을 수 있습니다.

## <a name="supported-document-formats"></a>지원되는 문서 형식

Azure Cognitive Search SharePoint Online 인덱서는 다음 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>증분 인덱싱 및 삭제 감지
기본적으로 SharePoint Online 인덱서는 증분 인덱싱을 지원 합니다 .이는 문서 라이브러리에서 변경 된 콘텐츠를 식별 하 고 이후 인덱싱 실행 시 업데이트 된 내용만 인덱싱합니다. 예를 들어 5 개의 Word 문서가 처음에 인덱서에 인덱싱되는 경우 1이 업데이트 된 다음 인덱서가 다시 실행 되며 인덱서는 업데이트 된 1 개의 Word 문서만 다시 인덱싱합니다.

삭제 검색도 기본적으로 지원 됩니다. 즉, SharePoint Online 문서 라이브러리에서 문서를 삭제 하는 경우 인덱서는 이후 인덱서를 실행 하는 동안 삭제를 검색 하 고 인덱스에서 문서를 제거 합니다.

## <a name="setting-up-sharepoint-online-indexing"></a>SharePoint Online 인덱싱 설정
SharePoint Online 인덱서를 설정 하려면 Azure Portal에서 일부 작업을 수행 하 고 미리 보기 REST API를 사용 하 여 일부 작업을 수행 해야 합니다. 이 미리 보기는 SDK에서 지원 되지 않습니다.

### <a name="step-1-enable-system-assigned-managed-identity"></a>1 단계: 시스템 할당 관리 id 사용
시스템 할당 관리 id를 사용 하는 경우 Azure는 인덱서에서 사용할 수 있는 검색 서비스에 대 한 id를 만듭니다.

![시스템 할당 관리 id 사용](media/search-howto-index-sharepoint-online/enable-managed-identity.png "시스템 할당 관리 id 사용")

**저장** 을 선택한 후 검색 서비스에 할당된 개체 ID가 표시됩니다.

![시스템 할당 관리 ID](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "시스템 할당 관리 ID")

### <a name="step-2-create-an-aad-application"></a>2 단계: AAD 응용 프로그램 만들기
SharePoint Online 인덱서는 인증을 위해이 AAD 응용 프로그램을 사용 합니다. 

1.  [Azure Portal](https://portal.azure.com/)로 이동합니다.

1.  기본 페이지의 왼쪽에 있는 메뉴를 열고 **Azure Active Directory** 선택한 다음 **앱 등록** 를 선택 합니다. **+ 새로 만들기 등록** 을 선택 합니다.
    1.  앱에 대 한 이름을 제공 합니다.
    2.  **단일 테 넌 트** 를 선택 합니다.
    3.  리디렉션 URI가 필요 하지 않습니다.
    4.  **등록** 을 선택합니다.

1.  왼쪽의 메뉴에서 **API 권한** 을 선택 하 고 **사용 권한을 추가한** 다음 위임 된 **권한** **Microsoft Graph** 합니다. 다음 API 권한을 추가 합니다. 
    1.  **위임 된 파일. 모두 읽기** 
    2.  **위임 된 사이트. 모두 읽기** 
    3.  **위임 됨-사용자 읽기**

    ![위임 된 API 사용 권한](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "위임 된 API 사용 권한")

    위임 된 권한을 사용 하면 인덱서가 사용자 컨텍스트에서 SharePoint 사이트에 액세스 하 게 됩니다. 따라서 인덱서를 실행 하는 경우 로그인 한 사용자가 액세스할 수 있는 콘텐츠에만 액세스할 수 있습니다. 사용자 로그인은 인덱서를 만들거나 날짜 원본을 업데이트할 때 발생 합니다. 로그인 단계는이 문서의 뒷부분에 설명 되어 있습니다.

1.  **인증** 탭을 선택 합니다. **공용 클라이언트 흐름 허용** 을 **예** 로 설정 하 고 **저장** 을 선택 합니다.

1.  **+ 플랫폼 추가**, **모바일 및 데스크톱 응용 프로그램**, 확인 `https://login.microsoftonline.com/common/oauth2/nativeclient` , **구성** 을 차례로 선택 합니다.

    ![AAD 앱 인증 구성](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "AAD 앱 인증 구성")

1.  관리자 동의를 제공 합니다 (특정 테 넌 트에만 필요).

    일부 테 넌 트는 이러한 위임 된 API 권한에 대해 관리자 동의가 필요한 방식으로 잠깁니다. 이 경우 인덱서를 만들기 전에 관리자에 게이 AAD 응용 프로그램에 대 한 동의를 부여 해야 합니다. 

    모든 테 넌 트가이 요구 사항이 없기 때문에 먼저이 단계를 건너뛰고 지침을 계속 진행 하는 것이 좋습니다. 인덱서를 만들 때 관리자 동의가 필요한 지 여부를 알 수 있습니다. 인증을 승인 하려면 관리자가 필요 하다는 메시지가 표시 됩니다. 이 경우 테 넌 트 관리자는 아래 단추를 사용 하 여 동의를 부여 합니다.

    ![AAD 앱 부여 관리자 동의](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD 앱 부여 관리자 동의")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>3 단계: 데이터 원본 만들기
> [!IMPORTANT] 
> 이 섹션에서 시작 하 여 나머지 단계에 대해 미리 보기 REST API를 사용 해야 합니다. Azure Cognitive Search REST API에 익숙하지 않은 경우이 [빠른](search-get-started-rest.md)시작을 살펴보는 것이 좋습니다.

데이터 원본은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명 및 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 데이터 원본을 동일한 검색 서비스의 여러 인덱서에서 사용할 수 있습니다.

SharePoint 인덱싱의 경우 데이터 원본에 다음과 같은 필수 속성이 있어야 합니다.
+ **name** 은 검색 서비스 내 데이터 원본의 고유 이름입니다.
+ **유형은** "sharepoint" 여야 합니다. 이는 대/소문자를 구분 합니다.
+ **자격 증명** 은 SharePoint ONLINE 끝점과 AAD 응용 프로그램 (클라이언트) ID를 제공 합니다. SharePoint Online 끝점의 예는 `https://microsoft.sharepoint.com/teams/MySharePointSite` 입니다. Sharepoint 사이트의 홈 페이지로 이동 하 고 브라우저에서 URL을 복사 하 여 SharePoint Online 끝점을 가져올 수 있습니다.
+ **컨테이너** 인덱싱할 문서 라이브러리를 지정 합니다. 컨테이너를 만드는 방법에 대 한 자세한 내용은이 문서의 [인덱싱되는 문서 제어](#controlling-which-documents-are-indexed) 섹션에서 찾을 수 있습니다.

데이터 원본을 만드는 방법:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>4 단계: 인덱스 만들기
인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

문서 라이브러리의 문서에서 추출 된 텍스트를 저장 하기 위해 검색 가능한 콘텐츠 필드를 사용 하 여 인덱스를 만드는 방법은 다음과 같습니다.

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

자세한 내용은 [인덱스 만들기 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조 하세요.

### <a name="step-5-create-an-indexer"></a>5 단계: 인덱서 만들기
인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다. 인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

이 섹션에서 SharePoint 사이트에 대 한 액세스 권한이 있는 조직 자격 증명을 사용 하 여 로그인 하 라는 메시지가 표시 됩니다. 가능 하면 새 조직 사용자 계정을 만들고 해당 사용자에 게 인덱서에 필요한 정확한 사용 권한을 부여 하는 것이 좋습니다.

인덱서를 만드는 몇 가지 단계가 있습니다.

1.  인덱서를 만들려고 시도 하는 요청을 보냅니다.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index"
        }
    
    ```

1.  처음으로 인덱서를 만들 때 오류가 발생 하 고 다음과 같은 오류가 표시 됩니다. 오류 메시지의 링크로 이동 합니다. 10 분 내에 링크로 이동 하지 않으면 코드가 만료 되므로 [데이터 소스](#create-data-source)를 다시 만들어야 합니다.

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  오류 메시지에 제공 된 코드를 제공 합니다.

    ![장치 코드 입력](media/search-howto-index-sharepoint-online/enter-device-code.png "장치 코드 입력")

1.  SharePoint 인덱서는 로그인 한 사용자로 SharePoint 콘텐츠에 액세스 합니다. 이 단계 중에 로그인 한 사용자는 로그인 한 사용자가 됩니다. 따라서 인덱싱할 문서 라이브러리의 문서에 대 한 액세스 권한이 없는 사용자 계정으로 로그인 하는 경우 해당 문서에 대 한 액세스 권한은 없습니다.

    가능 하면 새 사용자 계정을 만들고 해당 사용자에 게 인덱서에 필요한 정확한 사용 권한을 부여 하는 것이 좋습니다.

1.  요청 된 사용 권한을 승인 합니다.

    ![API 권한 승인](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "API 권한 승인")

1.  인덱서 만들기 요청을 다시 보냅니다. 이번에는 요청이 성공 해야 합니다. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>6 단계: 인덱서 상태 확인
인덱서를 만든 후에는 다음 요청을 수행 하 여 인덱서 상태를 확인할 수 있습니다.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

인덱서 상태에 대 한 자세한 내용은 [인덱서 상태 가져오기](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)를 참조 하세요.

## <a name="updating-the-data-source"></a>데이터 원본 업데이트
데이터 원본 개체에 대 한 업데이트가 없는 경우 인덱서는 사용자 상호 작용 없이 일정에 따라 실행 될 수 있습니다. 그러나 Azure Cognitive Search 데이터 원본 개체가 업데이트 될 때마다 인덱서를 실행 하기 위해 다시 로그인 해야 합니다. 예를 들어 데이터 원본 쿼리를 변경 하는 경우 및 새 코드를 사용 하 여 다시 로그인 해야 합니다 `https://microsoft.com/devicelogin` .

데이터 원본을 업데이트 한 후에는 다음 단계를 수행 합니다.

1.  수동으로 인덱서 실행을 시작 합니다.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    인덱서 실행 요청에 대 한 자세한 내용은 [인덱서 실행](https://docs.microsoft.com/rest/api/searchservice/run-indexer)에서 찾을 수 있습니다.

1.  인덱서 상태를 확인 합니다. 마지막 인덱서 실행에서로 이동 하 라는 오류가 발생 하는 경우 `https://microsoft.com/devicelogin` 해당 페이지로 이동 하 여 새 코드를 제공 합니다. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    인덱서 상태에 대 한 자세한 내용은 [인덱서 상태 가져오기](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)를 참조 하세요.

1.  로그인

1.  수동으로 인덱서 실행을 다시 시작 하 고 인덱서 상태를 확인 합니다. 이번에는 인덱서 실행을 성공적으로 시작 해야 합니다.

## <a name="indexing-document-metadata"></a>문서 메타 데이터 인덱싱
문서 메타 데이터를 인덱싱하는 인덱서를 설정한 경우 인덱스에서 다음 메타 데이터를 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 메타 데이터는 현재 버전의 미리 보기에 포함 되지 않습니다.

| ID | Type | 설명 | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | 사이트의 문서 라이브러리에서 항목을 고유 하 게 식별 하는 사이트 ID, 라이브러리 ID 및 항목 ID의 조합 키입니다. |
| metadata_spo_site_id | Edm.String | SharePoint Online 사이트의 ID입니다. |
| metadata_spo_library_id | Edm.String | 문서 라이브러리의 ID입니다. |
| metadata_spo_item_id | Edm.String | 라이브러리에 있는 (문서) 항목의 ID입니다. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | 항목의 마지막으로 수정한 날짜/시간 (UTC)입니다. |
| metadata_spo_item_name | Edm.String | 항목의 이름입니다. |
| metadata_spo_item_size | Edm.Int64 | 항목의 크기 (바이트)입니다. | 
| metadata_spo_item_content_type | Edm.String | 항목의 콘텐츠 형식입니다. | 
| metadata_spo_item_extension | Edm.String | 항목의 확장입니다. |
| metadata_spo_item_weburi | Edm.String | 항목의 URI입니다. |
| metadata_spo_item_path | Edm.String | 부모 경로와 항목 이름의 조합입니다. | 

SharePoint Online 인덱서는 각 문서 유형과 관련 된 메타 데이터도 지원 합니다. 자세한 내용은 [Azure Cognitive Search에 사용 되는 콘텐츠 메타 데이터 속성](search-blob-metadata-properties.md)에서 찾을 수 있습니다.

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>인덱싱되는 문서 제어
단일 SharePoint Online 인덱서는 하나 이상의 문서 라이브러리에서 콘텐츠를 인덱싱할 수 있습니다. 인덱싱할 문서 라이브러리를 나타내기 위해 데이터 소스를 만들 때 *container* 매개 변수를 사용 합니다. 데이터 원본 *컨테이너* 에는 *이름* 및 *쿼리* 라는 두 개의 속성이 있습니다. 

### <a name="name"></a>이름
*Name* 속성은 필수 이며 세 가지 값 중 하나 여야 합니다.
+ *defaultSiteLibrary*
    + 사이트 기본 문서 라이브러리의 모든 콘텐츠를 인덱싱합니다.
+   *allSiteLibraries*
    + 사이트의 모든 문서 라이브러리에서 모든 콘텐츠를 인덱싱합니다. 이는 하위 사이트의 문서 라이브러리를 인덱싱하지 않습니다. 이러한 *쿼리는 쿼리* 를 통해 지정할 수 있습니다.
+   *useQuery*
    + *쿼리에* 정의 된 인덱스 내용만이 있습니다.

### <a name="query"></a>쿼리
*쿼리* 속성은 키워드/값 쌍으로 구성 됩니다. 다음은 사용할 수 있는 키워드입니다. 값은 사이트 url 또는 문서 라이브러리 url 중 하나입니다.

> [!NOTE]
> 특정 키워드에 대 한 값을 가져오려면 브라우저에서 SharePoint Online을 열어 브라우저에서 포함/제외 하 고 URI를 복사 하려는 문서 라이브러리로 이동 하는 것이 좋습니다. 쿼리에서 키워드에 사용할 값을 가져오는 가장 쉬운 방법입니다.

| 키워드 | 쿼리 설명 | 예제 |
| ------------- | -------------- | ----------- |
| null | Null 이거나 비어 있는 경우 컨테이너 이름에 따라 기본 문서 라이브러리 또는 모든 문서 라이브러리를 인덱싱합니다. | 기본 사이트 라이브러리의 모든 콘텐츠를 인덱싱합니다. <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | 연결 문자열에서 정의 된 사이트에 있는 모든 라이브러리의 콘텐츠를 인덱싱합니다. 사이트의 하위 사이트로 제한 됩니다. <br><br> 이 키워드의 *쿼리* 값은 사이트 또는 하위 사이트의 URI 여야 합니다. | 내 사이트의 모든 문서 라이브러리에서 모든 콘텐츠를 인덱싱합니다. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | 이 라이브러리의 콘텐츠를 인덱싱합니다. <br><br> 이 키워드의 *쿼리* 값은 다음 형식 중 하나 여야 합니다. <br><br> 예제 1: <br><br> *includeLibrary = [사이트 또는 하위 사이트]/[문서 라이브러리]* <br><br> 예제 2: <br><br> 브라우저에서 복사 되는 URI입니다. | MyDocumentLibrary의 모든 콘텐츠를 인덱싱합니다. <br><br> 예제 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> 예제 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  이 라이브러리의 콘텐츠를 인덱싱하지 마십시오. <br><br> 이 키워드의 *쿼리* 값은 다음 형식 중 하나 여야 합니다. <br><br> 예제 1: <br><br> *excludeLibrary = [사이트 또는 하위 사이트 URI]/[문서 라이브러리]* <br><br> 예제 2: <br><br> 브라우저에서 복사 되는 URI입니다. | MyDocumentLibrary를 제외한 모든 라이브러리의 모든 콘텐츠를 인덱싱합니다. <br><br> 예제 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> 예제 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>파일 유형별 인덱스
인덱싱되는 문서와 건너뛸 문서를 제어할 수 있습니다.

### <a name="include-documents-having-specific-file-extensions"></a>특정 파일 확장명을 가진 문서 포함
인덱서 구성 매개 변수를 사용 하 여 지정한 파일 이름 확장명을 가진 문서만 인덱싱할 수 있습니다 `indexedFileNameExtensions` . 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어를 인덱싱합니다. PDF 및 .DOCX 문서, 다음을 수행 합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>특정 파일 확장명을 가진 문서 제외
구성 매개 변수를 사용 하 여 특정 파일 이름 확장명을 가진 문서를 인덱싱에서 제외할 수 있습니다 `excludedFileNameExtensions` . 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어를 사용 하 여 모든 콘텐츠를 인덱싱하는 경우입니다. PNG 및 JPEG 확장,이 작업을 수행 합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

`indexedFileNameExtensions`및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 Azure Cognitive Search는 먼저를 확인 `indexedFileNameExtensions` 한 다음에서를 찾습니다 `excludedFileNameExtensions` . 동일한 파일 확장명이 두 목록 모두에 있는 경우 인덱싱에서 제외되는 것을 의미합니다.

## <a name="handling-errors"></a>오류 처리
기본적으로 SharePoint Online 인덱서는 지원 되지 않는 콘텐츠 형식 (예: 이미지)의 문서를 발견 하는 즉시 중지 됩니다. 물론 `excludedFileNameExtensions` 매개 변수를 사용하여 특정 콘텐츠 형식을 건너뛸 수 있습니다. 그러나 가능한 모든 콘텐츠 형식을 미리 알지 못해도 문서를 인덱싱하지 않아도 됩니다. 지원 되지 않는 콘텐츠 형식이 발견 될 때 인덱싱을 계속 하려면 `failOnUnsupportedContentType` 구성 매개 변수를 false로 설정 합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

일부 문서에서 Azure Cognitive Search는 콘텐츠 형식을 확인할 수 없거나 지원 되지 않는 콘텐츠 형식의 문서를 처리할 수 없습니다. 이 오류 모드를 무시하려면 `failOnUnprocessableDocument` 구성 매개 변수를 False로 설정합니다.

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search은 인덱싱되는 문서 크기를 제한 합니다. 이러한 한도는 [Azure Cognitive Search의 서비스 제한](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)사항에 설명 되어 있습니다. 크기가 큰 문서는 기본적으로 오류로 처리 됩니다. 그러나 `indexStorageMetadataOnlyForOversizedDocuments` 구성 매개 변수를 true로 설정한 경우에도 크기가 큰 문서의 저장소 메타 데이터를 인덱싱할 수 있습니다.

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

문서를 구문 분석 하거나 인덱스에 문서를 추가 하는 동안 처리 중에 오류가 발생 하는 경우에도 인덱싱을 계속할 수 있습니다. 설정 개수의 오류를 무시하려면 `maxFailedItems` 및 `maxFailedItemsPerBatch` 구성 매개 변수를 원하는 값으로 설정합니다. 예를 들면 다음과 같습니다.

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>참고 항목
+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Azure Cognitive Search에 사용 되는 콘텐츠 메타 데이터 속성](search-blob-metadata-properties.md)
