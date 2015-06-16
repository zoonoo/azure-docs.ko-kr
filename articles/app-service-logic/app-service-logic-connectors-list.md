<properties 
	pageTitle="커넥터 및 API 앱의 목록 | Azure" 
	description="Azure 앱 서비스의 커넥터 및 API 앱 알아보기, 마이크로 서비스 아키텍처" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Microsoft Azure 앱 서비스의 커넥터 및 API 앱 목록
이 항목에는 Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

가격 정보 및 각 서비스 계층에 포함된 항목의 목록은 [Azure 앱 서비스 가격](http://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.


## 표준 커넥터
다음 표에는 표준 커넥터에서 사용할 수 있는, Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

<table border="1">
<tr bgcolor="FAF9F9">
        <th>이름</th>
        <th>설명</th>
</tr>

<tr>
<td colspan="2"><strong>앱 + 데이터 서비스 커넥터</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/">앱 + 데이터 서비스 커넥터</a>에서 이러한 커넥터에 대한 자세한 정보를 제공합니다.</td>
</tr>

<tr>
<td>Azure HDInsight</td>
<td>Azure HDInsight는 클라우드에서 Apache Hadoop 클러스터를 배포 및 프로비전하며, 빅데이터를 관리, 분석 및 보고하도록 설계된 소프트웨어 프레임워크를 제공합니다. 이 커넥터는 Azure에서 Hadoop 클러스터를 생성하고 Hive, Pig, MapReduce 및 스트리밍 MapReduce와 같은 다양한 Hadoop 작업을 제출합니다. 이 커넥터를 사용하여 클러스터를 스핀하고 작업을 제출하고 작업이 완료되기를 기다릴 수 있습니다.</td>
</tr>

<tr>
<td>Azure 서비스 버스</td>
<td>Azure 서비스 버스는 거의 모든 것을 연결하는 일반 클라우드 기반 메시징 시스템입니다. 이 커넥터를 통해 느슨하게 결합된 방식으로 메시지를 교환하면서 확장성 및 안정성은 개선할 수 있습니다. 이 커넥터를 사용하여 큐 및 항목과 같은 서비스 버스 엔터티에서 메시지를 보내고, 큐 및 구독과 같은 서비스 버스 엔터티에서 메시지를 받을 수 있습니다.</td>
</tr>

<tr>
<td>Azure 저장소 Blob</td>
<td>Azure 저장소 Blob은 세계 어느 곳에서나 액세스할 수 있는 텍스트 또는 이진 데이터와 같이 다량의 구조화되지 않은 데이터를 저장합니다. 이 커넥터를 사용하여 Blob 저장소에 연결하고 Blob 업로드, Blob 가져오기, Blob 삭제, 컨테이너의 Blob 나열, Blob 스냅숏, Blob 복사 등의 작업을 수행하고 트리거를 사용하여 Blob을 검색할 수 있습니다.</td>
</tr>

<tr>
<td>Box</td>
<td>Box는 파일 공유 서비스입니다. Box 커넥터는 Box에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.</td>
</tr>

<tr>
<td>Dropbox</td>
<td>Dropbox는 파일 호스팅 서비스입니다. 이 커넥터를 사용하여 Dropbox에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.</td>
</tr>

<tr>
<td>Microsoft Office 365</td>
<td>Office 365 커넥터는 메일을 보내고 받고, 일정을 관리하고, Office 365 계정을 사용하여 연락처를 관리할 수 있습니다. 또한 메일 보내기, 받기 및 가져오기, 일정에서 이벤트 생성 및 삭제, 연락처 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다.</td>
</tr>

<tr>
<td>Microsoft OneDrive</td>
<td>Microsoft OneDrive는 파일 호스팅 서비스입니다. OneDrive 커넥터는 개인의 Microsoft OneDrive에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>Microsoft SharePoint 커넥터를 사용하여 온-프레미스 Microsoft SharePoint Server 또는 SharePoint Online에 연결하고 문서를 관리하고 목록을 나열할 수 있습니다. 또한 문서 생성, 업데이트, 가져오기 및 삭제 작업을 수행하고 항목을 나열할 수도 있습니다. 기본 자격 증명, OAuth 2.0, Windows 인증 및 양식 기반 인증과 같은 다양한 인증 방법이 지원됩니다.</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>Microsoft SQL 커넥터는 온-프레미스 SQL Server 또는 Azure SQL 데이터베이스에 연결합니다. SQL 데이터베이스 테이블의 항목 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다.</td>
</tr>

<tr>
<td colspan="2"><strong>소셜 커넥터</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">소셜 커넥터</a>에서 이러한 커넥터에 대한 자세한 정보를 제공합니다.</td>
</tr>

<tr>
<td>Chatter</td>
<td>Chatter는 기업 소셜 네트워크입니다. Chatter 커넥터는 Chatter에 연결하고 메시지를 게시 및 검색하며 트리거를 사용하여 새 메시지를 검색할 수 있습니다.</td>
</tr>

<tr>
<td>Facebook</td>
<td>Facebook은 소셜 네트워킹 서비스입니다. Facebook 커넥터는 Facebook에 연결하고 메시지, 동영상, 사진, 제안 등을 게시할 수 있습니다. 또한 메시지, 설명, 이벤트, 사용자 피드, 사용자 정보 및 책, 게임, 영화 등을 비롯한 다양한 정보도 검색할 수 있습니다. 이 커넥터에는 페이지에 게시된 새 메시지를 검색하는 트리거도 포함되어 있습니다.</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>Microsoft Yammer는 기업 소셜 네트워크입니다. Yammer 커넥터는 Yammer에 연결합니다. 이 커넥터에는 메시지 게시 작업과 새 메시지를 검색할 수 있는 트리거가 포함되어 있습니다.</td>
</tr>

<tr>
<td>Twilio</td>
<td>Twilio는 통신 지향 SaaS 서비스입니다. Twilio 커넥터는 Twilio에 연결하고 메시지 보내기, 메시지 가져오기, 메시지 나열, 사용량 나열, 사용 가능한 무료 전화번호 가져오기, 사용 가능한 모바일 번호 가져오기, 사용 가능한 지역 번호 가져오기, 수신 전화번호 나열, 수신 전화번호 가져오기, 수신 전화번호 추가 등의 작업을 수행할 수 있습니다.</td>
</tr>

<tr>
<td>Twitter</td>
<td>Twitter는 사용자가 140자의 짧은 메시지를 보내고 읽을 수 있도록 하는 온라인 소셜 네트워킹 서비스입니다. Twitter 커넥터는 Twitter에 연결, 사용자 타임라인 가져오기, 트윗 검색, 팔로워 가져오기, 친구 가져오기, 사용자 검색, 홈 타임라인 가져오기, 멘션 타임라인 가져오기, 트윗 게시, 사용자에 트윗 게시, 직접 메시지 보내기 등의 작업을 수행할 수 있습니다. 또한 Twitter 커넥터는 키워드별 트윗 가져오기, 사용자 핸들별 트윗 가져오기 및 해시태그별 트윗 가져오기와 같은 트리거도 사용합니다.</td>
</tr>

<tr>
<td colspan="2"><strong>프로토콜 커넥터</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">프로토콜 커넥터</a>에서 이러한 커넥터에 대한 자세한 정보를 제공합니다.</td>
</tr>

<tr>
<td>파일</td>
<td>파일 커넥터를 이용하면 고객이 파일 시스템 또는 네트워크에 파일을 보내거나 업로드하고 파일 시스템 또는 네트워크에서 파일을 받거나 다운로드할 수 있습니다. 이 커넥터를 사용하여 온-프레미스 파일 서버에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.</td>
</tr>

<tr>
<td>FTP<br/>FTPS</td>
<td>FTP(파일 전송 프로토콜)는 호스트 간에 파일을 전송할 수 있는 대중적인 네트워크 프로토콜입니다. FTP 커넥터는 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행할 수 있으며 트리거를 사용해 파일도 검색할 수 있습니다.</td>
</tr>

<tr>
<td>HTTP Listener</td>
<td>HTTP Listener는 HTTP 서버 역할을 하는 끝점을 열고, 들어오는 HTTP 요청을 수신합니다.</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>POP3(Post Office Protocol)는 메일 클라이언트가 메일 서버로부터 메일을 검색하는 데 사용하는 프로토콜입니다. POP3 커넥터는 POP3 서버에 연결합니다. 이 커넥터에는 첨부 파일이 포함된 메일을 검색하는 트리거가 포함되어 있습니다.</td>
</tr>

<tr>
<td>SFTP</td>
<td>SFTP(SSH 파일 전송 프로토콜)는 파일을 안전하게 전송하는 데 사용하는 일반 프로토콜입니다. SFTP 커넥터는 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.</td>
</tr>

<tr>
<td>SMTP</td>
<td>SMTP(Simple Mail Transfer Protocol)는 메일 클라이언트와 메일 서버 간에 메일을 보내는 데 사용되는 프로토콜입니다. SMTP 커넥터는 SMTP 서버에 연결하고 첨부 파일과 함께 메일을 보냅니다.</td>
</tr>

<tr>
<td colspan="2"><strong>엔터프라이즈 커넥터</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">엔터프라이즈 커넥터</a>에서 이러한 커넥터에 대한 자세한 정보를 제공합니다.</td>
</tr>

<tr>
<td>Marketo</td>
<td>Marketo는 인바운드 마케팅, 소셜 마케팅, CRM 및 기타 관련 서비스를 제공하는 마케팅 자동화 소프트웨어를 개발합니다. Marketo 커넥터는 Marketo에 연결하고 잠재 고객 생성/업데이트, 잠재 고객 변경 가져오기, 캠페인 예약, 캠페인 요청, 잠재 고객 가져오기, 캠페인/목록 정보 가져오기, 목록에 잠재 고객 추가, 목록에서 잠재 고객 제거 등의 작업을 수행합니다.</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>QuickBooks 커넥터를 사용하여 Intuit QuickBooks에서 고객, 항목, 송장 등과 같은 다양한 엔터티 생성, 업데이트, 읽기, 삭제 및 쿼리 작업을 수행할 수 있습니다.</td>
</tr>

<tr>
<td>Salesforce</td>
<td>Salesforce 커넥터는 Salesforce 계정에서 계정, 잠재 고객, 기회, 사례 등의 다양한 엔터티를 관리합니다. 또한 다양한 엔터티 생성, 업데이트, 삽입, 쿼리 및 삭제 작업을 수행할 수 있습니다.</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>SugarCRM 커넥터는 SugarCRM 온라인에 연결하고 계정, 연락처, 제품 등과 같은 다양한 유형의 모듈 생성, 업데이트, 읽기 및 삭제 작업을 수행할 수 있습니다.</td>
</tr>

</table>


## BizTalk EAI API 앱
다음 표에는 BizTalk EAI 서비스에서 사용할 수 있는, Microsoft에서 만든 사용 가능한 모든 API 앱이 나열되어 있습니다.

	Name  | Description
------------- | -------------
BizTalk 플랫 파일 인코더 | 플랫 파일 데이터(예: excel 및 csv)와 XML 데이터 간의 상호 운용성을 제공합니다. 이 API 앱은 플랫 파일 인스턴스를 XML로 또는 그 반대로 변환할 수 있습니다.
BizTalk 변환 서비스 | BizTalk 변환 API 앱은 한 형식에서 다른 형식으로 데이터를 변환합니다. 또한 기존 맵(.trfm 파일)을 업로드하고, 원본과 대상 스키마 간 링크를 보여 주는 맵의 그래픽 표현을 확인하고, 샘플 입력 XML 콘텐츠로 '테스트' 기능을 사용할 수 있습니다. 문자열 조작, 조건부 할당, 산술 식, 날짜 시간 포맷터, 루핑 등을 비롯하여 다양한 기본 제공 함수도 사용할 수 있습니다.
BizTalk XML 유효성 검사기 | 유효성 검사기 API 앱은 미리 정의된 XML 스키마에 대해 XML 데이터의 유효성을 검사합니다. 기존 스키마를 사용하거나 플랫 파일 인스턴스, JSON 인스턴스 또는 기존 커넥터에 따라 스키마를 생성할 수 있습니다.
BizTalk XPath 추출기 | 추출기 API 앱은 지정된 XPath에 따라 XML 콘텐츠에서 데이터를 조회 및 추출합니다.

이러한 API 앱에 대한 자세한 내용은 [BizTalk 통합 커넥터](app-service-logic-integration-connectors.md)를 참조하세요.


## BizTalk EDI 커넥터 및 API 앱
다음 표에는 BizTalk EDI 서비스에서 사용할 수 있는, Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

	Name  | Description
------------- | -------------
AS2 커넥터 | AS2 커넥터는 B2B 통신에 AS2 전송 프로토콜을 사용하여 메시지를 주고받을 수 있습니다. 데이터는 디지털 인증서 및 암호화를 사용하여 인터넷을 통해 안전하고 안정적으로 전송됩니다.
BizTalk EDIFACT | EDIFACT API 앱은 B2B 통신에 EDIFACT 프로토콜을 사용하여 메시지를 받고 보냅니다.
BizTalk X12 | X12 API 앱은 B2B 통신에 X12 프로토콜을 사용하여 메시지를 받고 보냅니다.
BizTalk 거래 업체 관리 | 거래 업체 관리 API 앱은 파트너, 규약, 규약에 사용되는 스키마 및 인증서를 사용하여 B2B 관계를 정의 및 유지합니다. 이러한 관계는 AS2, EDIFACT 및 X12 API 앱을 사용하여 적용됩니다.

이러한 API 앱에 대한 자세한 내용은 [B2B 커넥터](app-service-logic-b2b-connectors.md)를 참조하세요.


## BizTalk 규칙 API 앱
다음 표에는 BizTalk 규칙에서 사용할 수 있는, Microsoft에서 만든 사용 가능한 모든 API 앱이 나열되어 있습니다.

	Name  | Description
------------- | -------------
BizTalk 규칙 | BizTalk 규칙은 조직의 구조, 작업 및 전략을 정의하고 제어합니다. 비즈니스 정책은 관련된 응용 프로그램을 다시 컴파일하고 다시 배포하지 않고도 업데이트할 수 있습니다.

이 API 앱에 대한 자세한 내용은 [BizTalk 통합 커넥터](app-service-logic-integration-connectors.md)를 참조하세요.


## 프리미엄 커넥터
다음 표에는 프리미엄 커넥터에서 사용할 수 있는, Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

	Name  | Description
------------- | -------------
DB2 커넥터 | DB2 커넥터는 Windows 운영 체제를 실행하는 온-프레미스 및 Azure 가상 컴퓨터에서 IBM DB2 데이터베이스에 연결합니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다. 동작에는 테이블 선택, 삽입, 업데이트, 삭제 및 사용자 지정 문이 포함됩니다.<br/><br/>[앱 + 데이터 서비스 커넥터](app-service-logic-data-connectors.md)
Informix | Informix 커넥터는 Windows 운영 체제를 실행하는 온-프레미스 및 Azure 가상 컴퓨터에서 온-프레미스 IBM Informix 데이터베이스에 연결합니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다. 동작에는 테이블 선택, 삽입, 업데이트, 삭제 및 사용자 지정 문이 포함됩니다.<br/><br/>[앱 + 데이터 서비스 커넥터](app-service-logic-data-connectors.md)
MQ | MQ 커넥터는 Windows 운영 체제를 실행하는 온-프레미스 및 Azure 가상 컴퓨터에서 IBM WebSphere MQ Server에 연결합니다.<br/><br/><strong>참고</strong> 현재 논리 앱과 함께 사용할 수 없습니다.<br/><br/>[앱 + 데이터 서비스 커넥터](app-service-logic-data-connectors.md)
Oracle 데이터베이스 | Oracle 데이터베이스 커넥터는 온-프레미스 Oracle 데이터베이스에 연결하고 데이터베이스 테이블의 항목 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다. <br/><br/>[앱 + 데이터 서비스 커넥터](app-service-logic-data-connectors.md)
SAP | SAP 커넥터는 온-프레미스 SAP 서버에 연결하고 RFC, BAPI 및 tRFC를 호출하며 IDOC를 보냅니다.<br/><br/>[엔터프라이즈 커넥터](app-service-logic-enterprise-connectors.md)


## 고유한 커넥터 및 API 앱 만들기
[REST API를 사용하여 커넥터 만들기](http://go.microsoft.com/fwlink/p/?LinkId=529766)를 참조하세요.


## 추가 커넥터

[BizTalk 통합 커넥터](app-service-logic-integration-connectors.md)<br/> [엔터프라이즈 커넥터](app-service-logic-enterprise-connectors.md)<br/> [B2B 커넥터](app-service-logic-b2b-connectors.md)<br/> [소셜 커넥터](app-service-logic-social-connectors.md)<br/> [프로토콜 커넥터](app-service-logic-protocol-connectors.md)<br/> [앱 + 데이터 서비스 커넥터](app-service-logic-data-connectors.md)<br/><br/> [커넥터 및 BizTalk API 앱 정의](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54--> 