<properties
   pageTitle="논리 앱에서 POP3 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="POP3 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="sameerch"/>


# POP3 커넥터 시작 및 논리 앱에 추가
>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

POP3 서버에 연결하여 첨부 파일이 있는 메일을 비롯한 메일을 검색합니다.

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. POP3 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.


## 논리 앱용 POP3 커넥터 만들기 ##
POP3 커넥터를 사용하려면 먼저 POP3 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이는 논리 앱 디자이너에서 직접 수행하거나 외부에서 수행할 수 있습니다. 디자이너 외부에서는 다음과 같은 방법으로 인스턴스를 만들 수 있습니다.

1.	Azure 포털 홈페이지에서 Azure 마켓플레이스를 엽니다.
2.	“모든 항목”에서 “POP3 커넥터”를 검색합니다.
3.	다음과 같이 POP3 커넥터를 구성합니다.

	![][1]
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - POP3 커넥터의 이름을 지정합니다.
	- **패키지 설정**
		- **사용자 이름** POP3 서버에 연결할 때 사용할 사용자 이름을 지정합니다.
		- **암호** POP3 서버에 연결할 때 사용할 암호를 지정합니다.
		- **서버 주소** POP3 서버 이름 또는 IP 주소를 지정합니다.
		- **서버 포트** POP3 서버 포트 번호를 지정합니다.
		- **SSL 사용** SSL/TLS 보안 채널을 통해 POP3를 사용하려면 true를 지정합니다.
4.	만들기를 클릭합니다. 새 POP3 커넥터가 만들어집니다.
5.	API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 POP3 커넥터를 사용할 수 있습니다.

## 논리 앱에서 POP3 커넥터 사용 ##
API 앱을 만들고 나면 이제 POP3 커넥터를 논리 앱에 대한 트리거로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 POP3 커넥터가 있는 동일한 리소스 그룹을 선택합니다.

	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.

	![][3]
3.	POP3 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다. 해당 커넥터를 선택합니다.

	![][4]
4.	"POP3 커넥터"를 클릭하여 POP3 커넥터 API 앱을 편집기에 놓을 수 있습니다.

5.	이제 흐름에서 POP3 커넥터를 사용할 수 있습니다. "전자 메일 가져오기" 트리거를 선택하고 빈도 및 간격을 구성합니다. 흐름의 다른 작업에서 POP3 트리거에서 검색된 전자 메일을 사용할 수 있습니다.
		 

	![][5]
	![][6]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure Logic Apps를 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=AcomDC_0727_2016-->