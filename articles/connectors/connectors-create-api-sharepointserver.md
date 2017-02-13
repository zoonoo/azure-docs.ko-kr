---
title: "논리 앱에서 SharePoint Online 커넥터 사용| Microsoft Docs"
description: "논리 앱에서 Azure 앱 서비스 SharePoint Online 커넥터 사용을 시작합니다."
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 76b6f627efdf189b873d9a6bdd722e73e837077d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online 커넥터 시작
SharePoint 커넥터는 SharePoint에서 목록으로 작업하기 위한 방법을 제공합니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
> 
> 

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## <a name="lets-talk-about-triggers-and-actions"></a>트리거 및 작업에 대한 정보
SharePoint 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다. 

SharePoint 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### <a name="sharepoint-actions"></a>SharePoint 작업
다음 작업을 수행할 수 있습니다.

| 작업 | 설명 |
| --- | --- |
| GetFileMetadata |문서 라이브러리에서 파일 메타데이터를 가져옵니다. |
| UpdateFile |문서 라이브러리에서 파일을 업데이트합니다. |
| DeleteFile |문서 라이브러리에서 파일을 삭제합니다. |
| GetFileMetadataByPath |문서 라이브러리에서 파일 메타데이터를 가져옵니다. |
| GetFileContentByPath |문서 라이브러리에서 파일을 가져옵니다. |
| GetFileContent |문서 라이브러리에서 파일을 가져옵니다. |
| CreateFile |문서 라이브러리에서 파일을 업로드합니다. |
| CopyFile |문서 라이브러리에서 파일을 복사합니다. |
| ExtractFolderV2 |문서 라이브러리에서 폴더를 추출합니다. |
| PostItem |SharePoint 목록에서 새 항목을 만듭니다. |
| GetItem |SharePoint 목록에서 단일 항목을 검색합니다. |
| DeleteItem |SharePoint 목록에서 항목을 삭제합니다. |
| PatchItem |SharePoint 목록에서 항목을 업데이트합니다. |

### <a name="sharepoint-triggers"></a>SharePoint 트리거
다음 이벤트를 수신할 수 있습니다.

| 트리거 | 설명 |
| --- | --- |
| OnNewFile |SharePoint 폴더에서 새 파일을 만들 때 흐름을 트리거합니다. |
| OnUpdatedFile |SharePoint 폴더에서 파일을 수정할 때 흐름을 트리거합니다. |
| GetOnNewItems |SharePoint 목록에서 새 항목을 만들 때 사용됩니다. |
| GetOnUpdatedItems |SharePoint 목록에서 기존 항목을 수정할 때 사용됩니다. |

## <a name="create-a-connection-to-sharepoint"></a>SharePoint에 대한 연결 만들기
SharePoint 커넥터를 사용하려면 먼저 **연결** 을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다. 

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |SharePoint 자격 증명 제공 |

**SharePoint Online**에 연결하려면 SharePoint Online에 ID(사용자 이름 및 암호, 스마트 카드 자격 증명 등)를 제공해야 합니다. 인증되면 논리 앱에서 SharePoint Online 커넥터를 계속 사용할 수 있습니다. 

논리 앱 디자이너에 있는 동안 다음 단계를 따라 SharePoint에 로그인하여 논리 앱에서 사용할 연결 **연결** 을 만듭니다.

1. 검색 상자에 SharePoint를 입력하고 이름에 SharePoint가 있는 모든 항목이 반환될 때까지 검색을 기다립니다.   
   ![SharePoint 구성][1]  
2.  **SharePoint Online - 파일을 만들 때**   
3. **SharePoint Online에 로그인**을 선택합니다.   
   ![SharePoint 구성][2]    
4. SharePoint 자격 증명을 제공하여 로그인하고 SharePoint에 권한을 부여합니다.   
   ![SharePoint 구성][3]     
5. 인증이 완료된 후 SharePoint의 **파일을 만들 때** 대화 상자를 구성하여 완료하기 위해 논리 앱으로 리디렉션됩니다.          
   ![SharePoint 구성][4]  
6. 그런 다음 논리 앱을 완료하는 데 필요한 다른 트리거 및 작업을 추가할 수 있습니다.   
7. 위의 메뉴 모음에서 **저장** 을 선택하여 작업을 저장합니다.  

## <a name="sharepoint-rest-api-reference"></a>SharePoint REST API 참조
#### <a name="this-documentation-is-for-version-10"></a>이 문서 적용 버전: 1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>문서 라이브러리에서 파일 메타데이터를 가져옵니다.
**```GET: /datasets/{dataset}/files/{id}```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| id |string |예 |path |없음 |파일의 고유 식별자 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>문서 라이브러리에서 파일을 업데이트합니다.
**```PUT: /datasets/{dataset}/files/{id}```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| id |string |예 |path |없음 |파일의 고유 식별자 |
| body | |예 |body |없음 |파일의 콘텐츠 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>문서 라이브러리에서 파일을 삭제합니다.
**```DELETE: /datasets/{dataset}/files/{id}```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| id |string |예 |path |없음 |파일의 고유 식별자 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>문서 라이브러리에서 파일 메타데이터를 가져옵니다.
**```GET: /datasets/{dataset}/GetFileByPath```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| path |string |yes |쿼리 |없음 |파일의 경로 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>문서 라이브러리에서 파일을 가져옵니다.
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| path |string |yes |쿼리 |없음 |파일의 경로 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>문서 라이브러리에서 파일을 가져옵니다.
**```GET: /datasets/{dataset}/files/{id}/content```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| id |string |예 |path |없음 |파일의 고유 식별자 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>문서 라이브러리에서 파일을 업로드합니다.
**```POST: /datasets/{dataset}/files```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| folderPath |string |yes |쿼리 |없음 |파일에 대한 경로 |
| 이름 |string |yes |쿼리 |없음 |파일의 이름 |
| body | |예 |body |없음 |파일의 콘텐츠 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>문서 라이브러리에서 파일을 복사합니다.
**```POST: /datasets/{dataset}/copyFile```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| 원본 |string |yes |쿼리 |없음 |원본 파일에 대한 경로 |
| destination |string |yes |쿼리 |없음 |대상 파일에 대한 경로 |
| overwrite |부울 |no |쿼리 |false |기존 파일을 덮어쓸 것인지 여부 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>SharePoint 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL |
| folderId |string |yes |쿼리 |없음 |SharePoint 폴더의 고유 식별자 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>SharePoint 폴더에서 파일을 수정할 때 흐름을 트리거합니다.
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL |
| folderId |string |yes |쿼리 |없음 |SharePoint 폴더의 고유 식별자 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>문서 라이브러리에서 폴더를 추출합니다.
**```POST: /datasets/{dataset}/extractFolderV2```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite |
| 원본 |string |yes |쿼리 |없음 |원본 파일에 대한 경로 |
| destination |string |yes |쿼리 |없음 |대상 폴더에 대한 경로 |
| overwrite |부울 |no |쿼리 |false |기존 파일을 덮어쓸 것인지 여부 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>SharePoint 목록에서 새 항목을 만들 때 사용됩니다.
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| 테이블 |string |예 |path |없음 |SharePoint 목록 이름 |
| $skip |정수 |no |쿼리 |없음 |건너뛸 항목의 수(기본값 = 0) |
| $top |정수 |no |쿼리 |없음 |검색할 항목의 최대 수(기본값 = 256) |
| $filter |string |no |쿼리 |없음 |항목의 수를 제한할 ODATA 필터 쿼리 |
| $orderby |string |no |쿼리 |없음 |항목의 순서를 지정하는 ODATA orderBy 쿼리 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>SharePoint 목록에서 기존 항목을 수정할 때 사용됩니다.
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| 테이블 |string |예 |path |없음 |SharePoint 목록 이름 |
| $skip |정수 |no |쿼리 |없음 |건너뛸 항목의 수(기본값 = 0) |
| $top |정수 |no |쿼리 |없음 |검색할 항목의 최대 수(기본값 = 256) |
| $filter |string |no |쿼리 |없음 |항목의 수를 제한할 ODATA 필터 쿼리 |
| $orderby |string |no |쿼리 |없음 |항목의 순서를 지정하는 ODATA orderBy 쿼리 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>SharePoint 목록에서 새 항목을 만듭니다.
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| 테이블 |string |예 |path |없음 |SharePoint 목록 이름 |
| 항목 | |예 |body |없음 |만들 항목 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>SharePoint 목록에서 단일 항목을 검색합니다.
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| 테이블 |string |예 |path |없음 |SharePoint 목록 이름 |
| id |정수 |예 |path |없음 |검색할 항목의 고유 식별자 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>SharePoint 목록에서 항목을 삭제합니다.
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| 테이블 |string |예 |path |없음 |SharePoint 목록 이름 |
| id |정수 |예 |path |없음 |삭제할 항목의 고유 식별자 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>SharePoint 목록에서 항목을 업데이트합니다.
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |예 |path |없음 |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| 테이블 |string |예 |path |없음 |SharePoint 목록 이름 |
| id |정수 |예 |path |없음 |업데이트할 항목의 고유 식별자 |
| 항목 | |예 |body |없음 |변경된 속성을 가진 항목 |

### <a name="here-are-the-possible-responses"></a>다음은 가능한 응답입니다.
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

- - -
## <a name="object-definitions"></a>개체 정의:
 **DataSetsMetadata**:

DataSetsMetadata에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| tabular |정의되지 않음 |
| Blob |정의되지 않음 |

 **TabularDataSetsMetadata**:

TabularDataSetsMetadata에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| 원본 |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**:

BlobDataSetsMetadata에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| 원본 |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**:

BlobMetadata에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| id |string |
| 이름 |string |
| displayName |string |
| path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

 **Object**:

Object에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
|  | |

 **TableMetadata**:

TableMetadata에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| 이름 |string |
| title |string |
| x-ms-permission |string |
| schema |정의되지 않음 |

 **DataSetsList**:

DataSetsList에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| value |array |

 **DataSet**:

DataSet에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| 이름 |string |
| displayName |string |

 **테이블**:

Table에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| 이름 |string |
| displayName |string |

 **항목**:

Item에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| ItemInternalId |string |

 **ItemsList**:

ItemsList에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| value |array |

 **TablesList**:

TablesList에 대한 필수 속성:

필수 속성이 없습니다. 

**모든 속성**: 

| 이름 | 데이터 형식 |
| --- | --- |
| value |array |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Nov16_HO3-->


