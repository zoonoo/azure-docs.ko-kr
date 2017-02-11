---
title: "논리 앱에서 SharePoint Online 커넥터 사용 방법 알아보기 | Microsoft Docs"
description: "SharePoint Online 커넥터를 사용하여 논리 앱을 만들고 SharePoint에서 목록을 관리합니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 76a37a567ae077e3d0fee7a1bc7f763d4d1c7cf2


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online 커넥터 시작
SharePoint Online 커넥터를 사용하여 SharePoint 목록을 관리합니다.  

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../app-service-logic/app-service-logic-create-a-logic-app.md) 시작할 수 있습니다.

## <a name="connect-to-sharepoint-online"></a>SharePoint Online에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

### <a name="create-a-connection-to-sharepoint-online"></a>SharePoint Online에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## <a name="use-a-sharepoint-online-trigger"></a>SharePoint Online 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## <a name="use-a-sharepoint-online-action"></a>SharePoint Online 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## <a name="technical-details"></a>기술 세부 정보
이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## <a name="sharepoint-online-triggers"></a>SharePoint Online 트리거
SharePoint에는 다음과 같은 트리거가 있습니다.  

| 트리거 | 설명 |
| --- | --- |
| [파일을 만들 때](connectors-create-api-sharepointonline.md#when-a-file-is-created) |이 작업은 SharePoint 폴더에서 새 파일을 만들 때 흐름을 트리거합니다. |
| [파일을 수정할 때](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |이 작업은 SharePoint 폴더에서 파일을 수정할 때 흐름을 트리거합니다. |
| [새 항목을 만든 경우](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |이 작업은 SharePoint 목록에서 새 항목을 만들 때 흐름을 트리거합니다. |
| [기존 항목을 수정할 때](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |이 작업은 SharePoint 목록에서 기존 항목을 수정할 때 흐름을 트리거합니다. |

## <a name="sharepoint-online-actions"></a>SharePoint Online 작업
SharePoint에는 다음과 같은 작업이 있습니다.

| 작업 | 설명 |
| --- | --- |
| [파일 메타데이터 가져오기](connectors-create-api-sharepointonline.md#get-file-metadata) |이 작업은 파일 ID를 사용하여 파일 메타데이터를 가져옵니다. |
| [파일 업데이트](connectors-create-api-sharepointonline.md#update-file) |이 작업은 파일 콘텐츠를 업데이트합니다. |
| [파일 삭제](connectors-create-api-sharepointonline.md#delete-file) |이 작업은 파일을 삭제합니다. |
| [경로를 사용하여 파일 메타데이터 가져오기](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |이 작업은 파일 경로를 사용하여 파일 메타데이터를 가져옵니다. |
| [경로를 사용하여 파일 콘텐츠 가져오기](connectors-create-api-sharepointonline.md#get-file-content-using-path) |이 작업은 파일 경로를 사용하여 파일 콘텐츠를 가져옵니다. |
| [파일 콘텐츠 가져오기](connectors-create-api-sharepointonline.md#get-file-content) |이 작업은 파일 ID를 사용하여 파일 콘텐츠를 가져옵니다. |
| [파일 만들기](connectors-create-api-sharepointonline.md#create-file) |이 작업은 SharePoint 사이트에 파일을 업로드합니다. |
| [파일 복사](connectors-create-api-sharepointonline.md#copy-file) |이 작업은 SharePoint 사이트에 파일을 복사합니다. |
| [폴더 나열](connectors-create-api-sharepointonline.md#list-folder) |이 작업은 SharePoint 폴더에 포함된 파일을 가져옵니다. |
| [루트 폴더 나열](connectors-create-api-sharepointonline.md#list-root-folder) |이 작업은 루트 SharePoint 폴더의 파일을 가져옵니다. |
| [폴더 추출](connectors-create-api-sharepointonline.md#extract-folder) |이 작업은 보관 파일을 SharePoint 폴더로 추출합니다(예: .zip). |
| [항목 가져오기](connectors-create-api-sharepointonline.md#get-items) |이 작업은 SharePoint 목록에서 항목을 가져옵니다. |
| [항목 만들기](connectors-create-api-sharepointonline.md#create-item) |이 작업은 SharePoint 목록에서 새 항목을 만듭니다. |
| [항목 가져오기](connectors-create-api-sharepointonline.md#get-item) |이 작업은 SharePoint 목록에서 ID로 단일 항목을 가져옵니다. |
| [항목 삭제](connectors-create-api-sharepointonline.md#delete-item) |이 작업은 SharePoint 목록에서 항목을 삭제합니다. |
| [항목 업데이트](connectors-create-api-sharepointonline.md#update-item) |이 작업은 SharePoint 목록에서 항목을 업데이트합니다. |
| [엔터티 값 가져오기](connectors-create-api-sharepointonline.md#get-entity-values) |이 작업은 SharePoint 엔터티에서 가능한 값을 가져옵니다. |
| [목록 가져오기](connectors-create-api-sharepointonline.md#get-lists) |이 작업은 사이트에서 SharePoint 목록을 가져옵니다. |

### <a name="action-details"></a>작업 세부 정보
이 커넥터에 대한 작업 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.

### <a name="get-file-metadata"></a>파일 메타데이터 가져오기
이 작업은 파일 ID를 사용하여 파일 메타데이터를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| id* |파일 식별자 |파일 선택 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="update-file"></a>파일 업데이트
이 작업은 파일 콘텐츠를 업데이트합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| id* |파일 식별자 |파일 선택 |
| body* |File Content(파일 내용) |파일 콘텐츠 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="delete-file"></a>파일 삭제
이 작업은 파일을 삭제합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| id* |파일 식별자 |파일 선택 |

*는 필수 속성을 나타냅니다.

### <a name="get-file-metadata-using-path"></a>경로를 사용하여 파일 메타데이터 가져오기
이 작업은 파일 경로를 사용하여 파일 메타데이터를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| path* |파일 경로 |파일 선택 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="get-file-content-using-path"></a>경로를 사용하여 파일 콘텐츠 가져오기
이 작업은 파일 경로를 사용하여 파일 콘텐츠를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| path* |파일 경로 |파일 선택 |

*는 필수 속성을 나타냅니다.

### <a name="get-file-content"></a>파일 콘텐츠 가져오기
이 작업은 파일 ID를 사용하여 파일 콘텐츠를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| id* |파일 식별자 |파일 선택 |

*는 필수 속성을 나타냅니다.

### <a name="create-file"></a>파일 만들기
이 작업은 SharePoint 사이트에 파일을 업로드합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| folderPath* |폴더 경로 |파일 선택 |
| name* |파일 이름 |파일의 이름 |
| body* |File Content(파일 내용) |파일 콘텐츠 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="copy-file"></a>파일 복사
이 작업은 SharePoint 사이트에 파일을 복사합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| source* |원본 파일 경로 |원본 파일에 대한 경로 |
| destination* |대상 파일 경로 |대상 파일에 대한 경로 |
| overwrite |덮어쓰기 플래그 |대상 파일을 덮어쓸지 여부(있는 경우) |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="when-a-file-is-created"></a>파일을 만들 때
이 작업은 SharePoint 폴더에서 새 파일을 만들 때 흐름을 트리거합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL |
| folderId* |폴더 ID |폴더 선택 |

*는 필수 속성을 나타냅니다.

### <a name="when-a-file-is-modified"></a>파일을 수정할 때
이 작업은 SharePoint 폴더에서 파일을 수정할 때 흐름을 트리거합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL |
| folderId* |폴더 ID |폴더 선택 |

*는 필수 속성을 나타냅니다.

### <a name="list-folder"></a>폴더 나열
이 작업은 SharePoint 폴더에 포함된 파일을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| id* |파일 식별자 |폴더의 고유 식별자 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="list-root-folder"></a>루트 폴더 나열
이 작업은 루트 SharePoint 폴더의 파일을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="extract-folder"></a>폴더 추출
이 작업은 보관 파일을 SharePoint 폴더로 추출합니다(예: .zip). 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| source* |원본 파일 경로 |원본 파일에 대한 경로 |
| destination* |대상 폴더 경로 |대상 폴더에 대한 경로 |
| overwrite |덮어쓰기 플래그 |대상 파일을 덮어쓸지 여부(있는 경우) |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
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

### <a name="when-a-new-item-is-created"></a>새 항목을 만든 경우
이 작업은 SharePoint 목록에서 새 항목을 만들 때 흐름을 트리거합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| table* |목록 이름 |SharePoint 목록 이름 |
| $filter |필터 쿼리 |반환된 항목을 제한할 ODATA 필터 쿼리 |
| $orderby |Order By |항목의 순서를 지정하는 ODATA orderBy 쿼리 |
| $skip |숫자 건너뛰며 세기 |건너뛸 항목의 수(기본값 = 0) |
| $top |최대 가져오기 수 |검색할 항목의 최대 수(기본값 = 256) |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

### <a name="when-an-existing-item-is-modified"></a>기존 항목을 수정할 때
이 작업은 SharePoint 목록에서 기존 항목을 수정할 때 흐름을 트리거합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| table* |목록 이름 |SharePoint 목록 이름 |
| $filter |필터 쿼리 |반환된 항목을 제한할 ODATA 필터 쿼리 |
| $orderby |Order By |항목의 순서를 지정하는 ODATA orderBy 쿼리 |
| $skip |숫자 건너뛰며 세기 |건너뛸 항목의 수(기본값 = 0) |
| $top |최대 가져오기 수 |검색할 항목의 최대 수(기본값 = 256) |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

### <a name="get-items"></a>항목 가져오기
이 작업은 SharePoint 목록에서 항목을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| table* |목록 이름 |SharePoint 목록 이름 |
| $filter |필터 쿼리 |반환된 항목을 제한할 ODATA 필터 쿼리 |
| $orderby |Order By |항목의 순서를 지정하는 ODATA orderBy 쿼리 |
| $skip |숫자 건너뛰며 세기 |건너뛸 항목의 수(기본값 = 0) |
| $top |최대 가져오기 수 |검색할 항목의 최대 수(기본값 = 256) |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

### <a name="create-item"></a>항목 만들기
이 작업은 SharePoint 목록에서 새 항목을 만듭니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| table* |목록 이름 |SharePoint 목록 이름 |
| item* |항목 |만들 항목 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
항목

| 속성 이름 | 데이터 형식 |
| --- | --- |
| ItemInternalId |string |

### <a name="get-item"></a>항목 가져오기
이 작업은 SharePoint 목록에서 ID로 단일 항목을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| table* |목록 이름 |SharePoint 목록 이름 |
| id* |Id |검색할 항목의 고유 식별자 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
항목

| 속성 이름 | 데이터 형식 |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-item"></a>항목 삭제
이 작업은 SharePoint 목록에서 항목을 삭제합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| table* |목록 이름 |SharePoint 목록 이름 |
| id* |Id |삭제할 항목의 고유 식별자 |

*는 필수 속성을 나타냅니다.

### <a name="update-item"></a>항목 업데이트
이 작업은 SharePoint 목록에서 항목을 업데이트합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |
| table* |목록 이름 |SharePoint 목록 이름 |
| id* |Id |업데이트할 항목의 고유 식별자 |
| item* |항목 |변경된 속성을 가진 항목 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
항목

| 속성 이름 | 데이터 형식 |
| --- | --- |
| ItemInternalId |string |

### <a name="get-entity-values"></a>엔터티 값 가져오기
이 작업은 SharePoint 엔터티에서 가능한 값을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |SharePoint 사이트 URL |SharePoint 사이트 URL |
| table* |테이블 이름 |테이블 이름 |
| id* |엔터티 ID |엔터티 ID |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
### <a name="get-lists"></a>목로 가져오기
이 작업은 사이트에서 SharePoint 목록을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |사이트 URL |SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite) |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
TablesList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

## <a name="http-responses"></a>HTTP 응답
위의 작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다. 

| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생. |
| 기본값 |작업이 실패했습니다. |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


