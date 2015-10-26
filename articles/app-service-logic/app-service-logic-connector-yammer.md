<properties
   pageTitle="논리 앱에서 Yammer 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Yammer 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
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


# 논리 앱에서 Yammer 커넥터 사용 #
Yammer에 연결하고 메시지 게시 동작 및 트리거를 사용하여 새 메시지를 검색합니다.

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

## 논리 앱용 Yammer 커넥터 만들기 ##
Yammer 커넥터를 사용하려면 먼저 Yammer 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "Yammer 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 다음과 같이 Yammer 커넥터를 구성합니다. ![][1]

	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **앱 서비스 계획** – 웹 호스팅 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **이름** - Yammer 커넥터의 이름을 지정합니다.

4. 만들기를 클릭합니다. 새 Yammer 커넥터가 만들어집니다.
5. API 앱 인스턴스가 생성되면 논리 앱을 만들어 Yammer 커넥터를 사용할 수 있습니다.

## 논리 앱에서 Yammer 커넥터 사용 ##
API 앱을 만들고 나면 이제 Yammer 커넥터를 논리 앱에 대한 트리거/동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만듭니다. ![][2]

2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. ![][3]

3.	Yammer 커넥터가 오른쪽의 갤러리에 있는 "이 리소스 그룹의 API 앱" 섹션에 나타납니다. ![][4]

4. “Yammer 커넥터”를 클릭하여 Yammer 커넥터 API 앱을 편집기에 놓을 수 있습니다. 권한 부여 단추를 클릭합니다. Yammer 자격 증명을 제공합니다. "허용"을 클릭합니다. ![][5] ![][6] ![][7]

이제 흐름에서 Yammer 커넥터를 사용할 수 있습니다.

## Yammer 커넥터를 트리거로 사용

흐름의 다른 동작에서 Yammer 트리거("새 메시지")로부터 검색된 새 메시지를 사용할 수 있습니다. 다음과 같이 Yammer 트리거에 대한 입력 속성을 구성합니다.

- **그룹 ID** - 새 메시지를 검색할 그룹의 ID입니다. 그룹 ID를 제공하지 않는 경우 다음 피드에서 메시지가 검색됩니다. Yammer의 그룹 URL에서 그룹 ID를 검색할 수 있습니다.
		
	예: 다음 URL에서 그룹 ID는 "5453203"입니다. https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203 ![][8] ![][9]

## Yammer 커넥터를 사용하여 메시지 게시

논리 앱에서 작업으로 Yammer 커넥터를 사용할 수 있습니다. 먼저 논리 앱에 대한 트리거를 지정하거나 아래와 같이 '이 논리를 수동으로 실행' 확인란을 선택합니다. Yammer 커넥터를 추가하고 적절하게 권한을 부여한 다음 "메시지 게시" 작업을 선택합니다. 다음과 같이 "메시지 게시" 동작에 대한 입력 속성을 구성합니다.

- **메시지 텍스트** - 게시할 메시지의 텍스트내용입니다.
- **그룹 ID** - 새 메시지를 게시할 그룹의 ID를 지정합니다. 그룹 ID를 제공하지 않는 경우 메시지는 회사 전체 피드로 게시됩니다. Yammer의 그룹 URL에서 그룹 ID를 검색할 수 있습니다.  

	예: 다음 URL에서 그룹 ID는 "5453203"입니다. https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
- 	**사용자 태그** - 메시지에 태그되어야 하는 사용자 네트워크의 배열입니다. ![][10] ![][11]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=Oct15_HO3-->