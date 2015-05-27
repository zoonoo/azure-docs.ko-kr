<properties 
	pageTitle="Microsoft Azure API 앱 소셜 커넥터 | API 앱 마이크로 서비스" 
	description="Microsoft Azure 소셜 커넥터 API 앱을 만들고 API 앱을 논리 앱에 추가하는 방법을 알아봅니다. 마이크로 서비스" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Microsoft Azure 앱 서비스의 소셜 커넥터


## 소셜 API 앱 커넥터 정의
소셜 커넥터는 인증된 사용자를 대신하여 소셜 응용 프로그램에 연결하고 여러 가지 *동작*을 수행할 수 있는 "API 앱"입니다. 대부분의 커넥터는 *트리거*로 구성할 수도 있습니다. 트리거는 일부 커넥터에서 구성할 수 있는 이벤트(.NET Framework의 이벤트와 유사)입니다. 트리거는 해당 커넥터(또는 다른 모든 커넥터)에 대해 구성된 작업을 시작할 수 있습니다. 예를 들어 Twitter 커넥터 인스턴스를 *새 메시지* 트리거로 구성할 수 있으며, 여기서 트리거는 @VisualStudio를 멘션하는 새 트윗으로 정의됩니다. 그런 다음 트윗을 리트윗하는 *동작*을 시작하도록 이 트리거를 구성할 수 있습니다.

Microsoft에서 제공되는 소셜 커넥터는 다음과 같습니다.

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio

다음은 각 소셜 커넥터에 대한 간단한 설명입니다.

<table>
<tr>
<th> 이름</th>
<th> 설명</th>
<th> 트리거</th>
<th>동작</th>

<tr>
<td>Facebook
<td>Facebook 커넥터를 통해 Facebook 피드에서 메시지, 의견, 이벤트 및 상태를 게시하고 읽을 수 있습니다. 동영상과 사진을 게시할 수도 있습니다.
<td>새 게시물
<td><li>게시물 게시
	<li>사진 게시 
</tr>

<tr>
<td>Twitter
<td>Twitter 커넥터를 통해 게시, 트윗 받기 및 트윗 검색과 같은 여러 가지 작업을 수행할 수 있습니다. Twitter 커넥터를 사용하여 Twitter의 현재 상황을 확인하고 트윗, 타임라인, 친구, 팔로워 등의 세부 정보를 가져올 수 있습니다.
<td>새 트윗
<td><li>트윗 검색
	<li>트윗
	<li>사용자 타임라인 가져오기
	<li>리트윗
</tr>

<tr>
<td>Chatter
<td>Chatter 커넥터를 통해 Chatter 피드의 메시지를 읽고 게시 및 검색할 수 있습니다.
<td>새 메시지
<td><li>메시지 게시
<li>메시지 검색
</tr>

<tr>
<td>Yammer
<td>Yammer 커넥터를 통해 Yammer 그룹의 메시지를 게시하고 읽을 수 있습니다.
<td>새 메시지
<td><li>메시지 게시	
	<li>그룹\피드에서 메시지 가져오기	
</tr>

<tr>
<td>Twilio
<td>Twilio 커넥터를 통해 Twilio 계정에서 SMS를 보내고 받을 수 있습니다. 전화 번호와 사용 데이터를 검색할 수도 있습니다.
<td>해당 없음
<td><li>메시지: 보내기, 나열, 가져오기 및 검색
	<li>전화번호: 구입, 나열, 로컬 및 무료 번호 가져오기
</tr>
</table>


소셜 커넥터의 기능을 파악했으므로 이제 이러한 커넥터의 몇 가지 간단한 사용 사례를 살펴보겠습니다.

## 커넥터를 사용하는 이유는 무엇인가요?

커넥터를 사용하면 앱 개발 속도가 향상되며 비개발자도 프로그래밍 언어를 배우거나 코드를 작성하지 않고 완전한 기능을 갖춘 엔터프라이즈급 응용 프로그램을 만들 수 있습니다.

### 고객 피드백 모니터링 ###
회사에서 최근에 새 앱을 릴리스했으며 팀이 소셜 미디어에서 앱에 대한 고객 피드백을 알고 싶어한다고 가정해 보세요. 각 팀 멤버가 주기적으로 다양한 소셜 미디어 사이트를 확인하고 고객이 앱 논의에 사용할 수 있는 키워드를 추측할 수도 있습니다. 그러나 간단히 Twitter 커넥터를 만들고 Twitter에서 특정 해시태그, 멘션 및 키워드를 모니터링하도록 구성하는 것이 더 효율적입니다. 그런 다음 SMTP 커넥터를 사용하여 일치하는 트윗의 내용이 포함된 메일을 팀 멤버에게 보낼 수 있습니다. Facebook 및 Yammer를 모니터링하고, Facebook 또는 Yammer 게시물의 내용이 고객에게 심각한 문제가 발생했을 수 있음을 나타낼 경우 Twilio 커넥터를 통해 DevOps 팀에게 SMS를 보내는 방법으로 유사한 작업을 수행할 수도 있습니다. 이 모든 작업을 한 줄의 코드도 작성하지 않고 수행할 수 있습니다.

이제 시작하겠습니다.


## 커넥터 만들기
Azure 포털에서 커넥터를 만들 수 있습니다.

### Microsoft Azure 포털에서 소셜 커넥터를 만듭니다.

1. Azure 포털에서 **새로 만들기** > **웹 + 모바일** > **Azure 마켓플레이스**를 선택합니다.
2. 커넥터를 **검색**하거나 목록에서 선택합니다. 선택하면 새 블레이드나 창이 열립니다. **만들기**를 선택합니다. 
3. 커넥터에 대해 다음과 같은 속성을 입력합니다. <table> <tr><th>속성</th> <th>설명</th> </tr> <tr><td>이름</td> <td>API 앱의 이름을 임의로 입력합니다. 예를 들어 이름을 RulesDiscountTaxCode 또는 APIAppValidateXML로 지정할 수 있습니다.</td> </tr> <tr><td>앱 서비스 계획</td> <td>요금제를 나열합니다. 필요한 리소스가 더 많거나 적은 경우 변경할 수 있습니다.</th> </td> <tr><td>가격 책정 계층</td> <td>Azure 구독 내에서 가격 책정 범주를 나열하는 읽기 전용 속성입니다.</td> </tr> <tr><td>리소스 그룹</td> <td>새 그룹을 만들거나 기존 그룹을 사용합니다. 이 속성은 리소스 그룹 사용에서 설명합니다.</td> </tr> <tr><td>구독</td> <td>현재 구독을 나열하는 읽기 전용 속성입니다.</td> </tr> <tr><td>위치</td> <td>Azure 서비스를 호스트하는 지리적 위치입니다. </td></tr> <tr><td>시작 보드에 추가</td> <td>시작 보드(홈페이지)에 API 앱을 추가하려면 이 속성을 선택합니다.</td></tr> </table> 
4. **만들기**를 선택합니다. 커넥터가 만들어집니다. 완료되는 데 시간이 걸릴 수 있으며, 커넥터를 만드는 동안 홈 화면이 표시됩니다. 왼쪽의 알림 메뉴 항목을 사용하여 커넥터 상태를 모니터링합니다.

첫 번째 커넥터를 만들었으므로 이제 이 커넥터를 사용한 웹앱, 모바일 앱 또는 논리 앱 빌드를 고려해 보세요.


### REST API를 사용하여 커넥터 액세스

[REST API를 사용하여 커넥터 액세스](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## 응용 프로그램에 커넥터 추가 
Microsoft Azure 앱 서비스는 이러한 커넥터를 사용할 수 있는 다양한 응용 프로그램 종류를 표시합니다. 예를 들어 하나 이상의 커넥터를 단일 앱에 *논리적으로* 결합하여 *논리* 앱을 만들 수 있습니다.

*논리* 앱 내에서 커넥터를 사용하려는 경우 목록에서 미리 구성된 커넥터를 선택하고 디자인 워크플로에 추가한 다음 필요에 따라 구성을 변경하면 사용할 준비가 완료됩니다.

다음 단계를 따르려면 웹앱, 모바일 앱 또는 논리 앱이 필요합니다. 특정 단계는 <>을(를) 참조하세요. 응용 프로그램을 사용할 수 있게 되면 커넥터를 추가합니다. 방법은 다음과 같습니다.

다음 단계에 따라 논리 앱에 커넥터를 추가합니다.

1. Azure 포털 시작 보드(홈페이지)에서 **마켓플레이스**로 이동하여 논리 앱, 모바일 앱 또는 웹앱을 검색합니다. 

	새 앱을 만드는 경우 논리 앱, 모바일 앱 또는 웹앱을 검색합니다. 앱을 선택하고 새 블레이드에서 **만들기**를 선택합니다. [논리 앱 만들기](app-service-logic-create-a-logic-app.md)에 단계가 나열되어 있습니다.

2. 앱을 열고 **트리거 및 동작**을 선택합니다.
3. **갤러리**에서 커넥터를 선택합니다. 그러면 앱에 추가됩니다.
4. 커넥터를 구성합니다.
5. 모든 커넥터에는 연결되는 환경과 서비스에 고유한 속성이 있습니다. 속성의 세부 정보를 입력합니다. 일부 속성은 선택 사항입니다.
6. **확인**을 선택하여 변경 내용을 저장합니다.


## 보안
커넥터는 OAuth 또는 사용자 이름 및 암호를 사용합니다.

## 논리 앱 및 웹앱 알아보기
[논리 앱 정의](app-service-logic-what-are-logic-apps.md) | [Azure 앱 서비스의 웹 사이트 및 웹앱](app-service-web-app-azure-portal.md) |


## 추가 커넥터

[BizTalk 통합 커넥터](app-service-logic-integration-connectors.md) | [엔터프라이즈 커넥터](app-service-logic-enterprise-connectors.md) | [B2B 커넥터](app-service-logic-b2b-connectors.md) | [프로토콜 커넥터](app-service-logic-protocol-connectors.md) | [앱 + 데이터 서비스 커넥터](app-service-logic-data-connectors.md) | [커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)<br/><br/> [커넥터 및 BizTalk API 앱 정의](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->