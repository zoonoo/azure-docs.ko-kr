<properties
    pageTitle="논리 앱에 Facebook 커넥터 추가 | Microsoft Azure"
    description="REST API 매개 변수를 사용하는 Facebook 커넥터 개요"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Facebook 커넥터 시작
Facebook에 연결하여 타임라인에 게시, 페이지 피드 가져오기 등의 작업을 수행합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.


Facebook을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Facebook에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다.
- 새 게시를 수신할 때 트리거를 사용합니다.
- 타임라인에 게시, 페이지 피드 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 타임라인에 새 게시물이 있는 경우 해당 게시물을 가져와서 Twitter 피드에 푸시할 수 있습니다.



논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Facebook 커넥터에는 다음 트리거 및 작업이 포함됩니다.

| 트리거 | 작업|
| --- | --- |
| <ul><li>내 타임라인에 새 게시물이 있는 경우</li></ul> |<ul><li>내 타임라인에서 피드 가져오기</li><li>내 타임라인에 게시</li><li>내 타임라인에 새 게시물이 있는 경우</li><li>페이지 피드 가져오기</li><li>사용자 타임라인 가져오기</li><li>페이지에 게시</li></ul>

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

## Facebook에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 Facebook에 연결할 권한을 논리 앱에 부여해야 합니다.

1. Facebook 계정으로 로그인
2. **권한 부여**를 선택하고 논리 앱에서 Facebook에 연결하고 사용할 수 있도록 허용합니다.

>[AZURE.INCLUDE [Facebook에 대한 연결을 만드는 단계](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 Facebook 연결을 사용할 수 있습니다.

## Swagger REST API 참조
적용 버전: 1.0

### 내 타임라인에서 피드 가져오기
로그인한 사용자의 타임라인에서 피드를 가져옵니다. ```GET: /me/feed```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|fields|string|no|쿼리|없음 |반환할 필드를 지정합니다. 예(id,name,picture)|
|limit|정수|no|쿼리| 없음|검색할 최대 게시물 수|
|다음으로 바꿀 수 있습니다.|string|no|쿼리| 없음|게시물 목록을 연결된 위치가 있는 게시물로만 제한합니다.|
|filter|string|no|쿼리| 없음|특정 스트림 필터와 일치하는 게시물만 검색합니다.|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 내 타임라인에 게시
로그인한 사용자의 타임라인에 상태 메시지를 게시합니다. ```POST: /me/feed```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|post|string |yes|body|없음 |게시할 새 메시지|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 내 타임라인에 새 게시물이 있는 경우
로그인한 사용자의 타임라인에 새 게시물이 있는 경우 새 흐름을 트리거합니다. ```GET: /trigger/me/feed```

매개 변수는 없습니다.

#### 응답
|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 페이지 피드 가져오기
지정된 페이지의 피드에서 게시물을 가져옵니다. ```GET: /{pageId}/feed```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|pageId|string|yes|path| 없음|게시물을 검색할 페이지의 ID입니다.|
|limit|정수|no|쿼리| 없음|검색할 최대 게시물 수|
|include\_hidden|부울|no|쿼리|없음 |페이지에 의해 숨겨진 모든 게시물을 포함할 것인지 여부|
|fields|string|no|쿼리|없음 |반환할 필드를 지정합니다. 예(id,name,picture)|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 사용자 타임라인 가져오기
사용자의 타임라인에서 게시물을 가져옵니다. ```GET: /{userId}/feed```

| 이름|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|userId|string|yes|path|없음 |타임라인을 검색할 사용자의 ID입니다.|
|limit|정수|no|쿼리|없음 |검색할 최대 게시물 수|
|다음으로 바꿀 수 있습니다.|string|no|쿼리|없음 |게시물 목록을 연결된 위치가 있는 게시물로만 제한합니다.|
|filter|string|no|쿼리| 없음|특정 스트림 필터와 일치하는 게시물만 검색합니다.|
|fields|string|no|쿼리| 없음|반환할 필드를 지정합니다. 예(id,name,picture)|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 페이지에 게시
로그인한 사용자로 Facebook 페이지에 메시지를 게시합니다. ```POST: /{pageId}/feed```

| Name|데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|pageId|string|yes|path|없음 |게시할 페이지의 ID입니다.|
|post|다양함 |yes|body|없음 |게시할 새 메시지입니다.|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


## 개체 정의

#### GetFeedResponse

|속성 이름 | 데이터 형식 | 필수|
|---|---|---|
|데이터|array|no|

#### TriggerFeedResponse

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|데이터|array|no|

#### PostItem: 프로필 피드의 단일 항목
프로필은 사용자, 페이지, 앱 또는 그룹일 수 있습니다.

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|no|
|admin\_creator|array|no|
|caption|string|no|
|created\_time|string|no|
|description|string|no|
|feed\_targeting|정의되지 않음|no|
|from|정의되지 않음|no|
|icon|string|no|
|is\_hidden|부울|no|
|is\_published|부울|no|
|link|string|no|
|message|string|no|
|name|string|no|
|object\_id|string|no|
|picture|string|no|
|place|정의되지 않음|no|
|privacy|정의되지 않음|no|
|properties|array|no|
|원본|string|no|
|status\_type|string|no|
|story|string|no|
|targeting|정의되지 않음|no|
|to|array|no|
|type|string|no|
|updated\_time|string|no|
|with\_tags|정의되지 않음|no|

#### TriggerItem: 프로필 피드의 단일 항목
프로필은 사용자, 페이지, 앱 또는 그룹일 수 있습니다.

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|no|
|created\_time|string|no|
|from|정의되지 않음|no|
|message|string|no|
|type|string|no|

#### AdminItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|no|
|link|string|no|

#### PropertyItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|name|string|no|
|텍스트|string|no|
|href|string|no|

#### UserPostFeedRequest

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|message|string|yes|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|place|string|no|
|tags|string|no|
|privacy|정의되지 않음|no|
|object\_attachment|string|no|

#### PagePostFeedRequest

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|message|string|yes|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|actions|array|no|
|place|string|no|
|tags|string|no|
|object\_attachment|string|no|
|targeting|정의되지 않음|no|
|feed\_targeting|정의되지 않음|no|
|published|부울|no|
|scheduled\_publish\_time|string|no|
|backdated\_time|string|no|
|backdated\_time\_granularity|string|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|부울|no|

#### PostFeedResponse

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|no|

#### ProfileCollection

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|데이터|array|no|

#### UserItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|no|
|first\_name|string|no|
|last\_name|string|no|
|name|string|no|
|gender|string|no|
|about|string|no|

#### ActionItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|name|string|no|
|link|string|no|

#### TargetItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|countries|array|no|
|locales|array|no|
|regions|array|no|
|cities|array|no|

#### FeedTargetItem: 이 게시물에 대한 뉴스 피드 대상 지정을 제어하는 개체
이러한 그룹의 모든 사용자는 다른 그룹의 사용자에 비해 이 게시물에 대한 관심이 높습니다. 페이지에만 적용됩니다.

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|countries|array|no|
|regions|array|no|
|cities|array|no|
|age\_min|정수|no|
|age\_max|정수|no|
|genders|array|no|
|relationship\_statuses|array|no|
|interested\_in|array|no|
|college\_years|array|no|
|interests|array|no|
|relevant\_until|정수|no|
|education\_statuses|array|no|
|locales|array|no|

#### PlaceItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|no|
|name|string|no|
|overall\_rating|number|no|
|location|정의되지 않음|no|

#### LocationItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|city|string|no|
|country|string|no|
|latitude|number|no|
|located\_in|string|no|
|longitude|number|no|
|name|string|no|
|region|string|no|
|state|string|no|
|street|string|no|
|zip|string|no|

#### PrivacyItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|description|string|no|
|value|string|yes|
|allow|string|no|
|deny|string|no|
|friends|string|no|

#### ChildAttachmentsItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|link|string|no|
|picture|string|no|
|image\_hash|string|no|
|name|string|no|
|description|string|no|

#### PostPhotoRequest

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|url|string|yes|
|caption|string|no|

#### PostPhotoResponse

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|yes|
|post\_id|string|yes|

#### PostVideoRequest

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|videoData|string|yes|
|description|string|yes|
|title|string|yes|
|uploadedVideoName|string|no|

#### GetPhotoResponse

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|데이터|정의되지 않음|yes|

#### GetPhotoResponseItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|url|string|yes|
|is\_silhouette|부울|yes|
|height|string|no|
|width|string|no|

#### GetEventResponse

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|데이터|array|yes|

#### GetEventResponseItem

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|yes|
|name|string|yes|
|start\_time|string|no|
|end\_time|string|no|
|timezone|string|no|
|location|string|no|
|description|string|no|
|ticket\_uri|string|no|
|rsvp\_status|string|yes|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->