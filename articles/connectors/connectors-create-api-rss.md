<properties
pageTitle="RSS | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. RSS 커넥터를 사용하여 사용자가 피드 항목을 게시 및 검색할 수 있습니다. 또한 새 항목이 피드에 게시된 경우 사용자가 작업을 트리거할 수 있습니다."
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

# RSS 커넥터 시작
RSS는 블로그 항목 및 뉴스 헤드라인처럼 자주 업데이트되는 콘텐츠를 게시하는 데 사용되는 인기 있는 웹 배포 형식입니다. 많은 콘텐츠 게시자는 사용자가 구독할 수 있는 RSS 피드를 제공합니다. RSS 커넥터를 사용하여 피드 정보를 검색하고 RSS 피드에 새 항목이 게시될 때 흐름을 트리거합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

RSS 커넥터는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 RSS 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### RSS 동작
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|모든 RSS 피드 항목을 가져옵니다.|
### RSS 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|새 피드 항목을 게시하는 경우|새 피드가 게시될 경우 워크플로 트리거|


## RSS에 대한 연결 만들기

>[AZURE.INCLUDE [RSS에 대한 연결을 만드는 단계](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## RSS에 대한 참조
적용 버전: 1.0

## OnNewFeed
새 피드 항목을 게시하는 경우: 새 피드가 게시될 경우 워크플로 트리거

```GET: /OnNewFeed```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|feedUrl|string|yes|쿼리|없음|피드 URL|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|202|수락됨|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다.|


## ListFeedItems
모든 RSS 피드 항목 나열: 모든 RSS 피드 항목을 가져옵니다.

```GET: /ListFeedItems```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|feedUrl|string|yes|쿼리|없음|피드 URL|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|202|수락됨|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다.|


## 개체 정의 

### TriggerBatchResponse[FeedItem]


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|value|array|아니요 |



### FeedItem


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|예 |
|title|string|예 |
|콘텐츠|string|예 |
|links|array|아니요 |
|updatedOn|string|아니요 |


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->