<properties 
   pageTitle="BizTalk 거래 업체 관리" 
   description="BizTalk 거래 업체 관리" 
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
   ms.date="06/14/2015"
   ms.author="rajram"/>

#BizTalk 거래 업체 관리
Microsoft Azure TPM(거래 업체 관리) 서비스를 사용하면 스키마, 인증서와 같은 아티팩트와 함께 파트너 및 규약과 같은 기업 간 관계를 정의하고 유지할 수 있습니다. 이러한 관계는 AS2, EDIFACT, X12와 같은 관련 API 서비스를 통해 적용할 수 있습니다.

TPM API 앱은 AS2 커넥터 및 X12 또는 EDIFACT API 앱의 기본 요구 사항입니다.

##필수 구성 요소
- 빈 SQL Azure 데이터베이스 - 새 TPM API 앱을 만들기 전에 빈 SQL Azure 데이터베이스를 만들어야 합니다.

##파트너, 규약 및 프로필 이해
거래 업체 규약에 대해 자세히 알아보려면 [여기][1]를 클릭하세요.

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=August15_HO6-->