---
title: "Azure Logic Apps용 커넥터 | Microsoft Docs"
description: "사용 가능한 모든 Microsoft 관리 커넥터 중에 선택하여 논리 앱을 빌드하고 만들기"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1e2dad92647630c8b9a4e7676eee9288c87daf44
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---
# 커넥터 목록
<a id="connectors-list" class="xliff"></a>
> [!TIP]
> 이 토픽의 [A-Z 전체 목록](#az)은 Logic Apps에서 사용할 수 있는 모든 커넥터를 나열합니다. [커넥터 세부 정보](/connectors/)에는 swagger에 정의된 모든 트리거 및 작업이 나열되며 각 커넥터에 대한 제한도 나열됩니다.

커넥터는 논리 앱을 만들 때 필수적인 부분입니다. 이러한 커넥터를 사용하면 온-프레미스 및 클라우드 응용 프로그램을 확장하여 새로 만든 데이터와 기존에 갖고 있던 데이터로 여러 가지 일을 할 수 있습니다. 커넥터는 다음 범주에 제공됩니다. 

* **표준 커넥터**: 논리 앱을 만들 때 자동으로 제공되고 포함됩니다. Service Bus, Power BI, Oracle Database, OneDrive 등을 예로 들 수 있습니다.

* **통합 계정 커넥터**: 통합 계정을 구입할 때 제공됩니다. 이러한 커넥터를 사용하면 XML을 변환하여 유효성을 검사하고, AS2/X12/EDIFACT를 사용하여 기업 간 메시지를 처리하고, 플랫 파일을 인코딩 및 디코딩할 수 있습니다. BizTalk Server를 작업할 때 이러한 커넥터는 BizTalk 워크플로를 Azure로 확장하는 데 적합합니다.  

    또한 BizTalk Server에는 논리 앱과의 송수신을 포함하는 [Logic Apps 어댑터](https://msdn.microsoft.com/library/mt787163.aspx)가 있습니다.

* **엔터프라이즈 커넥터**: MQ 및 SAP가 포함됩니다. 추가 비용 지불 시 사용할 수 있습니다. 

비용에 대한 자세한 내용은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/) 및 [가격 책정 모델](../logic-apps/logic-apps-pricing.md)을 참조하세요. 

## 인기 있는 커넥터
<a id="popular-connectors" class="xliff"></a>
이러한 커넥터를 사용하여 데이터와 정보를 성공적으로 처리하는 수천 개의 응용 프로그램과 수백만 개의 실행이 있습니다. 다음 테이블에는 사용자들에게 가장 인기 있고 많이 사용되는 일부 커넥터가 나열되어 있습니다.

| |  |  |  |
| --- | --- | --- | --- |
| [![API 아이콘][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | 저장소 계정을 사용하여 작업을 자동화하려는 경우 이 커넥터를 자세히 살펴보아야 합니다. CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 지원합니다. | [![API 아이콘][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | C# 또는 node.js의 사용자 지정 코드 조각을 실행하는 함수를 만들고 논리 앱에서 이러한 함수를 사용합니다.  |
| [![API 아이콘][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | 가장 많이 요청되는 커넥터 중 하나입니다. 리드가 포함된 워크플로를 자동화하는 데 도움이 되는 트리거와 작업 등을 갖고 있습니다. | [![API 아이콘][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Event Hub의 이벤트를 사용하고 게시합니다. 예를 들어 Event Hubs를 사용하여 논리 앱에서 출력을 가져온 다음 실시간 분석 공급자에게 보낼 수 있습니다. |
| [![API 아이콘][FTPicon]<br/>**FTP**][FTPdoc] | 인터넷에서 FTP 서버에 액세스할 수 있는 경우 파일 및 폴더를 작업하도록 워크플로를 자동화할 수 있습니다. <br/><br/>SFTP는 SFTP 커넥터에도 제공됩니다. | [![API 아이콘][HTTPicon]<br/>**HTTP**][httpdoc] | 논리 앱을 사용하여 HTTP를 통해 끝점과 통신합니다. |
| [![API 아이콘][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | 워크플로 내에서 Office 365 전자 메일 및 이벤트를 사용하는 많은 트리거와 많은 작업이 있습니다. <br/><br/>이 커넥터는 휴가 요청, 경비 보고서 등을 승인하는 *승인 전자 메일* 작업을 포함하고 있습니다. <br/><br/>Office 365 사용자에게는 Office 365 사용자 커넥터도 제공됩니다.| [![API 아이콘][HTTP-Requesticon]<br/>**요청/응답**][HTTP-Requestdoc] | 이 커넥터는 HTTPS URL을 제공합니다. 논리 앱이 이 URL에 대한 요청을 받으면 논리 앱이 시작됩니다. |
| [![API 아이콘][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Salesforce 계정으로 간편하게 로그인하여 리드 등의 개체에 액세스할 수 있습니다. |  [![API 아이콘][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | 논리 앱 내에서 가장 인기 있는 커넥터이며 비동기 메시지를 수행하고 큐, 구독 및 토픽을 게시/구독하는 트리거와 작업을 포함하고 있습니다. |
|  [![API 아이콘][SharePointicon]<br/>**SharePoint<br/>온라인**][SharePointdoc] | SharePoint로 작업을 수행하며 자동화가 도움이 되는 경우 이 커넥터를 자세히 살펴보시기 바랍니다. 온-프레미스 SharePoint 및 SharePoint Online과 함께 사용할 수 있습니다. | [![API 아이콘][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | 가장 많이 사용되는 커넥터 중 하나이며, 온-프레미스 SQL Server 및 Azure SQL Database에 연결할 수 있습니다. | 
| [![API 아이콘][Twittericon]<br/>**Twitter**][Twitterdoc] | Twitter 계정으로 간단하게 로그인한 후 새 트윗이 게시될 때 워크플로를 시작합니다. 그런 다음 SQL 데이터베이스 또는 SharePoint 목록에 이러한 트윗을 저장합니다. | | | 

## 통합 계정 커넥터
<a id="integration-account-connectors" class="xliff"></a> 

EIP(엔터프라이즈 통합 팩)는 BizTalk Server 커뮤니티에 잘 알려진 커넥터를 포함하고 있습니다. [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 구입할 때 다음 커넥터도 함께 제공됩니다. 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API 아이콘][as2icon]<br/>**AS2</br>디코딩**][as2decode] | [![API 아이콘][as2icon]<br/>**AS2</br>인코딩**][as2encode] | [![API 아이콘][x12icon]<br/>**EDIFACT</br>디코딩**][EDIFACTdecode] | [![API 아이콘][x12icon]<br/>**EDIFACT</br>인코딩**][EDIFACTencode] |
[![API 아이콘][flatfileicon]<br/>**플랫 파일</br>인코딩**][flatfiledoc] | [![API 아이콘][flatfiledecodeicon]<br/>**플랫 파일</br>디코딩**][flatfiledecodedoc] | [![API 아이콘][integrationaccounticon]<br/>**통합<br/>계정**][integrationaccountdoc] | [![API 아이콘][xmltransformicon]<br/>**변환<br/>XML**][xmltransformdoc] |
| [![API 아이콘][x12icon]<br/>**X12</br>디코딩**][x12decode] | [![API 아이콘][x12icon]<br/>**X12</br>인코딩**][x12encode] | [![API 아이콘][xmlvalidateicon]<br/>**XML <br/>유효성 검사**][xmlvalidatedoc] | |

## 엔터프라이즈 커넥터
<a id="enterprise-connectors" class="xliff"></a>

논리 앱 내의 엔터프라이즈 응용 프로그램에 연결합니다.

|  |  |
| --- | --- |
|[![API 아이콘][MQicon]<br/>**MQ**][mqdoc]|[![API 아이콘][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>A-Z 전체 목록

[커넥터 세부 정보](/connectors/)에는 swagger에 정의된 모든 트리거 및 작업이 나열되며 각 커넥터에 대한 제한도 나열됩니다.

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8 약속 일정<br/><br/><a name="a"></a>Act!<br/>Adobe Creative Cloud<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure AD(Active Directory)<br/>Azure API Management<br/>Azure App Services<br/>Azure 응용 프로그램<br/>Azure Automation<br/>[Azure Blob Storage][azureblobstoragedoc]<br/>Azure 데이터 레이크<br/>Azure DocumentDB(Cosmos DB)<br/>[Azure Functions][azure-functionsdoc]<br/>[Azure Logic Apps][nested-logic-appdoc]<br/>AzureML<br/>Azure 큐<br/>Azure 리소스 관리자<br/>[Azure SQL Database][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>Batch<br/>벤치마크 전자 메일<br/>Bing Search<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Calendly<br/>Campfire<br/>Capsule CRM<br/>Chatter<br/>Cognito 양식<br/>Cognitive Services Computer Vision API<br/>Cognitive Services Face API<br/>Cognitive Services LUIS<br/>Cognitive Services 텍스트 분석<br/>Common Data Service<br/>콘텐츠 변환<br/>제어-종료<br/>[사용자 지정 API/웹앱][api/web-appdoc]<br/><br/><a name="d"></a>데이터 작업<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>동적 NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[Event Hubs][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[파일 시스템][filesystemdoc]<br/>[플랫 파일][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>FreshService<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Google 캘린더<br/>Google 연락처<br/>Google 드라이브<br/>Google Sheets<br/>Google 태스크<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[HTTP Webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>통합 계정<br/>Intercom | <a name="j"></a>JotForm<br/>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>중간<br/>Microsoft Forms<br/>Microsoft 팀<br/>Microsoft Translator<br/>[MQ][mqdoc]<br/>MSN 날씨<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Office 365 사용자<br/>Office 365 비디오<br/>OneDrive<br/>OneDrive for Business<br/>OneNote(Business)<br/>[Oracle Database][oracle-db-doc]<br/>Outlook 고객 관리자<br/>Outlook 작업<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Parserr<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>Planner<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[요청/응답][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[SAP 응용 프로그램 서버][sapconnector]<br/>[SAP 메시지 서버][sapconnector]<br/>[일정][recurrencedoc]<br/>범위<br/>SendGrid<br/>일괄 처리할 메시지 보내기<br/>[Service Bus][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointdoc]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>스트라이프<br/>SurveyMonkey<br/>Switch Case<br/><br/><a name="t"></a>Teamwork 프로젝트<br/>Teradata<br/>Todoist<br/>Toodledo<br/>[XML 변환][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>변수<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[XML 유효성 검사][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> Azure 계정에 등록하기 전에 Azure Logic Apps를 시작하려면 [Logic Apps 사용해 보기](https://tryappservice.azure.com/?appservice=logic)로 이동하세요. 단기 시작 논리 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 트리거 및 작업으로 사용되는 커넥터
<a id="connectors-as-triggers-and-actions" class="xliff"></a>

**트리거**는 논리 앱의 인스턴스를 시작하거나 실행합니다. 일부 커넥터는 특정 이벤트가 발생하면 앱에 알리는 트리거를 제공합니다. 예를 들어 FTP 커넥터에는 파일이 업데이트되면 논리 앱을 시작하는 `OnUpdatedFile` 트리거가 있습니다. 

Logic Apps에는 다음과 같은 종류의 트리거가 포함되어 있습니다.  

* *폴링 트리거*: 이 트리거는 지정된 빈도로 서비스를 폴링하여 새 데이터를 확인합니다. 

    새 데이터를 사용할 수 있게 되면 논리 앱의 새 인스턴스가 해당 데이터를 입력으로 사용하여 실행됩니다. 

* *트리거 푸시*: 이 트리거는 끝점의 데이터 또는 발생할 이벤트를 수신한 다음 논리 앱의 새 인스턴스를 트리거합니다.

* *되풀이 트리거*: 이 트리거는 정해진 일정에 따라 논리 앱의 인스턴스를 인스턴스화합니다.

또한 커넥터는 워크플로에서 사용할 수 있는 **작업**을 제공합니다. 예를 들어 논리 앱은 데이터를 조회한 후 나중에 이 데이터를 논리 앱에서 사용할 수 있습니다. 좀 더 구체적으로 말해서, SQL 데이터베이스에서 고객 데이터를 조회한 후 이 고객 데이터를 사용하여 워크플로를 빌드할 수 있습니다. 

> [!TIP]
> [커넥터 개요](connectors-overview.md)는 트리거 및 작업에 대한 세부 정보를 제공합니다. 


## 메시지 조작 작업
<a id="message-manipulation-actions" class="xliff"></a>

논리 앱에는 페이로드 데이터를 변경 또는 조작할 수 있는 기본 제공 작업이 포함되어 있습니다. 기본 제공 **데이터 작업** 커넥터는 다음과 같은 작업을 포함하고 있습니다. 

| | |
|---|---|
| **작성** | 나중에 또는 워크플로를 빌드할 때 사용할 값 또는 개체를 빌드하거나 생성합니다. 예를 들어 여러 단계의 값을 사용하여 JSON 개체를 작성하거나 나중에 논리 앱을 실행할 때 참조할 상수를 계산할 수 있습니다. |
| **CSV 테이블 만들기**<br/>**HTML 테이블 만들기** | 배열 결과 집합을 CSV 또는 HTML 테이블로 변환합니다. 예를 들어 CRM "레코드 나열" 작업을 추가하고, 오늘 추가된 레코드에 대한 필터를 추가합니다. 그런 다음 결과를 전자 메일의 HTML 테이블로 보냅니다. |
| **배열 필터링**(쿼리) | 결과 집합을 관심 있는 항목으로 필터링합니다. 예를 들어 `#Azure`로 모든 트윗을 검색한 후 반환된 트윗을 "필터링"하여 `Tweeted_by_followers > 50`인 결과만 반환합니다. |
| **Join** | 구분 기호를 사용하여 배열을 조인합니다. 예를 들어 핵심 문구 검색 작업은 핵심 문구 배열을 반환합니다. `,` 또는 비슷한 것을 사용하여 핵심 문구를 "조인"할 수 있습니다. 따라서 `["Some", "Phrase"]` 대신 `"Some, Phrase"`가 있습니다. |
| **JSON 구문 분석** | 디자이너에서 JSON 개체의 값을 구문 분석하고 액세스합니다. 예를 들어 Azure Function이 JSON 페이로드를 반환하는 경우 해당 페이로드를 구문 분석하여 나중에 다른 단계에서 JSON 속성에 액세스할 수 있습니다. 또한 이 작업은 JSON이 런타임에 지정된 스키마와 일치하는지 확인합니다. | 
| **선택** | 추가 처리를 위해 배열의 특정 속성을 선택합니다. SQL에서 "레코드 나열" 작업을 수행했더니 15개 열이 반환되는 경우 그 중에서 추가로 처리할 열 몇 개만 선택합니다. 출력은 사용자가 선택하는 속성만 포함하는 배열입니다. |

## 사용자 지정 커넥터 및 Azure 인증
<a id="custom-connectors-and-azure-certification" class="xliff"></a> 

사용자 지정 코드를 실행하거나 커넥터로 사용할 수 없는 API를 호출하려면 [REST 기반 API Apps를 사용자 지정 커넥터로 만들어](../logic-apps/logic-apps-create-api-app.md) Logic Apps 플랫폼을 확장할 수 있습니다. 

사용자 지정 API Apps를 Azure에서 공개하고 사용할 수 있게 하려면 [Microsoft Azure 인증 프로그램](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)에 추천을 제출합니다.

## 도움말 보기
<a id="get-help" class="xliff"></a>

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 확인하려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Azure Logic Apps 및 커넥터 개선에 도움을 주려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.

커넥터 항목이나 중요하다고 생각 한 세부 정보를 빠뜨린 것이 있습니까? 그렇다면 이 기존 항목에 추가하거나 직접 작성해주세요. 설명서는 오픈 소스이며 GitHub에서 호스트됩니다. [GitHub 리포지토리](https://github.com/Microsoft/azure-docs)에서 시작하세요. 

## 다음 단계
<a id="next-steps" class="xliff"></a>
* [첫 번째 논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)
* [논리 앱에 대한 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)
* [논리 앱 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "App Service API Apps과 논리 앱 통합"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage 커넥터와 Blob 컨테이너의 파일 관리"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "논리 앱을 Azure Functions와 통합"
[db2doc]: ./connectors-create-api-db2.md "클라우드 또는 온-프레미스에서 IBM DB2에 연결합니다. 행 업데이트, 테이블 가져오기 등"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Dynamics CRM Online에 연결하여 CRM Online 데이터를 사용할 수 있습니다."
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs에 연결합니다. Logic Apps과 Event Hubs 간 이벤트 주고 받기"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "온-프레미스 파일 시스템에 연결"
[ftpdoc]: ./connectors-create-api-ftp.md "파일 업로드, 가져오기, 삭제 등과 같은 FTP 태스크의 경우 FTP / FTPS 서버에 연결"
[httpdoc]: ./connectors-native-http.md "HTTP 커넥터를 사용하여 HTTP 호출"
[http-requestdoc]: ./connectors-native-reqres.md "HTTP 요청에 작업 추가 및 논리 앱에 대한 응답"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "HTTP + Swagger 커넥터를 사용하여 HTTP 호출"
[informixdoc]: ./connectors-create-api-informix.md "클라우드 또는 온-프레미스에서 Informix에 연결합니다. 행 읽기, 테이블 나열 등"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "중첩된 워크플로와 논리 앱 통합"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Office 365 계정에 연결합니다. 메일 전송과 수신, 일정과 연락처 관리 등"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle 데이터베이스에 연결하여 행 추가, 삽입, 삭제 등"
[mqdoc]: ./connectors-create-api-mq.md "MQ 온-프레미스 또는 Azure에 연결하고 메시지 보내기 및 받기"
[recurrencedoc]:  ./connectors-native-recurrence.md "논리 앱에 대한 되풀이 작업 트리거"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Salesforce 계정에 연결합니다. 계정, 잠재 고객, 영업 기회 관리 등"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "온-프레미스 SAP 시스템에 연결"
[service-busdoc]: ./connectors-create-api-servicebus.md "Service Bus 큐 및 항목에서 메시지를 보내고 Service Bus 큐 및 구독에서 메시지를 받습니다."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "SharePoint Online에 연결합니다. 문서 관리, 항목 나열 등"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Azure SQL Database 또는 SQL server에 연결합니다. SQL Database 테이블에서 항목 생성, 업데이트, 가져오기 및 삭제"
[twitterdoc]: ./connectors-create-api-twitter.md "Twitter에 연결합니다. 타임라인 가져오기, 트윗에 게시 등"
[webhookdoc]: ./connectors-native-webhook.md "논리 앱에 Webhook 작업 및 트리거 추가"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "엔터프라이즈 통합 AS2에 대해 알아봅니다."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "엔터프라이즈 통합 X12에 대해 알아봅니다."
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "엔터프라이즈 통합 XML 유효성 검사에 대해 알아봅니다."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "엔터프라이즈 통합 변환에 대해 알아봅니다."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "엔터프라이즈 통합 AS2 디코딩에 대해 알아봅니다."
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "엔터프라이즈 통합 AS2 인코딩에 대해 알아봅니다."
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "엔터프라이즈 통합 X12 디코딩에 대해 알아봅니다."
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "엔터프라이즈 통합 X12 인코딩에 대해 알아봅니다."
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "엔터프라이즈 통합 EDIFACT 디코딩에 대해 알아봅니다."
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "엔터프라이즈 통합 EDIFACT 인코딩에 대해 알아봅니다."
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정에서 스키마, 맵, 파트너 등을 조회"


[boxDoc]: ./connectors-create-api-box.md "상자에 연결합니다. 파일 업로드, 가져오기, 삭제, 나열 등"
[delaydoc]: ./connectors-native-delay.md "지연된 작업 수행"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Dropbox에 연결합니다. 파일 업로드, 가져오기, 삭제, 나열 등"
[facebookdoc]: ./connectors-create-api-facebook.md "Facebook에 연결합니다. 타임라인에 게시, 페이지 피드 가져오기 등"
[githubdoc]: ./connectors-create-api-github.md "GitHub에 연결 및 문제 추적"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Google 드라이브에 연결하여 데이터를 사용할 수 있습니다."
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Google 시트에 연결하여 시트를 수정할 수 있습니다."
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Google 작업에 연결하여 태스크를 관리할 수 있습니다."
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Google 달력에 연결하고 달력을 관리할 수 있습니다."
[http-responsedoc]: ./connectors-native-reqres.md "HTTP 요청에 작업 추가 및 논리 앱에 대한 응답"
[instagramdoc]: ./connectors-create-api-instagram.md "Instagram에 연결합니다. 이벤트에서 트리거 또는 작업"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "MailChimp 계정에 연결합니다. 메일 관리 및 자동화"
[mandrilldoc]: ./connectors-create-api-mandrill.md "통신을 위해 Mandrill에 연결"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Microsoft Translator에 연결합니다. 텍스트 번역, 언어 감지 등" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Office 365 비디오에 대한 비디오 정보, 비디오 목록과 채널 및 재생 URL 가져오기"
[onedrivedoc]: ./connectors-create-api-onedrive.md "개인 Microsoft OneDrive에 연결합니다. 파일 업로드, 삭제, 나열 등"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "비즈니스 Microsoft OneDrive에 연결합니다. 파일 업로드, 삭제, 나열 등"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Outlook 사서함에 연결합니다. 전자 메일, 일정, 연락처 관리 등"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Microsoft Project Online에 연결합니다. 프로젝트, 태스크, 리소스 관리 등"
[querydoc]: ./connectors-native-query.md "쿼리 작업을 사용하여 배열 선택 및 필터링"
[rssdoc]: ./connectors-create-api-rss.md "피드 항목을 게시하고 검색하며 새 항목을 RSS 피드에 게시할 때 작업을 트리거합니다."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "SendGrid에 연결합니다. 전자 메일 보내기 및 수신자 목록 관리"
[sftpdoc]: ./connectors-create-api-sftp.md "SFTP 계정에 연결합니다. 파일 업로드, 가져오기, 삭제 등"
[slackdoc]: ./connectors-create-api-slack.md "Slack에 연결하고 Slack 채널에 메시지 게시"
[smtpdoc]: ./connectors-create-api-smtp.md "SMTP 서버에 연결 및 첨부 파일과 함께 전자 메일 보내기"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "통신을 위해 SparkPost에 연결"
[trellodoc]: ./connectors-create-api-trello.md "Trello에 연결합니다. 프로젝트 관리 및 다른 사람과 작업 구성"
[twiliodoc]: ./connectors-create-api-twilio.md "Twilio에 연결합니다. 메시지 보내기 및 가져오기, 사용 가능한 번호 가져오기 수신 전화 번호 관리 등"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Wunderlist에 연결합니다. 태스크 및 할 일 목록 관리, 동기화된 업무 유지 등"
[yammerdoc]: ./connectors-create-api-yammer.md "Yammer에 연결합니다. 메시지 게시, 새 메시지 가져오기 등"
[youtubedoc]: ./connectors-create-api-youtube.md "YouTube에 연결합니다. 비디오 및 채널 관리"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png

