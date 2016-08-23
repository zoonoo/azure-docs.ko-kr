<properties 
	pageTitle="파트너 및 엔터프라이즈 통합 팩 개요 | Microsoft Azure 앱 서비스" 
	description="엔터프라이즈 통합 팩 및 논리 앱에서 파트너를 사용하는 방법 알아보기" 
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
	ms.date="06/29/2016" 
	ms.author="deonhe"/>


# 규약 및 엔터프라이즈 통합 팩에 대해 알아보기

## 개요
규약은 B2B(기업 간) 통신의 토대이며 산업 표준 프로토콜을 사용하여 비즈니스 엔터티가 원활하게 통신할 수 있도록 합니다.

## 규약은 무엇입니까?

적어도 엔터프라이즈 통합 팩에 관해서 규약은 B2B 거래 파트너 간의 통신 배열입니다. 규약은 파트너가 수행하려고 하는 통신을 기반으로 하며 특정 프로토콜 또는 전송입니다.

엔터프라이즈 통합은 다음과 같은 세 가지 프로토콜/전송 표준을 지원합니다.

- [AS2](./app-service-logic-enterprise-integration-as2.md)
- [X12](./app-service-logic-enterprise-integration-x12.md)
- [EDIFACT](./app-service-logic-enterprise-integration-edifact.md)

## 규약을 사용하는 이유
규약을 사용하는 일반적인 이점은 다음과 같습니다.
- 서로 다른 조직 및 비즈니스가 잘 알려진 형식으로 정보를 교환할 수 있습니다.
- B2B 트랜잭션을 수행할 경우 효율성을 향상시킵니다.
- 엔터프라이즈 통합 앱을 생성할 경우 쉽게 작성, 관리 및 사용할 수 있습니다.

## 규약 생성 방법
- [AS2 규약 만들기](./app-service-logic-enterprise-integration-as2.md)
- [X12 규약 만들기](./app-service-logic-enterprise-integration-x12.md)

## 규약 사용 방법
규약을 만든 후에 Azure 포털을 통해 규약을 사용하여 B2B 기능을 포함한 [논리 앱](./app-service-logic-what-are-logic-apps.md "논리 앱에 대해 알아보기")을 만들 수 있습니다.

## 규약 편집 방법
다음 단계를 수행하여 모든 규약을 편집할 수 있습니다.
1. 수정하려는 규약을 포함하는 통합 계정을 선택합니다.
2. **규약** 타일을 선택합니다.
3. **규약** 블레이드에서 수정하려는 규약을 선택합니다.
4. 위의 메뉴에서 **편집**을 선택합니다.
5. 열린 편집 메뉴에서 변경 사항을 수행하고 **확인** 단추를 선택하여 변경 내용을 저장합니다.

## 규약 삭제 방법
삭제하려는 규약을 포함하는 통합 계정 내에서 이러한 단계를 수행하여 모든 규약을 삭제할 수 있습니다.
1. **규약** 타일을 선택합니다.
2. **규약** 블레이드에서 삭제하려는 규약을 선택합니다.
3. 위의 메뉴에서 **삭제**를 선택합니다.
4. 정말로 규약을 삭제할지 확인합니다.
5. 규약이 규약 블레이드에 더 이상 나열되지 않습니다.
 

## 다음 단계
- [AS2 규약 만들기](./app-service-logic-enterprise-integration-as2.md)

<!---HONumber=AcomDC_0810_2016-->