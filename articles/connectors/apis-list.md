<properties
	pageTitle="Microsoft 관리 커넥터 목록 | Microsoft Azure 앱 서비스"
	description="Azure 앱 서비스에서 Logic Apps를 빌드하는 데 사용할 수 있는 Microsoft 관리 커넥터의 전체 목록 가져오기"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/19/2016"
	ms.author="deonhe"/>

# 관리되는 커넥터의 목록

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전에 대한 내용을 보려면 [커넥터 목록](../app-service-logic/app-service-logic-connectors-list.md)를 클릭합니다.

가격 정보 및 각 서비스 계층에 포함된 항목의 목록은 [Azure 앱 서비스 가격](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.

> [AZURE.NOTE] Azure 계정에 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동하세요. 앱 서비스에서 단기 시작 논리 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

이러한 서비스를 호출하는 앱을 빌드하기 위해 이러한 커넥터를 신속하게 활용하는 방법에 대해 알아보려면 아이콘을 선택합니다. 이러한 커넥터는 Logic Apps, PowerApps 및 Flows를 빌드하는 데 사용할 수 있습니다.

|커넥터||||
|-----------|-----------|-----------|-----------|
|[![API 아이콘][blobicon]<br/>**Azure Blob**][azureblobdoc]|[![API 아이콘][bingsearchicon]<br/>**Bing 검색**][bingsearchdoc]|[![API 아이콘][boxicon]<br/>**Box**][boxDoc]|[![API 아이콘][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![API 아이콘][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![API 아이콘][facebookicon]<br/>**Facebook**][facebookdoc]|[![API 아이콘][ftpicon]<br/>**FTP**][ftpdoc]|[![API 아이콘][githubicon]<br/>**GitHub**][githubdoc]|
[![API 아이콘][googledriveicon]<br/>**Google 드라이브**][googledrivedoc]|[![API 아이콘][mailchimpicon]<br/>* * MailChimp * *][mailchimpdoc]|[![API 아이콘][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![API 아이콘][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|
|[![API 아이콘][office365icon]<br/>**Office 365**<br/>**사용자**][office365usersdoc]|[![API 아이콘][office365icon]<br/>**Office 365**<br/>**비디오**][office365videodoc]|[![API 아이콘][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API 아이콘][onedriveicon]<br/>**OneDrive<br/>for Business**][onedriveforbusinessdoc]|
|[![API 아이콘][outlookicon]<br/>**Outlook**][outlookdoc]|[![API 아이콘][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![API 아이콘][rssicon]<br/>**RSS**][rssdoc]|[![API 아이콘][salesforceicon]<br/>**Salesforce**][salesforcedoc]|
|[![API 아이콘][sendgridicon]<br/>**SendGrid**][sendgriddoc]|[![API 아이콘][servicebusicon]<br/>**서비스 버스**][servicebusdoc]|[![API 아이콘][sftpicon]<br/>**SFTP**][sftpdoc]|[![API 아이콘][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|
|[![API 아이콘][slackicon]<br/>**Slack**<br/>][slackdoc]|[![API 아이콘][smtpicon]<br/>**SMTP**][smtpdoc]|[![API 아이콘][sqlicon]<br/>**SQL Azure**][sqldoc]|[![API 아이콘][trelloicon]<br/>**Trello**][trellodoc]|
[![API 아이콘][twilioicon]<br/>**Twilio**][twiliodoc]|[![API 아이콘][twittericon]<br/>**Twitter**][twitterdoc]|[![API 아이콘][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![API 아이콘][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] 2014-12-01-preview 스키마를 사용하여 Logic Apps를 작성한 경우 엔터프라이즈 통합 API(예: BizTalk에 대한 커넥터)가 위에 나열되지 않은 것으로 나타납니다. 빠른 시일 내에 이를 해결하기 위해 최선을 다하고 있습니다. 정확한 공유 가능 날짜는 아직 정해지지 않았지만 최우선으로 고려하고 있습니다. 그 동안은 [논리 앱에서 v1 API 및 BizTalk API](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/)에 액세스할 수 있습니다. 이해해 주셔서 감사합니다. 기대하세요.


### 커넥터는 트리거가 될 수 있습니다.
몇몇 커넥터는 특정 이벤트가 발생할 때 앱에 알릴 수 있는 트리거를 제공합니다. 예를 들어 FTP 커넥터에는 OnUpdatedFile 트리거가 있습니다. 이 트리거를 수신 대기하고 트리거가 발생될 때마다 작업을 수행하는 Logic Apps, PowerApp 또는 Flow를 빌드할 수 있습니다.

트리거에는 두 가지 유형이 있습니다.

* 폴링 트리거: 이 트리거는 지정된 빈도로 서비스를 폴링하여 새 데이터를 확인합니다. 새 데이터를 사용할 수 있으면 앱의 새 인스턴스가 해당 데이터를 입력으로 사용하여 실행됩니다. 동일한 데이터가 여러 번 사용되지 않도록 트리거는 읽어서 앱으로 전달된 데이터를 정리합니다.
* 밀어넣기 트리거: 이 트리거는 끝점에서 데이터를 수신 대기하거나 발생하는 이벤트를 수신 대기합니다. 그런 다음 앱의 새 인스턴스를 트리거합니다. Twitter 커넥터가 한 예입니다.


### 커넥터는 작업이 될 수 있습니다.
커넥터를 앱의 내부에서 작업으로 사용할 수도 있습니다. 작업은 앱의 실행에 사용될 수 있는 데이터를 조회하는 데 유용합니다. 예를 들어 주문을 처리할 때 SQL 데이터베이스에서 고객 데이터를 조회해야 할 수 있습니다. 또는 대상 테이블에서 데이터를 작성, 업데이트 또는 삭제해야 할 수 있습니다. 커넥터가 제공하는 작업을 사용하여 이를 수행할 수 있습니다. 작업은 Swagger 메타데이터에 정의된 작업에 매핑됩니다.


[새로운 기능](../app-service-logic/app-service-logic-schema-2015-08-01.md) [지금 논리 앱 빌드](../app-service-logic/app-service-logic-create-a-logic-app.md) [지금 PowerApps 시작](../power-apps/powerapps-get-started-azure-portal.md) [최신 스키마 버전으로 기존 논리 앱 마이그레이션](connectors-schema-migration.md)

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Blob 컨테이너에서 파일을 관리하는 Azure Blob에 연결합니다."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "웹, 이미지, 뉴스 및 비디오에 대한 Bing을 검색합니다."
[boxDoc]: ./connectors-create-api-box.md "상자에 연결하고 업로드, 가져오기, 삭제, 나열 및 추가 파일 작업을 수행할 수 있습니다."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Dynamics CRM Online에 연결하고 CRM Online 데이터를 사용하여 더 많은 작업을 수행합니다."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Dropbox에 연결하고 가져오기, 삭제, 나열 및 추가 파일 작업을 수행할 수 있습니다."
[exceldoc]: ./connectors-create-api-excel.md "Excel에 연결합니다."
[facebookdoc]: ./connectors-create-api-facebook.md "Facebook에 연결하여 타임라인에 게시, 페이지 피드 가져오기 등의 작업을 수행합니다."
[ftpdoc]: ./connectors-create-api-ftp.md "FTP / FTPS 서버에 연결하여 파일 등의 업로드, 가져오기, 삭제를 포함한 다른 FTP 작업을 수행합니다."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Google 드라이브에 연결하고 데이터를 조작합니다."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Office 365 커넥터는 메일을 보내고 받고, 일정을 관리하고, Office 365 계정을 사용하여 연락처를 관리할 수 있습니다."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "사용자 개인 Microsoft OneDrive에 연결하여 파일 등을 업로드, 삭제, 나열합니다."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "비즈니스 Microsoft OneDrive에 연결하고 파일 업로드, 삭제, 나열 등을 수행합니다."
[outlookdoc]: ./connectors-create-api-outlook.md "Outlook 사서함에 연결 및 전자 메일 액세스 등을 수행합니다."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Microsoft Project Online에 연결합니다."
[rssdoc]: ./connectors-create-api-rss.md "RSS 커넥터를 사용하여 사용자가 피드 항목을 게시 및 검색할 수 있습니다. 또한 새 항목이 피드에 게시된 경우 사용자가 작업을 트리거할 수 있습니다."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Salesforce 계정에 연결하고 계정, 잠재 고객, 영업 기회 등을 관리합니다."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Microsoft Project Online에 연결합니다."
[servicebusdoc]: ./connectors-create-api-servicebus.md "서비스 버스 큐 및 항목에서 메시지를 보내고 서비스 버스 엔큐 및 구독에서 메시지를 받을 수 있습니다."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "문서 및 목록 항목을 관리하려면 SharePoint Online에 연결합니다."
[slackdoc]: ./connectors-create-api-slack.md "Slack에 연결하고 Slack 채널에 메시지를 게시합니다."
[sftpdoc]: ./connectors-create-api-sftp.md "SFTP에 연결하고 파일 등을 업로드하고, 가져오고 삭제할 수 있습니다."
[githubdoc]: ./connectors-create-api-github.md "GitHub에 연결하고 문제를 추적할 수 있습니다."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "더 나은 전자 메일을 보냅니다."
[smtpdoc]: ./connectors-create-api-smtp.md "SMTP 서버에 연결하여 첨부 파일과 함께 전자 메일을 보냅니다."
[sqldoc]: ./connectors-create-api-sqlazure.md "SQL Azure 데이터베이스에 연결합니다. SQL 데이터베이스 테이블의 항목 생성, 업데이트, 가져오기 및 삭제 작업을 수행할 수 있습니다."
[trellodoc]: ./connectors-create-api-trello.md "Trello는 무료이며 모든 사람과 어떠한 일이라도 구성하는 유연하고 시각적인 방법입니다."
[twiliodoc]: ./connectors-create-api-twilio.md "Twilio에 연결하여 메시지를 보내고 가져오고, 사용 가능한 번호를 가져와 수신 전화 번호 등을 관리합니다."
[twitterdoc]: ./connectors-create-api-twitter.md "Twitter에 연결하여 타임 라인을 가져오고 트윗에 게시합니다."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "업무를 동기화 상태로 유지합니다."
[yammerdoc]: ./connectors-create-api-yammer.md "Yammer에 연결하여 메시지를 게시하고 새 메시지를 받습니다."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0525_2016-->