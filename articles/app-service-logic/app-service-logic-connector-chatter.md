<properties
   pageTitle="논리 앱에서 Chatter 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Chatter 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Chatter 커넥터 시작 및 논리 앱에 추가 
Chatter에 연결하고 메시지를 게시하거나 피드를 검색합니다. 예를 들어 Chatter 피드를 검색할 수 있으며, 특별한 내용을 발견하면 Sales 그룹에 해당 Chatter 메시지를 게시할 수 있습니다.

Chatter 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 트리거 및 작업

트리거는 새로운 Chatter 메시지 도착과 같은 특정 이벤트를 기반으로 새 인스턴스를 시작합니다. 동작은 그 결과입니다. 예를 들어 새로운 Chatter 메시지를 받은 후 해당 메시지를 또 다른 Chatter 그룹이나 Facebook 또는 Twitter 같은 다른 소셜 미디어 사이트에 게시합니다.

Chatter 커넥터는 논리 앱에서 트리거나 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. Chatter 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | 작업
--- | ---
새 메시지 | <ul><li>메시지 게시</li><li>검색</li></ul>


## 논리 앱용 Chatter 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. “Chatter 커넥터”를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다. ![][1]  
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - Chatter 커넥터의 이름을 지정합니다.

4. **만들기**를 선택합니다.


## 논리 앱에서 Chatter Connector 사용
API 앱을 만들고 나면 이제 Chatter 커넥터를 논리 앱에서 트리거 또는 동작으로 사용할 수 있습니다. 다음을 수행합니다.

1. 논리 앱에서 **트리거 및 동작**을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.

2. 갤러리에 Chatter 커넥터가 나열됩니다. ![][4]
3. Chatter 커넥터를 선택하면 자동으로 디자이너에 추가됩니다. **인증**을 선택하고 자격 증명을 입력한 후 **허용**을 선택합니다. ![][5] ![][6] ![][7]

이제 흐름에서 Chatter Connector를 사용할 수 있습니다. 흐름의 다른 동작에서 Chatter Connector("새 메시지")로부터 검색된 새 메시지를 사용할 수 있습니다. 다음과 같이 Chatter 트리거에 대한 입력 속성을 구성합니다.

**그룹 ID** - 새 메시지를 검색할 그룹의 ID를 입력합니다. 그룹 ID를 제공하지 않는 경우 사용자의 피드에서 새 메시지가 검색됩니다. ![][8] ![][9]


유사한 방식으로 흐름에서 "메시지 게시" 동작을 선택하여 메시지를 게시하도록 Chatter 동작을 사용할 수 있습니다. "메시지 게시" 동작에 대한 입력 속성을 다음과 같이 구성합니다. **메시지 텍스트** - 게시할 메시지의 텍스트 내용 **그룹 ID** - 새 메시지가 게시될 그룹의 ID를 지정합니다. 그룹 ID를 제공하지 않으면 메시지가 사용자의 피드에 게시됩니다. **파일 이름** - 이 메시지와 함께 첨부될 파일의 이름 **콘텐츠 데이터** - 첨부 파일의 콘텐츠 데이터 **콘텐츠 형식** - 첨부 파일의 콘텐츠 형식 **콘텐츠 전송 인코딩** - 첨부 파일의 콘텐츠 전송 인코딩("none" | " base64 ") **멘션** - 이 메시지에 태깅할 사용자 이름 **해시 태그** - 메시지와 함께 게시될 해시 태그

![][10] ![][11]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=Oct15_HO3-->