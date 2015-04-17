<properties 
	pageTitle="커넥터 및 BizTalk API 앱 정의" 
	description="API 앱, 커넥터 및 BizTalk API 앱에 대해 알아봅니다." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# 커넥터 및 BizTalk API 앱 정의

Azure 앱 서비스는 API 앱을 통한 일반적인 연결 및 확장성의 원칙을 기반으로 하여 빌드되었습니다. *Connector*는 연결에 중점을 두는 API 앱 유형입니다. 다른 API 앱과 마찬가지로 커넥터는 웹앱, 모바일 앱 및 논리 앱에서 사용됩니다. 커넥터는 기존 서비스에 쉽게 연결할 수 있게 해주며 인증 관리에 도움이 되고 모니터링, 분석 등을 제공합니다.

모든 개발자는 고유한 API 앱을 만들고 사적으로 배포할 수 있으며, 나중에 마켓플레이스를 통해 앱을 공유하고 수익을 창출할 수 있습니다. 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

개발자가 Azure 앱 서비스로 솔루션을 신속하게 빌드할 수 있도록 Microsoft Azure 팀은 여러 일반적인 시나리오를 충족하는 많은 커넥터를 마켓플레이스에 추가했습니다. 뿐만 아니라 앱 서비스의 도달 범위를 복잡한 고급 통합 시나리오까지 확장하기 위해 많은 프리미엄 및 BizTalk 기능도 제공됩니다.

[Microsoft Azure 앱 서비스의 커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)


## 프로토콜 커넥터
앱 서비스는 웹에서 유래되었으며 HTTP를 사용하는 통신과 Swagger 같은 개방형 형식을 통한 메타데이터 공유를 선호합니다. 물론, 비즈니스는 다양한 프로토콜을 통해 통신해야 합니다. 프로토콜 커넥터는 그 차이를 좁히고 FTP, SFTP, POP3/IMAP, SMTP 및 SOAP 서비스를 사용하여 HTTP 호출만큼 쉽게 서비스와 통신하도록 할 수 있습니다.  

[Microsoft Azure 앱 서비스의 프로토콜 커넥터](app-service-logic-protocol-connectors.md)


## SaaS 커넥터
앱 서비스의 SaaS 커넥터는 Office 365, SalesForce, Sugar CRM, OneDrive, DropBox, Marketo, Facebook 등을 비롯한 오늘날 SaaS의 가장 유명한 이름들에 웹앱, 모바일 앱 및 논리 앱을 연결하기 위한 완성 인도 방식을 제공합니다.

[Microsoft Azure 앱 서비스의 소셜 커넥터](app-service-logic-social-connectors.md)

[Microsoft Azure 앱 서비스의 앱 및 데이터 서비스 커넥터](app-service-logic-data-connectors.md)


## 프리미엄 커넥터 
프리미엄 커넥터는 앱 서비스의 도달 범위를 SAP, Siebel, Oracle, DB2 등에 대한 연결이 있는 엔터프라이즈까지 확장합니다.

[Microsoft Azure 앱 서비스의 엔터프라이즈 커넥터](app-service-logic-enterprise-connectors.md)


## BizTalk API 앱
중요 업무용 앱을 빌드하려면 연결 이상의 것이 필요합니다. Microsoft의 업계 선도적인 통합 플랫폼인 BizTalk Server를 기반으로 하는 BizTalk API 앱은 웹앱, 모바일 앱 및 논리 앱에 쉽게 맞출 수 있는 고급 통합 기능을 제공합니다. 일괄 처리 및 개별 처리, VETR(유효성 검사, 추출, 변환 및 라우팅) 및 X12, EDIFACT, AS2 등의 EDI 형식 지원을 포함합니다.

[Microsoft Azure 앱 서비스의 B2B 커넥터 및 API 앱](app-service-logic-b2b-connectors.md)<br/>

[Microsoft Azure 앱 서비스의 BizTalk 통합 API 앱](app-service-logic-integration-connectors.md)

<!--HONumber=49-->