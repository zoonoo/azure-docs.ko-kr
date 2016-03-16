<properties
   pageTitle="논리 앱에서 Twilio 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Twilio 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="sameerch"/>


# Twilio 커넥터 시작 및 논리 앱에 추가
>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다. 2015-08-01-미리 보기 스키마 버전에 대한 내용을 보려면 [Twilio API](../connectors/create-api-twilio.md)를 클릭하세요.

Twilio 계정에 연결하여 SMS 메시지를 보내고 받습니다. 전화 번호와 사용 데이터를 검색할 수도 있습니다. 논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. Twilio 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 논리 앱용 Twilio 커넥터 만들기 ##
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "Twilio 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 다음과 같이 Twilio 커넥터를 구성합니다. 
	![][1]  
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - Twilio 커넥터의 이름을 지정합니다.
	- **패키지 설정**
		- **계정 SID** - 계정의 고유 식별자입니다. 계정에 대한 계정 SID는 <https://www.twilio.com/user/account/settings>에서 검색할 수 있습니다.
		- **인증 토큰** - 계정에 연결된 인증 토큰입니다. 계정에 대한 인증 토큰은 <https://www.twilio.com/user/account/settings>에서 검색할 수 있습니다.


4.	만들기를 클릭합니다. 새 Twilio 커넥터가 만들어집니다.
5.	API 앱 인스턴스가 생성되면 논리 앱을 만들어 Twilio 커넥터를 사용할 수 있습니다.

## 논리 앱에서 Twilio 커넥터 사용 ##
API 앱을 만들고 나면 이제 Twilio 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 Twilio 커넥터가 있는 동일한 리소스 그룹을 선택합니다.  
	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.  
	![][3]
3.	Twilio 커넥터가 오른쪽의 갤러리에 있는 "이 리소스 그룹의 API 앱" 섹션에 나타납니다.  
	![][4]
4. “Twilio 커넥터”를 클릭하여 Twilio 커넥터 API 앱을 편집기에 놓을 수 있습니다.

5.	이제 흐름에서 Twilio 커넥터를 사용할 수 있습니다. 흐름에서 "메시지 보내기" 동작을 사용하여 메시지를 보낼 수 있습니다. 다음과 같이 "메시지 보내기" 동작에 대한 입력 속성을 구성합니다.
	- **From Phone Number** - 보낼 메시지 형식에 사용할 수 있는 Twilio 전화 번호를 입력합니다. Twilio에서 구입한 전화 번호 또는 짧은 코드만 이 커넥터에 사용됩니다.
	- **To Phone Number** - 대상 전화 번호입니다. 허용된 형식은 + 다음에 국가 코드와 전화 번호입니다. 예: +16175551212. +를 생략하면 Twilio는 'From' 번호에 입력한 국가 코드를 사용합니다.
	- **Text** - 보낼 메시지의 텍스트입니다.

	![][5] 
	![][6]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=AcomDC_0224_2016-->