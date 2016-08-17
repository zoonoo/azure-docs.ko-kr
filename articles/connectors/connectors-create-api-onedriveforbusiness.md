<properties
pageTitle="비즈니스용 OneDrive | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. 비즈니스용 OneDrive에 연결하여 파일을 관리합니다. 파일에 대해 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# 비즈니스용 OneDrive 커넥터 시작



비즈니스용 OneDrive 커넥터는 다음에서 사용할 수 있습니다.

- [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [흐름](http://flows.microsoft.com)

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

비즈니스용 OneDrive 커넥터는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 비즈니스용 OneDrive 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### 비즈니스용 OneDrive 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|ID를 사용하여 비즈니스용 OneDrive에서 파일 메타데이터를 검색합니다.|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|비즈니스용 OneDrive에서 파일을 업데이트합니다.|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|비즈니스용 OneDrive에서 파일을 삭제합니다.|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|경로를 사용하여 비즈니스용 OneDrive에서 파일 메타데이터를 검색합니다.|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|경로를 사용하여 비즈니스용 OneDrive에서 파일 컨텐츠를 검색합니다.|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|ID를 사용하여 비즈니스용 OneDrive에서 파일 컨텐츠를 검색합니다.|
|[CreateFile](connectors-create-api-onedriveforbusiness.md#createfile)|비즈니스용 OneDrive에 파일을 업로드합니다.|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|비즈니스용 OneDrive에 파일을 복사합니다.|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|비즈니스용 OneDrive 폴더에 파일을 나열합니다.|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|비즈니스용 OneDrive 루트 폴더에 파일을 나열합니다.|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|비즈니스용 OneDrive로 폴더를 추출합니다.|
### 비즈니스용 OneDrive 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|파일을 만들 때|비즈니스용 OneDrive 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.|
|파일을 수정할 때|비즈니스용 OneDrive 폴더에서 파일을 수정할 때 흐름을 트리거합니다.|


## 비즈니스용 OneDrive에 대한 연결 만들기
비즈니스용 OneDrive로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|비즈니스용 OneDrive 자격 증명 제공|
연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

>[AZURE.INCLUDE [비즈니스용 OneDrive에 대한 연결을 만드는 단계](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## 비즈니스용 OneDrive에 대한 참조
적용 버전: 1.0

## GetFileMetadata
ID를 사용하여 파일 메타데이터 가져오기: ID를 사용하여 비즈니스용 OneDrive에서 파일 메타데이터를 검색합니다.

```GET: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음|파일 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## UpdateFile
파일 업데이트: 비즈니스용 OneDrive에서 파일을 업데이트합니다.

```PUT: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음|업데이트할 파일 지정|
|body| |yes|body|없음|비즈니스용 OneDrive에서 업데이트할 파일의 콘텐츠|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## DeleteFile
파일 삭제: 비즈니스용 OneDrive에서 파일을 삭제합니다.

```DELETE: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음|삭제할 파일 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## GetFileMetadataByPath
경로를 사용하여 파일 메타데이터 가져오기: 경로를 사용하여 비즈니스용 OneDrive에서 파일 메타데이터를 검색합니다.

```GET: /datasets/default/GetFileByPath```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음|비즈니스용 OneDrive의 파일에 대한 고유 경로|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## GetFileContentByPath
경로를 사용하여 파일 컨텐츠 가져오기: 경로를 사용하여 비즈니스용 OneDrive에서 파일 컨텐츠를 검색합니다.

```GET: /datasets/default/GetFileContentByPath```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음|비즈니스용 OneDrive의 파일에 대한 고유 경로|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## GetFileContent
ID를 사용하여 파일 콘텐츠 가져오기: ID를 사용하여 비즈니스용 OneDrive에서 파일 콘텐츠를 검색합니다.

```GET: /datasets/default/files/{id}/content```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음|파일 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## CreateFile
파일 만들기: 비즈니스용 OneDrive에 파일을 업로드합니다.

```POST: /datasets/default/files```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderPath|string|yes|쿼리|없음|비즈니스용 OneDrive에 파일을 업로드할 폴더 경로|
|name|string|yes|쿼리|없음|비즈니스용 OneDrive에서 만들 파일 이름|
|body| |yes|body|없음|비즈니스용 OneDrive에 업로드할 파일의 콘텐츠|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## CopyFile
파일 복사: 비즈니스용 OneDrive에 파일을 복사합니다.

```POST: /datasets/default/copyFile```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리|없음|원본 파일에 대한 URL|
|destination|string|yes|쿼리|없음|대상 파일 이름을 포함한 비즈니스용 OneDrive의 대상 파일 경로|
|overwrite|부울|no|쿼리|false|'true'로 설정할 경우 대상 덮어쓰기|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## OnNewFile
파일을 만들 경우: 비즈니스용 OneDrive 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.

```GET: /datasets/default/triggers/onnewfile```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderId|string|yes|쿼리|없음|폴더 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## OnUpdatedFile
파일을 수정할 경우: 비즈니스용 OneDrive 폴더에서 파일을 수정할 때 흐름을 트리거합니다.

```GET: /datasets/default/triggers/onupdatedfile```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderId|string|yes|쿼리|없음|폴더 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## ListFolder
폴더에 파일 나열: 비즈니스용 OneDrive 폴더에 파일을 나열합니다.

```GET: /datasets/default/folders/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음|폴더 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## ListRootFolder
루트 폴더 나열: 비즈니스용 OneDrive 루트 폴더에 파일을 나열합니다.

```GET: /datasets/default/folders```

이 호출에 대한 매개 변수는 없습니다.
#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## ExtractFolderV2
폴더 추출: 비즈니스용 OneDrive로 폴더를 추출합니다.

```POST: /datasets/default/extractFolderV2```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리|없음|보관 파일의 경로|
|destination|string|yes|쿼리|없음|보관 콘텐츠를 추출할 비즈니스용 OneDrive의 경로|
|overwrite|부울|no|쿼리|false|'true'로 설정할 경우 대상 파일 덮어쓰기|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## 개체 정의 

### DataSetsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|tabular|정의되지 않음|아니요 |
|Blob|정의되지 않음|아니요 |



### TabularDataSetsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|원본|string|아니요 |
|displayName|string|아니요 |
|urlEncoding|string|아니요 |
|tableDisplayName|string|아니요 |
|tablePluralName|string|아니요 |



### BlobDataSetsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|원본|string|아니요 |
|displayName|string|아니요 |
|urlEncoding|string|아니요 |



### BlobMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|Id|string|아니요 |
|이름|string|아니요 |
|DisplayName|string|아니요 |
|Path|string|아니요 |
|LastModified|string|아니요 |
|크기|정수|아니요 |
|MediaType|string|아니요 |
|IsFolder|부울|아니요 |
|ETag|string|아니요 |
|FileLocator|string|아니요 |



### Object


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->