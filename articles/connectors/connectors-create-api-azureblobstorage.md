---
title: "Logic Apps에 Azure Blob Storage 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Azure Blob 저장소 커넥터 개요"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9a739af2c9f6adaec58bfd7b339c370fae113641


---
# <a name="get-started-with-the-azure-blob-storage-connector"></a>Azure Blob 저장소 커넥터 시작
Azure Blob 저장소는 많은 양의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. Azure Blob 저장소에서 Blob 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다. 

Azure Blob 저장소를 사용하여 다음과 같은 작업을 수행합니다.

* 새 프로젝트를 업로드하거나 최근에 업데이트한 파일을 가져와 워크플로를 작성합니다.
* 파일 메타데이터 가져오기, 파일 삭제, 파일 복사 및 삭제를 위한 작업을 사용합니다. 예를 들어 도구가 Azure 웹 사이트에서 업데이트되면(트리거) Blob 저장소에서 파일을 업데이트합니다(작업). 

이 항목에서는 논리 앱에서 Blob 스토리지 커넥터를 사용하는 방법을 보여 주고 작업을 나열합니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 GA(일반 공급)에 적용됩니다. 
> 
> 

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../app-service-logic/app-service-logic-what-are-logic-apps.md) 및 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## <a name="connect-to-azure-blob-storage"></a>Azure Blob 저장소에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 저장소 계정에 연결하려면 먼저 Blob 저장소 *연결*을 만듭니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 Azure 저장소를 사용하는 경우 저장소 계정에 대한 자격 증명을 입력하여 연결을 만듭니다. 

#### <a name="create-the-connection"></a>연결 만들기
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]
> 
> 

## <a name="use-a-trigger"></a>트리거 사용
이 연결에는 트리거가 필요하지 않습니다. 다른 트리거(되풀이 트리거, HTTP 웹후크 트리거, 다른 커넥터와 함께 사용할 수 있는 트리거 포함)를 사용하여 논리 앱을 시작합니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)에서 예제를 제공하고 있습니다.

## <a name="use-an-action"></a>작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다.

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 중 하나 등 몇 가지가 표시됩니다.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. **작업 추가**를 선택합니다.
3. 사용 가능한 모든 작업의 목록을 표시하려면 텍스트 상자에 "blob"을 입력합니다.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. 이 예제에서는 **AzureBlob - 경로를 사용하여 파일 메타데이터 가져오기**를 선택합니다. 연결이 이미 있는 경우 **...** (선택기 표시) 단추를 선택하여 파일을 선택합니다.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 항목의 [연결 만들기](connectors-create-api-azureblobstorage.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다. 
   
   > [!NOTE]
   > 이 예제에서는 파일의 메타 데이터를 가져옵니다. 메타데이터를 보려면 다른 커넥터를 사용하여 새 파일을 만드는 다른 작업을 추가합니다. 예를 들어 메타데이터에 따라 새 "test" 파일을 만드는 OneDrive 작업을 추가합니다. 
   > 
   > 
5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

> [!TIP]
> [저장소 탐색기](http://storageexplorer.com/)는 여러 저장소 계정을 관리하는 유용한 도구입니다.
> 
> 

## <a name="technical-details"></a>기술 세부 정보
## <a name="storage-blob-actions"></a>저장소 Blob 작업
| 작업 | 설명 |
| --- | --- |
| [파일 메타데이터 가져오기](connectors-create-api-azureblobstorage.md#get-file-metadata) |이 작업은 파일 ID를 사용하여 파일 메타데이터를 가져옵니다. |
| [파일 업데이트](connectors-create-api-azureblobstorage.md#update-file) |이 작업은 파일을 업데이트합니다. |
| [파일 삭제](connectors-create-api-azureblobstorage.md#delete-file) |이 작업은 파일을 삭제합니다. |
| [경로를 사용하여 파일 메타데이터 가져오기](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path) |이 작업은 경로를 사용하여 파일 메타데이터를 가져옵니다. |
| [경로를 사용하여 파일 콘텐츠 가져오기](connectors-create-api-azureblobstorage.md#get-file-content-using-path) |이 작업은 경로를 사용하여 파일 콘텐츠를 가져옵니다. |
| [파일 콘텐츠 가져오기](connectors-create-api-azureblobstorage.md#get-file-content) |이 작업은 ID를 사용하여 파일 콘텐츠를 가져옵니다. |
| [파일 만들기](connectors-create-api-azureblobstorage.md#create-file) |이 작업은 파일을 업로드합니다. |
| [파일 복사](connectors-create-api-azureblobstorage.md#copy-file) |이 작업은 Azure Blob 저장소로 파일을 복사합니다. |
| [폴더에 보관 추출](connectors-create-api-azureblobstorage.md#extract-archive-to-folder) |이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip). |

### <a name="action-details"></a>작업 세부 정보
이 섹션에서는 모든 필수 또는 선택적 입력 속성 및 커넥터와 연결된 모든 해당 출력을 비롯한 각 작업에 대한 특정 세부 정보를 참조하세요.

#### <a name="get-file-metadata"></a>파일 메타데이터 가져오기
이 작업은 파일 ID를 사용하여 파일 메타데이터를 가져옵니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 선택 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- |
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

#### <a name="update-file"></a>파일 업데이트
이 작업은 파일을 업데이트합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 선택 |
| body* |파일 콘텐츠 |업데이트할 파일의 콘텐츠 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- |
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

#### <a name="delete-file"></a>파일 삭제
이 작업은 파일을 삭제합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| id* |파일 |파일 선택 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
없음.

#### <a name="get-file-metadata-using-path"></a>경로를 사용하여 파일 메타데이터 가져오기
이 작업은 경로를 사용하여 파일 메타데이터를 가져옵니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| path* |파일 경로 |파일 선택 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- |
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

#### <a name="get-file-content-using-path"></a>경로를 사용하여 파일 콘텐츠 가져오기
이 작업은 경로를 사용하여 파일 콘텐츠를 가져옵니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| path* |파일 경로 |파일 선택 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
없음.

#### <a name="get-file-content"></a>파일 콘텐츠 가져오기
이 작업은 ID를 사용하여 파일 콘텐츠를 가져옵니다.  

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| id* |string |파일 선택 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
없음.

#### <a name="create-file"></a>파일 만들기
이 작업은 파일을 업로드합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| folderPath* |폴더 경로 |폴더 선택 |
| name* |파일 이름 |업로드할 파일의 이름 |
| body* |파일 콘텐츠 |업로드할 파일의 콘텐츠 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- |
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

#### <a name="copy-file"></a>파일 복사
이 작업은 Azure Blob 저장소로 파일을 복사합니다.  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| source* |원본 URL |원본 파일에 대한 URL 지정 |
| destination* |대상 파일 경로 |대상 파일 이름을 포함한 대상 파일 경로 지정 |
| overwrite |덮어쓰기 |기존 대상 파일을 덮어쓰시겠습니까(true/false)? |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- |
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

#### <a name="extract-archive-to-folder"></a>폴더에 보관 추출
이 작업은 보관 파일을 FTP 서버의 폴더에 추출합니다(예: .zip).  

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| source* |원본 보관 파일 경로 |보관 파일 선택 |
| destination* |대상 폴더 경로 |추출할 콘텐츠 선택 |
| overwrite |덮어쓰기 |기존 대상 파일을 덮어쓰시겠습니까(true/false)? |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
BlobMetadata

| 속성 이름 | 데이터 형식 |
| --- | --- |
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
다른 작업을 호출할 때 특정 응답이 발생할 수 있습니다. 다음 표에서는 응답 및 해당 설명을 대략적으로 설명합니다.  

| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.




<!--HONumber=Nov16_HO3-->


