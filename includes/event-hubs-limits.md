---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901651"
---
다음 표는 [Azure Event Hubs에](https://azure.microsoft.com/services/event-hubs/)특정한 할당량 및 제한을 제공합니다. 이벤트 허브 가격에 대한 자세한 내용은 [이벤트 허브 가격을](https://azure.microsoft.com/pricing/details/event-hubs/)참조하십시오.

다음 제한은 기본, 표준 및 전용 계층에서 공통적입니다. 

| 제한 | Scope | 메모 | 값 |
| --- | --- | --- | --- |
| 구독당 Event Hubs 네임스페이스 수 |Subscription |- |100 |
| 네임스페이스당 Event Hubs 개수 |네임스페이스 |이후 새 이벤트 허브 만들기 요청이 거부됩니다. |10 |
| Event Hub당 파티션 수 |엔터티 |- |32 |
| Event Hub 이름의 최대 크기 |엔터티 |- |50자 |
| 소비자 그룹당 비 Epoch 수신자 수 |엔터티 |- |5 |
| 최대 처리량 단위 |네임스페이스 |처리량 단위 제한을 초과하면 데이터가 제한되고 [서버 사용 중 예외가](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)생성됩니다. 표준 계층에 대해 더 많은 수의 처리량 단위를 요청하려면 [지원 요청을](/azure/azure-portal/supportability/how-to-create-azure-support-request)제출합니다. [추가 처리량 단위](../articles/event-hubs/event-hubs-auto-inflate.md)는 약정된 구매를 기준으로 20개 단위로 사용할 수 있습니다. |20 |
| 네임스페이스당 권한 부여 규칙의 수 |네임스페이스|권한 부여 규칙 만들기에 대한 후속 요청은 거부됩니다.|12 |
| GetRuntimeInformation정보 메서드에 대한 호출 수 | 엔터티 | - | 초당 50 | 
| 가상 네트워크(VNet) 및 IP 구성 규칙 수 | 엔터티 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>이벤트 허브 기본 및 표준 - 할당량 및 제한
| 제한 | Scope | 메모 | Basic | Standard |
| --- | --- | --- | -- | --- |
| Event Hubs 이벤트의 최대 크기|엔터티 | &nbsp; | 256 KB | 1MB |
| Event Hub당 소비자 그룹 수 |엔터티 | &nbsp; |1 |20 |
| 네임스페이스당 AMQP 연결 수 |네임스페이스 |추가 연결에 대한 후속 요청이 거부되고 호출 코드에서 예외가 수신됩니다. |100 |5,000|
| 이벤트 데이터의 최대 보존 기간 |엔터티 | &nbsp; |1일 |1-7일 |
|아파치 카프카 사용 네임스페이스|네임스페이스 |이벤트 허브 네임스페이스는 Kafka 프로토콜을 사용하여 응용 프로그램을 스트리밍합니다. |예 | yes |
|캡처 |엔터티 | 활성화된 경우 동일한 스트림에서 마이크로 일괄 처리 |예 |yes |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>이벤트 허브 전용 - 할당량 및 제한
이벤트 허브 전용 오퍼링은 매월 고정 요금으로 청구되며 최소 4시간 사용시 청구됩니다. 전용 계층은 표준 계획의 모든 기능을 제공하지만 까다로운 워크로드를 가진 고객을 위한 엔터프라이즈 규모 용량 및 제한 기능을 제공합니다. 

| 기능 | 제한 |
| --- | ---|
| 대역폭 |  20개의 쿠스 |
| 네임스페이스 | CU당 50개 |
| Event Hubs |  네임스페이스당 1000개 |
| 수신 이벤트 | 포함 |
| 메시지 크기 | 1MB |
| 파티션 | CU당 2000개 |
| 소비자 그룹 | CU당 제한 없음, 이벤트 허브당 1000개 |
| 조정된 연결 | 100,000개 포함 |
| 메시지 보존 | 90일, CU당 10TB 포함 |
| 캡처 | 포함 |
