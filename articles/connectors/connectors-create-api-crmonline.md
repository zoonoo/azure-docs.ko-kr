<properties
	pageTitle="Logic Apps에 Dynamics CRM Online 커넥터 추가 | Microsoft Azure"
	description="Azure 앱 서비스로 논리 앱을 만듭니다. Dynamics CRM Online 연결 공급자는 Dynamics CRM Online의 항목으로 작업하기 위한 API를 제공합니다."
	services="logic-apps"    
	documentationCenter=""     
	authors="MandiOhlinger"    
	manager="erikre"    
	editor="" 
	tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/15/2016"
ms.author="mandia"/>

# Dynamics CRM Online 커넥터 시작
Dynamics CRM Online에 연결하여 새 레코드 만들기, 항목 업데이트 등의 작업을 수행합니다. CRM Online을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- CRM Online에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다.
- 레코드 삭제, 엔터티 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 CRM에서 항목이 업데이트되면 Office 365를 사용하여 메일을 보낼 수 있습니다.

이 토픽에서는 논리 앱에서 Dynamics CRM Online 커넥터를 사용하는 방법을 보여 주고 트리거 및 작업을 나열합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 GA(일반 공급)에 적용됩니다.

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../app-service-logic/app-service-logic-what-are-logic-apps.md) 및 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## Dynamics CRM Online에 연결

논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 Dynamics에 연결하려면 먼저 Dynamics CRM Online *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 Dynamics를 사용하는 경우 Dynamics CRM Online 계정에 대한 자격 증명을 입력하여 연결을 만듭니다.


### 연결 만들기

>[AZURE.INCLUDE [Dynamics CRM Online 연결 공급자에 대한 연결을 만드는 단계](../../includes/connectors-create-api-crmonline.md)]

## 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. 원하는 간격 및 빈도로 서비스의 "폴링"을 트리거합니다. [트리거에 대해 자세히 알아보세요](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. 논리 앱에서 트리거 목록을 가져오려면 "dynamics"를 입력합니다.

	![](./media/connectors-create-api-crmonline/dynamics-triggers.png)

2. **Dynamics CRM Online - 레코드가 만들어질 때**를 선택합니다. 연결이 이미 있는 경우 드롭다운 목록에서 조직 및 엔터티를 선택합니다.

	![](./media/connectors-create-api-crmonline/select-organization.png)

	로그인하라는 메시지가 표시되면 로그인 세부 정보를 입력하여 연결을 만듭니다. 이 토픽의 [연결 만들기](connectors-create-api-crmonline.md#create-the-connection)에 단계가 표시됩니다.

	> [AZURE.NOTE] 이 예제에서는 레코드가 만들어질 때 논리 앱이 실행됩니다. 이 트리거의 결과를 보려면 전자 메일을 보내는 다른 작업을 추가합니다. 예를 들어 새 파일이 추가될 때 전자 메일을 보내는 Office 365 *전자 메일 보내기* 작업을 추가합니다.

3. **편집** 단추를 선택하고 **빈도** 및 **간격** 값을 설정합니다. 예를 들어 15분마다 폴링을 트리거하려면 **빈도**를 **분**으로, **간격**을 **15**로 설정합니다.

	![](./media/connectors-create-api-crmonline/edit-properties.png)

4. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.


## 작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 등이 표시됩니다.

	![](./media/connectors-create-api-crmonline/add-action.png)

2. **작업 추가**를 선택합니다.

3. 텍스트 상자에 "dynamics"를 입력하여 사용 가능한 모든 작업의 목록을 표시합니다.

	![](./media/connectors-create-api-crmonline/dynamics-actions.png)

4. 이 예제에서는 **Dynamics CRM Online - 레코드를 업데이트**를 선택합니다. 연결이 이미 존재하는 경우 **조직 이름**, **엔터티 이름** 및 기타 속성을 선택합니다.

	![](./media/connectors-create-api-crmonline/sample-action.png)

	연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 토픽의 [연결 만들기](connectors-create-api-crmonline.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다.

	> [AZURE.NOTE] 이 예제에서는 CRM Online에서 기존 레코드를 업데이트합니다. 다른 트리거의 출력을 사용하여 레코드를 업데이트할 수 있습니다. 예를 들어 SharePoint *기존 항목이 수정될 때* 트리거를 추가합니다. 그런 다음 SharePoint 필드를 사용하여 CRM Online의 기존 레코드를 업데이트하는 CRM Online *레코드 업데이트* 동작을 추가합니다.

5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.


## 기술 세부 정보

## 트리거

|트리거 | 설명|
|--- | ---|
|[레코드가 만들어질 때](connectors-create-api-crmonline.md#when-a-record-is-created)|CRM에서 개체를 만들 때 흐름을 트리거합니다.|
|[레코드가 업데이트될 때](connectors-create-api-crmonline.md#when-a-record-is-updated)|CRM에서 개체가 수정될 때 흐름을 트리거합니다.|
|[레코드가 삭제될 때](connectors-create-api-crmonline.md#when-a-record-is-deleted)|CRM에서 개체가 삭제될 때 흐름을 트리거합니다.|


## 작업

|작업|설명|
|--- | ---|
|[레코드 목록](connectors-create-api-crmonline.md#list-records)|이 작업은 엔터티에 대한 레코드를 가져옵니다.|
|[새 레코드 만들기](connectors-create-api-crmonline.md#create-a-new-record)|이 작업은 엔터티에 대한 새 레코드를 만듭니다.|
|[레코드 가져오기](connectors-create-api-crmonline.md#get-record)|이 작업은 엔터티에 대해 지정된 레코드를 가져옵니다.|
|[레코드 삭제](connectors-create-api-crmonline.md#delete-a-record)|이 작업은 엔터티 컬렉션에서 레코드를 삭제합니다.|
|[레코드 업데이트](connectors-create-api-crmonline.md#update-a-record)|이 작업은 엔터티에 대한 기존 레코드를 업데이트합니다.|

### 트리거 및 작업 세부 정보

이 섹션에서는 모든 필수 또는 선택적 입력 속성 및 커넥터와 연결된 모든 해당 출력을 비롯한 각 트리거 및 작업에 대한 특정 세부 정보를 참조하세요.

#### 레코드가 만들어질 때
CRM에서 개체를 만들 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|가져올 항목의 최대 수(기본값 = 256)|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|


#### 레코드가 업데이트될 때
CRM에서 개체가 수정될 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|가져올 항목의 최대 수(기본값 = 256)|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|


#### 레코드가 삭제될 때
CRM에서 개체가 삭제될 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|가져올 항목의 최대 수(기본값 = 256)|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|


#### 레코드 목록
이 작업은 엔터티에 대한 레코드를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|가져올 항목의 최대 수(기본값 = 256)|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|


#### 새 레코드 만들기
이 작업은 엔터티에 대한 새 레코드를 만듭니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음.


#### 레코드 가져오기
이 작업은 엔터티에 대해 지정된 레코드를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|
|id*|항목 ID|레코드에 대한 ID를 지정|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음.


#### 레코드 삭제
이 작업은 엔터티 컬렉션에서 레코드를 삭제합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|
|id*|항목 ID|레코드에 대한 ID를 지정|

별표(*)는 속성이 필수 사항임을 의미합니다.


#### 레코드 업데이트
이 작업은 엔터티에 대한 기존 레코드를 업데이트합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|dataset*|조직 이름|Contoso와 같은 CRM 조직의 이름|
|table*|엔터티 이름|엔터티 이름|
|id*|파일 ID|레코드에 대한 ID를 지정|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음.


## HTTP 응답

작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다.

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

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.

<!---HONumber=AcomDC_0817_2016-->