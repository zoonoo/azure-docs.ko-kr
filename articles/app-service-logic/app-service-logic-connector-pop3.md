<properties
   pageTitle="POP3 커넥터 API 앱"
   description="POP3Connector 사용 방법"
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
   ms.author="sameerch"/>


# 논리 앱에서 POP3 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

POP3 커넥터는 POP3 서버에 연결하고 첨부 파일이 있는 전자 메일을 검색하는 트리거를 제공합니다.

## 논리 앱용 POP3 커넥터 만들기 ##
POP3 커넥터를 사용하려면 먼저 POP3 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이는 논리 앱 디자이너에서 직접 수행하거나 외부에서 수행할 수 있습니다. 디자이너 외부에서는 다음과 같은 방법으로 인스턴스를 만들 수 있습니다.

1.	Azure 포털 홈페이지에서 Azure 마켓플레이스를 엽니다.
2.	“모든 항목”에서 “POP3 커넥터”를 검색합니다.
3.	다음과 같이 POP3 커넥터를 구성합니다.

	![][1]
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들기 원하는 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 위치할 리소스 그룹을 선택하거나 만듭니다.
	- **웹 호스팅 계획** - 웹호스팅 계획을 선택하거나 만듭니다.
	- **가격 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - POP3 커넥터의 이름을 지정합니다.
	- **패키지 설정**
		- **사용자 이름** - POP3 서버에 연결할 때 사용할 사용자 이름을 지정합니다.
		- **암호** - POP3 서버에 연결할 때 사용할 암호를 지정합니다.
		- **서버 주소** - POP3 서버 이름 또는 IP 주소를 지정합니다.
		- **서버 포트** - POP3 서버 포트 번호를 지정합니다.
		- **SSL 사용** - SSL/TLS 보안 채널을 통해 POP3를 사용하려면 true를 지정합니다.
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
4.	“POP3 커넥터”를 클릭하여 POP3 커넥터 API 앱을 편집기에 놓을 수 있습니다.

5.	이제 흐름에서 POP3 커넥터를 사용할 수 있습니다. "전자 메일 가져오기" 트리거를 선택하고 빈도 및 간격을 구성합니다. 흐름의 다른 작업에서 POP3 트리거에서 검색된 전자 메일을 사용할 수 있습니다.
		 
	
	![][5]
	![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!----HONumber=July15_HO4-->