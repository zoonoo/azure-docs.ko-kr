<properties
pageTitle="PowerApps 엔터프라이즈 및 논리 앱에 Salesforce 커넥터 추가 | Microsoft Azure"
description="REST API 매개 변수를 사용하는 Salesforce 커넥터 개요"
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
ms.date="05/19/2016"
ms.author="deonhe"/>

# Salesforce 커넥터 시작 
Salesforce에 연결하여 개체 만들기, 개체 가져오기 등의 작업을 수행합니다. Salesforce 커넥터는 다음에서 사용할 수 있습니다.

- 논리 앱 
- PowerApps

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-salesforce.md)

&nbsp;

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

Salesforce를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Salesforce에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 개체를 만들거나 업데이트할 때 트리거를 사용합니다.
- 개체 만들기, 개체 삭제 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Salesforce에서 새 개체가 만들어지면 Office 365를 사용하여 메일을 보낼 수 있습니다.
- PowerApps 엔터프라이즈에 Salesforce 커넥터를 추가합니다. 이렇게 하면 사용자가 앱 내에서 이 커넥터를 사용할 수 있습니다. 

PowerApps 엔터프라이즈에서 커넥터를 추가하는 방법을 보려면 [PowerApps에서 커넥터 등록](../power-apps/powerapps-register-from-available-apis.md)으로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Salesforce API에는 다음 트리거 및 작업이 포함됩니다.

| 트리거 | 동작|
| --- | --- |
|<ul><li>개체를 만들 때</li><li>개체를 수정할 때</li></ul> | <ul><li>개체 만들기</li><li>개체 가져오기</li><li>개체를 만들 때</li><li>개체를 수정할 때</li><li>개체 삭제</li><li>개체 가져오기</li><li>개체 유형 가져오기(SObjects)</li><li>개체 업데이트</li></ul>

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

## Salesforce에 대한 연결 만들기 

논리 앱에 이 커넥터를 추가할 때 Salesforce에 연결할 권한을 논리 앱에 부여해야 합니다.

>[AZURE.INCLUDE [Salesforce에 대한 연결을 만드는 단계](../../includes/connectors-create-api-salesforce.md)]

연결을 만든 후에 테이블 이름 등의 Salesforce 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 연결을 사용할 수 있습니다.

## Swaggers REST API 참조
적용 버전: 1.0


### 개체 만들기
Salesforce 개체를 만듭니다. ```POST: /datasets/default/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|Salesforce SObject 형식(예: 'Lead')|
|항목| |yes|body|없음|만들 Salesforce 개체|

### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



### 개체 가져오기
Salesforce 개체를 검색합니다. ```GET: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|Salesforce SObject 형식(예: 'Lead')|
|id|string|yes|path|없음|검색할 Salesforce 개체의 고유 식별자|

### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



### 개체 삭제
Salesforce 개체를 삭제합니다. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|Salesforce SObject 형식(예: 'Lead')|
|id|string|yes|path|없음|삭제할 Salesforce 개체의 고유 식별자|

### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



### 개체 업데이트
Salesforce 개체를 업데이트합니다. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|Salesforce SObject 형식(예: 'Lead')|
|id|string|yes|path|없음|업데이트할 Salesforce 개체의 고유 식별자|
|항목| |yes|body|없음|변경된 속성을 가진 Salesforce 개체|

### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



### 개체를 만들 때
Salesforce에서 개체를 만들 때 흐름을 트리거합니다. ```GET: /datasets/default/tables/{table}/onnewitems```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|Salesforce SObject 형식(예: 'Lead')|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|

### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



### 개체를 수정할 때 
Salesforce에서 개체를 수정할 때 흐름을 트리거합니다. ```GET: /datasets/default/tables/{table}/onupdateditems```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|Salesforce SObject 형식(예: 'Lead')|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|

### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



## 개체 정의 

#### DataSetsMetadata

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|tabular|정의되지 않음|no|
|Blob|정의되지 않음|no|


#### TabularDataSetsMetadata

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|정의되지 않음|no|


#### DataSetsList

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|value|array|no|


#### DataSet

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|이름|string|
|DisplayName|string|no|


#### 테이블

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|이름|string|no|
|DisplayName|string|no|


#### 항목

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|ItemInternalId|string|no|


#### ItemsList

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|value|array|no|


#### TablesList

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|value|array|no|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API 목록](apis-list.md)으로 돌아갑니다.


[5]: https://developer.salesforce.com
[6]: ./media/connectors-create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/connectors-create-api-salesforce/salesforce-create-app.png
[8]: ./media/connectors-create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0525_2016-->