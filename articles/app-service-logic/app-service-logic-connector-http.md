<properties
   pageTitle="HTTP Listener 및 Connector"
   description="논리 앱에 HTTP Listener 및 HTTP 동작 사용"
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
   ms.date="07/02/2015"
   ms.author="prkumar"/>


#논리 앱에 HTTP Listener 및 HTTP 동작 사용#

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. 그러나 직접 HTTP 연결을 사용해야 하는 몇 가지 시나리오가 있습니다. 즉, 다음과 같은 경우입니다.

1.	웹 또는 모바일 사용자 대화형 프런트 엔드를 지원하는 논리 앱을 개발하는 경우
2.	즉시 사용 가능한 커넥터가 없는 웹 서비스에서 데이터를 가져오고 처리하는 경우
3.	이미 웹 서비스로 노출되었지만 API 앱으로 사용할 수 없는 동작을 수행하는 경우


이러한 시나리오의 경우 갤러리는 다음과 같은 두 가지 옵션을 제공합니다.

1. HTTP Listener: 이 커넥터는 트리거 역할을 하며 구성된 끝점에서 HTTP 요청을 수신 대기합니다. 구성된 끝점에 호출이 수신되면 이 커넥터는 흐름의 새 인스턴스를 트리거하고 요청에 수신된 데이터를 처리를 위해 흐름에 전달합니다. 또한 흐름이 시작되면 들어오는 요청에 대해 자동으로 응답하도록 이 커넥터를 구성하거나, 이 커넥터를 통해 흐름 실행을 기반으로 응답을 구성하고 호출자에게 응답을 보낼 수 있습니다.
2.	HTTP 동작: 이 동작을 통해 인터넷에서 사용할 수 있는 모든 끝점에 대한 웹 요청을 구성하고 응답을 받아 흐름의 동작에서 이 응답을 사용하도록 할 수 있습니다.

##논리 앱용 HTTP Listener 만들기
HTTP Listener를 사용하려면 먼저 HTTP Listener API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 오른쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"웹 및 모바일 > API 앱"으로 이동하여 "HTTP Listener"를 검색합니다.
3.	다음과 같이 HTTP Listener를 구성합니다.

	![][1]

4.	패키지 설정을 지정할 때 수신기가 자동으로 응답해야 할지 또는 사용자가 명시적 응답을 보내도록 해야 할지에 대한 다음과 같은 옵션이 표시됩니다. 직접 응답을 보내려면 이 옵션을 False로 설정합니다.

	![][2]

5.	확인을 클릭하여 만듭니다.
6.	API 앱 인스턴스를 만들고 나서 설정을 열어 보안을 구성합니다. HTTP Listener는 현재 기본 인증을 지원합니다. HTTP Listener를 열 때 보안 옵션을 사용하여 이를 구성할 수 있습니다.

	![][3] <br> <br> <b><u>알려진 문제</u></b><br> *보안 설정에서 "None"을 기본값으로 표시하지만 정의되어 있지 않습니다. 저장하기 전에 설정을 Basic으로 변경했다가 다시 None으로 설정하여 HTTP Listener가 제대로 구성되었는지 확인해야 합니다.*

7. 끝으로, API 앱의 보안 설정을 공용(익명)으로 설정하여 외부 클라이언트가 끝점에 액세스할 수 있도록 허용해야 합니다. 이 설정은 HTTP Listener API 앱의 "모든 설정 > 응용 프로그램 설정" 아래에서 사용할 수 있습니다.

	![][10]

이 작업을 완료하고 나면 이제 동일한 리소스 그룹에 HTTP Listener를 사용하기 위한 논리 앱을 만들 수 있습니다.

##논리 앱에 HTTP Listener 사용
API 앱을 만들고 나면 이제 HTTP Listener를 논리 앱의 트리거로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

4.	새 논리 앱을 만들고 HTTP Listener가 있는 것과 동일한 리소스 그룹을 선택합니다.
5.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. HTTP Listener가 오른쪽에 있는 갤러리의 "Recently Used(최근에 사용한 항목)" 섹션에 나타납니다. 해당 커넥터를 선택합니다.
6.	이제 수신기가 흐름을 트리거하도록 할 HTTP 메서드 및 상대 URL을 설정할 수 있습니다.<br>

	![][4] ![][5]

7.	전체 URI를 가져오려면 HTTP Listener를 두 번 클릭하여 해당 구성 설정을 보고 API 앱의 "호스트" URL을 복사합니다.


	![][6]
8.	이제 다음과 같이 HTTP 요청에서 받은 데이터를 흐름의 다른 동작에 사용할 수 있습니다.<br> ![][7] ![][8]
9.	마지막으로, 응답을 보내려면 다른 HTTP Listener를 추가하고 Send HTTP Response(HTTP 응답 보내기) 동작을 선택합니다. Request ID(요청 ID)를 HTTP Listener에서 얻은 RequestID로 설정하고 응답 본문 및 반환하려는 HTTP 상태를 채웁니다. ![][9]

##HTTP 동작 사용
기본적으로 논리 앱에서는 HTTP 동작을 지원하므로 HTTP 동작을 사용하기 위해 먼저 API 앱을 만들 필요가 없습니다. 논리 앱의 어느 지점에든 HTTP 동작을 삽입할 수 있으며 호출의 URI, 헤더 및 본문을 선택할 수 있습니다. HTTP 동작은 클라이언트 쪽 보안을 위한 여러 옵션을 지원합니다. 이러한 옵션을 사용하려면 [이 문서](http://aka.ms/logicapphttpauth)를 검토하세요.

HTTP 동작의 출력은 헤더 및 본문이며, 다른 동작 및 커넥터의 출력을 사용하는 방법과 유사하게 흐름의 추가 다운스트림에서 이러한 헤더 및 본문을 사용할 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=July15_HO4-->