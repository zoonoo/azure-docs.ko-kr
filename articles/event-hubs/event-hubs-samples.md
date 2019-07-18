---
title: 샘플 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 GitHub에 있는 Azure Event Hubs에 대한 샘플 목록을 제공합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 1c1198733fb56303d328ee97152442d25dbe945a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60343480"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>Azure Event Hubs에 대한 샘플이 있는 Git 리포지토리 
[GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)에 대한 Event Hubs 샘플을 찾을 수 있습니다. 이러한 샘플은 [Azure Event Hubs](/azure/event-hubs/)의 주요 기능을 보여줍니다. 이 문서는 사용할 수 있는 샘플 각각을 링크를 사용하여 범주화하고 설명합니다.

## <a name="net-samples"></a>.NET 샘플

| 샘플 이름 | 설명 | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | 이 샘플에서는 이벤트 허브로 이벤트 집합을 전송하는 .NET Core 콘솔 애플리케이션을 작성하는 방법을 보여줍니다. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | 이 샘플에서는 이벤트 프로세서 호스트 라이브러리를 사용하여 이벤트 허브에서 이벤트 집합을 수신하는 .NET Core 콘솔 애플리케이션을 작성하는 방법을 보여줍니다.  | 

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

## <a name="python-samples"></a>Python 샘플
[azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) GitHub 리포지토리에서 Azure Event Hubs에 대한 Python 샘플을 찾을 수 있습니다.

## <a name="nodejs-samples"></a>Node.js 샘플
Azure Event Hubs에 대 한 Node.js 샘플을 찾을 수 합니다 [js에 대 한 azure sdk](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) GitHub 리포지토리.

## <a name="go-samples"></a>Go 샘플
[azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples) GitHub 리포지토리에서 Azure Event Hubs에 대한 Go 샘플을 찾을 수 있습니다.

## <a name="azure-cli-samples"></a>Azure CLI 샘플
[azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI) GitHub 리포지토리에서 Azure Event Hubs에 대한 Azure CLI 샘플을 찾을 수 있습니다.

## <a name="azure-powershell-samples"></a>Azure PowerShell 샘플
[azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell) GitHub 리포지토리에서 Azure Event Hubs에 대한 Azure PowerShell 샘플을 찾을 수 있습니다.
 
## <a name="apache-kafka-samples"></a>Apache Kafka 샘플
[azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) GitHub 리포지토리에서 Apache Kafka용 Event Hubs 기능에 대한 샘플을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
- [Event Hubs 기능](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)