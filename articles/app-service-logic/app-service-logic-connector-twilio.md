<properties 
   pageTitle="Twilio 커넥터 API 앱" 
   description="TwilioConnector를 사용하는 방법" 
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# 논리 앱에서 Twilio 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

Twilio 커넥터를 사용하면 Twilio 계정에서 SMS를 보내고 받을 수 있습니다. 전화 번호와 사용 데이터를 검색할 수도 있습니다.

## 논리 앱용 Twilio 커넥터 만들기 ##
Twilio 커넥터를 사용하려면 먼저 Twilio 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"API 앱"으로 이동하여 "Twilio 커넥터"를 검색합니다.
3.	다음과 같이 Twilio 커넥터를 구성합니다.
 
	![][1] - **위치** - 커넥터를 배포할 지리적 위치를 선택합니다. - **구독** - 이 커넥터를 만들기 원하는 구독을 선택합니다. - **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다. - **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다. - **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다. - **이름** - Twilio Connector에 이름을 부여합니다.- **패키지 설정** - **계정 SID** - 계정의 고유 식별자입니다. 계정에 대한 계정 SID는 <https://www.twilio.com/user/account/settings>에서 검색할 수 있습니다 - **인증 토큰** -계정에 연결된 권한 부여 토큰입니다. 계정에 대한 인증 토큰은 <https://www.twilio.com/user/account/settings>에서 검색할 수 있습니다.


4.	만들기를 클릭합니다. 새 Twilio 커넥터가 만들어집니다.
5.	API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 Twilio 커넥터를 사용할 수 있습니다. 

## 논리 앱에서 Twilio 커넥터 사용 ##
API 앱을 만들고 나면 이제 Twilio 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 Twilio 커넥터가 있는 동일한 리소스 그룹을 선택합니다.
 
	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. 
 
	![][3]
3.	Twilio 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다.
 
	![][4]
4. “Twilio 커넥터”를 클릭하여 Twilio 커넥터 API 앱을 편집기에 놓을 수 있습니다.
 
5.	이제 흐름에서 Twilio 커넥터를 사용할 수 있습니다. 흐름에서 "메시지 보내기" 동작을 사용하여 메시지를 보낼 수 있습니다. 다음과 같이 "메시지 보내기" 동작에 대한 입력 속성을 구성합니다.
	- **From Phone Number** - 보낼 메시지 형식에 사용할 수 있는 Twilio 전화 번호를 입력합니다. Twilio에서 구입한 전화 번호 또는 짧은 코드만 이 커넥터에 사용됩니다.
	- **To Phone Number** - 대상 전화 번호입니다. 허용된 형식은 + 다음에 국가 코드와 전화 번호입니다. 예: +16175551212. +를 생략하면 Twilio는 'From' 번호에 입력한 국가 코드를 사용합니다.
	- **Text** - 보낼 메시지의 텍스트입니다.
 
	![][5] ![][6]



	<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG
 

<!---HONumber=62-->