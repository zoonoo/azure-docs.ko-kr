<properties 
   pageTitle="SMTP 커넥터 API 앱" 
   description="SMTPConnector를 사용하는 방법" 
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


# 논리 앱에서 SMTP 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

SMTP 커넥터를 사용하면 SMTP 서버에 연결하고 첨부 파일이 있는 전자 메일을 보내는 동작을 수행할 수 있습니다. SMTP 커넥터 "전자 메일 보내기" 동작을 사용하면 지정된 전자 메일 주소로 전자 메일을 보낼 수 있습니다.

## 논리 앱용 SMTP 커넥터 만들기 ##
SMTP 커넥터를 사용하려면 먼저 SMTP 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	“웹 및 모바일 > Azure 마켓플레이스”로 이동하여 “SMTP 커넥터”를 검색합니다.
3.	다음과 같이 SMTP 커넥터를 구성합니다.
 
	![][1] - **위치** -커넥터를 배포할 지리적 위치를 선택합니다. - **구독** - 이 커넥터를 만들기 원하는 구독을 선택합니다. - **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다. - **웹 호스팅 계획** - 웹호스팅 계획을 선택하거나 만듭니다. - **가격 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다. - **이름** - SMTP 커넥터의 이름을 지정합니다. - **패키지 설정** - **사용자 이름** - SMTP 서버에 연결할 때 사용할 사용자 이름을 지정합니다. - **암호** - SMTP 서버에 연결할 때 사용할 암호를 지정합니다. - **서버 주소** - SMTP 서버 이름 또는 IP 주소를 지정합니다. - **서버 포트** - SMTP 서버 포트 번호를 지정합니다. - **SSL 사용** - SSL/TLS 보안 채널을 통해 SMTP를 사용하려면 true로 지정합니다.
4.	만들기를 클릭합니다. 새 SMTP 커넥터가 만들어집니다.
5.	API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 SMTP 커넥터를 사용할 수 있습니다. 

## 논리 앱에서 SMTP 커넥터 사용 ##
API 앱을 만들고 나면 이제 SMTP 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 SMTP 커넥터가 있는 동일한 리소스 그룹을 선택합니다.
 
	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. 
 
	![][3]
3.	SMTP 커넥터가 오른쪽의 갤러리에 있는 “이 리소스 그룹의 API 앱” 섹션에 나타납니다. 해당 커넥터를 선택합니다.
 
	![][4]
4.	“SMTP 커넥터”를 클릭하여 SMTP 커넥터 API 앱을 편집기에 놓을 수 있습니다. 
	
7.	이제 흐름에서 SMTP 커넥터를 사용할 수 있습니다. "전자 메일 보내기" 작업을 선택하고 다음과 같이 입력 속성을 구성합니다.
	- **받는 사람** - 받는 사람의 전자 메일 주소입니다. 세미콜론(;)을 사용하여 여러 전자 메일 주소를 구분합니다. 예제: recipient1@domain.com;recipient2@domain.com.
	- **참조** - 참조 받는 사람의 전자 메일 주소입니다. 세미콜론(;)을 사용하여 여러 전자 메일 주소를 구분합니다. 예제: recipient1@domain.com;recipient2@domain.com.
	- **제목** - 전자 메일 제목입니다.
	- **본문** - 전자 메일 본문입니다.
	- **Is HTML** - 이 속성이 true로 설정되면 본문의 내용이 HTML로 보내집니다.
	- **숨은 참조** - 숨은 참조를 위한 받는 사람의 전자 메일 주소입니다. 세미콜론(;)을 사용하여 여러 전자 메일 주소를 구분합니다. 예제: recipient1@domain.com;recipient2@domain.com.
	- **중요도** - 전자 메일의 중요도입니다. 옵션은 Normal(보통), Low(낮음), High(높음)입니다.
	- **첨부** - 전자 메일과 함께 보낼 첨부 파일입니다. 다음 필드를 포함합니다.
		- 콘텐츠(문자열)
		- 콘텐츠 전송 인코딩(Enum)(“none”|”base64”)
		- 콘텐츠 형식(문자열)
		- 콘텐츠 ID(문자열)
		- 파일 이름(문자열)
	 
	
	![][5] ![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG
 

<!---HONumber=62-->