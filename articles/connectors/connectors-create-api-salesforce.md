<properties
pageTitle="논리 앱에서 Salesforce 커넥터 사용 방법 알아보기 | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. Salesforce 커넥터는 Salesforce 개체와 함께 작동하는 API를 제공합니다."
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
ms.date="07/22/2016"
ms.author="deonhe"/>

# Salesforce 커넥터 시작

Salesforce 커넥터는 Salesforce 개체와 함께 작동하는 API를 제공합니다.

[커넥터](./apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../app-service-logic/app-service-logic-create-a-logic-app.md) 시작할 수 있습니다.

## Salesforce 커넥터에 연결

논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](./connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.

### Salesforce 커넥터에 대한 연결 만들기

>[AZURE.INCLUDE [Salesforce 커넥터에 대한 연결을 만드는 단계](../../includes/connectors-create-api-salesforce.md)]

## Salesforce 커넥터 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

>[AZURE.INCLUDE [Salesforce 트리거를 만드는 단계](../../includes/connectors-create-api-salesforce-trigger.md)]

## 조건 추가 
>[AZURE.INCLUDE [Salesforce 조건을 만드는 단계](../../includes/connectors-create-api-salesforce-condition.md)]

## Salesforce 커넥터 작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

>[AZURE.INCLUDE [Salesforce 작업을 만드는 단계](../../includes/connectors-create-api-salesforce-action.md)]

## 기술 세부 정보

이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## Salesforce 커넥터 트리거

Salesforce 커넥터에는 다음과 같은 트리거가 있습니다.

|트리거 | 설명|
|--- | ---|
|[개체를 만들 때](connectors-create-api-salesforceconnector.md#when-an-object-is-created)|이 작업은 개체를 만들 때 흐름을 트리거합니다.|
|[개체를 수정할 때](connectors-create-api-salesforceconnector.md#when-an-object-is-modified)|이 작업은 개체를 수정할 때 흐름을 트리거합니다.|


## Salesforce 커넥터 작업

Salesforce 커넥터에는 다음과 같은 작업이 있습니다.


|작업|설명|
|--- | ---|
|[개체 가져오기](connectors-create-api-salesforceconnector.md#get-objects)|이 작업은 'Lead'와 같은 특정 개체 유형의 개체를 가져옵니다.|
|[개체 만들기](connectors-create-api-salesforceconnector.md#create-object)|이 작업은 개체를 만듭니다.|
|[개체 가져오기](connectors-create-api-salesforceconnector.md#get-object)|이 작업은 개체를 가져옵니다.|
|[개체 삭제](connectors-create-api-salesforceconnector.md#delete-object)|이 작업은 개체를 삭제합니다.|
|[개체 업데이트](connectors-create-api-salesforceconnector.md#update-object)|이 작업은 개체를 업데이트합니다.|
|[개체 유형 가져오기](connectors-create-api-salesforceconnector.md#get-object-types)|이 작업은 사용 가능한 개체 유형을 나열합니다.|
### 작업 세부 정보

이 커넥터에 대한 작업 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.



### 개체 가져오기
이 작업은 'Lead'와 같은 특정 개체 유형의 개체를 가져옵니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|개체 유형|'Lead' 등의 Salesforce 개체 유형|
|$filter|필터 쿼리|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|검색할 항목의 최대 수(기본값 = 256)|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

ItemsList


| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|




### 개체 만들기
이 작업은 개체를 만듭니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|개체 유형|'Lead' 등의 개체 유형|
|item*|Object|만들 개체|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

항목


| 속성 이름 | 데이터 형식 |
|---|---|
|ItemInternalId|string|




### 개체 가져오기
이 작업은 개체를 가져옵니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|개체 유형|'Lead' 등의 Salesforce 개체 유형|
|id*|개체 ID|가져올 개체의 식별자|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

항목


| 속성 이름 | 데이터 형식 |
|---|---|
|ItemInternalId|string|




### 개체 삭제
이 작업은 개체를 삭제합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|개체 유형|'Lead' 등의 개체 유형|
|id*|개체 ID|삭제할 개체의 식별자|

*는 필수 속성을 나타냅니다.




### 개체 업데이트
이 작업은 개체를 업데이트합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|개체 유형|'Lead' 등의 개체 유형|
|id*|개체 ID|업데이트할 개체의 식별자|
|item*|Object|변경된 속성을 가진 개체|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

항목


| 속성 이름 | 데이터 형식 |
|---|---|
|ItemInternalId|string|




### 개체를 만들 때
이 작업은 개체를 만들 때 흐름을 트리거합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|개체 유형|'Lead' 등의 개체 유형|
|$filter|필터 쿼리|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|검색할 항목의 최대 수(기본값 = 256)|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

ItemsList


| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|




### 개체를 수정할 때
이 작업은 개체를 수정할 때 흐름을 트리거합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|개체 유형|'Lead' 등의 개체 유형|
|$filter|필터 쿼리|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|검색할 항목의 최대 수(기본값 = 256)|

*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

ItemsList


| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|




### 개체 유형 가져오기
이 작업은 사용 가능한 개체 유형을 나열합니다.


이 호출에 대한 매개 변수는 없습니다.

#### 출력 세부 정보

TablesList


| 속성 이름 | 데이터 형식 | 
|---|---|
|value|array|



## HTTP 응답

위의 작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다.

|이름|설명|
|---|---|
|200|확인|
|202|수락됨|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생.|
|기본값|작업이 실패했습니다.|






## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
