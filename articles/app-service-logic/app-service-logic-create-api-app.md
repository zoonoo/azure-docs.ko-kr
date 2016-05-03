<properties 
	pageTitle="논리 앱에 대한 API 만들기" 
	description="논리 앱과 함께 사용할 사용자 지정 API 만들기" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="04/05/2016"
	ms.author="jehollan"/>
    
# 논리 앱과 함께 사용할 사용자 지정 API 만들기

논리 앱 플랫폼을 확장하려는 경우 여러 가지 방법으로 수많은 기본 커넥터 중 하나로 제공되지 않는 API 또는 시스템을 호출할 수 있습니다. 이러한 방법 중 하나는 논리 앱 워크플로 내에서 호출할 수 있는 API 앱을 만드는 것입니다.

## 유용한 도구

논리 앱에 가장 잘 작동하는 API를 위해 API에 지원되는 작업 및 매개 변수를 자세히 설명하는 [swagger](http://swagger.io) doc를 생성하는 것이 좋습니다. 사용자를 위해 swagger를 자동으로 생성하는 수많은 라이브러리(예: [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle))가 있습니다. 또한 논리 앱과 잘 작동하도록 swagger에 주석 추가하는 데 [TRex](https://github.com/nihaue/TRex)를 사용할 수도 있습니다(표시 이름, 속성 형식 등). 논리 앱에 기본 제공되는 API 앱의 몇 가지 샘플은 [GitHub 리포지토리](http://github.com/logicappsio) 또는 [블로그](http://aka.ms/logicappsblog)를 확인합니다.

## 동작

논리 앱에 대한 기본 동작은 HTTP 요청을 수락하고 응답(일반적으로 200)을 반환하는 컨트롤러입니다. 그러나 다양한 패턴에 따라 필요에 따라 동작을 확장할 수 있습니다.

기본적으로 논리 앱 엔진은 1분 후 시간 초과됩니다. 그러나 아래 자세히 설명된 비동기 또는 webhook 패턴을 따라 시간이 오래 걸리는 동작에 API를 실행하고 동작이 완료될 때까지 엔진이 기다리도록 할 수 있습니다.

### 장기 실행 동작 - 비동기 패턴

긴 단계 또는 작업을 실행할 때 가장 먼저 해야 할 일은 엔진이 사용자가 시간 초과되지 않았음을 알고 있는지 확인하는 것입니다. 또한 작업이 완료된 것을 어떻게 파악할지 엔진과 통신해야 하며 마지막으로 관련 데이터를 엔진에 반환하여 워크플로를 계속 진행할 수 있도록 해야 합니다. 아래 흐름에 따라 API를 통해 완료할 수 있습니다. 이러한 단계는 사용자 지정 API의 관점에서 설명한 것입니다.

1\. 요청을 받으면, 즉시 응답을 반환합니다(작업이 완료되기 전에). 이 응답은 `202 ACCEPTED` 응답이고 엔진에 사용자게 데이터를 받았고 페이로드를 수락했으며 지금 처리 중임을 알려줍니다. 202 응답에는 메서드에는 다음 헤더가 포함되어야 합니다.
 * `location` 헤더(필수): 작업 상태를 확인하는 데 사용할 수 있는 URL 논리 앱에 대한 절대 경로입니다.
 * `retry-after`(선택 사항, 동작에 대한 기본값은 20). 상태를 확인하기 위해 위치 헤더 URL을 폴링할 때까지 엔진이 대기할 시간(초)입니다.

2\. 작업 상태를 확인했으면 다음 확인을 수행합니다.
 * 작업을 완료한 경우: 응답 페이로드와 함께 `200 OK` 응답을 반환합니다.
 * 작업이 아직 처리 중인 경우: 초기 응답과 동일한 헤더와 함께 다른 `202 ACCEPTED` 응답을 반환합니다.

이 패턴을 사용하면 사용자 지정 API의 스레드 내에서 매우 긴 작업을 실행할 수 있지만 논리 앱 엔진과 활성 연결을 유지하므로 시간 초과되지 않으며 작업을 완료하기 전에 진행하지 않습니다. 논리 앱에 패턴을 추가할 때는 논리 앱에 대한 정의에서 폴링 및 상태 확인을 진행하기 위해 어떠한 작업도 수행하지 않아도 된다는 점에 유의해야 합니다. 엔진은 유효한 위치 헤더와 함께 202 수락됨 응답을 확인하는 즉시 비동기 패턴을 적용하고 202가 아닌 응답이 반환될 때까지 위치 헤더를 계속해서 폴링합니다.

이 패턴에 대한 샘플은 GitHub [여기](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)에서 볼 수 있습니다.

### Webhook 동작

워크플로 중에 논리 앱을 일시 중지하고 "콜백"이 진행되기를 기다릴 수 있습니다. 이 콜백은 HTTP POST 형태로 제공됩니다. 이 패턴을 구현하려면 컨트롤러에서 구독 및 구독 취소라는 두 개의 끝점을 제공해야 합니다.

'구독'에서 논리 앱은 API가 HTTP POST로 쉽게 저장 및 콜백할 수 있는 콜백 URL을 만들고 등록합니다. 모든 콘텐츠/헤더는 논리 앱으로 전달되고 워크플로의 나머지 부분 내에서 사용할 수 있습니다. 논리 앱 엔진은 해당 단계에 도달하는 즉시 실행 중 구독 지점을 호출합니다.

실행이 취소되었으면 논리 앱 엔진은 '구독 취소' 끝점을 호출합니다. 그러면 API로 필요에 따라 콜백 URL을 등록 취소할 수 있습니다.

현재는 논리 앱 디자이너에서 swagger를 통한 webhook 끝점 검색을 지원하지 않으므로 이러한 유형의 동작을 사용하려면 "Webhook" 동작을 추가하고 URL, 헤더 및 요청 본문을 지정해야 합니다. 필요에 따라 콜백 URL을 전달하기 위해 일부 필드에 `@listCallbackUrl()` 워크플로 함수를 사용할 수 있습니다.

webhook 패턴에 대한 샘플은 GitHub [여기](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)에서 볼 수 있습니다.

## 트리거

동작 외에도 논리 앱에 대한 트리거로 작동하는 사용자 지정 API를 포함할 수 있습니다. 논리 앱을 트리거하는 데 다음 두 가지 패턴을 따를 수 있습니다.

### 폴링 트리거

폴링 트리거는 위에서 설명한 장기 실행 비동기와 비슷하게 작동합니다. 논리 앱 엔진은 특정 기간이 경과한 후 트리거 끝점을 호출합니다(SKU에 따라 다름, 프리미엄의 경우 15초, 표준의 경우 1분, 무료인 경우 1시간).

사용 가능한 데이터가 없는 경우 트리거는 `202 ACCEPTED` 응답을 `location` 및 `retry-after` 헤더와 함께 반환합니다. 그러나 트리거의 경우 `location` 헤더에 `triggerState`의 쿼리 매개 변수를 포함하는 것이 좋습니다. 논리 앱이 마지막으로 수행된 경우 API에서 알아야 할 일부 식별자입니다. 사용 가능한 데이터가 있는 경우 트리거는 `200 OK` 응답을 콘텐츠 페이로드와 함께 반환합니다. 그러면 논리 앱이 수행됩니다.

예를 들어 파일을 사용할 수 있는지 확인하기 위해 폴링한다면 다음을 수행하는 폴링 트리거를 작성할 수 있습니다.

* triggerState 없이 요청을 수신한 경우 API는 현재 시간의 triggerState와 15의 `retry-after`를 포함하는 `location` 헤더와 함께 `202 ACCEPTED`를 반환합니다.
* triggerState와 함께 요청을 수신한 경우:
 * triggerState DateTime 이후 파일이 추가되었는지 확인합니다. 
  * 1개 파일이 있는 경우 콘텐츠 페이로드와 함께 `200 OK` 응답을 반환하고 triggerState를 반환한 파일의 DateTime으로 증가시킨 후 `retry-after`를 15로 설정합니다.
  * 파일이 여러 개 있는 경우 `200 OK`와 함께 한 번에 1개를 반환할 수 있으며 `location` 헤더에서 내 triggerState를 증가시키고 `retry-after`를 0으로 설정합니다. 이렇게 하면 엔진은 사용할 수 있는 데이터가 더 많다는 것을 알고 지정된 `location` 헤더에서 데이터를 즉시 요청합니다.
  * 사용 가능한 파일이 없는 경우 `202 ACCEPTED` 응답을 반환하고 `location` triggerState는 동일하게 유지합니다. `retry-after`를 15로 설정합니다.

폴링 트리거의 샘플은 GitHub [여기](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)에서 볼 수 있습니다.

### Webhook 트리거

Webhook 트리거는 위의 Webhook 동작과 비슷하게 작동합니다. 논리 앱 엔진은 webhook 트리거가 추가 및 저장될 때마다 '구독' 끝점을 호출합니다. API로 webhook URL을 등록하고 HTTP POST를 통해 데이터가 사용 가능할 때마다 호출할 수 있습니다. 콘텐츠 페이로드 및 헤더는 논리 앱 실행에 전달됩니다.

Webhook 트리거가 삭제되는 경우(논리 앱 전체 또는 webhook 트리거만) 엔진은 '구독 취소' URL을 호출하고 여기에서 API는 콜백 URL을 등록 취소하고 필요에 따라 모든 프로세스를 중지할 수 있습니다.

현재는 논리 앱 디자이너에서 swagger를 통한 webhook 트리거 검색을 지원하지 않으므로 이러한 유형의 동작을 사용하려면 "Webhook" 트리거를 추가하고 URL, 헤더 및 요청 본문을 지정해야 합니다. 필요에 따라 콜백 URL을 전달하기 위해 일부 필드에 `@listCallbackUrl()` 워크플로 함수를 사용할 수 있습니다.

webhook 트리거의 샘플은 GitHub [여기](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)에서 볼 수 있습니다.

<!---HONumber=AcomDC_0420_2016-->