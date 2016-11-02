<properties
pageTitle="논리 앱에서 Slack 커넥터 사용 | Microsoft Azure"
description="Microsoft Azure 앱 서비스 논리 앱에서 Slack 커넥터를 사용을 시작"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# Slack 커넥터 시작

Slack은 팀의 모든 통신을 한데 모아, 어디서나 즉시 검색 및 사용할 수 있는 팀 통신 도구입니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

Slack 커넥터를 사용하여 다음을 수행할 수 있습니다.

* 논리 앱 빌드

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업에 대한 정보

Slack 커넥터를 작업으로 사용할 수 있습니다. 트리거는 없습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 Slack 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### Slack 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|PostMessage|지정된 채널에 메시지를 게시합니다.|
## Slack에 대한 연결 만들기
Slack 커넥터를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|Slack 자격 증명 제공|

다음 단계를 따라 Slack에 로그인하고 논리 앱의 Slack **연결** 구성을 완료합니다.

1. **되풀이**를 선택합니다.
2. **빈도**를 선택하고 **간격**을 입력합니다.
3. **작업 추가**를 선택합니다.![Slack 구성][1]  
4. 검색 상자에 Slack을 입력하고 이름에 Slack이 있는 모든 항목이 반환될 때까지 검색을 기다립니다.
5. **Slack-메시지 게시**를 선택합니다.
6. **Slack에 로그인**을 선택합니다.![Slack 구성][2]
7. Slack 자격 증명을 제공하여 로그인하고 응용 프로그램에 권한을 부여합니다.![Slack 구성][3]  
8. 조직의 로그인 페이지로 리디렉션됩니다. 논리 앱과 상호 작용하도록 Slack에 **권한을 부여**합니다.![Slack 구성][5] 
9. 권한 부여가 완료된 후 **Slack - 모든 메시지 가져오기** 섹션을 구성하여 완료하기 위해 논리 앱으로 리디렉션됩니다. 필요한 다른 트리거 및 작업을 추가합니다.![Slack 구성][6]
10. 위의 메뉴 모음에서 **저장**을 선택하여 작업을 저장합니다.


>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Slack REST API 참조
#### 이 문서 적용 버전: 1.0


### 지정된 채널에 메시지를 게시합니다.
**```POST: /chat.postMessage```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|channel|string|yes|쿼리|없음|메시지를 보낼 채널, 비공개 그룹 또는 IM 채널입니다. 이름(예: #general) 또는 인코드된 ID가 될 수 있습니다.|
|텍스트|string|yes|쿼리|없음|보낼 메시지의 텍스트입니다. 서식 지정 옵션을 보려면 https://api.slack.com/docs/formatting.|을 참조하세요.
|username|string|no|쿼리|없음|봇의 이름입니다.|
|as\_user|부울|no|쿼리|없음|봇 대신 인증된 사용자로 메시지를 게시하려면 true를 전달합니다.|
|parse|string|no|쿼리|없음|메시지를 처리하는 방법을 변경합니다. 자세한 내용은 https://api.slack.com/docs/formatting.|을 참조하세요.
|link\_names|정수|no|쿼리|없음|채널 이름 및 사용자 이름을 찾아서 연결합니다.|
|unfurl\_links|부울|no|쿼리|없음|기본적으로 텍스트 기반 콘텐츠 펼치기를 사용하려면 true를 전달합니다.|
|unfurl\_media|부울|no|쿼리|없음|미디어 콘텐츠 펼치기를 사용하지 않으려면 false를 전달합니다.|
|icon\_url|string|no|쿼리|없음|이 메시지의 아이콘으로 사용할 이미지의 URL입니다.|
|icon\_emoji|string|no|쿼리|없음|이 메시지의 아이콘으로 사용할 이모지입니다.|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|408|요청 시간 초과|
|429|너무 많은 요청|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|503|Slack 서비스를 사용할 수 없음|
|504|게이트웨이 시간 초과|
|기본값|작업이 실패했습니다.|
------



## 개체 정의: 

 **Message**: Yammer 메시지

Message에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|id|정수|
|content\_excerpt|string|
|sender\_id|정수|
|replied\_to\_id|정수|
|created\_at|string|
|network\_id|정수|
|message\_type|string|
|sender\_type|string|
|url|string|
|web\_url|string|
|group\_id|정수|
|body|정의되지 않음|
|thread\_id|정수|
|direct\_message|부울|
|client\_type|string|
|client\_url|string|
|language|string|
|notified\_user\_ids|array|
|privacy|string|
|liked\_by|정의되지 않음|
|system\_message|부울|



 **PostOperationRequest**: yammer에 게시할 Yammer 커넥터에 대한 게시 요청을 나타냅니다.

PostOperationRequest에 대한 필수 속성:

body

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|body|string|
|group\_id|정수|
|replied\_to\_id|정수|
|direct\_to\_id|정수|
|broadcast|부울|
|topic1|string|
|topic2|string|
|topic3|string|
|topic4|string|
|topic5|string|
|topic6|string|
|topic7|string|
|topic8|string|
|topic9|string|
|topic10|string|
|topic11|string|
|topic12|string|
|topic13|string|
|topic14|string|
|topic15|string|
|topic16|string|
|topic17|string|
|topic18|string|
|topic19|string|
|topic20|string|



 **MessageList**: 메시지 목록

MessageList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|messages|array|



 **MessageBody**: 메시지 본문

MessageBody에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|parsed|string|
|plain|string|
|rich|string|



 **LikedBy**: 관심 있는 대상

LikedBy에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|count|정수|
|names|array|



 **YammmerEntity**: 관심 있는 대상

YammmerEntity에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|type|string|
|id|정수|
|full\_name|string|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)
## 개체 정의: 

 **WebResultModel**: Bing 웹 검색 결과

WebResultModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|설명|string|
|DisplayUrl|string|
|Id|string|
|FullUrl|string|



 **VideoResultModel**: Bing 비디오 검색 결과

VideoResultModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|DisplayUrl|string|
|Id|string|
|MediaUrl|string|
|런타임|정수|
|미리 보기|정의되지 않음|



 **ThumbnailModel**: 멀티미디어 요소의 미리 보기 속성

ThumbnailModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|MediaUrl|string|
|ContentType|string|
|너비|정수|
|높이|정수|
|FileSize|정수|



 **ImageResultModel**: Bing 이미지 검색 결과

ImageResultModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|DisplayUrl|string|
|Id|string|
|MediaUrl|string|
|SourceUrl|string|
|미리 보기|정의되지 않음|



 **NewsResultModel**: Bing 뉴스 검색 결과

NewsResultModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|설명|string|
|DisplayUrl|string|
|Id|string|
|원본|string|
|Date|string|



 **SpellResultModel**: Bing 맞춤법 제안 결과

SpellResultModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|Id|string|
|값|string|



 **RelatedSearchResultModel**: Bing 관련 검색 결과

RelatedSearchResultModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|Id|string|
|BingUrl|string|



 **CompositeSearchResultModel**: Bing 복합 검색 결과

CompositeSearchResultModel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|WebResultsTotal|정수|
|ImageResultsTotal|정수|
|VideoResultsTotal|정수|
|NewsResultsTotal|정수|
|SpellSuggestionsTotal|정수|
|WebResults|array|
|ImageResults|array|
|VideoResults|array|
|NewsResults|array|
|SpellSuggestionResults|array|
|RelatedSearchResults|array|


## 개체 정의: 

 **PostOperationResponse**: Slack에 게시하기 위한 Slack 커넥터의 게시 작업에 대한 응답을 나타냅니다.

PostOperationResponse에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|확인|부울|
|channel|string|
|ts|string|
|message|정의되지 않음|
|error|string|



 **MessageItem**: 채널 메시지입니다.

MessageItem에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|텍스트|string|
|id|string|
|사용자|string|
|created|정수|
|is\_user-deleted|부울|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

<!---HONumber=AcomDC_0525_2016-->