<properties 
   pageTitle="논리 앱에서 BizTalk 거래 업체 관리 커넥터 사용 | Microsoft Azure 앱 서비스"
	description="BizTalk 거래 업체 관리 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="rajram"/>

# BizTalk 거래 업체 관리 시작 및 논리 앱에 추가
BizTalk TPM(거래 업체 관리) 서비스를 사용하면 스키마, 인증서와 같은 아티팩트와 함께 파트너 및 규약과 같은 기업 간 관계를 정의하고 유지할 수 있습니다. 이러한 관계는 AS2, EDIFACT, X12와 같은 관련 API 서비스를 통해 적용할 수 있습니다.

TPM API 앱은 AS2 커넥터, X12 API 앱 및 EDIFACT API 앱의 기본 요구 사항입니다. BizTalk 거래 업체 관리를 비즈니스 워크플로에 추가하고 논리 앱 내에서 B2B 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 필수 조건
- 빈 SQL Azure 데이터베이스 - 새 TPM API 앱을 만들기 전에 빈 SQL Azure 데이터베이스를 만들어야 합니다.

## 파트너, 규약 및 프로필 이해
거래 업체 규약에 대해 자세히 알아보려면 [여기][1]를 클릭하세요.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=August15_HO9-->