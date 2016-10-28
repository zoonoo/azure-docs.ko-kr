<properties 
	pageTitle="엔터프라이즈 통합 팩에서 XML 유효성 검사 개요 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="유효성 검사가 엔터프라이즈 통합 팩 및 논리 앱에서 작동하는 방법 알아보기" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# XML 유효성과 엔터프라이즈 통합

## 개요
종종 B2B 시나리오에서 규약의 파트너는 데이터의 처리를 시작하기 전에 서로 교환한 메시지가 유효한지 검사해야 합니다. 엔터프라이즈 통합 팩에서는 XML 유효성 검사 커넥터를 사용하여 미리 정의된 스키마에 대한 문서의 유효성을 검사할 수 있습니다.

## XML 유효성 검사 커넥터와 문서의 유효성을 검사하는 방법
1. 논리 앱을 만들고 XML 데이터의 유효성을 검사하는 데 사용할 스키마를 포함하는 [통합 계정에 연결](./app-service-logic-enterprise-integration-accounts.md "논리 앱에 통합 계정을 연결하는 방법 알아보기")합니다.
2. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다. ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)
3. 먼저 **작업 추가**를 선택하여 **XML 유효성 검사** 작업을 추가합니다.
4. 사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 *xml*을 입력합니다.
5. **XML 유효성 검사**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)
6. **콘텐츠** 텍스트 상자를 선택합니다. ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. 본문 태그를 유효성을 검사할 콘텐츠로 선택합니다. ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)
8. **스키마 이름** 목록 상자를 선택하고 위에 있는 입력 *콘텐츠*의 유효성을 검사하는 데 사용할 스키마를 선택합니다. ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png)
9. 작업을 저장합니다. ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png)

이 시점에서 유효성 검사 커넥터의 설정이 완료됩니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB 응용 프로그램에 유효성 검사한 데이터를 저장할 수 있습니다. Salesforce에 유효성 검사의 출력을 보내는 작업을 쉽게 추가할 수 있습니다.

이제 HTTP 끝점에 요청하여 유효성 검사 작업을 테스트할 수 있습니다.

## 다음 단계

[엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

<!---HONumber=AcomDC_0803_2016-->