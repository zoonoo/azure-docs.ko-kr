<properties 
	pageTitle="Microsoft Azure API 앱 데이터 커넥터 | API 앱 마이크로 서비스" 
	description="Microsoft Azure 데이터 커넥터 API 앱을 만들고 논리 앱에 API 앱을 추가하는 방법 알아보기, 마이크로 서비스" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Microsoft Azure 앱 서비스의 데이터 커넥터


## 앱 + 데이터 서비스 API 앱 커넥터 정의
앱 및 데이터 서비스 커넥터는 인증된 사용자를 대신하여 다양한 데이터 서비스 응용 프로그램에 연결하고 여러 가지 *동작*을 수행할 수 있는 "API 앱"입니다. 이러한 커넥터는 대부분 *트리거*로 구성할 수도 있습니다. 트리거는 일부 커넥터에서 워크플로를 시작하도록 구성할 수 있는 이벤트(.NET Framework의 이벤트와 유사)입니다.

예를 들어 트리거를 실행하면 언제든지 모니터링 중인 Dropbox 계정에 새 파일이 추가되는 *새 파일* 트리거로 Dropbox 커넥터의 인스턴스를 구성할 수 있습니다. 그런 다음 파일을 *가져와* 온-프레미스 SharePoint 목록에 업로드하는 *동작*을 시작하도록 이 트리거를 구성할 수 있습니다.


다음은 Azure 갤러리에서 사용할 수 있는 각 앱 및 데이터 서비스 커넥터에 대한 간략한 개요입니다.

<table>
<tr>
<th> 이름</th>
<th> 설명</th>
<th> 트리거</th>
<th>동작</th>

<tr>
<td>Azure 미디어 서비스
<td>Azure 미디어 서비스 커넥터를 사용하면 유연하고 확장 가능한 인코딩, 패키징 및 배포가 포함된 종단 간 미디어 워크플로를 만들 수 있습니다. 또한 다양한 TV, PC 및 모바일 장치 끝점으로의 주문형 제공과 라이브 방식의 스트리밍 제공을 위해 동영상 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 스트리밍할 수 있습니다.
<td>없음
<td>없음 
</tr>

<tr>
<td>Azure 서비스 버스
<td>Azure 서비스 버스 커넥터를 사용하면 큐 및 항목과 같은 서비스 버스 엔터티에서 메시지를 보낼 수 있으며 큐 및 구독과 같은 서비스 버스 엔터티에서 메시지를 받을 수 있습니다.
<td>새 메시지
<td>메시지 보내기
</tr>
<tr>
<td>Azure 저장소 테이블
<td>Azure 저장소 테이블 커넥터를 사용하면 Azure 저장소 테이블에 연결하고 엔터티 가져오기, 엔터티 쿼리, 엔터티 삽입, 엔터티 업데이트, 엔터티 삭제와 같은 다양한 작업을 수행하고 데이터를 검색하는 트리거를 시작할 수 있습니다.
<td>새 엔터티
<td><li>엔터티 가져오기
	<li>엔터티 삽입
	<li>엔터티 삭제
	<li>엔터티 업데이트
	<li>쿼리

</tr>

<tr>
<td>Box
<td>Box 커넥터를 사용하면 Box에 연결하고 고유한 파일에 대해 다양한 작업을 수행할 수 있습니다. 
<td>새 파일 추가됨
<td><li>파일 업로드
<li>파일 가져오기
<li>파일 삭제
<li>파일 나열
</tr>

<tr>
<td>DB2
<td>DB2 커넥터를 사용하면 Windows 운영 체제를 실행하는 온-프레미스 및 Azure 가상 컴퓨터에서 IBM DB2 데이터베이스에 연결할 수 있습니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다. 온-프레미스를 사용하는 경우 VPN 또는 Azure Express 경로를 사용할 수 있습니다. 이 커넥터에는 TCP/IP 네트워크를 통해 Informix 서버에 연결하는 DRDA용 Microsoft 클라이언트도 포함되어 있습니다.
<td>없음
<td><li>테이블 선택
<li>삽입
<li>업데이트
<li>삭제
<li>사용자 지정 문
</tr>

<tr>
<td>Dropbox
<td>Dropbox 커넥터를 사용하면 Dropbox에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열과 같은 다양한 작업을 수행하고 파일을 검색하는 트리거를 시작할 수 있습니다.
<td>새 파일 추가됨
<td><li>파일 업로드
<li>파일 가져오기
<li>파일 삭제
<li>파일 나열
</tr>

<tr>
<td>HDInsight
<td>HDInsight 커넥터를 사용하면 Azure에서 Hadoop 클러스터를 생성하고 Hive, Pig, MapReduce 및 스트리밍 MapReduce와 같은 다양한 Hadoop 작업을 제출할 수 있습니다. 이 커넥터를 사용하여 클러스터를 스핀하고 작업을 제출하고 작업이 완료되기를 기다릴 수 있습니다.
<td>없음
<td><li>클러스터 만들기
		<li>클러스터 생성 대기
		<li>Pig 작업 제출
		<li>Hive 작업 제출
		<li>MapReduce 작업 제출
		<li>작업 완료 대기
		<li>클러스터 삭제
<li>MapReduce 작업 제출
<li>스트리밍 MapReduce 작업 제출
</tr>

<tr>
<td>Informix
<td>Informix 커넥터는 Windows 운영 체제를 실행하는 온-프레미스 및 Azure 가상 컴퓨터에서 온-프레미스 IBM Informix 데이터베이스에 연결합니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다. 온-프레미스를 사용하는 경우 VPN 또는 Azure Express 경로를 사용할 수 있습니다. 이 커넥터에는 TCP/IP 네트워크를 통해 Informix 서버에 연결하는 DRDA용 Microsoft 클라이언트도 포함되어 있습니다.
<td>없음
<td><li>테이블 선택
<li>삽입
<li>업데이트
<li>삭제
<li>사용자 지정 문
</tr>

<tr>
<td>Microsoft SQL
<td>Microsoft SQL 커넥터를 사용하면 Microsoft SQL Server 및 Azure SQL 데이터베이스 테이블의 항목을 생성 및 수정할 수 있습니다.
<td>데이터 폴링
<td><li>테이블에 삽입
	<li>테이블 업데이트
	<li>테이블에서 선택
	<li>테이블에서 삭제
	<li>저장 프로시저 호출
</tr>

<tr>
<td>MongoDB
<td>MongoDB 커넥터를 사용하면 MongoDB 컬렉션에서 문서 생성, 업데이트, 삭제 및 가져오기 작업을 수행할 수 있습니다.
<td>새 문서
<td>	<li>문서 추가
		<li>문서 업데이트
		<li>문서 가져오기
		<li>문서 삽입 또는 업데이트
		<li>문서 삭제
</tr>


<tr>
<td>MQ
<td>MQ 커넥터는 Windows 운영 체제를 실행하는 온-프레미스 및 Azure 가상 컴퓨터에서 IBM WebSphere MQ Server 버전 8에 연결합니다. 온-프레미스를 사용하는 경우 VPN 또는 Azure Express 경로를 사용할 수 있습니다. 커넥터에는 MQ용 Microsoft 클라이언트도 포함되어 있습니다.<br/><br/><strong>참고</strong> 현재 논리 앱과 함께 사용할 수 없습니다.
<td>없음
<td>없음
</tr>

<tr>
<td>Office 365
<td>Office 365 커넥터를 사용하면 메일을 보내고 받을 수 있으며, 일정 및 연락처를 관리할 수 있습니다.
<td>새 메시지
<td>	<li>메일 보내기
		<li>메일에 회신
		<li>이벤트 보내기
		<li>연락처 추가
</tr>

<tr>
<td>OneDrive
<td>OneDrive 커넥터를 사용하면 개인 Microsoft OneDrive 저장소 계정에 연결하고 파일의 업로드, 가져오기, 삭제, 나열과 같은 다양한 작업을 수행할 수 있습니다.
<td>새 파일
<td><li>파일: 업로드, 삭제, 나열, 다운로드
</tr>

<tr>
<td>Oracle
<td>Oracle 데이터베이스 커넥터를 사용하면 Oracle 데이터베이스 테이블의 항목을 생성하고 수정할 수 있습니다.
<td>쿼리에 따른 새 데이터
<td><li>테이블: 삽입, 업데이트, 선택, 삭제
<li>저장 프로시저 호출
</tr>

<tr>
<td>SharePoint Online
<td>Microsoft SharePoint 커넥터를 사용하면 SharePoint Server 또는 Microsoft SharePoint Online에서 문서를 생성 및 수정하고 항목을 나열할 수 있습니다.
<td><li>새 문서
<li>새 목록 항목
<td><li>문서 라이브러리: 업로드, 삭제 및 가져오기
<li>목록: 항목 삽입

</tr>

<tr>
<td>SharePoint Server
<td>SharePoint Server 커넥터를 사용하면 문서를 관리하고 SharePoint Server의 항목을 나열할 수 있습니다. 기본 자격 증명, Windows 인증 및 양식 기반 인증이 지원됩니다. 서버에 연결하려면 먼저 서비스 버스 연결 문자열을 제공하고 온-프레미스 수신기 에이전트를 설치해야 합니다.
<td><li>새 문서
<li>새 목록 항목
<td><li>문서 라이브러리: 업로드, 삭제 및 가져오기
<li>목록: 항목 삽입
</tr>
</table>


## 커넥터를 사용하는 이유는 무엇인가요?

커넥터를 사용하면 앱 개발 속도가 향상되며 비개발자도 프로그래밍 언어를 배우거나 코드를 작성하지 않고 완전한 기능을 갖춘 엔터프라이즈급 응용 프로그램을 만들 수 있습니다.

앱 + 데이터 서비스 커넥터의 기능에 대해 알아보았으므로 이제 이러한 커넥터의 간단한 사용 사례를 몇 가지 살펴보겠습니다.

### Dropbox 모니터링 및 SharePoint 업데이트
회사가 청사진을 포함하는 대용량 파일을 수신하는 건설 회사라고 가정합니다. 일반적으로 이러한 파일은 메일을 통해 처리하기에는 너무 커서 회사에서 Dropbox 계정을 설정하고 해당 고객에게 Dropbox에 청사진을 넣도록 요청했습니다. 그런 다음 각 직원에게 Dropbox 폴더에 새 청사진이 올라왔는지 지속적으로 확인한 후 해당 청사진을 SharePoint 서버에 업로드하도록 요청하였지만, 더 나은 방법이 있을 것으로 확신하고 있습니다! 다행히도 Microsoft가 최근에 Dropbox, Sharepoint 및 기타 데이터 서비스용 앱 + 데이터 서비스 커넥터를 릴리스한 사실을 알게 되었습니다. Dropbox 및 SharePoint용 커넥터를 손쉽게 생성하여 논리 앱에 추가하고 새로운 각 파일을 Dropbox 계정에서 SharePoint 목록으로 업로드하도록 해당 커넥터를 구성할 수 있습니다. Dropbox 커넥터에 *새 메시지* 트리거가 있으므로 해당 트리거를 사용하면 사용 가능한 새 파일이 있다는 것을 논리 앱에 알릴 수 있습니다. 그러면 Dropbox 커넥터가 파일을 다운로드할 수 있습니다. 그런 다음 SharePoint *업로드* 동작을 사용하여 SharePoint 목록에 파일을 업로드하도록 SharePoint 커넥터를 구성할 수 있습니다. 이 모든 작업을 한 줄의 코드도 작성하지 않고 수행할 수 있습니다.

이제 시작하겠습니다.

## 커넥터 만들기
Azure 포털을 사용하여 커넥터 API 앱을 만들 수 있습니다.

### Microsoft Azure 포털에서 SharePoint 커넥터 만들기

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
[논리 앱 정의](app-service-logic-what-are-logic-apps.md) | [Azure 앱 서비스의 웹 사이트 및 웹앱](../app-service-web/app-service-web-app-azure-portal.md) |



## 추가 커넥터
[BizTalk 통합 커넥터](app-service-logic-integration-connectors.md) | [엔터프라이즈 커넥터](app-service-logic-enterprise-connectors.md) | [B2B 커넥터](app-service-logic-b2b-connectors.md) | [소셜 커넥터](app-service-logic-social-connectors.md) | [프로토콜 커넥터](app-service-logic-protocol-connectors.md) | [앱 및 데이터 서비스 커넥터](app-service-logic-data-connectors.md) | [커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)<br/><br/> [커넥터 및 BizTalk API 앱 정의](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54--> 