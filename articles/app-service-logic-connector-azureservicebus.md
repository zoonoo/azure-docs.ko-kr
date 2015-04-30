<properties 
   pageTitle="Azure 서비스 버스 커넥터 API 응용 프로그램" 
   description="AzureServiceBusConnector를 사용하는 방법" 
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


# 논리 앱에서 Azure 서비스 버스 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. 

Azure 서비스 버스 커넥터를 사용하면 큐/항목/구독에서 메시지를 주고 받을 수 있습니다.

## 논리 앱에서 Azure 서비스 버스 커넥터 만들기 ##
Azure 서비스 버스 커넥터를 사용하려면 먼저 Azure 서비스 버스 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"웹 및 모바일 > API 앱"으로 이동하여 "Azure 서비스 버스 커넥터"를 검색합니다.
3.	다음과 같이 Azure 서비스 버스 커넥터를 구성합니다.
 
	![][1]
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - Azure 서비스 버스 커넥터의 이름을 지정합니다.
	- **패키지 설정**
		- **연결 문자열** - TAzure 서비스 버스 의 연결 문자열입니다. 예: Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key]
		- **엔터티 이름** - 큐 또는 항목의 이름입니다.
		- **구독 이름** - 메시지를 받을 구독의 이름입니다. 

4.	만들기를 클릭합니다. 새 Azure 서비스 버스 커넥터가 만들어집니다.
5.	API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 Azure 서비스 버스 커넥터를 사용할 수 있습니다. 

## 논리 앱에서 Azure 서비스 버스 커넥터 사용 ##
API 앱을 만들고 나면 이제 Azure 서비스 버스 커넥터를 논리 앱에 대한 트리거/동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 Azure 서비스 버스 커넥터가 있는 동일한 리소스 그룹을 선택합니다.
 
	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. 
 
	![][3]
3.	Azure 서비스 버스 커넥터가 오른쪽의 갤러리에 있는 "이 리소스 그룹의 API 앱" 섹션에 나타납니다.
 
	![][4]
4. "Azure 서비스 버스 커넥터"를 클릭하여 Azure 서비스 버스 커넥터 API 앱을 편집기에 놓을 수 있습니다.
 
5.	이제 흐름에서 Azure 서비스 버스 커넥터를 사용할 수 있습니다. 흐름의 다른 동작에서 Azure 서비스 버스 트리거("메시지 사용 가능")로부터 검색된 새 메시지를 사용할 수 있습니다.
 
	![][5]
	![][6] 
6.	유사한 방식으로 Azure 서비스 버스 동작 "메시지 보내기"를 사용하여 메시지를 보낼 수 있습니다.

	![][7]
	![][8]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!--HONumber=52-->