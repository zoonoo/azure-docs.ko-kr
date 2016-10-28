<properties
    pageTitle="논리 앱에 Google 드라이브 커넥터 추가 | Microsoft Azure"
    description="REST API 매개 변수를 사용하는 Google 드라이브 커넥터 개요"
    services=""
    suite=""
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
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Google 드라이브 커넥터 시작
Google 드라이브에 연결하여 파일 만들기, 행 가져오기 등의 작업을 수행합니다. Google 드라이브를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- 검색에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다.
- 이미지 검색, 뉴스 검색 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 비디오를 검색한 다음 Twitter를 사용하여 Twitter 피드에 해당 비디오를 게시할 수 있습니다.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.


## 트리거 및 작업
Google 드라이브에는 다음 작업이 포함됩니다. 트리거는 없습니다.

트리거 | 작업
--- | ---
없음 | <ul><li>파일 만들기</li><li>행 삽입</li><li>파일 복사</li><li>파일 삭제</li><li>행 삭제</li><li>폴더에 보관 추출</li><li>ID를 사용하여 파일 콘텐츠 가져오기</li><li>경로를 사용하여 파일 콘텐츠 가져오기</li><li>ID를 사용하여 파일 메타데이터 가져오기</li><li>경로를 사용하여 파일 메타데이터 가져오기</li><li>행 가져오기</li><li>파일 업데이트</li><li>행 업데이트</li></ul>

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.


## Google 드라이브에 대한 연결 만들기

논리 앱에 이 커넥터를 추가할 때 Google 드라이브에 연결할 권한을 논리 앱에 부여해야 합니다.

>[AZURE.INCLUDE [Google 드라이브에 대한 연결을 만드는 단계](../../includes/connectors-create-api-googledrive.md)]

연결을 만든 후에 폴더 경로 또는 파일 이름 등의 Google 드라이브 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 Google 드라이브 연결을 사용할 수 있습니다.


## Swagger REST API 참조
적용 버전: 1.0

### 파일 만들기    
Google 드라이브에 파일을 업로드합니다. ```POST: /datasets/default/files```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderPath|string|yes|쿼리|없음 |Google 드라이브에 파일을 업로드할 폴더 경로|
|name|string|yes|쿼리|없음 |Google 드라이브에 만들 파일의 이름|
|body|string(binary) |yes|body| 없음|Google 드라이브에 업로드할 파일의 콘텐츠|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 삽입    
Google 시트에 행을 삽입합니다. ```POST: /datasets/{dataset}/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path| 없음|Google 시트 파일의 고유 식별자|
|테이블|string|yes|path|없음 |워크시트의 고유 식별자|
|항목|ItemInternalId: string |yes|body|없음 |지정된 시트에 삽입할 행|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 복사    
Google 드라이브에 파일을 복사합니다. ```POST: /datasets/default/copyFile```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리| 없음|원본 파일에 대한 URL|
|destination|string|yes|쿼리|없음 |대상 파일 이름을 포함한 Google 드라이브의 대상 파일 경로|
|overwrite|부울|no|쿼리|없음 |'true'로 설정할 경우 대상 덮어쓰기|

#### 응답
|Name|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 삭제    
Google 드라이브에서 파일을 삭제합니다. ```DELETE: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Google 드라이브에서 삭제할 파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 삭제    
Google 시트에서 행을 삭제합니다. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음 |Google 시트 파일의 고유 식별자|
|테이블|string|yes|path|없음 |워크시트의 고유 식별자|
|id|string|yes|path|없음 |삭제할 행의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 폴더에 보관 추출    
보관 파일(예: .zip)을 Google 드라이브의 폴더에 추출합니다. ```POST: /datasets/default/extractFolderV2```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리|없음 |보관 파일의 경로|
|destination|string|yes|쿼리|없음 |보관 콘텐츠를 추출할 Google 드라이브의 경로|
|overwrite|부울|no|쿼리|없음 |'true'로 설정할 경우 대상 파일 덮어쓰기|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### ID를 사용하여 파일 콘텐츠 가져오기    
ID를 사용하여 Google 드라이브에서 파일 콘텐츠를 검색합니다. ```GET: /datasets/default/files/{id}/content```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Google 드라이브에서 검색할 파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 경로를 사용하여 파일 콘텐츠 가져오기    
경로를 사용하여 Google 드라이브에서 파일 콘텐츠를 검색합니다. ```GET: /datasets/default/GetFileContentByPath```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음 |Google 드라이브에서 파일의 경로|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### ID를 사용하여 파일 메타데이터 가져오기    
ID를 사용하여 Google 드라이브에서 메타데이터를 검색합니다. ```GET: /datasets/default/files/{id}```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Google 드라이브 파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 경로를 사용하여 파일 메타데이터 가져오기    
경로를 사용하여 Google 드라이브에서 메타데이터를 검색합니다. ```GET: /datasets/default/GetFileByPath```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음 |Google 드라이브에서 파일의 경로|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 가져오기    
Google 시트에서 단일 행을 검색합니다. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음 |Google 시트 파일의 고유 식별자|
|테이블|string|yes|path|없음 |워크시트의 고유 식별자|
|id|string|yes|path| 없음|검색할 행의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 업데이트    
Google 드라이브의 파일을 업데이트합니다. ```PUT: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Google 드라이브에서 업데이트할 파일의 고유 식별자|
|body|string(binary) |yes|body| 없음|Google 드라이브에 업로드할 파일의 콘텐츠|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 업데이트    
Google 시트의 행을 업데이트합니다. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음 |Google 시트 파일의 고유 식별자|
|테이블|string|yes|path| 없음|워크시트의 고유 식별자|
|id|string|yes|path|없음 |업데이트할 행의 고유 식별자|
|항목|ItemInternalId: string |yes|body|없음 |업데이트된 값을 가진 행|

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

#### TableMetadata

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|정의되지 않음|no|

#### TablesList

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|

#### 테이블

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### 항목

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API 목록](apis-list.md)으로 돌아갑니다.


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0824_2016-->