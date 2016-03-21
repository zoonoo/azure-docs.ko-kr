<properties
pageTitle="PowerApps 엔터프라이즈 또는 논리 앱에 Dynamic CRM Online API 추가 | Microsoft Azure"
description="REST API 매개 변수를 사용하는 CRM Online API 개요"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/02/2016"
ms.author="deonhe"/>

# CRM API 시작
Dynamics CRM Online에 연결하여 새 레코드 만들기, 항목 업데이트 등의 작업을 수행합니다. CRM Online API를 다음에서 사용할 수 있습니다.

- 논리 앱
- PowerApps

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/create-api-crmonline.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-crmonline.md)

CRM Online을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- CRM Online에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 레코드 삭제, 엔터티 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 CRM에서 항목이 업데이트되면 Office 365를 사용하여 메일을 보낼 수 있습니다.


PowerApps 엔터프라이즈에서 API를 추가하는 방법을 보려면 [PowerApps에서 API 등록](../power-apps/powerapps-register-from-available-apis.md)으로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
CRM API에는 다음 작업이 포함됩니다. 트리거는 없습니다.

| 트리거 | actions|
| --- | --- |
|없음| <ul><li>새 레코드 만들기</li><li>레코드 가져오기</li><li>레코드 삭제</li><li>레코드 가져오기</li><li>엔터티 가져오기</li><li>항목 업데이트</li></ul>

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## CRM Online에 대한 연결 만들기

논리 앱에 이 API를 추가할 때 Dynamics CRM Online에 로그인해야 합니다. 다음 단계를 따라 CRM Online에 로그인하고 논리 앱의 **연결** 구성을 완료합니다.

1. 논리 앱에서 **작업 추가**를 선택합니다. ![CRM 온라인 구성][13]
4. 검색 상자에 CRM을 입력하고 이름에 CRM이 있는 모든 항목이 반환될 때까지 검색을 기다립니다.
5. **Dynamics CRM Online - 새 레코드 만들기**를 선택합니다.
6. **Dynamics CRM Online에 로그인**을 선택합니다. ![CRM 온라인 구성][14]
7. CRM Online 자격 증명을 제공하여 로그인하고 응용 프로그램에 권한을 부여합니다. ![CRM 온라인 구성][15]  
8. 로그인한 후 다른 트리거 및 필요한 동작을 추가하여 완료하기 위해 논리 앱으로 돌아갑니다.
9. 위의 메뉴 모음에서 **저장**을 선택하여 작업을 저장합니다.

연결을 만든 후에 테이블 또는 데이터 집합 등의 CRM Online 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Swagger REST API 참조
적용 버전: 1.0

### 새 레코드 만들기 
엔터티에 새 레코드를 만듭니다. ```POST: /datasets/{dataset}/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|CRM 조직 contoso.crm의 고유 이름|
|테이블|string|yes|path|없음|엔터티 이름|
|항목| |yes|body|없음|만들 레코드|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 레코드 가져오기 
 엔터티에 대한 레코드를 가져옵니다. ```GET: /datasets/{dataset}/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|CRM 조직 contoso.crm의 고유 이름|
|테이블|string|yes|path|없음|엔터티 이름|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수입니다. 기본값은 0입니다.|
|$top|정수|no|쿼리|없음|가져올 항목의 최대 수. 기본값은 100입니다.|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정할 ODATA orderBy 쿼리|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|




### 데이터 집합 반환 
 데이터 집합을 반환합니다. ```GET: /datasets```

이 호출에 대한 매개 변수는 없습니다.

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



### 테이블 항목 가져오기 
CRM 엔터티에 대한 특정 레코드를 가져오는 데 사용됩니다. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|CRM 조직 contoso.crm의 고유 이름|
|테이블|string|yes|path|없음|엔터티 이름|
|id|string|yes|path|없음|레코드 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|

### 목록에서 항목 삭제 
목록에서 항목을 삭제합니다. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|CRM 조직 contoso.crm의 고유 이름|
|테이블|string|yes|path|없음|엔터티 이름|
|id|string|yes|path|없음|레코드 식별자|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|



### 기존 테이블 항목에 패치 적용 
CRM 엔터티에 대한 기존 레코드를 부분적으로 업데이트하는 데 사용됩니다. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|CRM 조직 contoso.crm의 고유 이름|
|테이블|string|yes|path|없음|엔터티 이름|
|id|string|yes|path|없음|레코드 식별자|
|항목| |yes|body|없음|업데이트할 레코드|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|

### 엔터티 가져오기 
CRM 인스턴스에 있는 엔터티 목록을 가져오는 데 사용됩니다. ```GET: /datasets/{dataset}/tables```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|dataset|string|yes|path|없음|CRM 조직 contoso.crm의 고유 이름|

#### 응답
|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## 개체 정의

#### DataSetsMetadata

|속성 이름 | 데이터 형식 | 필수|
|---|---|---|
|tabular|정의되지 않음|no|
|Blob|정의되지 않음|no|

#### TabularDataSetsMetadata

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|정의되지 않음|no|

#### DataSetsList

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|

#### DataSet

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|이름|string|no|
|DisplayName|string|no|


#### 테이블

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|이름|string|no|
|DisplayName|string|no|

#### 항목

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


#### TablesList

|속성 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API 목록](apis-list.md)으로 돌아갑니다.


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/create-api-crmonline/crmconfig1.png
[14]: ./media/create-api-crmonline/crmconfig2.png
[15]: ./media/create-api-crmonline/crmconfig3.png

<!---HONumber=AcomDC_0309_2016-->