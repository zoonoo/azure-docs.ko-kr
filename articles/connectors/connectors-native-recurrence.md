<properties
	pageTitle="논리 앱에서 되풀이 트리거 추가 | Microsoft Azure"
	description="되풀이 트리거 및 Azure 논리 앱에서 사용하는 방법을 대략적으로 설명합니다."
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# 되풀이 트리거 시작

되풀이 트리거를 사용하여 클라우드에서 다음과 같은 강력한 워크플로를 만들 수 있습니다.

- SQL 저장 프로시저를 매일 실행하도록 워크플로 예약
- 특정 해시 태그에 대한 지난 주의 모든 트윗 요약을 전자 메일로 전송

논리 앱에서 되풀이 트리거 사용을 시작하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

---

## 되풀이 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요](connectors-overview.md).

논리 앱에서 되풀이 트리거를 설정하는 방법의 예제 시퀀스는 다음과 같습니다.

1. **되풀이** 트리거를 논리 앱의 첫 번째 단계로 추가합니다.
1. 되풀이 간격에 대한 매개 변수를 입력합니다.
1. 논리 앱은 각 시간 간격 후에 실행되기 시작합니다.

![HTTP 트리거](./media/connectors-native-recurrence/using-trigger.png)

---

## 기술 세부 정보

되풀이 트리거 및 구성할 수 있는 매개 변수 세부 정보는 다음과 같습니다.

### 트리거 세부 정보

되풀이 트리거에는 구성할 수 있는 다음과 같은 속성이 있습니다.

#### 되풀이
지정된 시간 간격 후 논리 앱을 실행합니다. *는 필수 필드를 의미합니다.

|표시 이름|속성 이름|설명|
|---|---|---|
|빈도*|frequency|시간 단위 - `Second`, `Minute`, `Hour`, `Day` 또는 `Year`|
|간격*|interval|되풀이에 대해 지정된 빈도 간격|
|표준 시간대|timeZone|UTC 오프셋 없이 startTime이 제공될 경우 이 timeZone이 사용됩니다.|
|시작 시간|startTime|[ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 시작 시간입니다. UTC 오프셋 없이 startTime이 제공될 경우 이 timeZone이 사용됩니다.|
<br>


## 다음 단계

논리 앱 및 커뮤니티를 사용하여 앞으로 이동하는 방법에 대한 세부 정보는 다음과 같습니다.

## 논리 앱 만들기

지금 플랫폼을 사용해 보고 [논리 앱을 만드세요](../app-service-logic/app-service-logic-create-a-logic-app.md). [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인할 수 있습니다.

<!---HONumber=AcomDC_0727_2016-->