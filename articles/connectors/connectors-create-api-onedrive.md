<properties
	pageTitle="논리 앱에 OneDrive 커넥터 추가 | Microsoft Azure"
	description="REST API 매개 변수를 사용하는 OneDrive 커넥터 개요"
	services="app-servicelogic"    
	documentationCenter=""     
	authors="MandiOhlinger"    
	manager="erikre"    
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/26/2016"
   ms.author="mandia"/>

# OneDrive 커넥터 시작

OneDrive에 연결하여 파일 업로드, 가져오기, 삭제 등을 포함하여 파일을 관리합니다.

OneDrive를 사용하여 다음과 같은 작업을 수행합니다.

- OneDrive에서 파일을 저장하여 워크플로를 작성하거나 OneDrive의 기존 파일을 업데이트합니다.
- 트리거를 사용하여 OneDrive 내에서 파일이 만들어지거나 업데이트될 때 워크플로를 시작합니다.
- 파일 만들기, 파일 삭제 등의 작업을 사용합니다. 예를 들어 새 Office 365 전자 메일이 첨부 파일과 함께 수신되면(트리거) OneDrive에 새 파일을 만듭니다(작업).

이 항목에서는 논리 앱에서 OneDrive 커넥터를 사용하는 방법을 보여 주고 트리거 및 작업을 나열합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 GA(일반 공급)에 적용됩니다.

논리 앱에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../app-service-logic/app-service-logic-what-are-logic-apps.md) 및 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## OneDrive에 연결

논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 OneDrive에 연결하려면 먼저 OneDrive *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 OneDrive를 사용하는 경우 OneDrive 계정에 대한 자격 증명을 입력하여 연결을 만듭니다.

### 연결 만들기

>[AZURE.INCLUDE [OneDrive에 대한 연결을 만드는 단계](../../includes/connectors-create-api-onedrive.md)]

## 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. 원하는 간격 및 빈도로 서비스의 "폴링"을 트리거합니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

1. 논리 앱에서 트리거 목록을 가져오려면 "onedrive"를 입력합니다.

	![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. **파일을 수정할 때**를 선택합니다. 연결이 이미 있는 경우 선택 표시 단추를 선택하여 폴더를 선택합니다.

	![](./media/connectors-create-api-onedrive/sample-folder.png)

	로그인하라는 메시지가 표시되면 로그인 세부 정보를 입력하여 연결을 만듭니다. 이 항목의 [연결 만들기](connectors-create-api-onedrive.md#create-the-connection)에 단계가 표시됩니다.

	> [AZURE.NOTE] 이 예제에서는 선택한 폴더의 파일이 업데이트되면 논리 앱이 실행됩니다. 이 트리거의 결과를 보려면 전자 메일을 보내는 다른 작업을 추가합니다. 예를 들어 파일이 업데이트될 때 전자 메일을 보내는 Office 365 Outlook *전자 메일 보내기* 작업을 추가합니다.

3. **편집** 단추를 선택하고 **빈도** 및 **간격** 값을 설정합니다. 예를 들어 15분마다 폴링을 트리거하려면 **빈도**를 **분**으로, **간격**을 **15**로 설정합니다.

	![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.


## 작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 등이 표시됩니다.

	![](./media/connectors-create-api-onedrive/add-action.png)

2. **작업 추가**를 선택합니다.

3. 텍스트 상자에 "onedrive"를 입력하여 사용 가능한 모든 작업의 목록을 표시합니다.

	![](./media/connectors-create-api-onedrive/onedrive-actions.png)

4. 이 예제에서는 **OneDrive - 파일 만들기**를 선택합니다. 연결이 이미 존재하는 경우 **폴더 경로**를 선택하여 파일을 입력하고 **파일 이름**을 입력한 후 원하는 **파일 콘텐츠**를 선택합니다.

	![](./media/connectors-create-api-onedrive/sample-action.png)

	연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 항목의 [연결 만들기](connectors-create-api-onedrive.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다.

	> [AZURE.NOTE] 이 예제에서는 OneDrive 폴더에 새 파일을 만듭니다. 다른 트리거의 출력을 사용하여 OneDrive 파일을 만들 수 있습니다. 예를 들어 Office 365 Outlook *새 전자 메일이 도착했을 때* 트리거를 추가합니다. 그런 후 ForEach 내에서 Attachments 및 Content-Type 필드를 사용하는 OneDrive *파일 만들기* 작업을 추가하여 OneDrive에서 새 파일을 만듭니다.
	> 
	> ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.


## 기술 세부 정보

## 트리거

|트리거 | 설명|
|--- | ---|
|[파일을 만들 때](connectors-create-api-onedrive.md#when-a-file-is-created)|이 작업은 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.|
|[파일을 수정할 때](connectors-create-api-onedrive.md#when-a-file-is-modified)|이 작업은 폴더에서 파일을 수정할 때 흐름을 트리거합니다.|


## 동작

|작업|설명|
|--- | ---|
|[파일 메타데이터 가져오기](connectors-create-api-onedrive.md#get-file-metadata)|이 작업은 파일에 대한 메타데이터를 가져옵니다.|
|[파일 업데이트](connectors-create-api-onedrive.md#update-file)|이 작업은 파일을 업데이트합니다.|
|[파일 삭제](connectors-create-api-onedrive.md#delete-file)|이 작업은 파일을 삭제합니다.|
|[경로를 사용하여 파일 메타데이터 가져오기](connectors-create-api-onedrive.md#get-file-metadata-using-path)|이 작업은 경로를 사용하여 파일의 메타데이터를 가져옵니다.|
|[경로를 사용하여 파일 콘텐츠 가져오기](connectors-create-api-onedrive.md#get-file-content-using-path)|이 작업은 경로를 사용하여 파일의 콘텐츠를 가져옵니다.|
|[파일 콘텐츠 가져오기](connectors-create-api-onedrive.md#get-file-content)|이 작업은 파일의 콘텐츠를 가져옵니다.|
|[파일 만들기](connectors-create-api-onedrive.md#create-file)|이 작업은 파일을 만듭니다.|
|[파일 복사](connectors-create-api-onedrive.md#copy-file)|이 작업은 OneDrive에 파일을 복사합니다.|
|[폴더의 파일 나열](connectors-create-api-onedrive.md#list-files-in-folder)|이 작업은 폴더의 파일 및 하위 폴더 목록을 가져옵니다.|
|[루트 폴더의 파일 나열](connectors-create-api-onedrive.md#list-files-in-root-folder)|이 작업은 루트 폴더의 파일 및 하위 폴더 목록을 가져옵니다.|
|[폴더에 보관 추출](connectors-create-api-onedrive.md#extract-archive-to-folder)|이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip).|

### 작업 세부 정보

이 섹션에서는 모든 필수 또는 선택적 입력 속성 및 커넥터와 연결된 모든 해당 출력을 비롯한 각 작업에 대한 특정 세부 정보를 참조하세요.


#### 파일 메타데이터 가져오기
이 작업은 파일에 대한 메타데이터를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
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


#### 파일 업데이트
이 작업은 파일을 업데이트합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|
|body*|파일 콘텐츠|파일 콘텐츠|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
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


#### 파일 삭제
이 작업은 파일을 삭제합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음

#### 경로를 사용하여 파일 메타데이터 가져오기
이 작업은 경로를 사용하여 파일의 메타데이터를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|path*|파일 경로|파일 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
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


#### 경로를 사용하여 파일 콘텐츠 가져오기
이 작업은 경로를 사용하여 파일의 콘텐츠를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|path*|파일 경로|파일 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음


#### 파일 콘텐츠 가져오기
이 작업은 파일의 콘텐츠를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음

#### 파일 만들기
이 작업은 파일을 만듭니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderPath*|폴더 경로|폴더 선택|
|name*|파일 이름|파일의 이름|
|body*|파일 콘텐츠|파일 콘텐츠|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
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


#### 파일 복사
이 작업은 OneDrive에 파일을 복사합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|source*|원본 URL|원본 파일에 대한 URL|
|destination*|대상 파일 경로|대상 파일 이름을 포함한 대상 파일 경로|
|overwrite|덮어쓰기|'true'로 설정할 경우 대상 덮어쓰기|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
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


#### 파일을 만들 때
이 작업은 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderId*|폴더|폴더 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음

#### 파일을 수정할 때
이 작업은 폴더에서 파일을 수정할 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderId*|폴더|폴더 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음

#### 폴더의 파일 나열
이 작업은 폴더의 파일 및 하위 폴더 목록을 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|폴더|폴더 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
|---|---|
|Id|string|
|이름|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|크기|정수|
|MediaType|string||
|IsFolder|부울|
|ETag|string|
|FileLocator|string|


#### 루트 폴더의 파일 나열
이 작업은 루트 폴더의 파일 및 하위 폴더 목록을 가져옵니다.

이 호출에 대한 매개 변수는 없습니다.


##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
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

#### 폴더에 보관 추출
이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip).

|속성 이름| 표시 이름|설명|
| ---|---|---|
|source*|원본 보관 파일 경로|보관 파일의 경로|
|destination*|대상 폴더 경로|보관 콘텐츠를 추출할 경로|
|overwrite|덮어쓰기|'true'로 설정할 경우 대상 파일 덮어쓰기|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
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


## HTTP 응답

다음 표에서는 작업 및 트리거에 대한 응답과 응답 설명을 요약해서 보여 줍니다.

|이름|설명|
|---|---|
|200|확인|
|202|수락됨|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다.|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md) [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인하세요.

<!---HONumber=AcomDC_0727_2016-->