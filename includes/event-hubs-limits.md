---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ea1ac064799b0cede1de82851a514a2b389f20aa
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499068"
---
다음 표는 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)의 할당량과 제한을 제공합니다. Event Hubs 가격에 대한 자세한 내용은 [Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요.

### <a name="common-limits-for-all-tiers"></a>모든 계층에 대한 공통 제한
다음 제한은 모든 계층에서 공통적으로 적용됩니다. 

| 제한 |  메모 | 값 |
| --- |  --- | --- |
| 구독당 Event Hubs 네임스페이스 수 |- |100 |
| 네임스페이스당 Event Hubs 개수 | 새로운 이벤트 허브 생성에 대한 후속 요청은 거부됩니다. |10 |
| Event Hub당 파티션 수 |- |32 |
| 이벤트 허브 이름의 크기 |- | 256자 |
| 소비자 그룹 이름의 크기 |- | 256자 |
| 소비자 그룹당 비 Epoch 수신자 수 |- |5 |
| 네임스페이스당 권한 부여 규칙의 수 | 권한 부여 규칙 만들기에 대한 후속 요청이 거부됩니다.|12 |
| GetRuntimeInformation 메서드에 대한 호출 수 |  - | 초당 50 | 
| VNet(가상 네트워크) 및 IP 구성 규칙 수 | - | 128 | 


### <a name="basic-and-standard-tiers"></a>기본 및 표준 계층
다음 표에서는 기본 및 표준 계층에 대해 다를 수 있는 제한을 보여줍니다. 

| 제한 | 메모 | Basic | Standard |
| --- |  --- | -- | --- |
| Event Hubs 이벤트의 최대 크기| &nbsp; | 256 KB | 1MB |
| Event Hub당 소비자 그룹 수 | &nbsp; |1 |20 |
| 네임스페이스당 AMQP 연결 수 | 추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |100 |5,000|
| 이벤트 데이터의 최대 보존 기간 | &nbsp; |1일 |1-7일 |
| 최대 처리량 단위 |처리량 단위 제한을 초과하면 데이터가 정체되고 [서버 사용 중 예외](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)가 생성됩니다. 표준 계층에 대해 더 많은 수의 처리량 단위를 요청하려면 [지원 요청](/azure/azure-portal/supportability/how-to-create-azure-support-request)을 작성합니다. [추가 처리량 단위](../articles/event-hubs/event-hubs-auto-inflate.md)는 약정된 구매를 기준으로 20개 단위로 사용할 수 있습니다. |20 | 20 | 

### <a name="dedicated-tier"></a>전용 계층
Event Hubs Dedicated 제품은 고정된 월별 가격으로 청구되며, 최소 사용량은 4시간입니다. Dedicated 계층은 표준 요금제의 모든 기능을 제공합니다. 하지만 워크로드가 까다로운 고객을 위해 엔터프라이즈급 용량 및 제한을 제공합니다. 

Azure Portal을 사용하여 전용 Event Hubs 클러스터를 만드는 방법은 이 [문서](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal)를 참조하세요.

| 기능 | 제한 |
| --- | ---|
| 대역폭 |  20개 CU |
| 네임스페이스 | CU당 50개 |
| 이벤트 허브(영문) |  네임스페이스당 1,000개 |
| 메시지 크기 | 1MB |
| 파티션 | CU당 2,000개 |
| 소비자 그룹 | CU당 제한 없음, 이벤트 허브당 1,000개 |
| 조정된 연결 | 100,000개 포함 |
| 메시지 보존 시간 | 90일, CU당 10TB 포함 |
| 수신 이벤트 | 포함 |
| 캡처 | 포함 |


### <a name="schema-registry-limitations"></a>스키마 레지스트리 제한 사항

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>**표준** 및 **전용** 계층에 대해 동일한 제한 
| 기능 | 제한 | 
| --- |  --- | -- |
| 스키마 그룹 이름의 최대 길이 | 50 |  
| 스키마 이름의 최대 길이 | 100 |    
| 스키마당 크기(바이트) | 1MB |   
| 스키마 그룹당 속성 수 | 1024 |
| 그룹 속성 키당 크기(바이트) | 256 | 
| 그룹 속성 값당 크기(바이트) | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>**표준** 및 **전용** 계층에 대해 다른 제한 

| 제한 | Standard | 전용 | 
| --- |  --- | -- | --- |
| 스키마 레지스트리(네임스페이스) 크기(메가바이트) | 25 |  1024 |
| 스키마 레지스트리(네임스페이스)에 있는 스키마 그룹 수| 1(기본 항목 제외) | 1000 |
| 모든 스키마 그룹의 스키마 버전 수 | 25 | 10000 |





