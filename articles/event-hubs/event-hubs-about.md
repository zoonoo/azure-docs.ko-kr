---
title: Azure Event Hubs 정의 | Microsoft Docs
description: 초당 수백만 개의 이벤트를 수집하는 빅 데이터 스트리밍 서비스 Azure Event Hubs에 대해 알아봅니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: mvc
ms.date: 08/01/2018
ms.author: shvija
ms.openlocfilehash: f3f9abfb8373e7257237b39d16dfb3f36259ddd5
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281917"
---
# <a name="what-is-azure-event-hubs"></a>Azure Event Hubs 정의

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. 

Event Hubs는 다음과 같은 일반적인 시나리오에 사용됩니다.

- 변칙 검색(사기 행위/이상값)
- 응용 프로그램 로깅
- 클릭스트림 같은 분석 파이프라인
- 라이브 대시보드
- 데이터 보관
- 트랜잭션 처리
- 사용자 원격 분석 처리
- 장치 원격 분석 스트리밍 

## <a name="why-use-event-hubs"></a>Event Hubs를 사용하는 이유

데이터는 쉽게 처리할 수 있고 데이터 원본에서 인사이트를 적시에 얻을 수 있는 경우에만 유용합니다. Event Hubs는 대기 시간이 짧고 매끄럽게 통합되는 분산 스트림 프로세싱 플랫폼을 제공하며, Azure 내부 및 외부에는 완전한 빅 데이터 파이프라인을 만들기 위한 데이터 및 분석 서비스가 있습니다.

Event Hubs는 이벤트 파이프라인의 "현관" 역할을 하며, 솔루션 아키텍처에서는 *이벤트 수집기*라고 부르기도 합니다. 이벤트 ingestor는 이러한 이벤트에서 이벤트 스트림의 프로덕션을 분리하는 이벤트 게시자와 이벤트 소비자 간에 작용하는 구성 요소 또는 서비스입니다. Event Hubs는 통합 스트리밍 플랫폼에 시간 보존 버퍼를 제공하며, 이벤트 소비자로부터 이벤트 생산자를 분리합니다. 

다음 섹션에서는 Azure Event Hubs 서비스의 주요 기능을 설명합니다. 

## <a name="fully-managed-paas"></a>완전히 관리되는 PaaS 

이벤트 허브는 구성 또는 관리 오버헤드가 거의 없는 관리형 서비스이므로 비즈니스 솔루션에 집중할 수 있습니다. [Apache Kafka 에코시스템을 위한 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)는 클러스터를 관리, 구성 또는 실행할 필요가 없는 PaaS Kafka 환경을 제공합니다.

## <a name="support-for-real-time-and-batch-processing"></a>실시간 일괄 처리 지원

스트림을 실시간으로 수집, 버퍼, 저장 및 처리하여 실행 가능한 인사이트를 얻을 수 있습니다. Event Hubs는 [분할된 소비자 모델](event-hubs-features.md#partitions)을 사용하여 여러 응용 프로그램이 스트림을 동시에 처리할 수 있도록 지원하며, 처리 속도를 사용자가 제어할 수 있습니다.

[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 또는 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 에서 데이터를 실시간으로 [캡처](event-hubs-capture-overview.md)하여 데이터를 장기 보존하거나 마이크로 일괄 처리할 수 있습니다. 이 작업은 실시간 분석 파생에 사용하는 것과 동일한 스트림에서 수행할 수 있습니다. 캡처는 빠르게 설정할 수 있으며 실행을 위한 관리 비용이 없고 Event Hubs  [처리량 단위](event-hubs-features.md#throughput-units)에 따라 크기가 자동으로 조정됩니다. Event Hubs 캡처를 통해 데이터 캡처보다 데이터 처리에 집중할 수 있습니다.

Azure Event Hubs를 [Azure Functions](/azure/azure-functions/)와 통합하여 서버 없는 아키텍처를 구현할 수도 있습니다.

## <a name="scalable"></a>확장성 

Event Hubs를 사용하여 메가바이트 단위로 데이터 스트림을 시작하고 이후에 기가바이트 또는 테라바이트 단위로 확장할 수 있습니다. [자동 팽창](event-hubs-auto-inflate.md) 기능은 사용량 요구 사항을 충족하도록 처리량 단위 수를 조정하는 데 사용되는 여러 옵션 중 하나입니다. 

## <a name="rich-ecosystem"></a>풍부한 에코시스템

[Apache Kafka 에코시스템을 위한 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)를 사용하면 클러스터를 관리할 필요 없이 [Apache Kafka(1.0 이상)](https://kafka.apache.org/) 클라이언트 및 응용 프로그램이 Event Hubs와 통신합니다.
 
방대한 에코시스템이 다양한 [언어(.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs)로 제공되므로 Event Hubs에서 간편하게 스트림 처리를 시작할 수 있습니다. 지원되는 모든 클라이언트 언어는 하위 수준 통합을 제공합니다. 또한 에코시스템은 Stream Analytics 및 Azure Functions 같은 Azure 서비스와 중단 없이 결합되어 서버리스 아키텍처를 빌드할 수 있습니다.

## <a name="key-architecture-components"></a>주요 아키텍처 구성 요소

Event Hubs는 메시지 스트림 처리 기능을 제공하지만 기존 엔터프라이즈 메시지와 다른 특징을 가지고 있습니다. Event Hubs 기능은 높은 처리량 및 이벤트 처리 시나리오를 중심으로 구축됩니다. Event Hubs에는 다음과 같은 [주요 구성 요소](event-hubs-features.md)가 포함되어 있습니다.

- **이벤트 생산자**: 이벤트 허브에 데이터를 보내는 엔터티입니다. 이벤트 게시자는 HTTPS 또는 AMQP 1.0 또는 Apache Kafka 1.0 이상을 사용하여 이벤트를 게시할 수 있습니다.
- **파티션**: 각 소비자는 메시지 스트림의 특정 하위 집합 또는 파티션만 읽습니다.
- **소비자 그룹**: 전체 이벤트 허브의 보기(상태, 위치 또는 오프셋)입니다. 소비자 그룹은 여러 소비 응용 프로그램을 사용하여 이벤트 스트림의 별도 보기가 있으며 자신의 속도 및 자신의 오프셋으로 독립적으로 스트림을 읽을 수 있습니다.
- **처리량 단위**: Event Hubs의 처리량 용량을 제어하는 미리 구입한 용량 단위입니다.
- **이벤트 수신기**: 이벤트 허브에서 이벤트 데이터를 읽는 엔터티입니다. 모든 Event Hubs 소비자는 AMQP 1.0 세션을 통해 연결되며, 사용 가능한 상태가 되면 이 세션을 통해 이벤트가 전달됩니다. 모든 Kafka 소비자는 Kafka 프로토콜 1.0 이상을 통해 연결합니다.

다음 그림에서는 아키텍처를 처리하는 Event Hubs 스트림을 보여 줍니다.

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>다음 단계

Event Hubs를 시작하려면 다음 문서를 참조하세요.

1. **이벤트 허브 만들기**: [Azure portal](event-hubs-create.md), [Azure CLI](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [Azure Resource Manager 템플릿](event-hubs-resource-manager-namespace-event-hub.md)
2. **이벤트 허브로 이벤트 전송**: [.NET 표준](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
3. **이벤트 허브에서 이벤트 수신**: [.NET 표준](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)   

Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Event Hubs 기능 개요](event-hubs-features.md)
- [질문과 대답](event-hubs-faq.md)


