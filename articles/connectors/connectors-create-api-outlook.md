---
title: Outlook.com | Microsoft Docs
description: "Azure 앱 서비스로 논리 앱을 만듭니다. Outlook.com 커넥터를 사용하면 메일, 일정 및 연락처를 관리할 수 있습니다. 메일 보내기, 모임 예약, 연락처 추가 등 다양한 작업을 수행할 수 있습니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 409e1a104fa73d911ea508cbff311cb48fc20f9f


---
# <a name="get-started-with-the-outlookcom-connector"></a>Outlook.com 커넥터 시작
Outlook.com 커넥터를 사용하면 메일, 일정 및 연락처를 관리할 수 있습니다. 메일 보내기, 모임 예약, 연락처 추가 등 다양한 작업을 수행할 수 있습니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
>
>

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
Outlook.com 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 Outlook.com 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### <a name="outlookcom-actions"></a>Outlook.com 작업
다음 작업을 수행할 수 있습니다.

| 작업 | 설명 |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#getemails) |폴더에서 전자 메일 검색 |
| [SendEmail](connectors-create-api-outlook.md#sendemail) |전자 메일 전송 |
| [DeleteEmail](connectors-create-api-outlook.md#deleteemail) |ID별 전자 메일 삭제 |
| [MarkAsRead](connectors-create-api-outlook.md#markasread) |전자 메일을 읽은 것으로 표시 |
| [ReplyTo](connectors-create-api-outlook.md#replyto) |전자 메일에 회신 |
| [GetAttachment](connectors-create-api-outlook.md#getattachment) |ID별로 전자 메일 첨부 파일 검색 |
| [SendMailWithOptions](connectors-create-api-outlook.md#sendmailwithoptions) |여러 옵션이 포함된 메일 전송 및 받는 사람이 옵션 중 하나로 다시 응답하기를 대기 |
| [SendApprovalMail](connectors-create-api-outlook.md#sendapprovalmail) |승인 메일을 보내고 받는 사람으로부터 응답 대기 |
| [CalendarGetTables](connectors-create-api-outlook.md#calendargettables) |일정 검색 |
| [CalendarGetItems](connectors-create-api-outlook.md#calendargetitems) |일정에서 항목 검색 |
| [CalendarPostItem](connectors-create-api-outlook.md#calendarpostitem) |새 이벤트 만들기 |
| [CalendarGetItem](connectors-create-api-outlook.md#calendargetitem) |일정에서 특정 항목 검색 |
| [CalendarDeleteItem](connectors-create-api-outlook.md#calendardeleteitem) |일정 항목 삭제 |
| [CalendarPatchItem](connectors-create-api-outlook.md#calendarpatchitem) |일정 항목 부분적 업데이트 |
| [ContactGetTables](connectors-create-api-outlook.md#contactgettables) |연락처 폴더 검색 |
| [ContactGetItems](connectors-create-api-outlook.md#contactgetitems) |연락처 폴더에서 연락처 검색 |
| [ContactPostItem](connectors-create-api-outlook.md#contactpostitem) |새 연락처 만들기 |
| [ContactGetItem](connectors-create-api-outlook.md#contactgetitem) |연락처 폴더에서 특정 연락처 검색 |
| [ContactDeleteItem](connectors-create-api-outlook.md#contactdeleteitem) |연락처 삭제 |
| [ContactPatchItem](connectors-create-api-outlook.md#contactpatchitem) |연락처 부분적 업데이트 |

### <a name="outlookcom-triggers"></a>Outlook.com 트리거
다음 이벤트를 수신할 수 있습니다.

| 트리거 | 설명 |
| --- | --- |
| 곧 시작되는 이벤트 |예정된 일정 이벤트를 시작할 때 흐름 트리거 |
| 새 전자 메일에 |새 전자 메일이 도착했을 때 흐름 트리거 |
| 새 항목에 |새 일정 항목이 만들어질 때 트리거됨 |
| 업데이트된 항목에 |일정 항목이 수정될 때 트리거됨 |

## <a name="create-a-connection-to-outlookcom"></a>Outlook.com에 대한 연결 만들기
Outlook.com으로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |Outlook.com 자격 증명 제공 |

연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>
> [!TIP]
> 다른 논리 앱에서 이 연결을 사용할 수 있습니다.  
>
>

## <a name="reference-for-outlookcom"></a>Outlook.com에 대한 참조
적용 버전: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
곧 시작되는 이벤트: 예정된 일정 이벤트를 시작할 때 흐름을 트리거합니다.

```GET: /Events/OnUpcomingEvents```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |yes |쿼리 |없음 |일정의 고유 식별자 |
| lookAheadTimeInMinutes |정수 |no |쿼리 |15 |예정된 이벤트에 대해 조회할 시간(분) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 202 |작업이 성공했습니다. |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="getemails"></a>GetEmails
전자 메일 가져오기: 폴더에서 메일을 검색합니다.

```GET: /Mail```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |no |쿼리 |받은 편지함 |전자 메일을 검색하는 폴더의 경로(기본값: '받은 편지함') |
| top |정수 |no |쿼리 |10 |검색할 전자 메일의 수(기본값: 10) |
| fetchOnlyUnread |부울 |no |쿼리 |true |읽지 않은 전자 메일만 검색할 수 있습니까? |
| includeAttachments |부울 |no |쿼리 |false |true로 설정된 경우 첨부 파일도 전자 메일과 함께 검색됩니다. |
| searchQuery |string |no |쿼리 |없음 |전자 메일을 필터링하려는 검색 쿼리 |
| skip |정수 |no |쿼리 |0 |건너뛸 전자 메일의 수(기본값: 0) |
| skipToken |string |no |쿼리 |없음 |새 페이지를 가져오는 토큰 건너뛰기 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="sendemail"></a>SendEmail
전자 메일 보내기: 전자 메일을 보냅니다.

```POST: /Mail```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |yes |body |없음 |Email |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="deleteemail"></a>DeleteEmail
전자 메일 삭제: ID별 전자 메일 삭제

```DELETE: /Mail/{messageId}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |예 |path |없음 |삭제할 전자 메일의 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="markasread"></a>MarkAsRead
읽은 상태로 표시: 전자 메일을 읽은 것으로 표시합니다.

```POST: /Mail/MarkAsRead/{messageId}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |예 |path |없음 |읽은 상태로 표시될 메시지의 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="replyto"></a>ReplyTo
전자 메일에 회신: 전자 메일에 회신합니다.

```POST: /Mail/ReplyTo/{messageId}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |예 |path |없음 |회신할 전자 메일의 ID |
| 주석 |string |yes |쿼리 |없음 |회신 주석 |
| replyAll |부울 |no |쿼리 |false |모든 받는 사람에게 회신 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="getattachment"></a>GetAttachment
첨부 파일 가져오기: ID별로 전자 메일 첨부 파일을 검색합니다.

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |예 |path |없음 |전자 메일의 ID |
| attachmentId |string |예 |path |없음 |다운로드할 첨부 파일의 id |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="onnewemail"></a>OnNewEmail
새 전자 메일에: 새 메일이 도착했을 때 흐름을 트리거합니다.

```GET: /Mail/OnNewEmail```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |no |쿼리 |받은 편지함 |검색할 전자 메일 폴더(기본값: 받은 편지함) |
| to |string |no |쿼리 |없음 |받는 사람 전자 메일 주소 |
| from |string |no |쿼리 |없음 |보낸 사람 주소 |
| 중요도 |string |no |쿼리 |정상 |전자 메일의 중요도(높음, 보통, 낮음) (기본값: 보통) |
| fetchOnlyWithAttachment |부울 |no |쿼리 |false |첨부 파일이 있는 전자 메일만 검색 |
| includeAttachments |부울 |no |쿼리 |false |첨부 파일 포함 |
| subjectFilter |string |no |쿼리 |없음 |제목에서 찾을 문자열 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 202 |수락됨 |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="sendmailwithoptions"></a>SendMailWithOptions
옵션을 사용하여 메일 전송: 여러 옵션이 포함된 메일을 보내고 받는 사람이 옵션 중 하나를 사용하여 다시 응답하기를 기다립니다.

```POST: /mailwithoptions/$subscriptions```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |yes |body |없음 |옵션에 대한 구독 요청 전자 메일 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 201 |구독 만들어짐 |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="sendapprovalmail"></a>SendApprovalMail
승인 전자 메일 보내기: 승인 메일을 보내고 받는 사람으로부터 응답을 기다립니다.

```POST: /approvalmail/$subscriptions```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |yes |body |없음 |승인 전자 메일에 대한 구독 요청 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 201 |구독 만들어짐 |
| 400 |BadRequest |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendargettables"></a>CalendarGetTables
일정 가져오기: 일정을 검색합니다.

```GET: /datasets/calendars/tables```

이 호출에 대한 매개 변수는 없습니다.

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendargetitems"></a>CalendarGetItems
이벤트 가져오기: 일정에서 항목을 검색합니다.

```GET: /datasets/calendars/tables/{table}/items```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |검색할 일정의 고유 식별자 |
| $filter |string |no |쿼리 |없음 |항목의 수를 제한할 ODATA 필터 쿼리 |
| $orderby |string |no |쿼리 |없음 |항목의 순서를 지정하는 ODATA orderBy 쿼리 |
| $skip |정수 |no |쿼리 |없음 |건너뛸 항목의 수(기본값 = 0) |
| $top |정수 |no |쿼리 |없음 |검색할 항목의 최대 수(기본값 = 256) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendarpostitem"></a>CalendarPostItem
이벤트 만들기: 새 이벤트를 만듭니다.

```POST: /datasets/calendars/tables/{table}/items```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |일정의 고유 식별자 |
| 항목 | |예 |body |없음 |만들 일정 항목 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendargetitem"></a>CalendarGetItem
이벤트 가져오기: 일정에서 특정 항목을 검색합니다.

```GET: /datasets/calendars/tables/{table}/items/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |일정의 고유 식별자 |
| id |string |예 |path |없음 |검색할 일정 항목의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendardeleteitem"></a>CalendarDeleteItem
이벤트 삭제: 일정 항목을 삭제합니다.

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |일정의 고유 식별자 |
| id |string |예 |path |없음 |삭제할 일정 항목의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendarpatchitem"></a>CalendarPatchItem
이벤트 업데이트: 일정 항목을 부분적으로 업데이트합니다.

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |일정의 고유 식별자 |
| id |string |예 |path |없음 |업데이트할 일정 항목의 고유 식별자 |
| 항목 | |예 |body |없음 |업데이트할 일정 항목 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
새 항목에: 새 일정 항목이 만들어질 때 트리거됩니다.

```GET: /datasets/calendars/tables/{table}/onnewitems```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |일정의 고유 식별자 |
| $filter |string |no |쿼리 |없음 |항목의 수를 제한할 ODATA 필터 쿼리 |
| $orderby |string |no |쿼리 |없음 |항목의 순서를 지정하는 ODATA orderBy 쿼리 |
| $skip |정수 |no |쿼리 |없음 |건너뛸 항목의 수(기본값 = 0) |
| $top |정수 |no |쿼리 |없음 |검색할 항목의 최대 수(기본값 = 256) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
업데이트된 항목에: 일정 항목이 수정될 때 트리거됩니다.

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |일정의 고유 식별자 |
| $filter |string |no |쿼리 |없음 |항목의 수를 제한할 ODATA 필터 쿼리 |
| $orderby |string |no |쿼리 |없음 |항목의 순서를 지정하는 ODATA orderBy 쿼리 |
| $skip |정수 |no |쿼리 |없음 |건너뛸 항목의 수(기본값 = 0) |
| $top |정수 |no |쿼리 |없음 |검색할 항목의 최대 수(기본값 = 256) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="contactgettables"></a>ContactGetTables
연락처 폴더 가져오기: 연락처 폴더를 검색합니다.

```GET: /datasets/contacts/tables```

이 호출에 대한 매개 변수는 없습니다.

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="contactgetitems"></a>ContactGetItems
연락처 가져오기: 연락처 폴더에서 연락처를 검색합니다.

```GET: /datasets/contacts/tables/{table}/items```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |검색할 연락처 폴더의 고유 식별자 |
| $filter |string |no |쿼리 |없음 |항목의 수를 제한할 ODATA 필터 쿼리 |
| $orderby |string |no |쿼리 |없음 |항목의 순서를 지정하는 ODATA orderBy 쿼리 |
| $skip |정수 |no |쿼리 |없음 |건너뛸 항목의 수(기본값 = 0) |
| $top |정수 |no |쿼리 |없음 |검색할 항목의 최대 수(기본값 = 256) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="contactpostitem"></a>ContactPostItem
연락처 만들기: 새 연락처를 만듭니다.

```POST: /datasets/contacts/tables/{table}/items```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |연락처 폴더의 고유 식별자 |
| 항목 | |예 |body |없음 |만들 연락처 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="contactgetitem"></a>ContactGetItem
연락처 가져오기: 연락처 폴더에서 특정 연락처를 검색합니다.

```GET: /datasets/contacts/tables/{table}/items/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |연락처 폴더의 고유 식별자 |
| id |string |예 |path |없음 |검색할 연락처의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="contactdeleteitem"></a>ContactDeleteItem
연락처 삭제: 연락처를 삭제합니다.

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |연락처 폴더의 고유 식별자 |
| id |string |예 |path |없음 |삭제할 연락처의 고유 식별자 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="contactpatchitem"></a>ContactPatchItem
연락처 업데이트: 연락처를 부분적으로 업데이트합니다.

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 테이블 |string |예 |path |없음 |연락처 폴더의 고유 식별자 |
| id |string |예 |path |없음 |업데이트할 연락처의 고유 식별자 |
| 항목 | |예 |body |없음 |업데이트할 연락처 항목 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 기본값 |작업이 실패했습니다. |

## <a name="object-definitions"></a>개체 정의
### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse[IDictionary[String,Object]]
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="object"></a>Object
### <a name="sendmessage"></a>SendMessage
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 첨부 파일 |array |아니요 |
| 원본 |string |아니요 |
| 참조 |string |아니요 |
| Bcc |string |아니요 |
| 제목
 |string |예 |
| 본문 |string |예 |
| 중요도
 |string |아니요 |
| IsHtml |부울 |아니요 |
| 받는 사람
 |string |예 |

### <a name="sendattachment"></a>SendAttachment
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| @odata.type |string |아니요 |
| Name |string |예 |
| ContentBytes |string |예 |

### <a name="receivemessage"></a>ReceiveMessage
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |아니요 |
| IsRead |부울 |아니요 |
| HasAttachment |부울 |아니요 |
| DateTimeReceived |string |아니요 |
| 첨부 파일 |array |아니요 |
| 원본 |string |아니요 |
| 참조 |string |아니요 |
| Bcc |string |아니요 |
| 제목
 |string |예 |
| 본문 |string |예 |
| 중요도
 |string |아니요 |
| IsHtml |부울 |아니요 |
| 받는 사람
 |string |예 |

### <a name="receiveattachment"></a>ReceiveAttachment
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |예 |
| ContentType |string |예 |
| @odata.type |string |아니요 |
| Name |string |예 |
| ContentBytes |string |예 |

### <a name="digestmessage"></a>DigestMessage
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 제목
 |string |예 |
| 본문 |string |아니요 |
| 중요도
 |string |아니요 |
| Digest |array |예 |
| 첨부 파일 |array |아니요 |
| 받는 사람
 |string |예 |

### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse[ReceiveMessage]
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="datasetsmetadata"></a>DataSetsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| tabular |정의되지 않음 |아니요 |
| Blob |정의되지 않음 |아니요 |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 원본 |string |아니요 |
| displayName |string |아니요 |
| urlEncoding |string |아니요 |
| tableDisplayName |string |아니요 |
| tablePluralName |string |아니요 |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 원본 |string |아니요 |
| displayName |string |아니요 |
| urlEncoding |string |아니요 |

### <a name="tablemetadata"></a>TableMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |아니요 |
| title |string |아니요 |
| x-ms-permission |string |아니요 |
| x-ms-capabilities |정의되지 않음 |아니요 |
| schema |정의되지 않음 |아니요 |

### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| sortRestrictions |정의되지 않음 |아니요 |
| filterRestrictions |정의되지 않음 |아니요 |
| filterFunctions |array |아니요 |

### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| sortable |부울 |아니요 |
| unsortableProperties |array |아니요 |
| ascendingOnlyProperties |array |아니요 |

### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| filterable |부울 |아니요 |
| nonFilterableProperties |array |아니요 |
| requiredProperties |array |아니요 |

### <a name="optionsemailsubscription"></a>OptionsEmailSubscription
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| NotificationUrl |string |아니요 |
| Message |정의되지 않음 |아니요 |

### <a name="messagewithoptions"></a>MessageWithOptions
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 제목
 |string |예 |
| 옵션 |string |예 |
| 본문 |string |아니요 |
| 중요도
 |string |아니요 |
| 첨부 파일 |array |아니요 |
| 받는 사람
 |string |예 |

### <a name="subscriptionresponse"></a>SubscriptionResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |아니요 |
| resource |string |아니요 |
| notificationType |string |아니요 |
| notificationUrl |string |아니요 |

### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| NotificationUrl |string |아니요 |
| Message |정의되지 않음 |아니요 |

### <a name="approvalmessage"></a>ApprovalMessage
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 제목
 |string |예 |
| 옵션 |string |예 |
| 본문 |string |아니요 |
| 중요도
 |string |아니요 |
| 첨부 파일 |array |아니요 |
| 받는 사람
 |string |예 |

### <a name="approvalemailresponse"></a>ApprovalEmailResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| SelectedOption |string |아니요 |

### <a name="tableslist"></a>TablesList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="table"></a>테이블
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |아니요 |
| DisplayName |string |아니요 |

### <a name="item"></a>항목
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| ItemInternalId |string |아니요 |

### <a name="calendaritemslist"></a>CalendarItemsList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="calendaritem"></a>CalendarItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| ItemInternalId |string |아니요 |

### <a name="contactitemslist"></a>ContactItemsList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="contactitem"></a>ContactItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| ItemInternalId |string |아니요 |

### <a name="datasetslist"></a>DataSetsList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="dataset"></a>DataSet
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |아니요 |
| DisplayName |string |아니요 |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)



<!--HONumber=Jan17_HO3-->


