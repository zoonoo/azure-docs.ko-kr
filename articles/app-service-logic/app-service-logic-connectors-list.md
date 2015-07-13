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
	ms.date="06/08/2015" 
	ms.author="mandia"/>


# Microsoft Azure 앱 서비스의 커넥터 및 API 앱 목록
이 항목에는 Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

가격 정보 및 각 서비스 계층에 포함된 항목의 목록은 [Azure 앱 서비스 가격](http://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.


## 표준 커넥터
다음 표에는 표준 커넥터에서 사용할 수 있는, Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

이름 | 설명
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | 이 커넥터를 사용하여 Azure에서 Hadoop 클러스터를 생성하고 Hive, Pig, MapReduce, Streaming MapReduce와 같은 다양한 Hadoop 작업을 제출할 수 있습니다. 이 커넥터를 사용하여 클러스터를 스핀하고 작업을 제출하고 작업이 완료되기를 기다릴 수 있습니다.
[Azure 서비스 버스](app-service-logic-connector-azureservicebus.md) | 이 커넥터를 사용하여 큐 및 항목과 같은 서비스 버스 엔터티에서 메시지를 보내고, 큐 및 구독과 같은 서비스 버스 엔터티에서 메시지를 받을 수 있습니다.
[Azure 저장소 Blob](app-service-logic-connector-azurestorageblob.md) | Blob 저장소에 연결하고 Blob 업로드, Blob 가져오기, Blob 삭제, 컨테이너의 Blob 나열, Blob 스냅숏, Blob 복사 등의 작업을 수행하고 트리거를 사용하여 Blob을 검색할 수 있습니다.
Azure WebJobs | Webjob에 연결합니다.
[Box](app-service-logic-connector-box.md) | Box에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.
[Chatter](app-service-logic-connector-chatter.md) | Chatter에 연결하고 메시지를 게시 및 검색하며 트리거를 사용하여 새 메시지를 검색할 수 있습니다.
[Dropbox](app-service-logic-connector-dropbox.md) | 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.
[Facebook](app-service-logic-connector-facebook.md) | Facebook에 연결하고 메시지, 동영상, 사진, 제안 등을 게시할 수 있습니다. 또한 메시지, 설명, 이벤트, 사용자 피드, 사용자 정보 및 책, 게임, 영화 등을 비롯한 다양한 정보도 검색할 수 있습니다. 이 커넥터에는 페이지에 게시된 새 메시지를 검색하는 트리거도 포함되어 있습니다.
[파일](app-service-logic-connector-file.md) | 고객이 파일 시스템 또는 네트워크에 파일을 보내거나 업로드하고 파일 시스템 또는 네트워크에서 파일을 받거나 다운로드할 수 있습니다. 이 커넥터를 사용하여 온-프레미스 파일 서버에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | FTP / FTPS 서버에 연결하여 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행할 수 있으며 트리거를 사용해 파일도 검색할 수 있습니다.
[HTTP](app-service-logic-connector-http.md) | HTTP Listener는 HTTP 서버 역할을 하는 끝점을 열고, 들어오는 HTTP 요청을 수신합니다. 게시, 가져오기, 삭제 및 배치가 가능합니다. HTTP 작업은 API 앱이 필요하지 않고 논리 앱 내에서 고유하게 지원됩니다. HTTP 또는 HTTPS 프로토콜을 사용할 수 있습니다.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Office 365 커넥터는 메일을 보내고 받고, 일정을 관리하고, Office 365 계정을 사용하여 연락처를 관리할 수 있습니다. 또한 메일 보내기, 받기 및 가져오기, 일정에서 이벤트 생성 및 삭제, 연락처 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Microsoft OneDrive는 파일 호스팅 서비스입니다. OneDrive 커넥터는 개인의 Microsoft OneDrive에 연결하고 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | 온-프레미스 Microsoft SharePoint Server 또는 SharePoint Online에 연결하여 문서를 관리하고 목록을 나열합니다. 또한 문서 생성, 업데이트, 가져오기 및 삭제 작업을 수행하고 항목을 나열할 수도 있습니다. 기본 자격 증명, OAuth 2.0, Windows 인증 및 양식 기반 인증과 같은 다양한 인증 방법이 지원됩니다.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | 온-프레미스 SQL Server 또는 Azure SQL 데이터베이스에 연결합니다. SQL 데이터베이스 테이블의 항목 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Yammer에 연결합니다. 메시지 게시 동작 및 새 메시지를 검색하는 트리거를 포함합니다.
[POP3](app-service-logic-connector-pop3.md)(Post Office Protocol)| POP3 서버에 연결하며 첨부 파일이 포함된 전자 메일을 검색하는 트리거를 포함합니다.
[QuickBooks](app-service-logic-connector-quickbooks.md) | 이 커넥터를 사용하여 Intuit QuickBooks에서 고객, 항목, 송장 등과 같은 다양한 엔터티 생성, 업데이트, 읽기, 삭제 및 쿼리 작업을 수행할 수 있습니다.
[SFTP](app-service-logic-connector-sftp.md)(SSH 파일 전송 프로토콜)| SFTP에 연결하여 파일 업로드, 파일 가져오기, 파일 삭제, 파일 나열 등의 작업을 수행하고 트리거를 사용하여 파일을 검색할 수 있습니다.
[SMTP](app-service-logic-connector-smtp.md)(단순 메일 전송 프로토콜) | SMTP 서버에 연결하여 첨부 파일과 함께 전자 메일을 보냅니다.
[Slack](app-service-logic-connector-slack.md) | 
[Salesforce](app-service-logic-connector-salesforce.md) | Salesforce 커넥터는 Salesforce 계정에서 계정, 잠재 고객, 기회, 사례 등의 다양한 엔터티를 관리합니다. 또한 다양한 엔터티 생성, 업데이트, 삽입, 쿼리 및 삭제 작업을 수행할 수 있습니다.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | SugarCRM 온라인에 연결하여 계정, 연락처, 제품 등과 같은 다양한 유형의 모듈 생성, 업데이트, 읽기 및 삭제 작업을 수행할 수 있습니다.
[Twilio](app-service-logic-connector-twilio.md) | Twilio에 연결하고 메시지 보내기, 메시지 가져오기, 메시지 나열, 사용량 나열, 사용 가능한 무료 전화번호 가져오기, 사용 가능한 모바일 번호 가져오기, 사용 가능한 지역 번호 가져오기, 수신 전화번호 나열, 수신 전화번호 가져오기, 수신 전화번호 추가 등의 작업을 수행할 수 있습니다.
[Twitter](app-service-logic-connector-twitter.md) | Twitter에 연결하고 사용자 타임라인 가져오기, 트윗 검색, 팔로워 가져오기, 친구 가져오기, 사용자 검색, 홈 타임라인 가져오기, 멘션 타임라인 가져오기, 트윗 게시, 사용자에 트윗 게시, 직접 메시지 보내기 등의 작업을 수행할 수 있습니다. 또한 Twitter 커넥터는 키워드별 트윗 가져오기, 사용자 핸들별 트윗 가져오기 및 해시태그별 트윗 가져오기와 같은 트리거도 사용합니다.
Wait | 이 커넥터를 사용하여 앱의 실행을 지연할 수 있습니다. 앱을 특정 기간 동안 또는 특정 시간에 발생 될 때까지 연기할 수 있습니다.


## 프리미엄 커넥터
다음 표에는 프리미엄 커넥터에서 사용할 수 있는, Microsoft에서 만든 사용 가능한 모든 커넥터 및 API 앱이 나열되어 있습니다.

이름 | 설명
------------- | -------------
AS2 커넥터 | AS2 커넥터는 B2B 통신에 AS2 전송 프로토콜을 사용하여 메시지를 주고받을 수 있습니다. 데이터는 디지털 인증서 및 암호화를 사용하여 인터넷을 통해 안전하고 안정적으로 전송됩니다.
BizTalk EDIFACT | EDIFACT API 앱은 B2B 통신에 EDIFACT 프로토콜을 사용하여 메시지를 받고 보냅니다.
BizTalk X12 | X12 API 앱은 B2B 통신에 X12 프로토콜을 사용하여 메시지를 받고 보냅니다.
BizTalk 거래 업체 관리 | 거래 업체 관리 API 앱은 파트너, 규약, 규약에 사용되는 스키마 및 인증서를 사용하여 B2B 관계를 정의 및 유지합니다. 이러한 관계는 AS2, EDIFACT 및 X12 API 앱을 사용하여 적용됩니다.
BizTalk JSON 인코더 | JSON 및 XML 데이터 간에 사용자 앱을 상호 운용하는 인코더 및 디코더. 이는 지정된 JSON 인스턴스를 XML로 변환하며 그 반대로 변환할 수도 있습니다.
[BizTalk 규칙](app-service-logic-use-biztalk-rules.md) | BizTalk 규칙은 조직의 구조, 작업 및 전략을 정의하고 제어합니다. 비즈니스 정책은 관련된 응용 프로그램을 다시 컴파일하고 다시 배포하지 않고도 업데이트할 수 있습니다.
DB2 커넥터 | Windows 운영 체제를 실행하는 IBM DB2 데이터베이스, 온-프레미스 및 Azure 가상 컴퓨터에 연결합니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다. <br/><br/>트리거가 없습니다. 작업은 테이블 선택, 삽입, 업데이트, 삭제 및 사용자 지정 문을 포함합니다<br/><br/> 이 커넥터에는 TCP/IP 네트워크를 통해 Informix 서버에 연결하는 DRDA용 Microsoft 클라이언트도 포함되어 있습니다.
Informix | Windows 운영 체제를 실행하는 IBM Informix 데이터베이스, 온-프레미스 및 Azure 가상 컴퓨터에 연결합니다. Web API 및 OData API 작업을 Informix 구조적 쿼리 언어 명령에 매핑할 수 있습니다.<br/><br/>트리거가 없습니다. 작업은 테이블 선택, 삽입, 업데이트, 삭제 및 사용자 지정 문을 포함합니다.<br/><br/>온-프레미스를 사용하여 VPN 또는 Azure ExpressRoute를 사용할 수 있습니다. 이 커넥터에는 TCP/IP 네트워크를 통해 Informix 서버에 연결하는 DRDA용 Microsoft 클라이언트도 포함되어 있습니다.
MQ | Windows 운영 체제를 실행하는 IBM WebSphere MQ Server 버전 8, 온-프레미스 및 Azure 가상 컴퓨터에 연결합니다. 온-프레미스를 사용하는 경우 VPN 또는 Azure Express 경로를 사용할 수 있습니다. 커넥터에는 MQ용 Microsoft 클라이언트도 포함되어 있습니다.<br/><br/>트리거가 없습니다. 작업이 없습니다.<br/><br/><strong>참고</strong> 현재는 논리 앱과 함께 사용할 수 없습니다.
[Oracle 데이터베이스](app-service-logic-connector-oracle.md) | Oracle 데이터베이스 커넥터는 온-프레미스 Oracle 데이터베이스에 연결하고 데이터베이스 테이블의 항목 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다.
[SAP](app-service-logic-connector-sap.md) | SAP 커넥터는 온-프레미스 SAP 서버에 연결하고 RFC, BAPI 및 tRFC를 호출하며 IDOC를 보냅니다.
[BizTalk 플랫 파일 인코더](app-service-logic-flatfile-encoder.md) | 플랫 파일 데이터(예: excel 및 csv)와 XML 데이터 간의 상호 운용성을 제공합니다. 이 API 앱은 플랫 파일 인스턴스를 XML로 또는 그 반대로 변환할 수 있습니다.
[BizTalk 변환 서비스](app-service-logic-transform-xml-documents.md) | BizTalk 변환 API 앱은 한 형식에서 다른 형식으로 데이터를 변환합니다. 또한 기존 맵(.trfm 파일)을 업로드하고, 원본과 대상 스키마 간 링크를 보여 주는 맵의 그래픽 표현을 확인하고, 샘플 입력 XML 콘텐츠로 '테스트' 기능을 사용할 수 있습니다. 문자열 조작, 조건부 할당, 산술 식, 날짜 시간 포맷터, 루핑 등을 비롯하여 다양한 기본 제공 함수도 사용할 수 있습니다.
[BizTalk XML 유효성 검사기](app-service-logic-xml-validator.md) | 유효성 검사기 API 앱은 미리 정의된 XML 스키마에 대해 XML 데이터의 유효성을 검사합니다. 기존 스키마를 사용하거나 플랫 파일 인스턴스, JSON 인스턴스 또는 기존 커넥터에 따라 스키마를 생성할 수 있습니다.
[BizTalk XPath 추출기](app-service-logic-xpath-extract.md) | 추출기 API 앱은 지정된 XPath에 따라 XML 콘텐츠에서 데이터를 조회 및 추출합니다.

## 트리거로 사용되는 커넥터
일부 커넥터는 논리 앱에 대한 트리거를 제공합니다. 다음 두 가지 유형의 트리거가 있습니다.

1. 폴링 트리거: 이 트리거는 지정된 빈도로 서비스를 폴링하여 새 데이터를 확인합니다. 새 데이터를 사용할 수 있으면 논리 앱의 새 인스턴스가 해당 데이터를 입력으로 사용하여 실행됩니다. 동일한 데이터가 여러 번 사용되지 않도록 트리거는 읽어서 논리 앱으로 전달된 데이터를 정리합니다. 이러한 커넥터의 예로 파일, SQL 및 Azure 저장소가 있습니다.
2. 밀어넣기 트리거: 이 트리거는 끝점에서 데이터를 수신 대기하거나 발생하는 이벤트를 수신 대기합니다. 그런 다음 논리 앱의 새 인스턴스를 트리거합니다. 이러한 커넥터의 예로 HTTP 수신기와 Twitter가 있습니다.

## 작업으로 사용되는 커넥터
커넥터를 논리 앱의 내부에서 작업으로 사용할 수도 있습니다. 작업은 다음 실행에 사용되는 논리 앱 내에서 데이터를 조회하는 데 유용합니다. 예를 들어 주문을 처리할 때 고객에 대한 추가적인 내용은 SQL 데이터베이스에서 데이터를 조회할 필요가 있을 수 있습니다. 또는 대상 위치에서 데이터를 작성, 업데이트 또는 삭제해야 할 수 있습니다. 커넥터가 제공하는 작업을 사용하여 이를 수행할 수 있습니다. 작업은 해당 Swagger 메타데이터에 정의된 대로 API 앱의 작업에 매핑됩니다.

## 고유한 커넥터 및 API 앱 만들기
[커넥터 및 API 앱 참조](http://aka.ms/appservicesconnectorreference)<br/> [Azure 앱 서비스 API 앱 트리거](../app-service-api/app-service-api-dotnet-triggers.md)


## 추가 커넥터

[커넥터 및 BizTalk API 앱 정의](app-service-logic-what-are-biztalk-api-apps.md)<br/> [Azure 앱 서비스에서 하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)

<!---HONumber=62-->