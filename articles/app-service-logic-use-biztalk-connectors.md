<properties 
   pageTitle="커넥터 사용" 
   description="커넥터 사용" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


#커넥터 사용#

커넥터는 데이터 가져오기 또는 put을 수행하거나 작업을 수행하기 위해 클라우드나 온-프레미스에서 실행 중인 서비스에 연결할 수 있게 해주는 API 앱입니다. 이 앱은 Azure 앱 서비스 전반에 걸쳐 웹앱, 모바일 앱 및 논리 앱에서 사용할 수 있습니다.

Azure 앱 서비스는 응용 프로그램의 실제 비즈니스 논리에 집중하는 동시에 관심 있는 서비스에 연결하도록 돕기 위해 기본적으로 여러 커넥터를 제공합니다. 또한 레거시 응용 프로그램에 연결하는 고유한 API 앱을 작성하여 플랫폼을 쉽게 확장할 수 있습니다.

BizTalk 커넥터의 주요 기능은 다음과 같습니다.

1. 인기 있는 SaaS 서비스(Facebook, Twitter, Office 365, SalesForce)에 연결하기 위한 OAuth 보안 처리.
2. 서비스 버스 릴레이를 통한 온-프레미스 자산 연결.
3. 데이터를 보내고 받기 위한 일반 프로토콜 연결.

##커넥터 및 논리 앱##
논리 앱은 트리거와 작업으로 구성됩니다. 일부 커넥터는 새 데이터를 사용할 수 있는지 확인하고 해당 데이터를 입력으로 사용하여 논리 앱을 트리거하기 위한 트리거로 사용할 수 있습니다. 커넥터를 논리 앱 중간에 작업으로 사용하여 데이터 조회, 데이터 기록 또는 커넥터가 지원하는 기타 작업을 수행할 수도 있습니다.

###트리거로 사용되는 커넥터###
일부 커넥터는 논리 앱에 대한 트리거를 제공합니다. 다음 두 가지 유형의 트리거가 있습니다.

1. 폴링 트리거: 이 트리거는 지정된 빈도로 관심 있는 서비스를 폴링하여 새 데이터를 확인합니다. 새 데이터를 사용할 수 있으면 논리 앱의 새 인스턴스가 해당 데이터를 입력으로 사용하여 실행됩니다. 트리거는 읽어서 논리 앱으로 전달된 데이터 정리와 같은 추가 작업을 수행하여 동일한 데이터가 여러 번 사용되지 않도록 할 수도 있습니다. 이러한 커넥터의 예로 파일, SQL 및 Azure 저장소가 있습니다.
2. 푸시 트리거: 이 트리거는 끝점에서 데이터를 수신 대기하거나 발생하는 이벤트를 수신 대기하고 논리 앱의 새 인스턴스를 트리거합니다. 이러한 커넥터의 예로 HTTP 수신기와 Twitter가 있습니다.

###작업으로 사용되는 커넥터###
커넥터를 논리 앱의 일부인 작업으로 사용할 수도 있습니다. 이 기능은 논리 앱에서 실행에 사용할 데이터를 조회하는 데 유용할 수 있습니다. 예를 들어 논리 앱에서 고객의 주문을 처리할 때 고객에 대한 추가 정보를 SQL 데이터베이스에서 조회해야 할 수 있습니다. 또는 커넥터가 제공하는 작업을 사용할 수 있는 대상에서 데이터를 기록, 업데이트 또는 삭제해야 할 수도 있습니다.

##커넥터 사용 방법##
다음 문서에서는 일부 기본 커넥터를 사용하는 방법의 예를 제공합니다.

* [HTTP 커넥터 사용]
* [FTP 커넥터 사용]
* [Box 커넥터 사용]
* [Azure 저장소 Blob 커넥터 사용]
* [Facebook 커넥터 사용]
* [Twitter 커넥터 사용]
* [Salesforce 커넥터 사용]
* [Office 365 커넥터 사용]
* [Sharepoint 커넥터 사용]
* [SAP 커넥터 사용]

자세한 내용은 커넥터 API 참조[http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference)를 참조하세요.


<!-- Links -->

[Box 커넥터 사용]: app-service-logic-connector-box.md
[Facebook 커넥터 사용]: app-service-logic-connector-facebook.md
[Salesforce 커넥터 사용]: app-service-logic-connector-salesforce.md
[Twitter 커넥터 사용]: app-service-logic-connector-twitter.md
[SAP 커넥터 사용]: app-service-logic-connector-sap.md
[FTP 커넥터 사용]: app-service-logic-connector-ftp.md
[HTTP 커넥터 사용]: app-service-logic-connector-http.md
[Azure 저장소 Blob 커넥터 사용]: app-service-logic-connector-azurestorageblob.md
[Office 365 커넥터 사용]: app-service-logic-connector-office365.md
[Sharepoint 커넥터 사용]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->