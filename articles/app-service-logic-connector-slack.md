<properties 
	pageTitle="Slack 커넥터"
	description="Slack 커넥터 시작"
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
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# 논리 앱에서 Slack 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

Slack 커넥터를 사용하여 Slack 채널에 메시지를 게시할 수 있습니다.

## 논리 앱용 Slack 커넥터 만들기 ##
Slack 커넥터를 사용하려면 먼저 Slack 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 “+ 새로 만들기” 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	“웹 및 모바일 > Azure 마켓플레이스”로 이동하여 “Slack 커넥터”를 검색합니다.
3.	다음과 같이 Slack 커넥터를 구성합니다.
 
	![][1] 
	- **이름** - Slack 커넥터의 이름 제공
	- **앱 서비스 계획** - 앱 서비스 계획 선택 또는 만들기
	-  **가격 계층** - 커넥터에 대한 가격 책정 계층 선택
	-   **리소스 그룹** - 커넥터가 있어야 하는 리소스 그룹 선택 또는 만들기
	-    **구독** - 이 커넥터를 만들 구독 선택
	-     **위치** - 커넥터를 배포할 지리적 위치 선택

4. 만들기를 클릭합니다. 새 Slack 커넥터가 만들어집니다.
5. API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 Slack 커넥터를 사용할 수 있습니다.

## 논리 앱에서 Slack 커넥터 사용 ##
API 앱을 만들고 나면 이제 Slack 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 Slack 커넥터가 있는 동일한 리소스 그룹을 선택합니다. 지침에 따라 [새 논리 앱을 만듭니다].  	
	
2.	만들어진 논리 앱 내에서 “트리거 및 동작”을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.
	
3.	Slack 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다.
 
	![][2]
4.	“Slack 커넥터”를 클릭하여 Slack 커넥터 API 앱을 편집기에 놓을 수 있습니다. 권한 부여 단추를 클릭합니다. Microsoft 자격 증명을 제공합니다(자동 로그인하지 않는 경우). 다음 단계에 따라 Slack 계정에 로그인합니다. 거의 마지막 단계에서 Slack 계정에 액세스할 수 있는 권한을 커넥터에 제공할지 묻는 메시지가 나타납니다. "권한 부여"를 클릭하세요.
 
	![][3]
	![][4]
	![][5]
	![][6]
	
5.	이제 흐름에서 Slack 커넥터를 사용할 수 있습니다. 현재 트리거는 Slack 커넥터 내에서 사용할 수 없습니다. 사용할 수 있는 작업은 메시지 게시입니다.
 
	![][7]

6.	"메시지 게시" 환경을 살펴보겠습니다. 이 작업을 사용하여 모든 Slack 채널에 메시지를 게시할 수 있습니다.
 
	![][8]

	다음과 같이 "메시지 게시" 동작에 대한 입력 속성을 구성합니다.

 - **텍스트** - 게시할 메시지의 텍스트를 지정합니다.
 - **채널 이름** - 이 메시지를 업로드할 Slack 채널을 지정합니다. 제공하지 않으면 #general에 메시지가 게시됩니다.

 	**고급 속성**
 	- **Bot 사용자 이름** - 이 메시지에 사용할 Bot의 이름입니다. 지정하지 않으면 메시지가 "Bot"으로 게시됩니다.
 	- **아이콘 URL** - 이 메시지에 대한 아이콘으로 사용할 이미지의 URL입니다.
 	- **아이콘 이모지** - 이 메시지에 대한 아이콘으로 사용할 이모지입니다. 아이콘 URL을 재정의합니다.
 

7. 논리 앱 외부에서 커넥터를 사용하려면 커넥터를 통해 노출되는 REST API를 활용할 수 있습니다. 탐색->Api 앱->Slack 커넥터를 사용하여 이 API 정의를 볼 수 있습니다. 이제 통계 섹션 아래의 API 정의 렌즈를 클릭하여 이 커넥터를 통해 노출되는 모든 API를 볼 수 있습니다.

	![][9]

9. API에 대한 자세한 내용은 [Slack API 정의]에서도 확인할 수 있습니다.

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

<!-- Links -->
[새 논리 앱을 만듭니다]: app-service-logic-create-a-logic-app.md
[Slack API 정의]: https://msdn.microsoft.com/ko-kr/library/dn708020.aspx
<!--HONumber=52-->
