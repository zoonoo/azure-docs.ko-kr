---
title: 논리 앱에서 FTP 커넥터를 사용하는 방법 알아보기 | Microsoft Docs
description: Azure 앱 서비스로 논리 앱을 만듭니다. FTP 서버에 연결하여 파일을 관리합니다. FTP 서버에서 파일 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: deonhe

---
# FTP 커넥터 시작
FTP 커넥터를 사용하여 FTP 서버에서 파일을 모니터링 및 관리하고 파일을 만듭니다.

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../app-service-logic/app-service-logic-create-a-logic-app.md) 시작할 수 있습니다.

## FTP에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.

### FTP에 대한 연결 만들기
> [!INCLUDE [FTP에 대한 연결을 만드는 단계](../../includes/connectors-create-api-ftp.md)]
> 
> 

## FTP 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

> [!IMPORTANT]
> FTP 커넥터를 사용하려면 인터넷에서 액세스할 수 있고 PASSIVE 모드로 작동하도록 구성된 FTP 서버가 필요합니다. 또한 FTP 커넥터는 **암시적 FTPS(FTP over SSL)와 호환되지 않습니다**. FTP 커넥터는 명시적 FTPS(FTP over SSL)만 지원합니다.
> 
> 

이 예제에서는 파일이 FTP 서버에 추가되거나 수정될 때 **FTP - 파일을 추가하거나 수정할 때** 트리거를 사용하여 논리 앱 워크플로를 시작하는 방법을 보여 줍니다. 엔터프라이즈 예에서는 이 트리거를 사용하여 고객의 주문을 나타내는 새 파일에 대한 FTP 폴더를 모니터링할 수 있습니다. 그런 다음 **파일 콘텐츠 가져오기**와 같은 FTP 커넥터 작업을 사용하여 추가 처리할 주문 및 주문 데이터베이스에 있는 저장소의 콘텐츠를 가져올 수 있습니다.

1. 논리 앱 디자이너의 검색 상자에 *ftp*를 입력한 후 **FTP - 파일을 추가하거나 수정할 때** 트리거를 선택합니다.  
   ![FTP 트리거 이미지 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   **파일을 추가하거나 수정할 때** 컨트롤이 열립니다.  
   ![FTP 트리거 이미지 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. 컨트롤의 오른쪽에 있는 **...**를 선택합니다. 이렇게 하면 폴더 선택 컨트롤이 열립니다.  
   ![FTP 트리거 이미지 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. **>**(오른쪽 화살표)를 선택하고 새 파일 또는 수정된 파일을 모니터링할 폴더를 탐색하여 찾습니다. 해당 폴더를 선택하면 이제 폴더가 **폴더** 컨트롤에 표시됩니다.  
   ![FTP 트리거 이미지 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)  

이제, 논리 앱은 파일이 특정 FTP 폴더에서 수정되거나 만들어질 때 워크플로의 다른 트리거 및 작업의 실행을 시작하는 트리거로 구성되었습니다.

> [!NOTE]
> 논리 앱이 작동하려면 하나 이상의 트리거와 작업이 있어야 합니다. 다음 섹션의 단계에 따라 작업을 추가합니다.
> 
> 

## FTP 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

이제 트리거를 추가했으므로 다음 단계에 따라 트리거가 찾은 새 파일 또는 수정된 파일의 콘텐츠를 가져올 작업을 추가합니다.

1. **+ 새 단계**를 선택하여 FTP 서버에서 파일의 콘텐츠를 가져올 작업을 추가합니다.
2. **작업 추가** 링크를 선택합니다.  
   ![FTP 작업 이미지 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. *FTP*를 입력하여 FTP와 관련된 모든 작업을 검색합니다.
4. FTP 폴더에서 새 파일 또는 수정된 파일을 찾을 때 수행할 작업으로 **FTP - 파일 콘텐츠 가져오기**를 선택합니다.  
   ![FTP 작업 이미지 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   **파일 콘텐츠 가져오기** 컨트롤이 열립니다. **참고**: 논리 앱에 FTP 서버 계정에 액세스하기 위한 권한을 아직 부여하지 않았으면 이러한 권한을 부여하라는 메시지가 표시됩니다.  
   ![FTP 작업 이미지 3](./media/connectors-create-api-ftp/ftp-action-3.png)  
5. **파일** 컨트롤(**FILE*** 아래에 흰색 영역)을 선택합니다. 여기에서 FTP 서버에서 찾은 새 파일 또는 수정된 파일의 다양한 속성을 사용할 수 있습니다.
6. **파일 콘텐츠** 옵션을 선택합니다.  
   ![FTP 작업 이미지 4](./media/connectors-create-api-ftp/ftp-action-4.png)  
7. 컨트롤이 업데이트되고 **FTP - 파일 콘텐츠 가져오기** 작업에서 FTP 서버에 있는 새 파일 또는 수정된 파일의 *파일 콘텐츠*를 가져오는 것이 표시됩니다.  
   ![FTP 작업 이미지 5](./media/connectors-create-api-ftp/ftp-action-5.png)       
8. 작업을 저장한 후 파일을 FTP 폴더에 추가하여 워크플로를 테스트합니다.

이제 FTP 서버에서 폴더를 모니터링하고 FTP 서버에서 새 파일 또는 수정된 파일을 찾으면 워크플로를 시작하는 트리거로 논리 앱이 구성되었습니다.

또한 새 파일 또는 수정된 파일의 콘텐츠를 가져오는 작업으로도 논리 앱이 구성되었습니다.

이제 새 파일 또는 수정된 파일의 콘텐츠를 SQL 데이터베이스 테이블에 삽입하는 [SQL Server - 행 삽입](connectors-create-api-sqlazure.md#insert-row) 작업과 같은 다른 작업을 추가할 수 있습니다.

## 기술 세부 정보
이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## FTP 트리거
FTP에는 다음과 같은 트리거가 있습니다.

| 트리거 | 설명 |
| --- | --- |
| [파일을 추가하거나 수정할 때](connectors-create-api-ftp.md#when-a-file-is-added-or-modified) |이 작업은 폴더에서 파일을 추가하거나 수정할 때 흐름을 트리거합니다. |

## FTP 작업
FTP에는 다음과 같은 작업이 있습니다.

| 작업 | 설명 |
| --- | --- |
| [파일 메타데이터 가져오기](connectors-create-api-ftp.md#get-file-metadata) |이 작업은 파일에 대한 메타데이터를 가져옵니다. |
| [파일 업데이트](connectors-create-api-ftp.md#update-file) |이 작업은 파일을 업데이트합니다. |
| [파일 삭제](connectors-create-api-ftp.md#delete-file) |이 작업은 파일을 삭제합니다. |
| [경로를 사용하여 파일 메타데이터 가져오기](connectors-create-api-ftp.md#get-file-metadata-using-path) |이 작업은 경로를 사용하여 파일의 메타데이터를 가져옵니다. |
| [경로를 사용하여 파일 콘텐츠 가져오기](connectors-create-api-ftp.md#get-file-content-using-path) |이 작업은 경로를 사용하여 파일의 콘텐츠를 가져옵니다. |
| [파일 콘텐츠 가져오기](connectors-create-api-ftp.md#get-file-content) |이 작업은 파일의 콘텐츠를 가져옵니다. |
| [파일 만들기](connectors-create-api-ftp.md#create-file) |이 작업은 파일을 만듭니다. |
| [파일 복사](connectors-create-api-ftp.md#copy-file) |이 작업은 FTP 서버에 파일을 복사합니다. |
| [폴더의 파일 나열](connectors-create-api-ftp.md#list-files-in-folder) |이 작업은 폴더의 파일 및 하위 폴더 목록을 가져옵니다. |
| [루트 폴더의 파일 나열](connectors-create-api-ftp.md#list-files-in-root-folder) |이 작업은 루트 폴더의 파일 및 하위 폴더 목록을 가져옵니다. |
| [폴더 추출](connectors-create-api-ftp.md#extract-folder) |이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip). |

### 작업 세부 정보
이 커넥터에 대한 작업 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.

### 파일 메타데이터 가져오기
이 작업은 파일에 대한 메타데이터를 가져옵니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 선택 |

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

### 파일 업데이트
이 작업은 파일을 업데이트합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 선택 |
| body* |파일 콘텐츠 |파일 콘텐츠 |

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

### 파일 삭제
이 작업은 파일을 삭제합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 선택 |

*는 필수 속성을 나타냅니다.

### 경로를 사용하여 파일 메타데이터 가져오기
이 작업은 경로를 사용하여 파일의 메타데이터를 가져옵니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| path* |파일 경로 |파일 선택 |

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

### 경로를 사용하여 파일 콘텐츠 가져오기
이 작업은 경로를 사용하여 파일의 콘텐츠를 가져옵니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| path* |파일 경로 |파일 선택 |

*는 필수 속성을 나타냅니다.

### 파일 콘텐츠 가져오기
이 작업은 파일의 콘텐츠를 가져옵니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 선택 |

*는 필수 속성을 나타냅니다.

### 파일 만들기
이 작업은 파일을 만듭니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| folderPath* |폴더 경로 |폴더 선택 |
| name* |파일 이름 |파일의 이름 |
| body* |파일 콘텐츠 |파일 콘텐츠 |

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

### 파일 복사
이 작업은 FTP 서버에 파일을 복사합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| source* |원본 URL |원본 파일에 대한 URL |
| destination* |대상 파일 경로 |대상 파일 이름을 포함한 대상 파일 경로 |
| overwrite |덮어쓰기 |'true'로 설정할 경우 대상 덮어쓰기 |

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

### 파일을 추가하거나 수정할 때
이 작업은 폴더에서 파일을 추가하거나 수정할 때 흐름을 트리거합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| folderId* |폴더 |폴더 선택 |

*는 필수 속성을 나타냅니다.

### 폴더의 파일 나열
이 작업은 폴더의 파일 및 하위 폴더 목록을 가져옵니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |폴더 |폴더 선택 |

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

### 루트 폴더의 파일 나열
이 작업은 루트 폴더의 파일 및 하위 폴더 목록을 가져옵니다.

이 호출에 대한 매개 변수는 없습니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

### 폴더 추출
이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip).

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| source* |원본 보관 파일 경로 |보관 파일의 경로 |
| destination* |대상 폴더 경로 |대상 폴더에 대한 경로 |
| overwrite |덮어쓰기 |'true'로 설정할 경우 대상 파일 덮어쓰기 |

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| Id |string |
| 이름 |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| 크기 |정수 |
| MediaType |string |
| IsFolder |부울 |
| ETag |string |
| FileLocator |string |

## HTTP 응답
위의 작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다.

| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생. |
| 기본값 |작업이 실패했습니다. |

## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
