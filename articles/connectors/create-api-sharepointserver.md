<properties
pageTitle="논리 앱에서 SharePoint Server API 사용 | Microsoft Azure"
description="논리 앱 및 파워 앱에서 Azure 앱 서비스 SharePoint Server API를 사용하여 시작합니다."
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/18/2016"
ms.author="deonhe"/>

# SharePoint API 시작

SharePoint 연결 공급자는 SharePoint에서 목록으로 작업하기 위한 API를 제공합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전에 대한 내용을 보려면 [SharePoint](../app-service-logic/app-service-logic-connector-SharePoint.md)를 클릭하세요.

SharePoint를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* 논리 앱 빌드
* 파워 앱 빌드  

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업에 대한 정보

SharePoint API는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

SharePoint API에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### SharePoint 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|GetFileMetadata|문서 라이브러리에서 파일 메타데이터를 가져옵니다.|
|UpdateFile|문서 라이브러리에서 파일을 업데이트합니다.|
|DeleteFile|문서 라이브러리에서 파일을 삭제합니다.|
|GetFileMetadataByPath|문서 라이브러리에서 파일 메타데이터를 가져옵니다.|
|GetFileContentByPath|문서 라이브러리에서 파일을 가져옵니다.|
|GetFileContent|문서 라이브러리에서 파일을 가져옵니다.|
|CreateFile|문서 라이브러리에서 파일을 업로드합니다.|
|CopyFile|문서 라이브러리에서 파일을 복사합니다.|
|ExtractFolderV2|문서 라이브러리에서 폴더를 추출합니다.|
|PostItem|SharePoint 목록에서 새 항목을 만듭니다.|
|GetItem|SharePoint 목록에서 단일 항목을 검색합니다.|
|DeleteItem|SharePoint 목록에서 항목을 삭제합니다.|
|PatchItem|SharePoint 목록에서 항목을 업데이트합니다.|
### SharePoint 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|OnNewFile|SharePoint 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.|
|OnUpdatedFile|SharePoint 폴더에서 파일을 수정할 때 흐름을 트리거합니다.|
|GetOnNewItems|SharePoint 목록에서 새 항목을 만들 때 사용됩니다.|
|GetOnUpdatedItems|SharePoint 목록에서 기존 항목을 수정할 때 사용됩니다.|


## SharePoint에 대한 연결 만들기
SharePoint API를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|SharePoint 자격 증명 제공|  

>[AZURE.TIP] 다른 논리 앱 또는 파워 앱 또는 둘 다에서 이 연결을 사용할 수 있습니다.

## SharePoint REST API 참조
#### 이 문서 적용 버전: 1.0


### 문서 라이브러리에서 파일 메타데이터를 가져옵니다.
**```GET: /datasets/{dataset}/files/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|없음|파일의 고유 식별자|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 파일을 업데이트합니다.
**```PUT: /datasets/{dataset}/files/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|없음|파일의 고유 식별자|
|body| |yes|body|없음|파일의 콘텐츠|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 파일을 삭제합니다.
**```DELETE: /datasets/{dataset}/files/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|없음|파일의 고유 식별자|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 파일 메타데이터를 가져옵니다.
**```GET: /datasets/{dataset}/GetFileByPath```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|path|string|yes|쿼리|없음|파일의 경로|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 파일을 가져옵니다.
**```GET: /datasets/{dataset}/GetFileContentByPath```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|path|string|yes|쿼리|없음|파일의 경로|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 파일을 가져옵니다.
**```GET: /datasets/{dataset}/files/{id}/content```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|없음|파일의 고유 식별자|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 파일을 업로드합니다.
**```POST: /datasets/{dataset}/files```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|folderPath|string|yes|쿼리|없음|파일에 대한 경로|
|name|string|yes|쿼리|없음|파일의 이름|
|body| |yes|body|없음|파일의 콘텐츠|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 파일을 복사합니다.
**```POST: /datasets/{dataset}/copyFile```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|원본|string|yes|쿼리|없음|원본 파일에 대한 경로|
|destination|string|yes|쿼리|없음|대상 파일에 대한 경로|
|overwrite|부울|no|쿼리|false|기존 파일을 덮어쓸 것인지 여부|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.
**```GET: /datasets/{dataset}/triggers/onnewfile```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL|
|folderId|string|yes|쿼리|없음|SharePoint 폴더의 고유 식별자|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 폴더에서 파일을 수정할 때 흐름을 트리거합니다.
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL|
|folderId|string|yes|쿼리|없음|SharePoint 폴더의 고유 식별자|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 문서 라이브러리에서 폴더를 추출합니다.
**```POST: /datasets/{dataset}/extractFolderV2```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL입니다. 예: http://contoso.sharepoint.com/sites/mysite|
|원본|string|yes|쿼리|없음|원본 파일에 대한 경로|
|destination|string|yes|쿼리|없음|대상 폴더에 대한 경로|
|overwrite|부울|no|쿼리|false|기존 파일을 덮어쓸 것인지 여부|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 목록에서 새 항목을 만들 때 사용됩니다.
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite)|
|테이블|string|yes|path|없음|SharePoint 목록 이름|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 목록에서 기존 항목을 수정할 때 사용됩니다.
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite)|
|테이블|string|yes|path|없음|SharePoint 목록 이름|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 목록에서 새 항목을 만듭니다.
**```POST: /datasets/{dataset}/tables/{table}/items```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite)|
|테이블|string|yes|path|없음|SharePoint 목록 이름|
|항목| |yes|body|없음|만들 항목|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 목록에서 단일 항목을 검색합니다.
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite)|
|테이블|string|yes|path|없음|SharePoint 목록 이름|
|id|정수|yes|path|없음|검색할 항목의 고유 식별자|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 목록에서 항목을 삭제합니다.
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite)|
|테이블|string|yes|path|없음|SharePoint 목록 이름|
|id|정수|yes|path|없음|삭제할 항목의 고유 식별자|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### SharePoint 목록에서 항목을 업데이트합니다.
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|SharePoint 사이트 URL(예: http://contoso.sharepoint.com/sites/mysite)|
|테이블|string|yes|path|없음|SharePoint 목록 이름|
|id|정수|yes|path|없음|업데이트할 항목의 고유 식별자|
|항목| |yes|body|없음|변경된 속성을 가진 항목|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



## 개체 정의: 

 **DataSetsMetadata**:

DataSetsMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|tabular|정의되지 않음|
|Blob|정의되지 않음|



 **TabularDataSetsMetadata**:

TabularDataSetsMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|원본|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

BlobDataSetsMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|원본|string|
|displayName|string|
|urlEncoding|string|



 **BlobMetadata**:

BlobMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|Id|string|
|이름|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|크기|정수|
|MediaType|string|
|IsFolder|부울|
|ETag|string|
|FileLocator|string|



 **Object**:

Object에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|



 **TableMetadata**:

TableMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|name|string|
|title|string|
|x-ms-permission|string|
|schema|정의되지 않음|



 **DataSetsList**:

DataSetsList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **DataSet**:

DataSet에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|이름|string|
|DisplayName|string|



 **테이블**:

Table에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|이름|string|
|DisplayName|string|



 **Item**:

Item에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|ItemInternalId|string|



 **ItemsList**:

ItemsList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **TablesList**:

TablesList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md) [파워 앱 만들기](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0224_2016-->