<properties 
   pageTitle="Facebook Connector API 앱" 
   description="FacebookConnector 사용 방법" 
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
   ms.date="03/20/2015"
   ms.author="adgoda"/>


# 논리 앱에 Facebook Connector 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

Facebook Connector를 사용하면 Facebook 계정에서 "New Post on User Timeline(사용자 타임라인의 새 게시물)", "New Post on Page(페이지의 새 게시물)", "Publish Post(게시물 게시)", "Publish Photo(사진 게시)" 등을 검색할 수 있습니다.

- Facebook Connector 트리거는 "New Post on User Timeline(사용자 타임라인의 새 게시물)" 또는 "New Post on Page(페이지의 새 게시물)"를 검색합니다. 새로운 트윗이 검색되면 흐름의 새 인스턴스를 트리거하고 요청에서 수신된 데이터를 처리하기 위해 흐름에 전달합니다. 
- Facebook Connector 동작을 통해 "Publish Post(게시물 게시)", "Publish Photo(사진 게시)" 등을 수행할 수 있습니다. 이러한 동작은 응답을 다시 가져오고 흐름의 동작에서 이를 사용할 수 있도록 합니다.

## 논리 앱용 Facebook Connector 만들기 ##
Facebook Connector를 사용하려면 먼저 Facebook Connector API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"웹 및 모바일 > API 앱"으로 이동하여 "Facebook Connector"를 검색합니다.
3.	다음과 같이 Facebook Connector를 구성합니다.
 
	![][1]
4.	확인을 클릭하여 만듭니다.
5.	API 앱 인스턴스를 만들고 나면 동일한 리소스 그룹에 Facebook Connector를 사용하기 위한 논리 앱을 만들 수 있습니다. 
	- 논리 앱에서 Facebook Connector API 앱 인스턴스를 만들 수도 있습니다. 
	- 논리 앱 편집기를 열고 오른쪽의 갤러리에서 사용할 수 있는 Facebook Connector를 클릭합니다.
	- 그러면 논리 앱이 만들어진 것과 동일한 리소스 그룹에 Facebook Connector API 앱 인스턴스가 만들어집니다.


## 논리 앱에 Facebook Connector 사용 ##
API 앱을 만들고 나면 이제 Facebook Connector를 논리 앱의 트리거/동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 Facebook Connector가 있는 것과 동일한 리소스 그룹을 선택합니다.
 
	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. 
 
	![][3]
3.	Facebook Connector가 오른쪽에 있는 갤러리의 "Recently Used(최근에 사용한 항목)" 섹션에 나타납니다. 해당 커넥터를 선택합니다.
 
	![][4]
4.	오른쪽에 있는 갤러리의 "Recently Used(최근에 사용한 항목)" 아래에 있는 "Facebook Connector"를 클릭하여 Facebook Connector API 앱을 편집기로 끌어올 수 있습니다. 권한 부여 단추를 클릭합니다. Facebook 자격 증명을 입력합니다.
  
	![][5]
5.	"Azure AppService Logic Apps(Azure AppService 논리 앱)"를 허용합니다. 

	![][6] 
	![][7] 
	![][8] 
6.	트리거를 선택합니다.
 
	![][9]
7.	이제 Facebook 트리거에서 검색된 게시물을 다른 동작에 사용할 수 있습니다. 아래 흐름에서는 사용자의 Facebook 타임라인에 새 게시물이 게시될 때마다 동일한 게시물이 사용자의 Twitter 타임라인에 트윗됩니다.
 
	![][10]
8.	비슷한 방식으로 Facebook Connector 동작을 사용하여 흐름을 만들 수 있습니다. 아래 흐름에서는 Yammer 그룹에 게시된 새 메시지를 검색하여 사용자가 관리하는 Facebook 페이지에 동일한 게시물을 게시합니다.
 
	![][11]

**팁** - Facebook 페이지 ID 또는 Yammer 그룹 ID를 가져오려면 URL에서 숫자 코드를 찾습니다.

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!--HONumber=54--> 