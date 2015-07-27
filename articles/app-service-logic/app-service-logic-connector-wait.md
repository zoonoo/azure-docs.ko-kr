<properties 
   pageTitle="Wait 커넥터" 
   description="Wait 커넥터" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/29/2015"
   ms.author="rajram"/>

#Wait 커넥터
Wait 커넥터를 사용하면 앱 실행을 지정된 기간 동안 또는 지정된 시간이 될 때까지 지연할 수 있습니다. 흐름에서 사용할 경우 실행을 지연하는 데 사용될 수 있습니다.

##Wait 커넥터 사용
Wait 커넥터를 사용하려면 먼저 Wait 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure 마켓플레이스에서 Wait 커넥터 API 앱을 선택하여 수행할 수 있습니다.

##논리 앱 디자이너 화면에서 Wait 커넥터 사용
Wait 커넥터는 동작으로 사용할 수 있습니다. 트리거가 필요하지 않습니다.

###작업
- 오른쪽 창에서 Wait 커넥터를 클릭합니다.

	![작업 목록][1]
- Wait 커넥터는 다음 두 동작을 지원합니다. 
	- 지연
	- 다음까지 지연
	 
- *지연*을 선택합니다.

	![지연 입력][2]
- 동작에 대한 입력을 제공하고 구성합니다.

	![구성된 동작][3]

<table>
	<tr>
		<th>매개 변수</th>
		<th>형식</th>
		<th>매개 변수에 대한 설명</th>
	</tr>
	<tr>
		<td>기간(분)</td>
		<td>정수</td>
		<td>지연 기간(분)</td>
	</tr>
</table>


## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 흐름에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

커넥터의 성능 통계 및 제어 보안을 검토할 수 있습니다. [API 앱 및 커넥터를 관리 및 모니터링](../app-service-api/app-service-api-manage-in-portal.md)을 참조하세요.

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=July15_HO3-->