---
title: Wunderlist | Microsoft Docs
description: "Azure 앱 서비스로 논리 앱을 만듭니다. Wunderlist는 사람들이 작업할 수 있도록 할 일 목록 및 작업 관리자를 제공합니다.  친구와 식료품 목록 공유하거나 프로젝트에 대해 작업하고 휴가를 계획할 경우 Wunderlist를 사용하여 할 일을 쉽게 캡처하고 공유하며 완료할 수 있습니다. Wunderlist는 어디에서든 모든 작업에 액세스할 수 있도록 전화, 태블릿 및 컴퓨터 간을 즉시 동기화합니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7a528a47373d7e9ae2cdc7d62328ed8e83112863


---
# <a name="get-started-with-the-wunderlist-connector"></a>Wunderlist 커넥터 시작
Wunderlist는 사람들이 작업할 수 있도록 할 일 목록 및 작업 관리자를 제공합니다.  친구와 식료품 목록 공유하거나 프로젝트에 대해 작업하고 휴가를 계획할 경우 Wunderlist를 사용하여 할 일을 쉽게 캡처하고 공유하며 완료할 수 있습니다. Wunderlist는 어디에서든 모든 작업에 액세스할 수 있도록 전화, 태블릿 및 컴퓨터 간을 즉시 동기화합니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
> 
> 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
Wunderlist 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 Wunderlist 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### <a name="wunderlist-actions"></a>Wunderlist 작업
다음 작업을 수행할 수 있습니다.

| 작업 | 설명 |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |계정에 연결된 목록을 검색합니다. |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |목록을 만듭니다. |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |특정 목록에서 작업을 검색합니다. |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |작업을 만듭니다. |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |특정 목록 또는 특정 작업에서 하위 작업을 검색합니다. |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |특정 작업 내에서 하위 작업을 만듭니다. |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |특정 목록이나 특정 작업에 대한 정보를 검색합니다. |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |특정 작업에 참고 사항을 추가합니다. |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |특정 목록이나 특정 작업에 대한 작업 주석을 검색합니다. |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |특정 작업에 주석을 추가합니다. |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |특정 목록이나 특정 작업에 대한 미리 알림을 검색합니다. |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |미리 알림을 설정합니다. |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |특정 목록이나 특정 작업에 대한 파일을 검색합니다. |
| [GetList](connectors-create-api-wunderlist.md#getlist) |특정 목록을 검색합니다. |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |목록을 삭제합니다 |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |특정 목록을 업데이트합니다. |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |특정 작업을 검색합니다. |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |특정 작업을 업데이트합니다. |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |특정 작업을 삭제합니다. |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |특정 하위 작업을 검색합니다. |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |특정 하위 작업을 업데이트합니다. |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |특정 하위 작업을 삭제합니다. |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |특정 참고 사항을 검색합니다. |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |특정 참고 사항을 업데이트합니다. |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |특정 참고 사항을 삭제합니다. |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |특정 작업 주석을 검색합니다. |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |특정 미리 알림을 업데이트합니다. |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |특정 미리 알림을 삭제합니다. |

### <a name="wunderlist-triggers"></a>Wunderlist 트리거
다음 이벤트를 수신할 수 있습니다.

| 트리거 | 설명 |
| --- | --- |
| 작업이 만료되는 경우 |목록의 작업이 만료되는 경우 새 흐름을 트리거합니다. |
| 새 작업을 만든 경우 |목록에 새 작업을 만든 경우 새 흐름을 트리거합니다. |
| 미리 알림이 발생할 경우 |미리 알림이 발생하는 경우 새 흐름을 트리거합니다. |

## <a name="create-a-connection-to-wunderlist"></a>Wunderlist에 대한 연결 만들기
Wunderlist로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |Wunderlist 자격 증명을 제공 합니다. |

연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> 다른 논리 앱에서 이 연결을 사용할 수 있습니다.
> 
> 

## <a name="reference-for-wunderlist"></a>Wunderlist에 대한 참조
적용 버전: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
작업이 만료되는 경우: 목록의 작업이 만료되는 경우 새 흐름을 트리거합니다.

```GET: /trigger/tasksdue```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |

## <a name="triggertasknew"></a>TriggerTaskNew
새 작업을 만든 경우: 목록에 새 작업을 만든 경우 새 흐름을 트리거합니다.

```GET: /trigger/tasksnew```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |

## <a name="triggerreminder"></a>TriggerReminder
미리 알림이 발생할 경우: 미리 알림이 발생하는 경우 새 흐름을 트리거합니다.

```GET: /trigger/reminders```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| task_id |정수 |no |쿼리 |없음 |작업 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |

## <a name="retrievelists"></a>RetrieveLists
목록 가져오기: 계정에 연결된 목록을 검색합니다.

```GET: /lists```

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createlist"></a>CreateList
목록 만들기: 목록을 만듭니다.

```POST: /lists```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |없음 |만들 새 목록 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 기본값 |작업이 실패했습니다. |

## <a name="listtasks"></a>ListTasks
작업 가져오기: 특정 목록에서 작업을 검색합니다.

```GET: /tasks```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| 완료됨 |부울 |no |쿼리 |없음 |Completed |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createtask"></a>CreateTask
작업 만들기: 작업을 만듭니다.

```POST: /tasks```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |없음 |만들 새 작업 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 201 |작성자 |

## <a name="listsubtasks"></a>ListSubTasks
하위 작업 가져오기: 특정 목록 또는 특정 작업에서 하위 작업을 검색합니다.

```GET: /subtasks```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| task_id |정수 |no |쿼리 |없음 |작업 ID |
| 완료됨 |부울 |no |쿼리 |없음 |Completed |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createsubtask"></a>CreateSubTask
하위 작업 만들기: 특정 작업 내에서 하위 작업을 만듭니다.

```POST: /subtasks```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |없음 |만들 새 하위 작업 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 201 |작성자 |

## <a name="listnotes"></a>ListNotes
참고 사항 가져오기: 특정 목록이나 특정 작업에 대한 정보를 검색합니다.

```GET: /notes```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| task_id |정수 |no |쿼리 |없음 |작업 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createnote"></a>CreateNote
참고 사항 만들기: 특정 작업에 참고 사항을 추가합니다.

```POST: /notes```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |없음 |만들 새 참고 사항 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 201 |작성자 |

## <a name="listcomments"></a>ListComments
작업 주석 가져오기: 특정 목록이나 특정 작업에 대한 작업 주석을 검색합니다.

```GET: /task_comments```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| task_id |정수 |no |쿼리 |없음 |작업 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createcomment"></a>CreateComment
작업에 주석 추가: 특정 작업에 주석을 추가합니다.

```POST: /task_comments```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |없음 |만들 새 작업 주석 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 201 |작성자 |

## <a name="retrievereminders"></a>RetrieveReminders
미리 알림 가져오기: 특정 목록이나 특정 작업에 대한 미리 알림을 검색합니다.

```GET: /reminders```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| task_id |정수 |no |쿼리 |없음 |작업 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createreminder"></a>CreateReminder
미리 알림 설정: 미리 알림을 설정합니다.

```POST: /reminders```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |없음 |만들 새 미리 알림 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 기본값 |작업이 실패했습니다. |

## <a name="retrievefiles"></a>RetrieveFiles
파일 가져오기: 특정 목록이나 특정 작업에 대한 파일을 검색합니다.

```GET: /files```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| task_id |정수 |no |쿼리 |없음 |작업 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="getlist"></a>GetList
목록 가져오기: 특정 목록을 검색합니다.

```GET: /lists/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |목록 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="deletelist"></a>DeleteList
목록 삭제: 목록을 삭제합니다.

```DELETE: /lists/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |목록 ID |
| revision |정수 |yes |쿼리 |없음 |수정 버전 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 204 |내용 없음 |

## <a name="updatelist"></a>UpdateList
목록 업데이트: 특정 목록을 업데이트합니다.

```PATCH: /lists/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |목록 ID |
| post | |yes |body |없음 |목록 세부 정보 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="gettask"></a>GetTask
작업 가져오기: 특정 작업을 검색합니다.

```GET: /tasks/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| id |정수 |예 |path |없음 |작업 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="updatetask"></a>UpdateTask
작업 업데이트: 특정 작업을 업데이트합니다.

```PATCH: /tasks/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| id |정수 |예 |path |없음 |작업 ID |
| post | |yes |body |없음 |태스크 세부 정보 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="deletetask"></a>DeleteTask
작업 삭제: 특정 작업을 삭제합니다.

```DELETE: /tasks/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |정수 |yes |쿼리 |없음 |목록 ID |
| id |정수 |예 |path |없음 |작업 ID |
| revision |정수 |yes |쿼리 |없음 |수정 버전 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 204 |내용 없음 |

## <a name="getsubtask"></a>GetSubTask
하위 작업 가져오기: 특정 하위 작업을 검색합니다.

```GET: /subtasks/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |하위 작업 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="updatesubtask"></a>UpdateSubTask
하위 작업 업데이트: 특정 하위 작업을 업데이트합니다.

```PATCH: /subtasks/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |하위 작업 ID |
| post | |yes |body |없음 |하위 작업 세부 정보 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="deletesubtask"></a>DeleteSubTask
하위 작업 삭제: 특정 하위 작업을 삭제합니다.

```DELETE: /subtasks/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |하위 작업 ID |
| revision |정수 |yes |쿼리 |없음 |수정 버전 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 204 |내용 없음 |

## <a name="getnote"></a>GetNote
참고 사항 가져오기: 특정 참고 사항을 검색합니다.

```GET: /notes/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |참고 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="updatenote"></a>UpdateNote
참고 사항 업데이트: 특정 참고 사항을 업데이트합니다.

```PATCH: /notes/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |참고 ID |
| post | |yes |body |없음 |참고 사항 세부 정보 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="deletenote"></a>DeleteNote
참고 사항 삭제: 특정 참고 사항을 삭제합니다.

```DELETE: /notes/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |참고 ID |
| revision |정수 |yes |쿼리 |없음 |수정 버전 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 204 |내용 없음 |

## <a name="getcomment"></a>GetComment
작업 주석 가져오기: 특정 작업 주석을 검색합니다.

```GET: /task_comments/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |string |예 |path |없음 |주석 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="updatereminder"></a>UpdateReminder
미리 알림 업데이트: 특정 미리 알림을 업데이트합니다.

```PATCH: /reminders/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |미리 알림 ID |
| post | |yes |body |없음 |미리 알림 세부 정보 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |

## <a name="deletereminder"></a>DeleteReminder
미리 알림 삭제: 특정 미리 알림을 삭제합니다.

```DELETE: /reminders/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| id |정수 |예 |path |없음 |미리 알림의 ID |
| revision |정수 |yes |쿼리 |없음 |수정 버전 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 204 |내용 없음 |

## <a name="object-definitions"></a>개체 정의
### <a name="list"></a>나열
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| created_at |string |아니요 |
| title |string |아니요 |
| list_type |string |아니요 |
| type |string |아니요 |
| revision |정수 |아니요 |

### <a name="createdlist"></a>CreatedList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| created_at |string |아니요 |
| title |string |아니요 |
| revision |정수 |아니요 |
| type |string |아니요 |

### <a name="task"></a>작업
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| assignee_id |정수 |아니요 |
| assigner_id |정수 |아니요 |
| created_at |string |아니요 |
| created_by_id |정수 |아니요 |
| due_date |string |아니요 |
| list_id |정수 |아니요 |
| revision |정수 |아니요 |
| starred |부울 |아니요 |
| title |string |아니요 |

### <a name="subtask"></a>하위 작업
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| task_id |정수 |아니요 |
| created_at |string |아니요 |
| created_by_id |정수 |아니요 |
| revision |string |아니요 |
| title |string |아니요 |

### <a name="note"></a>참고
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| task_id |정수 |아니요 |
| 콘텐츠 |string |아니요 |
| created_at |string |아니요 |
| updated_at |string |아니요 |
| revision |정수 |아니요 |

### <a name="comment"></a>주석
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| task_id |정수 |아니요 |
| revision |정수 |아니요 |
| 텍스트 |string |아니요 |
| type |string |아니요 |
| created_at |string |아니요 |

### <a name="reminder"></a>미리 알림
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| date |string |아니요 |
| task_id |정수 |아니요 |
| revision |정수 |아니요 |
| type |string |아니요 |
| created_at |string |아니요 |
| updated_at |string |아니요 |

### <a name="createdreminder"></a>CreatedReminder
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| date |string |아니요 |
| task_id |정수 |아니요 |
| revision |정수 |아니요 |
| created_at |string |아니요 |
| updated_at |string |아니요 |

### <a name="file"></a>파일
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| url |string |아니요 |
| task_id |정수 |아니요 |
| list_id |정수 |아니요 |
| user_id |정수 |아니요 |
| file_name |string |아니요 |
| content_type |string |아니요 |
| file_size |정수 |아니요 |
| local_created_at |string |아니요 |
| created_at |string |아니요 |
| updated_at |string |아니요 |
| type |string |아니요 |
| revision |정수 |아니요 |

### <a name="newtask"></a>NewTask
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| list_id |정수 |예 |
| title |string |예 |
| assignee_id |정수 |아니요 |
| 완료됨 |부울 |아니요 |
| recurrence_type |string |아니요 |
| recurrence_count |정수 |아니요 |
| due_date |string |아니요 |
| starred |부울 |아니요 |

### <a name="newlist"></a>NewList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| title |string |예 |

### <a name="newsubtask"></a>NewSubtask
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| list_id |정수 |예 |
| task_id |정수 |예 |
| title |string |예 |
| 완료됨 |부울 |아니요 |

### <a name="newnote"></a>NewNote
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| list_id |정수 |예 |
| task_id |정수 |예 |
| 콘텐츠 |string |예 |

### <a name="newcomment"></a>NewComment
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| list_id |정수 |예 |
| task_id |정수 |예 |
| 텍스트 |string |예 |

### <a name="newreminder"></a>NewReminder
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| list_id |정수 |예 |
| task_id |정수 |예 |
| date |string |예 |

### <a name="updatetask"></a>UpdateTask
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| revision |정수 |아니요 |
| title |string |아니요 |
| assignee_id |정수 |아니요 |
| 완료됨 |부울 |아니요 |
| recurrence_type |string |아니요 |
| recurrence_count |정수 |아니요 |
| due_date |string |아니요 |
| starred |부울 |아니요 |

### <a name="updatelist"></a>UpdateList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| revision |정수 |아니요 |
| title |string |아니요 |

### <a name="updatesubtask"></a>UpdateSubtask
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| revision |정수 |아니요 |
| title |string |아니요 |
| 완료됨 |부울 |아니요 |

### <a name="updatenote"></a>UpdateNote
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| revision |정수 |아니요 |
| 콘텐츠 |string |아니요 |

### <a name="updatereminder"></a>UpdateReminder
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| date |string |아니요 |
| revision |정수 |아니요 |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


