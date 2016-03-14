<properties
	pageTitle="논리 앱에 SQL Azure API 추가 | Microsoft Azure"
	description="REST API 매개 변수를 사용하는 SQL Azure API 개요"
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
   ms.date="02/25/2016"
   ms.author="mandia"/>


# SQL Azure API 시작
SQL Azure에 연결하여 행 삽입, 테이블 가져오기 등 테이블 및 행을 관리합니다.

SQL Azure API를 다음에서 사용할 수 있습니다.

- 논리 앱 

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전에 대한 내용을 보려면 [SQL 커넥터](../app-service-logic/app-service-logic-connector-sql.md)를 클릭하세요.

SQL Azure로 다음을 수행할 수 있습니다.

- SQL Azure에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 행 가져오기, 행 삽입 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 SQL Azure에서 데이터 행을 가져와 Excel에 추가할 수 있습니다. 

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.


## 트리거 및 작업
SQL에는 다음 작업이 포함됩니다. 트리거는 없습니다.

트리거 | actions
--- | ---
없음 | <ul><li>행 가져오기</li><li>행 가져오기</li><li>행 삽입</li><li>행 삭제</li><li>테이블 가져오기</li><li>행 업데이트</li></ul>

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## SQL에 대한 연결 만들기
논리 앱에 이 API를 추가할 때 다음과 같은 값을 입력합니다.

|속성| 필수|설명|
| ---|---|---|
|SQL 연결 문자열|예|SQL Azure 연결 문자열 입력|

연결을 만든 후에 테이블 이름 등의 SQL 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Swagger REST API 참조
적용 버전: 1.0

### 행 가져오기 
SQL 테이블에서 단일 행을 검색합니다. ```GET: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|id|string|yes|path|없음|검색할 행의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 가져오기 
SQL 테이블에서 행을 검색합니다. ```GET: /datasets/default/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 삽입 
SQL 테이블에 새 행을 삽입합니다. ```POST: /datasets/default/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|항목|ItemInternalId: string|yes|body|없음|SQL의 지정된 테이블에 삽입할 행|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 삭제 
SQL 테이블에서 행을 삭제합니다. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|id|string|yes|path|없음|삭제할 행의 고유 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 테이블 가져오기 
SQL 데이터베이스에서 테이블을 검색합니다. ```GET: /datasets/default/tables```

이 호출에 대한 매개 변수는 없습니다.

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 행 업데이트 
SQL 테이블에서 기존 행을 업데이트합니다. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|id|string|yes|path|없음|업데이트할 행의 고유 식별자|
|항목|ItemInternalId: string|yes|body|없음|업데이트된 값을 가진 행|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|

## 개체 정의

#### DataSetsMetadata

|속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|tabular|정의되지 않음|no|
|Blob|정의되지 않음|no|

#### TabularDataSetsMetadata

|속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|정의되지 않음|no|

#### DataSetsList

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|value|array|no|

#### DataSet

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|이름|string|no|
|DisplayName|string|no|

#### 테이블

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|이름|string|no|
|DisplayName|string|no|

#### 항목

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|value|array|no|

#### TablesList

|속성 이름 | 데이터 형식 |필수 |
|---|---|---|
|value|array|no|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0302_2016-->