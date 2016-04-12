<properties
	pageTitle="논리 앱에 Azure Blob 저장소 API 추가 | Microsoft Azure"
	description="REST API 매개 변수를 사용하는 Azure Blob 저장소 개요"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/16/2016"
   ms.author="mandia"/>

# Azure Blob 저장소 API 시작
Azure Blob에 연결하여 파일 만들기, 파일 삭제 등 Blob 컨테이너의 파일을 관리합니다. Azure Blob 저장소 API는 다음에서 사용할 수 있습니다.

- 논리 앱 

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전에 대한 내용을 보려면 [Azure 저장소 Blob 커넥터](../app-service-logic/app-service-logic-connector-azurestorageblob.md)를 클릭하세요.

Azure Blob 저장소를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Blob에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 파일 만들기, 파일 콘텐츠 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Blob의 파일에서 "긴급"을 검색한 다음 Office 365를 사용하여 "긴급"이 포함된 모든 파일을 메일로 보낼 수 있습니다. 

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Azure Blob에는 다음 작업이 포함됩니다. 트리거는 없습니다.

| 트리거 | 동작|
| --- | --- |
| 없음 | <ul><li>파일 만들기</li><li>파일 복사</li><li>파일 삭제</li><li>폴더에 보관 추출</li><li>파일 콘텐츠 가져오기</li><li>경로를 사용하여 파일 콘텐츠 가져오기</li><li>파일 메타데이터 가져오기</li><li>경로를 사용하여 파일 메타데이터 가져오기</li><li>파일 업데이트</li></ul> |

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## Azure Blob에 대한 연결 만들기
논리 앱에 이 API를 추가할 때 다음과 같은 저장소 계정 값을 입력합니다.

|속성| 필수|설명|
| ---|---|---|
|Azure 저장소 계정 이름 | yes | Blob 저장소 계정 이름|
|Azure 저장소 계정 액세스 키 | yes | Blob 저장소 계정에 대한 액세스 키|

연결을 만든 후에 폴더 경로 또는 파일 이름 등의 Blob 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 Blob 연결을 사용할 수 있습니다.
 

## Swagger REST API 참조
적용 버전: 1.0

### 파일 만들기
Azure Blob 저장소에 파일을 업로드합니다. ```POST: /datasets/default/files```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderPath|string|yes|쿼리| 없음 |Azure Blob 저장소에 파일을 업로드할 폴더 경로|
|name|string|yes|쿼리|없음 |Azure Blob 저장소에 만들 파일의 이름|
|body|string(binary) |yes|body|없음|Azure Blob 저장소에 업로드할 파일의 콘텐츠|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|

### 파일 복사
Azure Blob 저장소에 파일을 복사합니다. ```POST: /datasets/default/copyFile```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리|없음 |원본 파일에 대한 URL|
|destination|string|yes|쿼리| 없음|대상 파일 이름을 포함한 Azure Blob 저장소의 대상 파일 경로|
|overwrite|부울|no|쿼리|없음 |'true'로 설정할 경우 대상 덮어쓰기|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 삭제
Azure Blob 저장소에서 파일을 삭제합니다. ```DELETE: /datasets/default/files/{id}```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Azure Blob 저장소에서 삭제할 파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 폴더에 보관 추출
보관 파일(예: .zip)을 Azure Blob 저장소의 폴더에 추출합니다. ```POST: /datasets/default/ExtractFolderV2```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리| 없음|보관 파일의 경로|
|destination|string|yes|쿼리|없음 |보관 콘텐츠를 추출할 Azure Blob 저장소의 경로|
|overwrite|부울|no|쿼리|없음 |'true'로 설정할 경우 대상 파일 덮어쓰기|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 콘텐츠 가져오기
ID를 사용하여 Azure Blob 저장소에서 파일 콘텐츠를 검색합니다. ```GET: /datasets/default/files/{id}/content```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음|파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 경로를 사용하여 파일 콘텐츠 가져오기
경로를 사용하여 Azure Blob 저장소에서 파일 콘텐츠를 가져옵니다. ```GET: /datasets/default/GetFileContentByPath```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음 |Azure Blob 저장소의 파일에 대한 고유 경로|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 메타데이터 가져오기
파일 ID를 사용하여 Azure Blob 저장소에서 파일 메타데이터를 검색합니다. ```GET: /datasets/default/files/{id}```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 경로를 사용하여 파일 메타데이터 가져오기
경로를 사용하여 Azure Blob 저장소에서 파일 메타데이터를 검색합니다. ```GET: /datasets/default/GetFileByPath```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음|Azure Blob 저장소의 파일에 대한 고유 경로|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 업데이트
Azure Blob 저장소의 파일을 업데이트합니다. ```PUT: /datasets/default/files/{id}```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Azure Blob 저장소에서 업데이트할 파일의 고유 식별자|
|body|string(binary) |yes|body|없음 |Azure Blob 저장소에서 업데이트할 파일의 콘텐츠|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|

## 개체 정의

#### DataSetsMetadata

|속성 이름 | 데이터 형식 | 필수|
|---|---|---|
|tabular|정의되지 않음|no|
|Blob|정의되지 않음|no|

#### TabularDataSetsMetadata

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### BlobMetadata

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|Id|string|no|
|이름|string|no|
|DisplayName|string|no|
|Path|string|no|
|LastModified|string|no|
|크기|정수|no|
|MediaType|string|no|
|IsFolder|부울|no|
|ETag|string|no|
|FileLocator|string|no|

## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0323_2016-->