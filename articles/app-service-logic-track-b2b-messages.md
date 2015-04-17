<properties 
   pageTitle="B2B 메시지 추적" 
   description="이 항목에서는 B2B 처리 추적에 대해 설명합니다." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="hariag"/>


# B2B 메시지 추적

## B2B 추적 정보
B2B 통신에는 거래 업체 간의 메시지 처리가 필요합니다. 관계는 두 거래 업체 간의 계약으로 정의됩니다. 통신이 설정되고 나면 통신이 예상대로 수행되는지 모니터링할 수 있는 방법이 있어야 합니다. 
B2B API 앱을 Azure 앱 서비스의 일부로 통합하는 과정에서 추적 데이터를 사용하도록 설정했으며 동일한 설정이 Azure 포털을 통해서도 노출됩니다. 

## AS2
AS2 API 앱 인스턴스를 만든 후 해당 인스턴스를 찾아서 추적 파트로 이동합니다. 여기서 모든 AS2 추적 정보를 볼 수 있으며 노출된 필터 블레이드를 통해 필터링할 수도 있습니다.

![][1]  

## EDIFACT
EDIFACT API 앱 인스턴스를 만든 후 해당 인스턴스를 찾아서 추적 파트로 이동합니다. 여기서 모든 EDIFACT 추적 정보를 볼 수 있으며 노출된 필터를 통해 필터링할 수도 있습니다.
또한 교환 수준 데이터, 그룹 수준 데이터 및 트랜잭션 집합 수준 데이터를 하나의 보기 단계로 볼 수 있습니다. 

연결된 거래 업체 관리 API 앱에서 EDIFACT 계약의 일부로 배치가 만들어진 경우 일괄 처리 파트에 이러한 배치가 모두 나열됩니다. 배치를 한 단계씩 코드 실행하여 활성 메시지를 구성하는 메시지(있는 경우) 및 과거에 완료된 배치에 대한 정보를 확인할 수도 있습니다.

![][2]      

## X12
X12 API 앱 인스턴스를 만든 후 해당 인스턴스를 찾아서 추적 파트로 이동합니다. 여기서 모든 X12 추적 정보를 볼 수 있으며 노출된 필터를 통해 필터링할 수도 있습니다.
또한 교환 수준 데이터, 그룹 수준 데이터 및 트랜잭션 집합 수준 데이터를 하나의 보기 단계로 볼 수 있습니다. 

연결된 거래 업체 관리 API 앱에서 X12 계약의 일부로 배치가 만들어진 경우 일괄 처리 파트에 이러한 배치가 모두 나열됩니다. 배치를 한 단계씩 코드 실행하여 활성 메시지를 구성하는 메시지(있는 경우) 및 과거에 완료된 배치에 대한 정보를 확인할 수도 있습니다. 

X12 및 EDIFACT에는 유사한 추적 뷰가 있습니다. 

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!--HONumber=49-->