<properties 
	pageTitle="Microsoft Azure API 앱 프로토콜 커넥터 | API 앱 마이크로 서비스" 
	description="Microsoft Azure 프로토콜 커넥터 API 앱을 만들고 API 앱을 논리 앱에 추가하는 방법을 알아봅니다. 마이크로 서비스" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="Deonhe" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/> 


# Microsoft Azure 앱 서비스의 프로토콜 커넥터


## 프로토콜 커넥터 정의
프로토콜 커넥터는 HTTP, SMTP, FTP 등 다양한 프로토콜을 사용하여 통신하는 앱을 만드는 데 사용할 수 있는 API 앱*입니다. 

Microsoft Azure 포털에서 **새로 만들기** > **웹 + 모바일** > **Azure 마켓플레이스**를 클릭하고 > 검색 키워드 **프로토콜**을 입력한 다음 > Enter 키를 누르면 프로토콜 커넥터를 확인할 수 있습니다.

Auzre 마켓플레이스에서 사용할 수 있는 프로토콜 커넥터는 다음과 같습니다.

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- 파일

다음은 각 프로토콜 커넥터에 대한 간단한 설명입니다. 

<table>
<tr>
<th> 이름</th>
<th> 설명</th>
<th> 트리거</th>
<th> 작업</th>

<tr>
<td>SFTP
<td>SFTP 커넥터를 통해 SFTP 서버에서 파일을 다운로드하거나 업로드할 수 있습니다.
<td>SFTP 폴더에서 새 파일을 사용할 수 있음

<td>파일: 업로드, 삭제, 나열, 가져오기 

</tr>

<tr>
<td>POP3
<td>POP3 커넥터를 통해 POP3 서버에서 메일을 다운로드할 수 있습니다.
<td>새 메시지 도착
<td>해당 없음
</tr>

<tr>
<td>FTP
<td>FTP 커넥터를 통해 FTP 서버에서 파일을 다운로드하거나 업로드할 수 있습니다.
<td>FTP 폴더에서 새 파일을 사용할 수 있음
<td>파일: 업로드, 가져오기, 삭제, 나열
</tr>

<tr>
<td>HTTP
<td>HTTP 커넥터를 통해 HTTP 또는 HTTPS 프로토콜을 사용하여 데이터를 HTTP 서버로 전송할 수 있습니다.
<td>없음
<td>게시, 가져오기, 삭제, put
</tr>

<tr>
<td>SMTP
<td>SMTP 커넥터를 통해 SMTP 서버에서 메일을 보낼 수 있습니다.
<td>해당 없음
<td>메일 보내기
</tr>

<tr>
<td>파일
<td>파일 커넥터를 통해 온-프레미스 파일 서버에 연결하고 파일 업로드, 가져오기, 삭제 및 나열과 같은 작업을 수행할 수 있습니다. 파일을 검색하는 트리거도 있습니다.
<td>새 파일이 장치에 추가됨
<td>파일: 업로드, 가져오기, 삭제, 나열
</tr>


</table>

프로토콜 커넥터의 기능을 파악했으므로 이제 이러한 커넥터의 몇 가지 간단한 사용 사례를 살펴보겠습니다.

### 고객 피드백 모니터링 ###
회사에서 최근에 새 앱을 릴리스했으며 팀이 소셜 미디어에서 앱에 대한 고객 피드백을 알고 싶어한다고 가정해 보세요. 각 팀 멤버가 주기적으로 다양한 소셜 미디어 사이트를 확인하고 고객이 앱 논의에 사용할 수 있는 키워드를 추측할 수도 있습니다. 그러나 간단히 Twitter 커넥터를 만들고 Twitter에서 특정 해시태그, 멘션 및 키워드를 모니터링하도록 구성하는 것이 더 효율적입니다. 그런 다음 SMTP 커넥터를 사용하여 일치하는 트윗의 내용이 포함된 메일을 만들고 팀 멤버에게 보낼 수 있습니다. 또한 HTTP 커넥터를 사용하여 이러한 트윗을 내부 회사 웹 사이트에 게시할 수 있습니다. 이 모든 작업을 한 줄의 코드도 작성하지 않고 수행할 수 있습니다.  

이제 시작하겠습니다. 

## 커넥터 만들기

Azure 포털이나 REST API를 사용하여 커넥터를 만들 수 있습니다. [REST API를 사용하여 커넥터 만들기](http://go.microsoft.com/fwlink/p/?LinkId=529766)

### Azure 포털에서 커넥터 만들기

Azure 마켓플레이스를 사용하여 SMTP 커넥터를 만드는 과정을 살펴보겠습니다.

1. Microsoft Azure [포털](https://manage.windowsazure.com)에 로그인합니다.
2. **새로 만들기** > **웹 + 모바일** > **Azure 마켓플레이스**를 선택합니다.
3. **검색 상자**에 **프로토콜**을 입력하고 **Enter** 키를 누릅니다. 목록에서 선택할 수도 있습니다. 선택하면 새 블레이드나 창이 열립니다. **만들기**를 선택합니다. 
4. 다음과 같은 속성을 입력합니다.

<table>
<tr><th>속성</th> <th>설명</th> </tr>
<tr><td>이름</td> <td>설명</td> </tr>
<tr><td>패키지 설정</td> <td>패키지 설정 속성을 사용하여 SMTP 서버에 대한 사용자 이름, 암호, 포트 번호, 서버 주소 등의 인증 정보를 모두 입력합니다. </td> </tr>
<tr><td>앱 서비스 계획</td> <td>요금제를 나열합니다. 필요한 리소스가 더 많거나 적은 경우 변경할 수 있습니다.</th> </td>
<tr><td>가격 책정 계층</td> <td>Azure 구독 내에서 가격 책정 범주를 나열하는 읽기 전용 속성입니다.</td> </tr>
<tr><td>리소스 그룹</td> <td>새 그룹을 만들거나 기존 그룹을 사용합니다. 이 속성은 리소스 그룹 사용에서 설명</td> </tr>
<tr><td>구독</td> <td>현재 구독을 나열하는 읽기 전용 속성</td> </tr>
<tr><td>위치</td> <td>Azure 서비스를 호스트하는 지리적 위치</th> </td>
<tr><td>시작 보드에 추가</td> <td>시작 보드(홈페이지)에 API 앱을 추가하려면 이 속성을 선택합니다.</td></tr>
</table> 

### REST API를 사용하여 커넥터 만들기
[REST API를 사용하여 커넥터 만들기](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## 응용 프로그램에 커넥터 추가 
Microsoft Azure 앱 서비스는 이러한 커넥터를 사용할 수 있는 다양한 응용 프로그램 종류를 표시합니다. 예를 들어 하나 이상의 커넥터를 *logically* 단일 앱에 결합하여 *Logic* 앱을 만들 수 있습니다.

*Logic*  앱 내에서 커넥터를 사용하려는 경우 목록에서 미리 구성된 커넥터를 선택하고 디자인 워크플로에 추가한 다음 필요에 따라 구성을 변경하면 사용할 준비가 완료됩니다. 

다음 단계를 따르려면 웹앱, 모바일 앱 또는 논리 앱이 필요합니다. 특정 단계는 <>을(를) 참조하세요. 응용 프로그램을 사용할 수 있게 되면 커넥터를 추가합니다. 방법은 다음과 같습니다.

다음 단계에 따라 논리 앱에 커넥터를 추가합니다. 

1. Azure 포털 시작 보드(홈페이지)에서 **마켓플레이스**로 이동하여 논리, 모바일 또는 웹앱을 검색합니다. 

	새 앱을 만드는 경우 논리 앱, 모바일 앱 또는 웹앱을 검색합니다. 앱을 선택하고 새 블레이드에서 **만들기**를 선택합니다. [논리 앱 만들기](app-service-logic-create-a-logic-app.md)에 단계가 나열되어 있습니다. 

2. 앱을 열고 **트리거 및 동작**을 선택합니다. 
3. **갤러리**에서 커넥터를 선택합니다. 그러면 앱에 추가됩니다.
4. 커넥터를 구성합니다.
5. 모든 커넥터에는 연결되는 환경과 서비스에 고유한 속성이 있습니다. 속성의 세부 정보를 입력합니다. 일부 속성은 선택 사항입니다.
6. **확인**을 선택하여 변경 내용을 저장합니다.


## 보안
커넥터는 OAuth 또는 사용자 이름 및 암호를 사용합니다.

## 논리 앱 및 웹앱 알아보기
[논리 앱이란 무엇인가요?](app-service-logic-what-are-logic-apps.md) |
[Azure 앱 서비스의 웹 사이트 및 웹앱](app-service-web-app-azure-portal.md) |


## 추가 커넥터

[BizTalk 통합 커넥터](app-service-logic-integration-connectors.md) |
[엔터프라이즈 커넥터](app-service-logic-enterprise-connectors.md) |
[B2B 커넥터](app-service-logic-b2b-connectors.md) |
[소셜 커넥터](app-service-logic-social-connectors.md) |
[앱 및 데이터 서비스 커넥터](app-service-logic-data-connectors.md) |
[커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)

<!--HONumber=49-->