<properties 
   pageTitle="Azure 앱 서비스의 논리 앱에서 B2B 메시지 추적 | Microsoft Azure" 
   description="이 항목에서는 B2B 처리 추적에 대해 설명합니다." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>


# B2B 메시지 추적

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

## B2B 추적 정보
B2B 통신에는 거래 업체 간의 메시지 처리가 필요합니다. 관계는 두 거래 업체 간의 계약으로 정의됩니다. 통신이 설정되면 통신이 예상대로 수행되는지 모니터링할 수 있는 방법이 있어야 합니다.

AS2, EDIFACT 및 X12 등 다음 B2B 시나리오에 대한 메시지 추적을 구현했습니다.

## AS2
AS2 API 앱의 인스턴스를 만들면 해당 인스턴스를 찾아서 **추적**을 선택합니다. 여기에서 모든 AS2 추적 정보를 보고 필터링할 수 있습니다.

![][1]

## EDIFACT
EDIFACT API 앱의 인스턴스를 만들면 해당 인스턴스를 찾아서 **추적**을 선택합니다. 여기에서 모든 EDIFACT 추적 정보를 보고 필터링할 수 있습니다. 또한 교환 수준, 그룹 수준 및 트랜잭션 집합 수준 데이터를 하나의 보기로 볼 수 있습니다.

연결된 거래 업체 관리 API 앱에서 EDIFACT 계약의 일부로 배치가 만들어진 경우 배치 섹션에는 이러한 배치가 모두 나열됩니다. 배치를 선택하여 활성화된 메시지(있는 경우) 및 완료된 작업에 대한 정보를 볼 수 있습니다.

![][2]

## X12
X12 API 앱 인스턴스를 만들면 해당 인스턴스를 찾아서 **추적**을 선택합니다. 여기에서 모든 X12 추적 정보를 보고 필터링할 수 있습니다. 또한 교환 수준, 그룹 수준 및 트랜잭션 집합 수준 데이터를 하나의 보기로 볼 수 있습니다.

연결된 거래 업체 관리 API 앱에서 X12 계약의 일부로 배치가 만들어진 경우 배칭 섹션에는 이러한 배치가 모두 나열됩니다. 배치를 선택하여 활성화된 메시지(있는 경우) 및 완료된 배치에 대한 정보를 볼 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png

<!---HONumber=AcomDC_0727_2016-->