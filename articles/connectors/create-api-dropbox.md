<properties
	pageTitle="PowerApps 엔터프라이즈 또는 논리 앱에 Dropbox API 추가 | Microsoft Azure"
	description="REST API 매개 변수를 사용하는 Dropbox API 개요"
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
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Dropbox API 시작 
Dropbox에 연결하여 파일 만들기, 파일 가져오기 등 파일을 관리합니다.

Dropbox API는 PowerApps 엔터프라이즈 및 논리 앱에서 사용할 수 있습니다.

Dropbox를 사용하여 다음과 같은 작업을 수행할 수 있습니다.

- Dropbox에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 파일을 만들거나 업데이트할 때 트리거를 사용합니다.
- 파일 만들기, 파일 삭제 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Dropbox에서 새 파일이 만들어지면 Office 365를 사용하여 해당 파일을 메일로 보낼 수 있습니다.
- PowerApps 엔터프라이즈에 Dropbox API를 추가합니다. 이렇게 하면 사용자가 앱 내에서 이 API를 사용할 수 있습니다. 

PowerApps 엔터프라이즈에서 API를 추가하는 방법을 보려면 [Register an API in PowerApps](../power-apps/powerapps-register-from-available-apis.md)(PowerApps에서 API 등록)로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Dropbox에는 다음 트리거 및 작업이 포함됩니다.

트리거 | 동작
--- | ---
<ul><li>파일을 만들 때</li><li>파일을 수정할 때</li></ul> | <ul><li>파일 만들기</li><li>파일을 만들 때</li><li>파일 복사</li><li>파일 삭제</li><li>폴더에 보관 추출</li><li>ID를 사용하여 파일 콘텐츠 가져오기</li><li>경로를 사용하여 파일 가져오기</li><li>ID를 사용하여 파일 메타데이터 가져오기</li><li>경로를 사용하여 파일 메타데이터 가져오기</li><li>파일 업데이트</li><li>파일을 수정할 때</li></ul>

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## Dropbox에 대한 연결 만들기

### PowerApps에 구성 추가
PowerApps 엔터프라이즈에 Dropbox를 추가할 때 Dropbox 응용 프로그램의 **앱 키**와 **앱 암호**를 입력합니다. 또한 **URL 리디렉션** 값도 Dropbox 응용 프로그램에서 사용됩니다. Dropbox 응용 프로그램이 없는 경우 다음 단계를 사용하여 응용 프로그램을 만들 수 있습니다.

1. [Dropbox][1]에 로그인합니다.
2. Dropbox 개발자 사이트로 이동하여 **My Apps**를 선택합니다. ![Dropbox 개발자 사이트][8]  
3. **Create app**을 선택합니다. ![Dropbox 앱 만들기][9]  
4. **Create a new app on the Dropbox platform**에서:  

	1. **Choose API**에서 **Dropbox API**를 선택합니다.
	2. **Choose the type of access you need**에서 **Full Dropbox...**를 선택합니다.  
	3. 앱의 이름을 입력합니다.  

	![Dropbox 앱 만들기 페이지 1][10]

5. 앱 설정 페이지에서:

	1. **OAuth 2**에서 Azure 포털에 Dropbox API를 추가할 때 표시된 **URL 리디렉션** 값을 입력합니다. **추가**를 선택합니다.  
	2. **표시** 링크를 선택하여 **앱 암호**를 나타냅니다.  

	![Dropbox 앱 만들기 페이지 2][11]

이제 Azure 포털의 Dropbox 구성에서 **앱 키** 및 **앱 암호** 값을 복사하여 붙여넣습니다.

### 논리 앱에 구성 추가
논리 앱에 이 API를 추가할 때 Dropbox에 연결할 권한을 논리 앱에 부여해야 합니다.

1. Dropbox 계정에 로그인합니다.
2. **권한 부여**를 선택하고 논리 앱에서 Dropbox에 연결하고 사용할 수 있도록 허용합니다. 

연결을 만든 후에 폴더 경로 또는 파일 이름 등의 Dropbox 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 Dropbox 연결을 사용할 수 있습니다.

## Swagger REST API 참조

### 파일 만들기    
Dropbox에 파일을 업로드합니다. ```POST: /datasets/default/files```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderPath|string|yes|쿼리|없음 |Dropbox에 파일을 업로드할 폴더 경로|
|name|string|yes|쿼리|없음 |Dropbox에 만들 파일의 이름|
|body|string(binary) |yes|body|없음 |Dropbox에 업로드할 파일의 콘텐츠|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일을 만들 때    
Dropbox 폴더에서 새 파일을 만들 때 흐름을 트리거합니다. ```GET: /datasets/default/triggers/onnewfile```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderId|string|yes|쿼리|없음 |Dropbox에서 폴더의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 복사    
Dropbox에 파일을 복사합니다. ```POST: /datasets/default/copyFile```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리|없음 |원본 파일에 대한 URL|
|destination|string|yes|쿼리| 없음|대상 파일 이름을 포함한 Dropbox의 대상 파일 경로|
|overwrite|부울|no|쿼리|없음 |'true'로 설정할 경우 대상 덮어쓰기|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 삭제    
Dropbox에서 파일을 삭제합니다. ```DELETE: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음|Dropbox에서 삭제할 파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 폴더에 보관 추출    
보관 파일(예: .zip)을 Dropbox의 폴더에 추출합니다. **```POST: /datasets/default/extractFolderV2```**

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|원본|string|yes|쿼리|없음 |보관 파일의 경로|
|destination|string|yes|쿼리|없음 |보관 콘텐츠를 추출할 Dropbox의 경로|
|overwrite|부울|no|쿼리|없음 |'true'로 설정할 경우 대상 파일 덮어쓰기|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### ID를 사용하여 파일 콘텐츠 가져오기    
ID를 사용하여 Dropbox에서 파일 콘텐츠를 검색합니다. ```GET: /datasets/default/files/{id}/content```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Dropbox에서 파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 경로를 사용하여 파일 콘텐츠 가져오기    
경로를 사용하여 Dropbox에서 파일 콘텐츠를 검색합니다. ```GET: /datasets/default/GetFileContentByPath```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음 |Dropbox의 파일에 대한 고유 경로|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### ID를 사용하여 파일 메타데이터 가져오기    
파일 ID를 사용하여 Dropbox에서 파일 메타데이터를 검색합니다. ```GET: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path|없음 |Dropbox에서 파일의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 경로를 사용하여 파일 메타데이터 가져오기    
경로를 사용하여 Dropbox에서 파일 메타데이터를 검색합니다. ```GET: /datasets/default/GetFileByPath```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|path|string|yes|쿼리|없음 |Dropbox의 파일에 대한 고유 경로|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일 업데이트    
Dropbox에서 파일을 업데이트합니다. ```PUT: /datasets/default/files/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|id|string|yes|path| 없음|Dropbox에서 업데이트할 파일의 고유 식별자|
|body|string(binary) |yes|body|없음 |Dropbox에서 업데이트할 파일의 콘텐츠|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 파일을 수정할 때    
Dropbox 폴더에서 파일을 수정할 때 흐름을 트리거합니다. ```GET: /datasets/default/triggers/onupdatedfile```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderId|string|yes|쿼리|없음 |Dropbox에서 폴더의 고유 식별자|

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
PowerApps 엔터프라이즈에 Dropbox API를 추가한 후 해당 앱에서 API를 사용할 [권한을 사용자에게 부여](../power-apps/powerapps-manage-api-connection-user-access.md)합니다.

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0224_2016-->