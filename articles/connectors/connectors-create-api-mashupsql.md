<properties
pageTitle="Sql 커넥터 | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. Sql 커넥터는 Sql 데이터베이스와 함께 작동하는 API를 제공합니다."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# Sql 커넥터 커넥터 시작



Sql 커넥터 커넥터는 다음에서 사용할 수 있습니다.

- [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [흐름](http://flows.microsoft.com)  

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

Sql 커넥터 커넥터는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 Sql 커넥터 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### Sql 커넥터 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|[GetTables](connectors-create-api-sqlconnector.md#gettables)|SQL 데이터베이스에서 테이블을 검색합니다.|
|[PostItem](connectors-create-api-sqlconnector.md#postitem)|SQL 테이블에 새 행을 삽입합니다.|
|[GetItem](connectors-create-api-sqlconnector.md#getitem)|SQL 테이블에서 단일 행을 검색합니다.|
|[DeleteItem](connectors-create-api-sqlconnector.md#deleteitem)|SQL 테이블에서 행을 삭제합니다.|
|[PatchItem](connectors-create-api-sqlconnector.md#patchitem)|SQL 테이블에서 기존 행을 업데이트합니다.|
### Sql 커넥터 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|


## Sql 커넥터에 대한 연결 만들기
Sql 커넥터로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

|속성| 필수|설명|
| ---|---|---|
|SqlConnectionString|예|SQL 연결 문자열 제공|
연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

>[AZURE.INCLUDE [매시업 SQL에 대한 연결을 만드는 단계](../../includes/connectors-create-api-mashupsql.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Sql 커넥터에 대한 참조
적용 버전: 1.0

## GetTables
테이블 가져오기: SQL 데이터베이스에서 테이블을 검색합니다.

```GET: /datasets/default/tables```

이 호출에 대한 매개 변수는 없습니다.
#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## PostItem
행 삽입: SQL 테이블에 새 행을 삽입합니다.

```POST: /datasets/default/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|항목| |yes|body|없음|SQL의 지정된 테이블에 삽입할 행|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## GetItem
행 가져오기: SQL 테이블에서 단일 행을 검색합니다.

```GET: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|id|string|yes|path|없음|검색할 행의 고유 식별자|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## DeleteItem
행 삭제: SQL 테이블에서 행을 삭제합니다.

```DELETE: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|id|string|yes|path|없음|삭제할 행의 고유 식별자|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## PatchItem
행 업데이트: SQL 테이블에서 기존 행을 업데이트합니다.

```PATCH: /datasets/default/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|SQL 테이블의 이름|
|id|string|yes|path|없음|업데이트할 행의 고유 식별자|
|항목| |yes|body|없음|업데이트된 값을 가진 행|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## 개체 정의 

### DataSetsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|tabular|정의되지 않음|아니요 |
|Blob|정의되지 않음|아니요 |



### TabularDataSetsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|원본|string|아니요 |
|displayName|string|아니요 |
|urlEncoding|string|아니요 |
|tableDisplayName|string|아니요 |
|tablePluralName|string|아니요 |



### BlobDataSetsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|원본|string|아니요 |
|displayName|string|아니요 |
|urlEncoding|string|아니요 |



### TableMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|name|string|아니요 |
|title|string|아니요 |
|x-ms-permission|string|아니요 |
|x-ms-capabilities|정의되지 않음|아니요 |
|schema|정의되지 않음|아니요 |



### TableCapabilitiesMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|sortRestrictions|정의되지 않음|아니요 |
|filterRestrictions|정의되지 않음|아니요 |
|filterFunctions|array|아니요 |



### Object


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|



### TableSortRestrictionsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|sortable|부울|아니요 |
|unsortableProperties|array|아니요 |
|ascendingOnlyProperties|array|아니요 |



### TableFilterRestrictionsMetadata


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|filterable|부울|아니요 |
|nonFilterableProperties|array|아니요 |
|requiredProperties|array|아니요 |



### DataSetsList


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|value|array|아니요 |



### DataSet


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|이름|string|아니요 |
|DisplayName|string|아니요 |



### 테이블


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|이름|string|아니요 |
|DisplayName|string|아니요 |



### 항목


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|ItemInternalId|string|아니요 |



### TablesList


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|value|array|아니요 |



### ItemsList


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|value|array|아니요 |


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->