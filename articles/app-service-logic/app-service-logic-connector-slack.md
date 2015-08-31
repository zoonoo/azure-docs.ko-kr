<properties 
	pageTitle="Azure 앱 서비스에서 Slack 커넥터 사용"
	description="Slack 커넥터를 시작하는 방법"
	authors="anuragdalmia" 
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
	ms.date="08/19/2015"
	ms.author="andalmia"/>

# Slack 커넥터

Slack 채널에 연결하고 팀에 메시지를 게시합니다. 커넥터는 논리 앱에서 다양한 작업을 수행하기 위한 "워크플로"의 일부로 사용될 수 있습니다. 워크플로에서 Slack 커넥터를 활용하면 다른 커넥터를 사용하여 다양한 시나리오를 수행할 수 있습니다. 예를 들어 워크플로의 [Facebook 커넥터](app-service-logic-connector-facebook.md)를 사용하여 Slack 채널에 메시지를 게시할 수 있습니다.

## 트리거 및 작업
*트리거*는 발생하는 이벤트입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가되는 것이 트리거입니다. *작업*은 트리거의 결과입니다. 예를 들어 주문이 업데이트되면 영업 직원에게 경고를 보내는 것이 작업입니다. 또는 새 고객이 추가되면 새 고객에게 환영 전자 메일을 보냅니다.

Slack 커넥터는 논리 앱에서 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. 현재 Slack 커넥터에 대해 사용할 수 있는 트리거는 없습니다.

Slack 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
없음 | 메시지 게시

## Slack 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. **API 앱**을 선택하고 "Slack 커넥터"를 검색합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다. 
<br/>
![][1] 

4. **만들기**를 클릭합니다.

## 논리 앱에서 커넥터를 동작으로 사용

> [AZURE.IMPORTANT]커넥터 및 논리 앱은 항상 동일한 리소스 그룹에서 만들어집니다.

Slack 커넥터를 만든 후 논리 앱에 동작으로 추가할 수 있습니다.

1.	논리 앱 내에서 **트리거 및 동작**을 엽니다. [새 논리 앱 만들기](app-service-logic-create-a-logic-app.md)

2.	Slack 커넥터가 오른쪽의 갤러리에 표시됩니다. <br/> ![][2]

3.	논리 앱에 자동으로 추가하기 위해 만든 Slack 커넥터를 선택합니다.
4.	**인증**을 선택합니다. Slack 계정으로 로그인합니다. 거의 마지막 단계에서 Slack 계정에 액세스할 수 있는 권한을 커넥터에 제공할지 묻는 메시지가 나타납니다. **인증**을 선택합니다. 
<br/>
![][3]
![][4]
![][5]
![][6]
	
5.	이제 흐름에서 Slack 커넥터를 사용할 수 있습니다. 메시지 게시 동작을 사용할 수 있습니다. 
<br/>
![][7]


"메시지 게시" 환경을 살펴보겠습니다. 이 동작을 사용하여 모든 Slack 채널에 메시지를 게시할 수 있습니다.
 
![][8]

다음과 같이 "메시지 게시" 동작에 대한 입력 속성을 구성합니다.

속성 | 설명
--- | ---
텍스트 | 게시할 메시지의 텍스트를 입력합니다.
채널 이름 | 이 메시지가 게시되는 Slack 채널을 입력합니다. 채널을 입력하지 않으면 메시지는 #general에 게시됩니다.
고급 속성 | **Bot 사용자 이름**: 이 메시지에 사용할 bot의 이름입니다. 입력하지 않으면 메시지가 "Bot"으로 게시됩니다.<p><p>**아이콘 URL**: 이 메시지에 대한 아이콘으로 사용할 이미지 URL입니다.<p><p>**아이콘 이모지**: 이 메시지에 대한 아이콘으로 사용할 이모지입니다. 이 속성은 아이콘 URL 속성을 재정의합니다.


논리 앱 외부에서 Slack 커넥터를 사용할 수 있도록 이 커넥터에는 사용 가능한 REST API가 있습니다. Slack 커넥터를 열고 **API 정의**를 선택합니다.

![][9]


## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!---HONumber=August15_HO8-->