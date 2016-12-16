---
title: "논리 앱에 SFTP 커넥터 사용 방법 알아보기 | Microsoft Docs"
description: "Azure 앱 서비스로 논리 앱을 만듭니다. SFTP API에 연결하여 파일을 보내고 받습니다. 파일 만들기, 업데이트, 가져오기 또는 삭제와 같은 다양한 작업을 수행할 수 있습니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 93b77197d69d8c6261ea786aecb653c69129dc9d


---
# <a name="get-started-with-the-sftp-connector"></a>SFTP 커넥터 시작
SFTP 커넥터를 사용하여 SFTP 계정에 액세스하여 파일을 보내고 받습니다. 파일 만들기, 업데이트, 가져오기 또는 삭제와 같은 다양한 작업을 수행할 수 있습니다.  

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../app-service-logic/app-service-logic-create-a-logic-app.md) 시작할 수 있습니다.

## <a name="connect-to-sftp"></a>SFTP에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

### <a name="create-a-connection-to-sftp"></a>SFTP에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>SFTP 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)  

이 예제에서는 파일이 SFTP 서버에 추가되거나 수정될 때 **SFTP - 파일을 추가하거나 수정할 때** 트리거를 사용하여 논리 앱 워크플로를 시작하는 방법을 보여 줍니다. 예제에서는 새 파일 또는 수정된 파일의 콘텐츠를 확인하는 조건을 추가하고 해당 콘텐츠를 사용하기 전에 추출해야 하는 것으로 나타나는 경우 파일을 추출하도록 결정하는 방법에 대해서도 알아봅니다. 마지막으로, 파일의 콘텐츠를 추출하고 추출한 콘텐츠를 SFTP 서버의 폴더에 배치하는 작업을 추가하는 방법에 대해 알아봅니다. 

엔터프라이즈 예에서는 이 트리거를 사용하여 고객의 주문을 나타내는 새 파일에 대한 SFTP 폴더를 모니터링할 수 있습니다.  그런 다음 **파일 콘텐츠 가져오기**와 같은 SFTP 커넥터 작업을 사용하여 추가 처리할 주문 및 주문 데이터베이스에 있는 저장소의 콘텐츠를 가져올 수 있습니다.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>조건 추가
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>SFTP 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="technical-details"></a>기술 세부 정보
이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## <a name="sftp-triggers"></a>SFTP 트리거
SFTP에는 다음과 같은 트리거가 있습니다.  

| 트리거 | 설명 |
| --- | --- |
| [파일을 추가하거나 수정할 때](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |이 작업은 폴더에서 파일을 추가하거나 수정할 때 흐름을 트리거합니다. |

## <a name="sftp-actions"></a>SFTP 작업
SFTP에는 다음과 같은 작업이 있습니다.

| 작업 | 설명 |
| --- | --- |
| [파일 메타데이터 가져오기](connectors-create-api-sftp.md#get-file-metadata) |이 작업은 파일 ID를 사용하여 파일 메타데이터를 가져옵니다. |
| [파일 업데이트](connectors-create-api-sftp.md#update-file) |이 작업은 파일 콘텐츠를 업데이트합니다. |
| [파일 삭제](connectors-create-api-sftp.md#delete-file) |이 작업은 파일을 삭제합니다. |
| [경로를 사용하여 파일 메타데이터 가져오기](connectors-create-api-sftp.md#get-file-metadata-using-path) |이 작업은 파일 경로를 사용하여 파일 메타데이터를 가져옵니다. |
| [경로를 사용하여 파일 콘텐츠 가져오기](connectors-create-api-sftp.md#get-file-content-using-path) |이 작업은 파일 경로를 사용하여 파일 콘텐츠를 가져옵니다. |
| [파일 콘텐츠 가져오기](connectors-create-api-sftp.md#get-file-content) |이 작업은 파일 ID를 사용하여 파일 콘텐츠를 가져옵니다. |
| [파일 만들기](connectors-create-api-sftp.md#create-file) |이 작업은 SFTP 서버에 파일을 업로드합니다. |
| [파일 복사](connectors-create-api-sftp.md#copy-file) |이 작업은 SFTP 서버에 파일을 복사합니다. |
| [폴더의 파일 나열](connectors-create-api-sftp.md#list-files-in-folder) |이 작업은 폴더에 포함된 파일을 가져옵니다. |
| [루트 폴더의 파일 나열](connectors-create-api-sftp.md#list-files-in-root-folder) |이 작업은 루트 폴더의 파일을 가져옵니다. |
| [폴더 추출](connectors-create-api-sftp.md#extract-folder) |이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip). |

### <a name="action-details"></a>작업 세부 정보
이 커넥터에 대한 작업 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.

### <a name="get-file-metadata"></a>파일 메타데이터 가져오기
이 작업은 파일 ID를 사용하여 파일 메타데이터를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 지정 |

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
| id* |파일 |파일 지정 |
| body* |파일 콘텐츠 |업데이트할 파일의 콘텐츠 |

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
| id* |파일 |파일 지정 |

*는 필수 속성을 나타냅니다.

### <a name="get-file-metadata-using-path"></a>경로를 사용하여 파일 메타데이터 가져오기
이 작업은 파일 경로를 사용하여 파일 메타데이터를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| path* |파일 경로 |파일의 고유 경로 |

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
| path* |파일 경로 |파일의 고유 경로 |

*는 필수 속성을 나타냅니다.

### <a name="get-file-content"></a>파일 콘텐츠 가져오기
이 작업은 파일 ID를 사용하여 파일 콘텐츠를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 지정 |

*는 필수 속성을 나타냅니다.

### <a name="create-file"></a>파일 만들기
이 작업은 SFTP 서버에 파일을 업로드합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| folderPath* |폴더 경로 |폴더의 고유 경로 |
| name* |파일 이름 |파일의 이름 |
| body* |파일 콘텐츠 |만들 파일의 콘텐츠 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
BlobMetadata

|  | 속성 이름 | 데이터 형식 |
| --- | --- | --- |
| id |string | |
| 이름 |string | |
| displayName |string | |
| path |string | |
| LastModified |string | |
| 크기 |정수 | |
| MediaType |string | |
| IsFolder |부울 | |
| ETag |string | |
| FileLocator |string | |

### <a name="copy-file"></a>파일 복사
이 작업은 SFTP 서버에 파일을 복사합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| source* |원본 파일 경로 |원본 파일에 대한 경로 |
| destination* |대상 파일 경로 |파일 이름을 포함한 대상 파일의 경로 |
| overwrite |덮어쓰기 |'true'로 설정할 경우 대상 덮어쓰기 |

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

### <a name="when-a-file-is-added-or-modified"></a>파일을 추가하거나 수정할 때
이 작업은 폴더에서 파일을 추가하거나 수정할 때 흐름을 트리거합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| folderId* |폴더 |폴더 지정 |

*는 필수 속성을 나타냅니다.

### <a name="list-files-in-folder"></a>폴더의 파일 나열
이 작업은 폴더에 포함된 파일을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |폴더 |폴더 지정 |

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

### <a name="list-files-in-root-folder"></a>루트 폴더의 파일 나열
이 작업은 루트 폴더의 파일을 가져옵니다. 

이 호출에 대한 매개 변수는 없습니다.

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
이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip). 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| source* |원본 보관 파일 경로 |보관 파일의 경로 |
| destination* |대상 폴더 경로 |대상 폴더에 대한 경로 |
| overwrite |덮어쓰기 |'true'로 설정할 경우 대상 파일 덮어쓰기 |

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


