<properties
   pageTitle="Azure 앱 서비스에서 Azure 서비스 버스 커넥터 사용"
   description="Azure 서비스 버스 커넥터를 사용하는 방법"
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
   ms.date="06/29/2015"
   ms.author="andalmia"/>


# Azure 서비스 버스 커넥터

Azure 서비스 버스 커넥터를 사용하면 큐 및 항목과 같은 서비스 버스 엔터티에서 메시지를 보낼 수 있으며 큐 및 구독과 같은 서비스 버스 엔터티에서 메시지를 받을 수 있습니다.

## 트리거 및 작업
트리거는 발생하는 이벤트입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가되는 것이 트리거입니다. 동작은 트리거의 결과입니다. 예를 들어 주문 또는 새 메시지가 큐에 대기되면 경고 또는 메시지를 보냅니다.

Azure 서비스 버스 커넥터는 논리 앱에서 트리거나 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다.

Azure 서비스 버스 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
메시지 사용 가능 | 메시지 보내기

## Azure 서비스 버스 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. **API 앱**을 선택하고 "Azure 서비스 버스 커넥터"를 검색합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.
<br/>
![][1]

4. 다음 패키지 설정을 입력합니다.

	이름 | 설명
--- | ---
연결 문자열 | Azure 서비스 버스에 대한 연결 문자열입니다. 예를 들어 다음을 입력합니다. *Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key]*.
엔터티 이름 | 큐 또는 항목의 이름을 입력합니다.
구독 이름 | 메시지를 받을 구독의 이름을 입력합니다.

5. **만들기**를 클릭합니다.

커넥터가 만들어지면 동일한 리소스 그룹의 논리 앱에 추가할 수 있습니다.

## 논리 앱에서 서비스 버스 커넥터 사용
커넥터를 만들고 나면 이제 Azure 서비스 버스 커넥터를 논리 앱에 대한 트리거 또는 동작으로 사용할 수 있습니다. 다음을 수행합니다.

1.	새 논리 앱을 만들고 Azure 서비스 버스 커넥터가 있는 동일한 리소스 그룹을 선택합니다.
<br/>
![][2]

2.	“트리거 및 동작”을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.
<br/>
![][3]

3. Azure 서비스 버스 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다.
<br/>
![][4]

4. “Azure 서비스 버스 커넥터”를 클릭하여 Azure 서비스 버스 커넥터를 편집기에 놓을 수 있습니다.

5.	이제 워크플로에서 Azure 서비스 버스 커넥터를 사용할 수 있습니다. 흐름의 다른 동작에서 Azure 서비스 버스 트리거("메시지 사용 가능")로부터 검색된 새 메시지를 사용할 수 있습니다. <br/>
![][5]
<br/>
![][6]

Azure 서비스 버스 "메시지 보내기" 동작을 사용할 수도 있습니다.
<br/>
![][7]
<br/>
![][8]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

REST API를 사용하여 API 앱을 만듭니다. [커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)를 참조하세요.

커넥터의 성능 통계 및 제어 보안을 검토할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=July15_HO3-->