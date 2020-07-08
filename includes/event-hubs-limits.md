---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 333f2317fcc834a10b7336bbda9a43ba16a7ad38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317493"
---
다음 표는 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)의 할당량과 제한을 제공합니다. Event Hubs 가격에 대한 자세한 내용은 [Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요.

다음 제한은 기본 및 표준 계층에서 공통적으로 적용됩니다. 

| 제한 | 범위 | 메모 | 값 |
| --- | --- | --- | --- |
| 구독당 Event Hubs 네임스페이스 수 |Subscription |- |100 |
| 네임스페이스당 Event Hubs 개수 |네임스페이스 |새로운 이벤트 허브 생성에 대한 후속 요청은 거부됩니다. |10 |
| Event Hub당 파티션 수 |엔터티 |- |32 |
| Event Hub 이름의 최대 크기 |엔터티 |- | 256자 |
| 소비자 그룹 이름의 최대 크기 |엔터티 |- | 256자 |
| 소비자 그룹당 비 Epoch 수신자 수 |엔터티 |- |5 |
| 최대 처리량 단위 |네임스페이스 |처리량 단위 제한을 초과하면 데이터가 정체되고 [서버 사용 중 예외](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)가 생성됩니다. 표준 계층에 대해 더 많은 수의 처리량 단위를 요청하려면 [지원 요청](/azure/azure-portal/supportability/how-to-create-azure-support-request)을 작성합니다. [추가 처리량 단위](../articles/event-hubs/event-hubs-auto-inflate.md)는 약정된 구매를 기준으로 20개 단위로 사용할 수 있습니다. |20 |
| 네임스페이스당 권한 부여 규칙의 수 |네임스페이스|권한 부여 규칙 만들기에 대한 후속 요청이 거부됩니다.|12 |
| GetRuntimeInformation 메서드에 대한 호출 수 | 엔터티 | - | 초당 50 | 
| VNet(가상 네트워크) 및 IP 구성 규칙 수 | 엔터티 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic 및 Standard - 할당량 및 제한
| 제한 | 범위 | 메모 | Basic | Standard |
| --- | --- | --- | -- | --- |
| Event Hubs 이벤트의 최대 크기|엔터티 | &nbsp; | 256 KB | 1MB |
| Event Hub당 소비자 그룹 수 |엔터티 | &nbsp; |1 |20 |
| 네임스페이스당 AMQP 연결 수 |네임스페이스 |추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |100 |5,000|
| 이벤트 데이터의 최대 보존 기간 |엔터티 | &nbsp; |1일 |1-7일 |
|Apache Kafka 사용 설정 네임스페이스|네임스페이스 |Kafka 프로토콜을 사용한 Event Hubs 네임스페이스 스트림 애플리케이션 |예 | 예 |
|캡처 |엔터티 | 사용 설정한 경우 동일한 스트림의 마이크로 단위 일괄 처리 |예 |예 |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated - 할당량 및 제한
Event Hubs Dedicated 제품은 고정된 월별 가격으로 청구되며, 최소 사용량은 4시간입니다. Dedicated 계층은 표준 요금제의 모든 기능을 제공합니다. 하지만 워크로드가 까다로운 고객을 위해 엔터프라이즈급 용량 및 제한을 제공합니다. 

| 기능 | 제한 |
| --- | ---|
| 대역폭 |  20개 CU |
| 네임스페이스 | CU당 50개 |
| Event Hubs |  네임스페이스당 1,000개 |
| 수신 이벤트 | 포함 |
| 메시지 크기 | 1MB |
| 파티션 | CU당 2,000개 |
| 소비자 그룹 | CU당 제한 없음, 이벤트 허브당 1,000개 |
| 조정된 연결 | 100,000개 포함 |
| 메시지 보존 | 90일, CU당 10TB 포함 |
| 캡처 | 포함 |
