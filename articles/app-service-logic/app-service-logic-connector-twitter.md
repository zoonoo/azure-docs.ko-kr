<properties
   pageTitle="Microsoft Azure 앱 서비스에서 Twitter 커넥터 사용"
   description="Twitter 커넥터 API 앱 사용 방법"
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
   ms.date="07/14/2015"
   ms.author="sameerch"/>


# Twitter 커넥터

Twitter 피드에 연결하여 트윗을 게시하고 본인의 타임라인, 친구의 타임라인, Twitter 계정의 팔로워로부터 투윗을 가져올 수 있습니다. 커넥터는 논리 앱에 사용하여 "워크플로"의 일부로 데이터 검색하기, 처리 또는 푸시를 수행할 수 있습니다. 워크플로에서 Twitter 커넥터를 활용하면 다양한 시나리오를 수행할 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- 지정된 키워드 또는 텍스트와 연결된 새 트윗을 가져옵니다. 새 트윗이 검색되면 워크플로의 새 인스턴스를 트리거하고 워크플로의 다음 커넥터로 데이터를 전달합니다. 예를 들어 Twitter 커넥터를 만들고 New Tweet From Search(새 트윗 검색) 트리거를 사용하여 #peanutbutterandjelly를 모니터링할 수 있습니다. #peanutbutterandjelly에 대한 새 트윗이 있을 때마다 워크플로(즉, 논리 앱)가 자동으로 시작됩니다.
- "트윗 검색" 등의 다른 동작을 사용하여 응답을 얻은 후 워크플로 내에서 사용할 수 있습니다. 예를 들어 트윗에서 회사 이름을 검색할 수 있습니다. 회사 이름이 발견되면 논리 앱을 사용하여 SQL Server 데이터베이스에 이 데이터를 쓸 수 있습니다. 그런 후 SQL Server 데이터를 사용하여 회사에 대해 트윗되는 내용을 확인할 수 있습니다. 


## 트리거 및 작업
*트리거*는 발생하는 이벤트입니다. 예를 들어 새 트윗은 워크플로 또는 프로세스를 트리거하거나 시작할 수 있습니다. *동작*은 어떤 것의 결과입니다. 예를 들어 특정 트윗을 검색한 후 결과를 찾으면 전자 메일을 보내거나 데이터베이스를 업데이트할 수 있습니다.

Twitter 데이터베이스 커넥터는 논리 앱에서 트리거나 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다.

Twitter 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
New Tweet From Search(새 트윗 검색) | <ul><li>사용자 타임라인 가져오기</li><li>트윗 검색</li><li>트윗</li><li>멘션 타임라인 가져오기</li><li>홈 타임라인 가져오기</li><li>팔로워 가져오기</li><li>친구 가져오기</li><li>사용자 세부 정보 가져오기</li><li>사용자에 트윗</li><li>직접 메시지 보내기</li></ul>

> [AZURE.IMPORTANT]**새 트윗** 트리거는 보관되었습니다. 현재, 계속 고급 작업으로 사용할 수 있습니다. **리트윗** 동작은 제거되었으며 더 이상 지원되지 않습니다. 리트윗 동작을 사용하면 런타임에 실패합니다. 따라서 논리 앱에서 리트윗 동작을 제거하세요.


## Twitter 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "Twitter 커넥터"를 검색합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.

	![][1]
4.	**만들기**를 클릭합니다.


## 논리 앱에서 Twitter 커넥터 사용
API 앱이 생성되면 이제 논리 앱에 대한 트리거 또는 동작으로 Twitter 커넥터를 사용할 수 있습니다. 다음을 수행합니다.

1.	새 논리 앱을 만들거나 기존 논리 앱을 엽니다.

	![][2]
2.	**트리거 및 동작**을 열어 논리 앱 디자이너를 엽니다.

	![][3]
3.	Twitter 커넥터가 오른쪽에 표시됩니다. 이를 선택하면 논리 앱에 자동으로 추가됩니다.

	![][4]
4.	**인증**을 선택하고 Twitter 자격 증명을 입력한 후 **앱 증인**을 선택합니다.

	![][5]


이제 워크플로를 빌드하도록 Twitter 커넥터를 구성할 수 있습니다. 흐름의 다른 작업에서 Twitter 트리거에서 검색된 트윗을 사용할 수 있습니다.

![][6]

유사한 방식으로 워크플로에서 Twitter 동작을 사용할 수 있습니다. Twitter 동작을 선택하고 해당 동작에 대한 입력을 구성합니다.

![][7] ![][8]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

REST API를 사용하여 API 앱을 만듭니다. [커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)를 참조하세요.

커넥터의 성능 통계 및 제어 보안을 검토할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png

<!---HONumber=July15_HO4-->