<properties
	pageTitle="PowerApps 또는 논리 앱에 Bing 검색 API 추가 | Microsoft Azure"
	description="REST API 매개 변수를 사용하는 Bing 검색 API 개요"
	services=""
    suite=""
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
   ms.date="03/16/2016"
   ms.author="mandia"/>

# Bing 검색 API 시작 
Bing 검색에 연결하여 뉴스 검색, 비디오 검색 등의 작업을 수행합니다. Bing 검색 API를 다음에서 사용할 수 있습니다.

- 논리 앱 
- PowerApps

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/connectors-create-api-bingsearch.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-bingsearch.md)


Bing 검색을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- 검색에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 이미지 검색, 뉴스 검색 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 비디오를 검색한 다음 Twitter를 사용하여 Twitter 피드에 해당 비디오를 게시할 수 있습니다.
- PowerApps 엔터프라이즈에 Bing 검색 API를 추가합니다. 이렇게 하면 사용자가 앱 내에서 이 API를 사용할 수 있습니다. 

PowerApps 엔터프라이즈에서 API를 추가하는 방법을 보려면 [PowerApps에서 API 등록](../power-apps/powerapps-register-from-available-apis.md)으로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Bing 검색에는 다음 작업이 포함됩니다. 트리거는 없습니다.

트리거 | actions
--- | ---
없음 | <ul><li>웹 검색</li><li>비디오 검색</li><li>이미지 검색</li><li>뉴스 검색</li><li>관련 검색</li><li>맞춤법 검색</li><li>모두 검색</li></ul>

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.


## Swagger REST API 참조
적용 버전: 1.0

### 웹 검색 
Bing 검색에서 웹 사이트를 검색합니다. ```GET: /Web```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |검색할 텍스트(예: 'xbox')|
|maxResult|정수|no|쿼리|없음 |반환할 결과의 최대 수|
|startOffset|정수|no|쿼리| 없음|건너뛸 결과의 수|
|adultContent|string|no|쿼리|없음 |성인 콘텐츠 필터입니다. 유효한 값: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|쿼리|없음 |검색 범위를 좁힐 시장 또는 지역(예: ko-KR)|
|longitude|number|no|쿼리| 없음|검색 범위를 좁힐 경도(동/서 좌표)(예: 47.603450)|
|latitude|number|no|쿼리| 없음|검색 범위를 좁힐 위도(남/북 좌표)(예: -122.329696)|
|webFileType|string|no|쿼리|없음 |검색 범위를 좁힐 파일 형식(예: 'DOC')|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 비디오 검색 
Bing 검색에서 비디오를 검색합니다. ```GET: /Video```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |검색할 텍스트(예: 'xbox')|
|maxResult|정수|no|쿼리| 없음|반환할 결과의 최대 수|
|startOffset|정수|no|쿼리|없음 |건너뛸 결과의 수|
|adultContent|string|no|쿼리|없음 |성인 콘텐츠 필터입니다. 유효한 값: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|쿼리|없음 |검색 범위를 좁힐 시장 또는 지역(예: ko-KR)|
|longitude|number|no|쿼리|없음 |검색 범위를 좁힐 경도(동/서 좌표)(예: 47.603450)|
|latitude|number|no|쿼리|없음 |검색 범위를 좁힐 위도(남/북 좌표)(예: -122.329696)|
|videoFilters|string|no|쿼리|없음 |크기, 측면, 색, 스타일, 서체 또는 둘의 조합을 기반으로 검색을 필터링합니다. 유효한 값: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>예: 'Duration:Short+Resolution:High'|
|videoSortBy|string|no|쿼리|없음 |결과의 정렬 순서입니다. 유효한 값: <ul><li>Date</li><li>Relevance</li></ul> <p>날짜 정렬 순서는 내림차순입니다.</p>|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 이미지 검색    
Bing 검색에서 이미지를 검색합니다. ```GET: /Image```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |검색할 텍스트(예: 'xbox')|
|maxResult|정수|no|쿼리|없음 |반환할 결과의 최대 수|
|startOffset|정수|no|쿼리|없음 |건너뛸 결과의 수|
|adultContent|string|no|쿼리|없음 |성인 콘텐츠 필터입니다. 유효한 값: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|쿼리|없음 |검색 범위를 좁힐 시장 또는 지역(예: ko-KR)|
|longitude|number|no|쿼리| 없음|검색 범위를 좁힐 경도(동/서 좌표)(예: 47.603450)|
|latitude|number|no|쿼리|없음 |검색 범위를 좁힐 위도(남/북 좌표)(예: -122.329696)|
|imageFilters|string|no|쿼리|없음 |크기, 측면, 색, 스타일, 서체 또는 둘의 조합을 기반으로 검색을 필터링합니다. 유효한 값: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>예: 'Size:Small+Aspect:Square'|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 뉴스 검색    
Bing 검색에서 뉴스 결과를 검색합니다. ```GET: /News```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |검색할 텍스트(예: 'xbox')|
|maxResult|정수|no|쿼리|없음 |반환할 결과의 최대 수|
|startOffset|정수|no|쿼리| 없음|건너뛸 결과의 수|
|adultContent|string|no|쿼리|없음 |성인 콘텐츠 필터입니다. 유효한 값: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|쿼리|없음 |검색 범위를 좁힐 시장 또는 지역(예: ko-KR)|
|longitude|number|no|쿼리|없음 |검색 범위를 좁힐 경도(동/서 좌표)(예: 47.603450)|
|latitude|number|no|쿼리|없음 |검색 범위를 좁힐 위도(남/북 좌표)(예: -122.329696)|
|newsSortBy|string|no|쿼리| 없음|결과의 정렬 순서입니다. 유효한 값: <ul><li>Date</li><li>Relevance</li></ul> <p>날짜 정렬 순서는 내림차순입니다.</p>|
|newsCategory|string|no|쿼리| |검색 범위를 좁힐 뉴스의 범주(예: 'rt\_Business')|
|newsLocationOverride|string|no|쿼리|없음 |Bing 위치 검색을 재정의합니다. 이 매개 변수는 ko-KR 지역/국가에서만 적용할 수 있습니다. 입력 형식은 US./<state />(예: 'US.WA')입니다.|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 맞춤법 검색    
맞춤법 제안을 검색합니다. ```GET: /SpellingSuggestions```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리| 없음|검색할 텍스트(예: 'xbox')|
|maxResult|정수|no|쿼리|없음 |반환할 결과의 최대 수|
|startOffset|정수|no|쿼리| 없음|건너뛸 결과의 수|
|adultContent|string|no|쿼리|없음 |성인 콘텐츠 필터입니다. 유효한 값: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|쿼리| 없음|검색 범위를 좁힐 시장 또는 지역(예: ko-KR)|
|longitude|number|no|쿼리|없음 |검색 범위를 좁힐 경도(동/서 좌표)(예: 47.603450)|
|latitude|number|no|쿼리|없음 |검색 범위를 좁힐 위도(남/북 좌표)(예: -122.329696)|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 관련 검색    
Bing 검색에서 관련 검색 결과를 검색합니다. ```GET: /RelatedSearch```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |검색할 텍스트(예: 'xbox')|
|maxResult|정수|no|쿼리|없음 |반환할 결과의 최대 수|
|startOffset|정수|no|쿼리| 없음|건너뛸 결과의 수|
|adultContent|string|no|쿼리|없음 |성인 콘텐츠 필터입니다. 유효한 값: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|쿼리|없음 |검색 범위를 좁힐 시장 또는 지역(예: ko-KR)|
|longitude|number|no|쿼리|없음 |검색 범위를 좁힐 경도(동/서 좌표)(예: 47.603450)|
|latitude|number|no|쿼리| 없음|검색 범위를 좁힐 위도(남/북 좌표)(예: -122.329696)|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 모두 검색    
Bing 검색에서 모든 웹 사이트, 비디오, 이미지 등을 검색합니다. ```GET: /CompositeSearch```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|쿼리|string|yes|쿼리|없음 |검색할 텍스트(예: 'xbox')|
|maxResult|정수|no|쿼리|없음 |반환할 결과의 최대 수|
|startOffset|정수|no|쿼리|없음 |건너뛸 결과의 수|
|adultContent|string|no|쿼리|없음 |성인 콘텐츠 필터입니다. 유효한 값: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|쿼리|없음 |검색 범위를 좁힐 시장 또는 지역(예: ko-KR)|
|longitude|number|no|쿼리|없음 |검색 범위를 좁힐 경도(동/서 좌표)(예: 47.603450)|
|latitude|number|no|쿼리|없음 |검색 범위를 좁힐 위도(남/북 좌표)(예: -122.329696)|
|webFileType|string|no|쿼리|없음 |검색 범위를 좁힐 파일 형식(예: 'DOC')|
|videoFilters|string|no|쿼리|없음 |크기, 측면, 색, 스타일, 서체 또는 둘의 조합을 기반으로 검색을 필터링합니다. 유효한 값: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>예: 'Duration:Short+Resolution:High'|
|videoSortBy|string|no|쿼리|없음 |결과의 정렬 순서입니다. 유효한 값: <ul><li>Date</li><li>Relevance</li></ul> <p>날짜 정렬 순서는 내림차순입니다.</p>|
|imageFilters|string|no|쿼리|없음 |크기, 측면, 색, 스타일, 서체 또는 둘의 조합을 기반으로 검색을 필터링합니다. 유효한 값: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>예: 'Size:Small+Aspect:Square'|
|newsSortBy|string|no|쿼리|없음 |결과의 정렬 순서입니다. 유효한 값: <ul><li>Date</li><li>Relevance</li></ul> <p>날짜 정렬 순서는 내림차순입니다.</p>|
|newsCategory|string|no|쿼리|없음 |검색 범위를 좁힐 뉴스의 범주(예: 'rt\_Business')|
|newsLocationOverride|string|no|쿼리|없음 |Bing 위치 검색을 재정의합니다. 이 매개 변수는 ko-KR 지역/국가에서만 적용할 수 있습니다. 입력 형식은 US./<state />(예: 'US.WA')입니다.|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## 개체 정의

#### WebResultModel: Bing 웹 검색 결과

|속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|제목|string|no|
|설명|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|FullUrl|string|no|

#### VideoResultModel: Bing 비디오 검색 결과

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|제목|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|MediaUrl|string|no|
|런타임|정수|no|
|미리 보기|정의되지 않음|no|

#### ThumbnailModel: 멀티미디어 요소의 미리 보기 속성

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|MediaUrl|string|no|
|ContentType|string|no|
|너비|정수|no|
|높이|정수|no|
|FileSize|정수|no|

#### ImageResultModel: Bing 이미지 검색 결과

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|제목|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|MediaUrl|string|no|
|SourceUrl|string|no|
|미리 보기|정의되지 않음|no|

#### NewsResultModel: Bing 뉴스 검색 결과

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|제목|string|no|
|설명|string|no|
|DisplayUrl|string|no|
|Id|string|no|
|원본|string|no|
|Date|string|no|

#### SpellResultModel: Bing 맞춤법 제안 결과

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|Id|string|no|
|값|string|no|

#### RelatedSearchResultModel: Bing 관련 검색 결과

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|제목|string|no|
|Id|string|no|
|BingUrl|string|no|

#### CompositeSearchResultModel: Bing 복합 검색 결과

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|WebResultsTotal|정수|no|
|ImageResultsTotal|정수|no|
|VideoResultsTotal|정수|no|
|NewsResultsTotal|정수|no|
|SpellSuggestionsTotal|정수|no|
|WebResults|array|no|
|ImageResults|array|no|
|VideoResults|array|no|
|NewsResults|array|no|
|SpellSuggestionResults|array|no|
|RelatedSearchResults|array|no|

## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API 목록](apis-list.md)으로 돌아갑니다.

<!---HONumber=AcomDC_0323_2016-->