<properties 
	pageTitle="논리 앱 모니터링" 
	description="논리 앱의 기능을 확인하는 방법" 
	authors="stepsic-microsoft-com" 
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
	ms.date="03/10/2015"
	ms.author="stepsic"/>

#논리 앱 모니터링

[논리 앱 만들기](app-service-logic-create-a-logic-app.md)에 설명된 단계에 따라 논리 앱을 만든 후 Azure 포털에서 전체 실행 기록을 확인할 수 있습니다. 기록을 보려면 포털 화면의 왼쪽에서 **찾아보기**를 클릭하고 **논리 앱**을 선택합니다. 구독에 포함된 논리 앱 목록이 나타납니다. 논리 앱을 **사용** 또는 **사용 안 함**으로 설정할 수 있습니다. **사용**으로 설정된 논리 앱은 트리거가 트리거 이벤트에 대한 응답으로 논리 앱을 실행함을 의미하고, **사용 안 함**으로 설정된 논리 앱은 이벤트에 대한 응답으로 실행되지 않습니다.

![개요](./media/app-service-logic-monitor-your-logic-apps/overview.png)

논리 앱에 대한 블레이드가 표시되는 경우 유용한 다음 두 개의 섹션이 있습니다.

- **요약** - 최신 상태를 알려주며 논리 앱 편집을 위한 진입점입니다.
- **모든 실행** - 이 논리 앱의 실행 목록을 보여 줍니다.

##실행

![모든 실행](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

이 실행 목록은 특정 실행의 **시작 시간**, **실행 식별자**(REST API 호출 시 사용할 수 있음) 및 **기간**을 보여 줍니다. 행을 클릭하면 해당 실행의 세부 정보가 표시됩니다.

세부 정보 블레이드는 실행에 포함된 모든 작업의 실행 시간 및 순서 그래프를 보여 줍니다. 그 아래에는 실행된 모든 작업의 전체 목록이 있습니다.

![실행 및 동작](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

끝으로, 특정 동작의 **입력** 및 **출력** 섹션에서 해당 동작으로 전달되고 동작에서 수신된 모든 데이터를 확인할 수 있습니다.

다른 중요한 정보는 **추적 ID**입니다. 이 식별자는 모든 작업 호출의 헤더에 전달됩니다. 고유한 서비스 내에 로깅이 있는 경우 추적 ID를 로깅하는 것이 좋으며, 그런 다음 고유한 로그와 이 식별자를 상호 참조할 수 있습니다.

##트리거 기록 및 버전 관리

현재 UI에서는 사용할 수 없지만(곧 제공 예정) [REST API](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409)를 통해 사용할 수 있는 두 가지 추가 기능이 있습니다.

1. **트리거 기록** - 폴링 트리거는 일정한 간격으로 API를 확인하지만 응답에 따라 실행을 시작하지 않을 수도 있습니다(예: `200`은 실행, `202`는 실행 안 함을 의미). 트리거 기록을 통해 수행되었지만 논리 앱을 실행하지 않는 모든 호출(`202` 응답)을 확인할 수 있습니다.

2. **이전 버전** - 논리 앱의 정의를 업데이트하면 이전 버전의 정의가 저장됩니다. 이는 진행 중인 실행이 있는 경우 해당 실행에서 실행을 시작할 때 존재한 논리 앱 버전을 참조하기 때문입니다. 실행이 진행되는 동안에는 실행 정의를 변경할 수 없습니다. 버전 기록 REST API를 통해 이 정보에 액세스할 수 있습니다.
 

<!---HONumber=62-->