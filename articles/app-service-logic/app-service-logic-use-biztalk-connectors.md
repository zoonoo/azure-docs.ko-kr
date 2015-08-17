<properties
   pageTitle="커넥터 사용"
   description="커넥터 사용"
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
   ms.date="07/07/2015"
   ms.author="prkumar"/>

#커넥터#

> [AZURE.NOTE]이 항목은 사용 중지 됩니다. 모든 사용 가능한 기본 제공 커넥터 및 API 앱을 보려면 [커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)을 참조하세요.


커넥터는 클라우드나 온-프레미스에서 실행 중인 데이터 및 서비스에 연결할 수 있게 해주는 API 앱입니다. 커넥터를 사용하면 논리 앱 등에서 쉽게 액세스할 수 있는 다양한 기본 제공 트리거 및 동작을 통해 데이터에 쉽게 연결할 수 있습니다.

Azure 앱 서비스는 바로 사용할 수 있는 다양한 커넥터를 제공합니다.

##표준 커넥터##
* [Azure 서비스 버스 커넥터]
* [Azure 저장소 Blob 커넥터]
* Azure WebJobs 커넥터
* [Box 커넥터]
* [Chatter 커넥터]
* [DropBox 커넥터]
* [Facebook 커넥터]
* [파일 커넥터]
* [FTP 커넥터]
* [HDInsight 커넥터]
* [HTTP 커넥터]
* [Office 365 커넥터]
* [OneDrive 커넥터]
* [POP3 커넥터]
* [QuickBooks 커넥터]
* [Salesforce 커넥터]
* [SFTP 커넥터]
* [Sharepoint 커넥터]
* [Slack 커넥터]
* [SMTP 커넥터]
* [SQL 커넥터]
* [SugarCRM 커넥터]
* [Twilio 커넥터]
* [Twitter 커넥터]
* [Wait 커넥터]
* [Yammer 커넥터]


##프리미엄 커넥터 및 API 앱##
* [AS2 커넥터]
* [BizTalk EDIFACT]
* [BizTalk 플랫 파일 인코더]
* BizTalk JSON 인코더
* [BizTalk 변환 서비스]
* [BizTalk 규칙]
* [BizTalk X12]
* [BizTalk XPath 추출기]
* [BizTalk XML 유효성 검사기]
* DB2 커넥터
* Informix 커넥터
* [Oracle 커넥터]
* MQ 커넥터
* [SAP 커넥터]

자세한 내용은 커넥터 API 참조 [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference)를 참조하세요.

##커넥터 및 논리 앱##
논리 앱은 트리거와 작업으로 구성됩니다. 일부 커넥터는 이벤트나 일부 데이터의 가용성에 따라 워크플로를 인스턴스화하는 트리거로 사용할 수 있습니다. 커넥터는 데이터 읽기 및 쓰기나 커넥터가 지원하는 기타 동작을 수행하는 동작으로도 사용됩니다.

###트리거로 사용되는 커넥터###
일부 커넥터는 논리 앱에 대한 트리거를 제공합니다. 다음 두 가지 유형의 트리거가 있습니다.

1. 폴링 트리거: 이 트리거는 지정된 빈도로 관심 있는 서비스를 폴링하여 새 데이터를 확인합니다. 새 데이터를 사용할 수 있으면 논리 앱의 새 인스턴스가 해당 데이터를 입력으로 사용하여 실행됩니다. 트리거는 읽어서 논리 앱으로 전달된 데이터 정리와 같은 추가 작업을 수행하여 동일한 데이터가 여러 번 사용되지 않도록 할 수도 있습니다. 이러한 커넥터의 예로 파일, SQL 및 Azure 저장소가 있습니다.
2. 밀어넣기 트리거: 이 트리거는 끝점에서 데이터를 수신 대기하거나 발생하는 이벤트를 수신 대기하고 논리 앱의 새 인스턴스를 트리거합니다. 이러한 커넥터의 예로 HTTP 수신기와 Twitter가 있습니다.

###작업으로 사용되는 커넥터###
커넥터를 논리 앱의 일부인 작업으로 사용할 수도 있습니다. 동작은 논리 앱에서 실행에 사용할 데이터를 조회하는 데 유용합니다. 예를 들어 주문을 처리할 때 고객에 대한 추가 정보를 SQL DB에서 조회해야 할 수 있습니다. 또는 커넥터가 제공하는 작업을 사용할 수 있는 대상에서 데이터를 기록, 업데이트 또는 삭제해야 할 수도 있습니다. 동작은 해당 Swagger 메타데이터에 정의된 대로 API 앱의 작업에 매핑됩니다.


<!-- Links -->

[Box 커넥터]: app-service-logic-connector-box.md
[Facebook 커넥터]: app-service-logic-connector-facebook.md
[Salesforce 커넥터]: app-service-logic-connector-salesforce.md
[Twitter 커넥터]: app-service-logic-connector-twitter.md
[SAP 커넥터]: app-service-logic-connector-sap.md
[FTP 커넥터]: app-service-logic-connector-ftp.md
[HTTP 커넥터]: app-service-logic-connector-http.md
[Azure 저장소 Blob 커넥터]: app-service-logic-connector-azurestorageblob.md
[Office 365 커넥터]: app-service-logic-connector-office365.md
[Sharepoint 커넥터]: app-service-logic-connector-sharepoint.md
[SugarCRM 커넥터]: app-service-logic-connector-sugarcrm.md
[QuickBooks 커넥터]: app-service-logic-connector-quickbooks.md
[Yammer 커넥터]: app-service-logic-connector-yammer.md
[Twilio 커넥터]: app-service-logic-connector-twilio.md
[SMTP 커넥터]: app-service-logic-connector-smtp.md
[SFTP 커넥터]: app-service-logic-connector-sftp.md
[POP3 커넥터]: app-service-logic-connector-pop3.md
[DropBox 커넥터]: app-service-logic-connector-dropbox.md
[Chatter 커넥터]: app-service-logic-connector-chatter.md
[HDInsight 커넥터]: app-service-logic-connector-hdinsight.md
[Azure 서비스 버스 커넥터]: app-service-logic-connector-azureservicebus.md
[Oracle 커넥터]: app-service-logic-connector-oracle.md
[SQL 커넥터]: app-service-logic-connector-sql.md
[OneDrive 커넥터]: app-service-logic-connector-onedrive.md
[파일 커넥터]: app-service-logic-connector-file.md
[Slack 커넥터]: app-service-logic-connector-slack.md
[BizTalk 플랫 파일 인코더]: app-service-logic-flatfile-encoder.md
[BizTalk XPath 추출기]: app-service-logic-xpath-extract.md
[BizTalk XML 유효성 검사기]: app-service-logic-xml-validator.md
[BizTalk 규칙]: app-service-logic-use-biztalk-rules.md
[AS2 커넥터]: app-service-logic-connector-as2.md
[BizTalk EDIFACT]: app-service-logic-connector-edifact.md
[BizTalk X12]: app-service-logic-connector-x12.md
[JSON Encoder]: app-service-logic-json-encoder.md
[BizTalk 변환 서비스]: app-service-logic-transform-xml-documents.md
[Wait 커넥터]: app-service-logic-wait.md

<!---HONumber=August15_HO6-->