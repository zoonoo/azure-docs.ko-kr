<properties 
   pageTitle="Twitter 커넥터 API 앱" 
   description="TwitterConnector를 사용하는 방법" 
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


# 논리 앱에서 Twitter 커넥터 사용 #

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

Twitter 커넥터를 사용하여 타임라인에서 트윗을 게시하고 Twitter 계정에서 프로그램, 친구 및 팔로워를 가져올 수 있습니다.

- Twitter 커넥터 트리거는 지정된 키워드에 연결된 새 트윗을 검색합니다. 새로운 트윗이 검색되면 흐름의 새 인스턴스를 트리거하고 요청에서 수신된 데이터를 처리하기 위해 흐름에 전달합니다. 
- Twitter 커넥터 작업을 사용하여 "트윗", "트윗 검색", "리트윗", "사용자 타임라인 가져오기" 등의 작업을 수행할 수 있습니다. 이러한 동작은 응답을 다시 가져오고 흐름의 동작에서 이를 사용할 수 있도록 합니다.

## 논리 앱용 Twitter 커넥터 만들기 ##
Twitter 커넥터를 사용하려면 먼저 Twitter 커넥터 API 앱의 인스턴스를 만듭니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1. Azure 포털의 왼쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
1. "API 앱"으로 이동하여 "Twitter 커넥터"를 검색합니다.
1. 다음과 같이 Twitter 커넥터를 구성합니다.

	![][1]
4.	확인을 클릭하여 만듭니다.
5.	API 앱 인스턴스가 생성되면 동일한 리소스 그룹에서 논리 앱을 만들어 Twitter 커넥터를 사용할 수 있습니다. 
	- 또한 논리 앱에서 Twitter 커넥터 API 앱 인스턴스를 만들 수도 있습니다. 
	- 논리 앱 편집기를 열고 오른쪽의 갤러리에서 사용할 수 있는 Twitter 커넥터를 클릭합니다.
	- 그러면 논리 앱이 생성된 것과 동일한 리소스 그룹에 Twitter 커넥터 API 앱 인스턴스가 생성됩니다.


## 논리 앱에서 Twitter 커넥터 사용 ##
API 앱이 생성되면 이제 논리 앱에 대한 트리거/동작으로 Twitter 커넥터를 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 Twitter 커넥터가 있는 동일한 리소스 그룹을 선택합니다.
 	
	![][2]
2.	"트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. 
 	
	![][3]
3.	Twitter 커넥터가 오른쪽의 갤러리에서 "최근에 사용된 항목" 섹션에 나타납니다. 해당 커넥터를 선택합니다.
 
	![][4]
4.	오른쪽의 갤러리에서 "최근에 사용된 항목" 아래에 있는 "Twitter 커넥터"를 클릭하여 편집기에 Twitter 커넥터 API 앱을 놓을 수 있습니다. 권한 부여 단추를 클릭합니다. Twitter 자격 증명을 제공합니다. "앱 인증"을 클릭합니다.
 
	![][5]
6.	이제 흐름에서 Twitter 커넥터를 사용할 수 있습니다. 이제 흐름의 다른 작업에서 Twitter 트리거에서 검색된 트윗을 사용할 수 있습니다.
 
	![][6]
7.	유사한 방식으로 흐름에서 Twitter 작업을 사용할 수 있습니다. Twitter 작업을 선택하고 각 작업에 대한 입력을 구성합니다.

	![][7]
	![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png
 

<!----HONumber=62-->