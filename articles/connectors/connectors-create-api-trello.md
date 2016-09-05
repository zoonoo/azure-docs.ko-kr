<properties
pageTitle="Trello | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. Trello는 회사 및 집에서 모든 프로젝트를 통해 큐브 뷰를 제공합니다. 쉽고 무료로 유연하며 시각적인 방법으로 프로젝트를 관리하고 무엇이든 구성할 수 있습니다. Trello에 연결하여 보드, 목록 및 카드 관리"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# Trello 커넥터 시작

Trello는 회사 및 집에서 모든 프로젝트를 통해 큐브 뷰를 제공합니다. 쉽고 무료로 유연하며 시각적인 방법으로 프로젝트를 관리하고 무엇이든 구성할 수 있습니다. Trello에 연결하여 보드, 목록 및 카드를 관리합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

Trello 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 Trello 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### Trello 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|보드에 카드 나열|
|[GetCard](connectors-create-api-trello.md#getcard)|ID별로 카드 가져오기|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|카드 업데이트|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|카드 삭제|
|[CreateCard](connectors-create-api-trello.md#createcard)|Trello 계정에 새 카드 만들기|
|[ListBoards](connectors-create-api-trello.md#listboards)|보드 나열|
|[GetBoard](connectors-create-api-trello.md#getboard)|ID별로 보드 가져오기|
|[ListLists](connectors-create-api-trello.md#listlists)|보드에 카드 목록 나열|
|[GetList](connectors-create-api-trello.md#getlist)|ID별로 목록 가져오기|
### Trello 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|보드에 새 카드를 추가한 경우|보드에 새 카드를 추가한 경우 흐름 트리거|
|목록에 새 카드를 추가한 경우|목록에 새 카드를 추가한 경우 흐름 트리거|


## Trello에 대한 연결 만들기
Trello로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

|속성| 필수|설명|
| ---|---|---|
|위임|예|Trello 자격 증명 제공|
연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

>[AZURE.INCLUDE [Trello에 대한 연결을 만드는 단계](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Trello에 대한 참조
적용 버전: 1.0

## OnNewCardInBoard
보드에 새 카드를 추가한 경우: 보드에 새 카드를 추가한 경우 흐름 트리거

```GET: /trigger/boards/{board_id}/cards```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|없음|카드를 인출할 보드의 고유 ID|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## OnNewCardInList
목록에 새 카드를 추가한 경우: 목록에 새 카드를 추가한 경우 흐름 트리거

```GET: /trigger/lists/{list_id}/cards```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|쿼리|없음|카드를 인출할 보드의 고유 ID|
|list\_id|string|yes|path|없음|카드를 인출할 목록의 고유 ID|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## ListCards
보드에 카드 나열: 보드에 카드 나열

```GET: /boards/{board_id}/cards```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|없음|카드를 모두 인출할 보드의 ID|
|actions|string|no|쿼리|없음|반환할 작업을 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|attachments|부울|no|쿼리|없음|첨부 파일 표시|
|attachment\_fields|string|no|쿼리|없음|반환할 첨부 파일 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|stickers|부울|no|쿼리|없음|스티커 표시|
|members|부울|no|쿼리|없음|멤버 표시|
|memeber\_fields|string|no|쿼리|없음|반환할 멤버 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|CheckItemStates|부울|no|쿼리|없음|카드 상태 반환|
|검사 목록|string|no|쿼리|없음|검사 목록 표시|
|limit|정수|no|쿼리|없음|1과 1000 사이에서 반환할 결과의 최대 수입니다.|
|since|string|no|쿼리|없음|이 날짜 이후 모든 카드 인출|
|이전|string|no|쿼리|없음|이 날짜 이전 모든 카드 인출|
|filter|string|no|쿼리|없음|응답 필터링|
|fields|string|no|쿼리|없음|반환할 카드 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## GetCard
ID별로 카드 가져오기: ID별로 카드 가져오기

```GET: /cards/{card_id}```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|쿼리|없음|카드를 인출할 보드의 ID|
|card\_id|string|yes|path|없음|인출할 카드의 ID|
|actions|string|no|쿼리|없음|반환할 작업을 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|actions\_entities|부울|no|쿼리|없음|작업 엔터티 반환|
|actions\_display|부울|no|쿼리|없음|동작 표시 반환|
|actions\_limit|정수|no|쿼리|없음|반환할 작업의 최대 수|
|action\_fields|string|no|쿼리|없음|각 작업에 반환할 작업 필드의 목록입니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|action\_memberCreator\_fields|string|no|쿼리|없음|반환할 작업 멤버 작성자 필드의 목록입니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|attachments|부울|no|쿼리|없음|첨부 파일 반환|
|attachement\_fields|string|no|쿼리|없음|각 첨부 파일에 대해 반환할 첨부 파일 필드의 목록입니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|members|부울|no|쿼리|없음|멤버 반환|
|member\_fields|string|no|쿼리|없음|각 멤버에 반환할 멤버 필드의 목록입니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|membersVoted|부울|no|쿼리|없음|가결된 멤버 반환|
|memberVoted\_fields|string|no|쿼리|없음|각 가결된 멤버에 대해 반환할 가결된 멤버 필드의 목록입니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|checkItemStates|부울|no|쿼리|없음|카드 상태 반환|
|checkItemState\_fields|string|no|쿼리|없음|각 카드 항목 상태에 대해 반환할 상태 필드의 목록입니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|checklists|string|no|쿼리|없음|검사 목록 반환|
|checklist\_fields|string|no|쿼리|없음|각 검사 목록에 대해 반환할 검사 목록 필드의 목록입니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|board|부울|no|쿼리|없음|카드가 속한 보드 반환|
|board\_fields|string|no|쿼리|없음|반환할 보드 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|list|부울|no|쿼리|없음|카드가 속한 목록 반환|
|list\_fields|string|no|쿼리|없음|반환할 목록 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|stickers|부울|no|쿼리|없음|스티커 반환|
|sticker\_fields|string|no|쿼리|없음|각 스티커에 대해 반환할 스티커 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|fields|string|no|쿼리|없음|반환할 카드 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## UpdateCard
카드 업데이트: 카드 업데이트

```PUT: /cards/{card_id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|쿼리|없음|카드를 인출할 보드의 ID|
|card\_id|string|yes|path|없음|업데이트할 카드의 ID|
|updateCard| |yes|body|없음|업데이트된 카드 값|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## DeleteCard
카드 삭제: 카드 삭제

```DELETE: /cards/{card_id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|쿼리|없음|카드를 인출할 보드의 ID|
|card\_id|string|yes|path|없음|삭제할 카드의 ID|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## CreateCard
카드 만들기: Trello 계정에 새 카드 만들기

```POST: /cards```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|쿼리|없음|카드를 만들 보드의 고유 ID|
|newCard| |yes|body|없음|Trello 보드에 추가할 새 카드|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## ListBoards
보드 나열: 보드 나열

```GET: /member/me/boards```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|filter|string|no|쿼리|없음|보드 결과에 적용할 필터를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|fields|string|no|쿼리|없음|반환할 보드 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|actions|string|no|쿼리|없음|반환할 작업 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|actions\_entities|부울|no|쿼리|없음|작업 엔터티 반환|
|actions\_limit|정수|no|쿼리|없음|반환할 작업의 최대 수|
|actions\_format|string|no|쿼리|없음|반환할 작업의 형식을 지정합니다|
|actions\_since|string|no|쿼리|없음|지정된 날짜 이후에 작업 반환|
|action\_fields|string|no|쿼리|없음|반환할 작업의 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|memberships|string|no|쿼리|없음|반환할 멤버 자격 정보를 지정합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|organization|부울|no|쿼리|없음|조직 정보를 반환하도록 지정합니다.|
|organization\_fields|string|no|쿼리|없음|반환할 조직 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|lists|string|no|쿼리|없음|보드에 속하는 목록을 반환할 것인지를 지정합니다.|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## GetBoard
ID 별로 가져오기: ID 별로 가져오기

```GET: /boards/{board_id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|없음|가져올 보드의 고유 ID|
|actions|string|no|쿼리|없음|반환할 작업을 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|action\_entities|부울|no|쿼리|없음|작업 엔터티를 반환할 것인지를 지정합니다.|
|actions\_display|부울|no|쿼리|없음|작업 표시를 반환할 것인지를 지정합니다.|
|actions\_format|string|no|쿼리|없음|반환할 작업의 형식을 지정합니다|
|actions\_since|string|no|쿼리|없음|이 날짜 이후에 작업만 반환|
|actions\_limit|정수|no|쿼리|없음|반환할 작업의 최대 수|
|action\_fields|string|no|쿼리|없음|각 필드를 반환할 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|action\_memeber|부울|no|쿼리|없음|작업 멤버를 반환할 것인지를 지정합니다.|
|action\_member\_fields|string|no|쿼리|없음|각 작업 멤버를 반환할 멤버 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|action\_memberCreator|부울|no|쿼리|없음|작업 멤버 작성자를 반환할 것인지를 지정합니다.|
|action\_memberCreator\_fields|string|no|쿼리|없음|반환할 작업 멤버 작성자 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|cards|string|no|쿼리|없음|반환할 카드 지정|
|card\_fields|string|no|쿼리|없음|각 카드에 반환할 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|card\_attachments|부울|yes|쿼리|없음|카드에 첨부 파일을 반환할지를 지정합니다.|
|card\_attachment\_fields|string|no|쿼리|없음|각 첨부 파일에 대해 반환할 첨부 파일 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|card\_checklists|string|no|쿼리|없음|각 카드에 반환할 검사 목록을 지정합니다.|
|card\_stickers|부울|no|쿼리|없음|카드 스티커를 반환할지를 지정합니다.|
|boardStarts|string|no|쿼리|없음|반환할 보드 별을 지정합니다.|
|레이블|string|no|쿼리|없음|반환할 레이블을 지정합니다.|
|label\_fields|string|no|쿼리|없음|각 레이블에 반환할 레이블 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|labels\_limits|정수|no|쿼리|없음|반환할 레이블의 최대 수|
|lists|string|no|쿼리|없음|반환할 목록을 지정합니다.|
|list\_fields|string|no|쿼리|없음|각 목록에 반환할 목록 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|memberships|string|no|쿼리|없음|반환할 멤버 자격을 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|memberships\_member|부울|no|쿼리|없음|멤버 자격 멤버를 반환할 것인지를 지정합니다.|
|memberships\_member\_fields|string|no|쿼리|없음|각 멤버 자격 멤버에 반환할 멤버 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|members|string|no|쿼리|없음|반환할 멤버를 나열합니다.|
|member\_fields|string|no|쿼리|없음|각 멤버에 반환할 멤버 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|membersInvited|string|no|쿼리|없음|반환할 초대된 멤버를 지정합니다.|
|membersInvited\_fields|string|no|쿼리|없음|각각에 반환할 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|checklists|string|no|쿼리|없음|반환할 검사 목록을 지정합니다.|
|checklist\_fields|string|no|쿼리|없음|각 검사 목록에 대해 반환할 검사 목록 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|organization|부울|no|쿼리|없음|조직 정보를 반환할지를 지정합니다.|
|organization\_fields|string|no|쿼리|없음|각 조직에 반환할 조직 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|organization\_memberships|string|no|쿼리|없음|반환할 조직 멤버 자격을 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|myPerfs|부울|no|쿼리|없음|내 perfs를 반환할지를 지정합니다.|
|fields|string|no|쿼리|없음|반환할 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## ListLists
보드에 카드 목록 나열: 보드에 카드 목록 나열

```GET: /boards/{board_id}/lists```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|없음|목록을 인출할 보드의 고유 ID|
|cards|string|no|쿼리|없음|반환할 카드 지정|
|card\_fields|string|no|쿼리|없음|반환할 카드 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|filter|string|no|쿼리|없음|목록에 필터 속성을 지정합니다.|
|fields|string|no|쿼리|없음|반환할 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## GetList
ID별로 목록 가져오기: ID별로 목록 가져오기

```GET: /lists/{list_id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|board\_id|string|yes|쿼리|없음|목록을 인출할 보드의 고유 ID|
|list\_id|string|yes|path|없음|인출할 목록의 고유 ID|
|cards|string|no|쿼리|없음|반환할 카드 지정|
|card\_fields|string|no|쿼리|없음|각 카드에 반환할 카드 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|board|부울|no|쿼리|없음|보드 정보를 반환할지를 지정합니다.|
|board\_fields|string|no|쿼리|없음|반환할 보드 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|
|fields|string|no|쿼리|없음|반환할 목록 필드를 나열합니다. '모두' 또는 유효한 값의 쉼표로 구분된 목록 지정|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다|


## 개체 정의 

### 카드


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|아니요 |
|checkItemStates|array|아니요 |
|closed|부울|아니요 |
|dateLastActivity|string|아니요 |
|desc|string|아니요 |
|idBoard|string|아니요 |
|idList|string|아니요 |
|idMembersVoted|array|아니요 |
|idShort|정수|아니요 |
|idAttachmentCover|string|아니요 |
|manualCoverAttachment|부울|아니요 |
|idLabels|array|아니요 |
|name|string|아니요 |
|pos|정수|아니요 |
|shortLink|string|아니요 |
|badges|정의되지 않음|아니요 |
|due|string|아니요 |
|email|string|아니요 |
|shortUrl|string|아니요 |
|subscribed|부울|아니요 |
|url|string|아니요 |



### 배지


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|투표|정수|아니요 |
|ViewingMemberVoted|부울|아니요 |
|구독함|부울|아니요 |
|Fogbugz|string|아니요 |
|CheckItems|정수|아니요 |
|CheckItemsChecked|정수|아니요 |
|설명|정수|아니요 |
|첨부 파일|정수|아니요 |
|설명|부울|아니요 |
|기한|string|아니요 |



### Object


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|



### CreateCard


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|idList|string|예 |
|name|string|예 |
|desc|string|아니요 |
|pos|string|아니요 |
|idMembers|string|아니요 |
|idLabels|string|아니요 |
|urlSource|string|아니요 |
|fileSource|string|아니요 |
|idCardSource|string|아니요 |
|keepFromSource|string|아니요 |



### UpdateCard


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|name|string|아니요 |
|desc|string|아니요 |
|closed|부울|아니요 |
|idMembers|string|아니요 |
|idAttachmentCover|string|아니요 |
|idList|string|아니요 |
|idBoard|string|아니요 |
|pos|string|아니요 |
|due|string|아니요 |
|subscribed|부울|아니요 |



### 보드


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|아니요 |
|closed|부울|아니요 |
|dateLastActivity|string|아니요 |
|dateLastView|string|아니요 |
|desc|string|아니요 |
|idOrganization|string|아니요 |
|invitations|array|아니요 |
|invited|부울|아니요 |
|labelNames|정의되지 않음|아니요 |
|memberships|array|아니요 |
|name|string|아니요 |
|pinned|부울|아니요 |
|powerUps|array|아니요 |
|perfs|정의되지 않음|아니요 |
|shortLink|string|아니요 |
|shortUrl|string|아니요 |
|starred|string|아니요 |
|subscribed|string|아니요 |
|url|string|아니요 |



### 레이블


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|green|string|아니요 |
|yellow|string|아니요 |
|orange|string|아니요 |
|red|string|아니요 |
|purple|string|아니요 |
|blue|string|아니요 |
|sky|string|아니요 |
|lime|string|아니요 |
|pink|string|아니요 |
|black|string|아니요 |



### 멤버 자격


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|아니요 |
|idMember|string|아니요 |
|memberType|string|아니요 |
|unconfirmed|부울|아니요 |



### Perfs


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|permissionLevel|string|아니요 |
|voting|string|아니요 |
|설명|string|아니요 |
|invitations|string|아니요 |
|selfJoin|부울|아니요 |
|cardCovers|부울|아니요 |
|calendarFeedEnabled|부울|아니요 |
|background|string|아니요 |
|backgroundColor|string|아니요 |
|backgroundImage|string|아니요 |
|backgroundImageScaled|string|아니요 |
|backgroundTile|부울|아니요 |
|backgroundBrightness|string|아니요 |
|canBePublic|부울|아니요 |
|canBeOrg|부울|아니요 |
|canBePrivate|부울|아니요 |
|canInvite|부울|아니요 |



### 나열


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|아니요 |
|name|string|아니요 |
|closed|부울|아니요 |
|idBoard|string|아니요 |
|pos|number|아니요 |
|subscribed|부울|아니요 |
|cards|array|아니요 |
|board|정의되지 않음|아니요 |


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->