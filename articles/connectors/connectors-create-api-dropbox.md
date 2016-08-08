<properties
pageTitle="Dropbox | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. Dropbox에 연결하여 파일을 관리합니다. Dropbox에서 파일 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# Dropbox 커넥터 시작

Dropbox에 연결하여 파일을 관리합니다. Dropbox에서 파일 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다.

[커넥터](./apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../app-service-logic/app-service-logic-create-a-logic-app.md) 시작할 수 있습니다.

## Dropbox에 연결

논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 Dropbox에 연결하려면 먼저 Dropbox *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 제공해야 합니다. 따라서 Dropbox 예제에서는 Dropbox에 대한 연결을 만들기 위해 Dropbox 계정에 대한 자격 증명이 필요합니다. [연결에 대한 자세한 정보]()

### Dropbox에 대한 연결 만들기

>[AZURE.INCLUDE [Dropbox에 대한 연결을 만드는 단계](../../includes/connectors-create-api-dropbox.md)]

## Dropbox 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

이 예제에서는 **파일을 만들 때** 트리거를 사용합니다. 이 트리거가 발생하면 **경로를 사용하여 파일 콘텐츠 가져오기** Dropbox 작업을 호출합니다.

1. 논리 앱 디자이너의 검색 상자에 *dropbox*를 입력한 후 **Dropbox - 파일을 만들 때** 트리거를 선택합니다. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)
  
2. 파일 생성을 추적할 폴더를 선택합니다. ...(빨간색 상자에 표시)를 선택하고 트리거의 입력을 선택할 폴더를 찾습니다. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)

## Dropbox 작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

이제 트리거가 추가되었고 다음 단계에 따라 새 파일의 콘텐츠를 가져올 작업을 추가합니다.

1. **+ 새 단계**를 선택하여 새 파일을 만들 때 수행할 작업을 추가합니다. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)

2. **작업 추가**를 선택합니다. 수행할 작업을 검색할 수 있는 검색 상자가 열립니다. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)

3. *dropbox*를 입력하여 Dropbox와 관련된 작업을 검색합니다.

4. 선택한 Dropbox 폴더에서 새 파일이 생성될 때 수행할 작업으로 **Dropbox - 경로를 사용하여 파일 콘텐츠 가져오기**를 선택합니다. 작업 제어 블록이 열립니다. 논리 앱에 Dropbox 계정에 액세스하기 위한 권한을 아직 부여하지 않았으면 이러한 권한을 부여하라는 메시지가 표시됩니다. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)

5. ...(**파일 경로** 컨트롤의 오른쪽에 있음)를 선택하고 사용할 파일 경로를 찾습니다. 또는 **파일 경로** 토큰을 사용하여 빠르게 논리 앱을 만들 수 있습니다. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)

6. 작업을 저장하고 Dropbox에 새 파일을 만들어 워크플로를 활성화합니다.

## 기술 세부 정보

이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## Dropbox 트리거

Dropbox 커넥터에는 다음과 같은 트리거가 있습니다.

|트리거 | 설명|
|--- | ---|
|[파일을 만들 때](connectors-create-api-dropbox.md#when-a-file-is-created)|이 작업은 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.|
|[파일을 수정할 때](connectors-create-api-dropbox.md#when-a-file-is-modified)|이 작업은 폴더에서 파일을 수정할 때 흐름을 트리거합니다.|

## Dropbox 작업

Dropbox 커넥터에는 다음과 같은 작업이 있습니다.

|작업|설명|
|--- | ---|
|[파일 메타데이터 가져오기](connectors-create-api-dropbox.md#get-file-metadata)|이 작업은 파일에 대한 메타데이터를 가져옵니다.|
|[파일 업데이트](connectors-create-api-dropbox.md#update-file)|이 작업은 파일을 업데이트합니다.|
|[파일 삭제](connectors-create-api-dropbox.md#delete-file)|이 작업은 파일을 삭제합니다.|
|[경로를 사용하여 파일 메타데이터 가져오기](connectors-create-api-dropbox.md#get-file-metadata-using-path)|이 작업은 경로를 사용하여 파일의 메타데이터를 가져옵니다.|
|[경로를 사용하여 파일 콘텐츠 가져오기](connectors-create-api-dropbox.md#get-file-content-using-path)|이 작업은 경로를 사용하여 파일의 콘텐츠를 가져옵니다.|
|[파일 콘텐츠 가져오기](connectors-create-api-dropbox.md#get-file-content)|이 작업은 파일의 콘텐츠를 가져옵니다.|
|[파일 만들기](connectors-create-api-dropbox.md#create-file)|이 작업은 파일을 만듭니다.|
|[파일 복사](connectors-create-api-dropbox.md#copy-file)|이 작업은 Dropbox에 파일을 복사합니다.|
|[폴더의 파일 나열](connectors-create-api-dropbox.md#list-files-in-folder)|이 작업은 폴더의 파일 및 하위 폴더 목록을 가져옵니다.|
|[루트 폴더의 파일 나열](connectors-create-api-dropbox.md#list-files-in-root-folder)|이 작업은 루트 폴더의 파일 및 하위 폴더 목록을 가져옵니다.|
|[폴더에 보관 추출](connectors-create-api-dropbox.md#extract-archive-to-folder)|이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip).|

### 작업 세부 정보

이 커넥터에 대한 작업 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.


### 파일 메타데이터 가져오기
이 작업은 파일에 대한 메타데이터를 가져옵니다.

                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

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




### 파일 업데이트
이 작업은 파일을 업데이트합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|
|body*|파일 콘텐츠|파일 콘텐츠|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

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




### 파일 삭제
이 작업은 파일을 삭제합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|

*는 필수 속성을 나타냅니다.




### 경로를 사용하여 파일 메타데이터 가져오기
이 작업은 경로를 사용하여 파일의 메타데이터를 가져옵니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|path*|파일 경로|파일 선택|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

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




### 경로를 사용하여 파일 콘텐츠 가져오기
이 작업은 경로를 사용하여 파일의 콘텐츠를 가져옵니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|path*|파일 경로|파일 선택|

*는 필수 속성을 나타냅니다.




### 파일 콘텐츠 가져오기
이 작업은 파일의 콘텐츠를 가져옵니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|파일|파일 선택|

*는 필수 속성을 나타냅니다.




### 파일 만들기
이 작업은 파일을 만듭니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderPath*|폴더 경로|폴더 선택|
|name*|파일 이름|파일의 이름|
|body*|파일 콘텐츠|파일 콘텐츠|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

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




### 파일 복사
이 작업은 Dropbox에 파일을 복사합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|source*|원본 URL|원본 파일에 대한 URL|
|destination*|대상 파일 경로|대상 파일 이름을 포함한 대상 파일 경로|
|overwrite|덮어쓰기|'true'로 설정할 경우 대상 덮어쓰기|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

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




### 파일을 만들 때
이 작업은 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderId*|폴더|폴더 선택|

*는 필수 속성을 나타냅니다.




### 파일을 수정할 때
이 작업은 폴더에서 파일을 수정할 때 흐름을 트리거합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderId*|폴더|폴더 선택|

*는 필수 속성을 나타냅니다.




### 폴더의 파일 나열
이 작업은 폴더의 파일 및 하위 폴더 목록을 가져옵니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|id*|폴더|폴더 선택|

*는 필수 속성을 나타냅니다.



#### 출력 세부 정보

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




### 루트 폴더의 파일 나열
이 작업은 루트 폴더의 파일 및 하위 폴더 목록을 가져옵니다.


이 호출에 대한 매개 변수는 없습니다.

#### 출력 세부 정보

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




### 폴더에 보관 추출
이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip).


|속성 이름| 표시 이름|설명|
| ---|---|---|
|source*|원본 보관 파일 경로|보관 파일의 경로|
|destination*|대상 폴더 경로|보관 콘텐츠를 추출할 경로|
|overwrite|덮어쓰기|'true'로 설정할 경우 대상 파일 덮어쓰기|

*는 필수 속성을 나타냅니다.



#### 출력 세부 정보

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

위의 작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다.

| 이름 | 설명 |
|---|---|
|200|확인|
|202|수락됨|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생.|
|기본값|작업이 실패했습니다.|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->