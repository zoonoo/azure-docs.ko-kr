<properties
pageTitle="PowerApps 엔터프라이즈에 Excel API 추가 | Microsoft Azure"
description="REST API 매개 변수를 사용하는 Excel API 개요"
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
ms.date="02/23/2016"
ms.author="deonhe"/>

# Excel API 시작

Excel에 연결하여 행을 삽입하거나 삭제하는 등의 작업을 수행합니다.

Excel API는 PowerApps 엔터프라이즈에서 사용할 수 있습니다.

Excel을 사용하여 다음을 수행할 수 있습니다.

- PowerApps 엔터프라이즈에 Excel API를 추가합니다. 이렇게 하면 사용자가 앱 내에서 이 API를 사용할 수 있습니다. 

PowerApps 엔터프라이즈에서 API를 추가하는 방법을 보려면 [Register an API in PowerApps](../power-apps/powerapps-register-from-available-apis.md)(PowerApps에서 API 등록)로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Excel에는 다음 작업이 포함됩니다. 트리거는 없습니다.

|트리거|동작|
|--- | ---|
|없음 | <ul><li>행 가져오기</li><li>행 삽입</li><li>행 삭제</li><li>행 가져오기</li><li>테이블 가져오기</li><li>행 업데이트</li></ul>

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## Swagger REST API 참조
#### 이 문서 적용 버전: 1.0


### Excel 테이블에 새 행을 삽입합니다.
**```POST: /datasets/{dataset}/tables/{table}/items```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|Excel 파일 이름|
|테이블|string|yes|path|없음|Excel 테이블 이름|
|항목| |yes|body|없음|지정된 Excel 테이블에 삽입할 행|


### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### Excel 테이블에서 단일 행을 검색합니다.
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|Excel 파일 이름|
|테이블|string|yes|path|없음|Excel 테이블 이름|
|id|string|yes|path|없음|검색할 행의 고유 식별자|


### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### Excel 테이블에서 행을 삭제합니다.
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|Excel 파일 이름|
|테이블|string|yes|path|없음|Excel 테이블 이름|
|id|string|yes|path|없음|삭제할 행의 고유 식별자|


### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### Excel 테이블에서 기존 행을 업데이트합니다.
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|Excel 파일 이름|
|테이블|string|yes|path|없음|Excel 테이블 이름|
|id|string|yes|path|없음|업데이트할 행의 고유 식별자|
|항목| |yes|body|없음|업데이트된 값을 가진 행|


### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



## 개체 정의

#### DataSetsMetadata

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|tabular|정의되지 않음|no|
|Blob|정의되지 않음|no|

#### TabularDataSetsMetadata

| 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

| 이름 | 데이터 형식 |필수|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|정의되지 않음|no|

#### DataSetsList

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|

#### DataSet

| 이름 | 데이터 형식 |필수|
|---|---|---|
|이름|string|no|
|DisplayName|string|no|

#### 테이블

| 이름 | 데이터 형식 |필수|
|---|---|---|
|이름|string|no|
|DisplayName|string|no|

#### 항목

| 이름 | 데이터 형식 |필수|
|---|---|---|
|ItemInternalId|string|no|

#### TablesList

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|

#### ItemsList

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md) [파워 앱 만들기](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0224_2016-->