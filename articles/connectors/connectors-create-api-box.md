---
title: "논리 앱에 Box 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Box 커넥터 개요"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 949579cf-a81c-4790-9ef5-fe39b4fbd0c5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: bb6bef230f4ba4f7019fd2f120c2115d97649b81


---
# <a name="get-started-with-the-box-connector"></a>Box 커넥터 시작
Box에 연결하여 파일 만들기, 파일 삭제 등의 작업을 수행합니다. 

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
> 
> 

Box를 사용하여 다음을 수행할 수 있습니다.

* Box에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 파일을 만들거나 업데이트할 때 트리거를 사용합니다.
* 파일 복사, 파일 삭제 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Box에서 파일이 변경된 경우 해당 파일을 가져와 Office 365를 사용하여 전자 메일로 보낼 수 있습니다.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
Box에는 다음 트리거 및 작업이 포함됩니다.

| 트리거 | 동작 |
| --- | --- |
| <ul><li>파일을 만들 때</li><li>파일을 수정할 때</li></ul> |<ul><li>파일 만들기</li><li>파일을 만들 때</li><li>파일 복사</li><li>파일 삭제</li><li>폴더에 보관 추출</li><li>ID를 사용하여 파일 콘텐츠 가져오기</li><li>경로를 사용하여 파일 콘텐츠 가져오기</li><li>ID를 사용하여 파일 메타데이터 가져오기</li><li>경로를 사용하여 파일 메타데이터 가져오기</li><li>파일 업데이트</li><li>파일을 수정할 때</li></ul> |

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

## <a name="create-a-connection-to-box"></a>Box에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 Box에 연결할 권한을 논리 앱에 부여해야 합니다.

> [!INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]
> 
> 

연결을 만든 후에 Box 속성을 입력합니다. 이 항목의 **REST API 참조** 에서는 이러한 속성에 대해 설명합니다.

> [!TIP]
> 다른 논리 앱에서 이와 동일한 Box 연결을 사용할 수 있습니다.
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 참조
적용 버전: 1.0

### <a name="create-file"></a>파일 만들기
Box에 파일을 업로드합니다.  
```POST: /datasets/default/files```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |예 |쿼리 |없음 |Box에 파일을 업로드할 폴더 경로 |
| 이름 |string |예 |쿼리 |없음 |Box에 만들 파일의 이름 |
| body |string(binary) |예 |body |없음 |Box에 업로드할 파일의 콘텐츠 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="when-a-file-is-created"></a>파일을 만들 때
Box 폴더에서 새 파일을 만들 때 흐름을 트리거합니다.  
```GET: /datasets/default/triggers/onnewfile```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| folderId |string |예 |쿼리 |없음 |Box에서 폴더의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="copy-file"></a>파일 복사
Box에 파일을 복사합니다.  
```POST: /datasets/default/copyFile```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 원본 |string |예 |쿼리 |없음 |원본 파일에 대한 URL |
| destination |string |예 |쿼리 |없음 |대상 파일 이름을 포함한 Box의 대상 파일 경로 |
| overwrite |부울 |아니요 |쿼리 |없음 |'true'로 설정할 경우 대상 덮어쓰기 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="delete-file"></a>파일 삭제
Box에서 파일을 삭제합니다.  
```DELETE: /datasets/default/files/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |Box에서 삭제할 파일의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="extract-archive-to-folder"></a>폴더에 보관 추출
보관 파일(예: .zip)을 Box의 폴더에 추출합니다.  
```POST: /datasets/default/extractFolderV2```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 원본 |string |예 |쿼리 | |보관 파일의 경로 |
| destination |string |예 |쿼리 | |보관 콘텐츠를 추출할 Box의 경로 |
| overwrite |부울 |아니요 |쿼리 | |'true'로 설정할 경우 대상 파일 덮어쓰기 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="get-file-content-using-id"></a>ID를 사용하여 파일 콘텐츠 가져오기
ID를 사용하여 Box에서 파일 콘텐츠를 검색합니다.  
```GET: /datasets/default/files/{id}/content```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |Box 파일의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="get-file-content-using-path"></a>경로를 사용하여 파일 콘텐츠 가져오기
경로를 사용하여 Box에서 파일 콘텐츠를 검색합니다.  
```GET: /datasets/default/GetFileContentByPath```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| path |string |예 |쿼리 |없음 |Box의 파일에 대한 고유 경로 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="get-file-metadata-using-id"></a>ID를 사용하여 파일 메타데이터 가져오기
파일 ID를 사용하여 Box에서 파일 메타데이터를 검색합니다.  
```GET: /datasets/default/files/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |Box 파일의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="get-file-metadata-using-path"></a>경로를 사용하여 파일 메타데이터 가져오기
경로를 사용하여 Box에서 파일 메타데이터를 검색합니다.  
```GET: /datasets/default/GetFileByPath```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| path |string |예 |쿼리 |없음 |Box의 파일에 대한 고유 경로 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="update-file"></a>파일 업데이트
Box에서 파일을 업데이트합니다.  
```PUT: /datasets/default/files/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |Box에서 업데이트할 파일의 고유 식별자 |
| body |string(binary) |예 |body |없음 |Box에서 업데이트할 파일의 콘텐츠 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

### <a name="when-a-file-is-modified"></a>파일을 수정할 때
Box 폴더에서 파일을 수정할 때 흐름을 트리거합니다.  
```GET: /datasets/default/triggers/onupdatedfile```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| folderId |string |예 |쿼리 |없음 |Box에서 폴더의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="object-definitions"></a>개체 정의
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| tabular |정의되지 않음 |no |
| Blob |정의되지 않음 |no |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 원본 |string |no |
| displayName |string |no |
| urlEncoding |string |no |
| tableDisplayName |string |no |
| tablePluralName |string |no |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 원본 |string |no |
| displayName |string |no |
| urlEncoding |string |no |

#### <a name="blobmetadata"></a>BlobMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |no |
| 이름 |string |no |
| displayName |string |no |
| path |string |no |
| LastModified |string |no |
| 크기 |정수 |no |
| MediaType |string |no |
| IsFolder |부울 |no |
| ETag |string |no |
| FileLocator |string |no |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO2-->


