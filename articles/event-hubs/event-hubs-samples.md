---
title: Azure Event Hubs 샘플 | Microsoft Docs
description: Azure Event Hubs 샘플
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: shvija
ms.openlocfilehash: fbde6e5a5ed053d6c151b3af25535c397a496ef4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005337"
---
# <a name="event-hubs-samples"></a>Event Hubs 샘플 
[GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)에 대한 Event Hubs 샘플을 찾을 수 있습니다. 이러한 샘플은 [Azure Event Hubs](/azure/event-hubs/)의 주요 기능을 보여줍니다. 이 문서는 사용할 수 있는 샘플 각각을 링크를 사용하여 범주화하고 설명합니다.

## <a name="net-samples"></a>.NET 샘플

| 샘플 이름 | 설명 | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | 이 샘플에서는 이벤트 허브로 이벤트 집합을 전송하는 .NET Core 콘솔 응용 프로그램을 작성하는 방법을 보여줍니다. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | 이 샘플에서는 이벤트 프로세서 호스트 라이브러리를 사용하여 이벤트 허브에서 이벤트 집합을 수신하는 .NET Core 콘솔 응용 프로그램을 작성하는 방법을 보여줍니다.  | 

## <a name="java-samples"></a>Java 샘플

| 샘플 이름 | 설명 | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | 이 샘플에서는 이벤트 허브로 이벤트의 일괄 처리를 수집하는 방법을 보여줍니다. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | 이 샘플에서는 이벤트 허브로 이벤트를 수집하는 방법을 보여줍니다. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | 이 샘플에서는 이벤트를 수집하기 위해 Event Hubs에서 사용할 수 있는 다양한 옵션을 보여줍니다. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | 이 샘플에서는 특정 날짜-시간 오프셋을 사용하여 이벤트 허브 파티션에서 이벤트를 수신하는 방법을 보여줍니다. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | 이 샘플에서는 특정 데이터 오프셋을 사용하여 이벤트 허브 파티션에서 이벤트를 수신하는 방법을 보여줍니다. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | 이 샘플에서는 시퀀스 번호를 사용하여 이벤트 허브 파티션에서 수신하는 방법을 보여줍니다. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |이 샘플에서는 여러 동시 수신기 간에 자동 파티션 선택 및 장애 조치를 제공하는 이벤트 프로세서 호스트를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 보여줍니다. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | 이 샘플에서는 이벤트 허브가 고부하에서 자동으로 강화되는 방법을 보여줍니다. 예제는 이벤트 허브의 구성 속도를 초과하는 속도로 이벤트를 보내 이벤트 허브를 강화해줍니다. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | 이 샘플에서는 수신 속도를 측정할 수 있습니다. | 

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
- [Event Hubs 기능](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)