<properties
	pageTitle="논리 앱에 지연 추가 | Microsoft Azure"
	description="지연 및 다음까지 지연 작업과 Azure 논리 앱에서 사용하는 방법을 대략적으로 설명합니다."
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# 지연 및 다음까지 지연 작업 시작

지연 및 다음까지 지연 작업을 사용하면 다음과 같은 워크플로 시나리오를 완료할 수 있습니다.

- 평일까지 기다렸다가 전자 메일로 상태 업데이트 전송
- 다시 시작하고 결과를 검색하기 전에 HTTP 호출이 완료될 때까지 워크플로 지연

논리 앱에서 지연 작업 사용을 시작하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

---

## 지연 작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다](connectors-overview.md).

논리 앱에서 지연 단계는 사용하는 방법의 예제 시퀀스는 다음과 같습니다.

1. 트리거를 추가한 후 **새 단계**를 클릭하여 작업을 추가합니다.
1. "delay"를 검색하여 지연 작업을 불러옵니다. 이 예제에서는 **지연**을 선택합니다.

	![지연 작업](./media/connectors-native-delay/using-action-1.png)

1. 작업 속성을 완료하여 지연을 구성합니다.

	![지연 구성](./media/connectors-native-delay/using-action-2.png)

1. "저장"을 클릭하여 논리 앱을 게시하고 활성화합니다.

---

## 기술 세부 정보

지연 및 다음까지 지연 작업에 대한 세부 정보는 다음과 같습니다.

### 작업 세부 정보

되풀이 트리거에는 구성할 수 있는 다음과 같은 속성이 있습니다.

#### 지연

특정 시간 간격 동안 실행을 지연합니다. *는 필수 필드를 의미합니다.

|표시 이름|속성 이름|설명|
|---|---|---|
|Count*|count|지연할 시간 단위 수|
|Unit*|단위|시간 단위 - `Second`, `Minute`, `Hour` 또는 `Day`|
<br>

#### 다음까지 지연

지정된 날짜/시간까지 실행을 지연합니다. *는 필수 필드를 의미합니다.

|표시 이름|속성 이름|설명|
|---|---|---|
|Year*|timestamp|다음까지 지연 연도(GMT)|
|Month*|timestamp|다음가지 지연 월(GMT)|
|Day*|timestamp|다음까지 지연 일(GMT)|
<br>


## 다음 단계

논리 앱 및 커뮤니티를 사용하여 앞으로 이동하는 방법에 대한 세부 정보는 다음과 같습니다.

## 논리 앱 만들기

지금 플랫폼을 사용해 보고 [논리 앱을 만드세요](../app-service-logic/app-service-logic-create-a-logic-app.md). [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인할 수 있습니다.

<!---HONumber=AcomDC_0720_2016-->