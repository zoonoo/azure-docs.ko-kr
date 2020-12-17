---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bc6b7553d240de05404d24f828a5f7db14772f93
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657510"
---
## <a name="what-is-a-replication-task"></a>복제 작업 이란?

복제 작업은 원본에서 이벤트를 수신 하 고 대상으로 전달 합니다.
대부분의 복제 작업은 변경 되지 않은 이벤트를 전달 하 고 원본 및 대상 프로토콜이 다른 경우 메타 데이터 구조 간에 대부분의 매핑을 수행 합니다. 

복제 태스크는 일반적으로 상태 비저장입니다. 즉, 작업의 순차적 또는 병렬 실행에 대 한 상태 또는 기타 부작용을 공유 하지 않습니다. 일괄 처리 및 연결의 경우에도 마찬가지입니다 .이는 모두 스트림의 기존 상태를 기반으로 구현 될 수 있습니다. 

이를 통해 복제 작업은 일반적으로 상태를 저장 하는 집계 작업과 다르며 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction.md)와 같은 분석 프레임 워크 및 서비스의 도메인입니다.

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Azure Functions의 복제 응용 프로그램 및 태스크

Azure Functions에서 복제 태스크는 구성 된 원본에서 하나 이상의 입력 메시지를 가져오는 [트리거와](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings.md) 원본에서 구성 된 대상으로 복사 된 메시지를 전달 하는 [출력 바인딩을](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings.md#binding-direction) 사용 하 여 구현 됩니다. 

| 트리거  | 출력 |
|----------|--------|
| [Azure Event Hubs 트리거](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Azure Event hubs 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Azure Service Bus 트리거](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Azure Service Bus 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Azure IoT Hub 트리거](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Azure IoT Hub 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Azure Event Grid 트리거](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Azure Event Grid 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Azure Queue Storage 트리거](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Azure Queue Storage 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Apache Kafka 트리거](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka 출력 바인딩](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ 트리거](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ 출력 바인딩](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure Notification Hubs 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Azure SignalR 서비스 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Twilio SendGrid 출력 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

복제 작업은 다른 Azure Functions 응용 프로그램과 동일한 배포 방법으로 복제 응용 프로그램에 배포 됩니다. 동일한 응용 프로그램에 여러 작업을 구성할 수 있습니다. 

Azure Functions 프리미엄을 사용 하면 여러 복제 응용 프로그램이 App Service 계획 이라고 하는 동일한 기본 리소스 풀을 공유할 수 있습니다. 즉, 예를 들어 Java로 작성 된 복제 태스크를 사용 하 여 .NET으로 작성 된 복제 작업을 쉽게 배치 수 있습니다. 이는 다른 복제 작업에 다른 언어와 런타임을 자주 선호 하는 경우에도 Java에만 사용할 수 있는 Apache 낙 타와 같은 특정 라이브러리와 특정 통합 경로에 가장 적합 한 옵션을 활용 하려는 경우에 해당 합니다. 

사용할 수 있는 경우 개별 이벤트 나 메시지를 전달 하는 트리거에서 일괄 처리 지향 트리거를 사용 하는 것이 좋습니다 .이 경우에는 항상 Azure Function의 [매개 변수 바인딩 식을](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns)사용 하는 대신 완전 한 이벤트 또는 메시지 구조를 가져와야 합니다.

함수 이름에는 연결 하려는 소스와 대상의 쌍이 반영 되어야 하며,이 이름을 사용 하 여 응용 프로그램 구성 파일에서 연결 문자열 또는 기타 구성 요소에 대 한 참조를 접두사로 사용 해야 합니다. 

### <a name="data-and-metadata-mapping"></a>데이터 및 메타 데이터 매핑

한 쌍의 입력 트리거 및 출력 바인딩을 결정 한 후에는 트리거와 출력이 동일한 경우를 제외 하 고는 다른 이벤트 또는 메시지 유형 간에 몇 가지 매핑을 수행 해야 합니다.

Event Hubs와 Service Bus 간에 메시지를 복사 하는 간단한 복제 작업의 경우 사용자 고유의 코드를 작성할 필요가 없지만 복제 샘플과 함께 제공 되는 [유틸리티 라이브러리](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 를 사용할 수 있습니다.

### <a name="retry-policy"></a>재시도 정책

복제 함수 양쪽의 가용성 이벤트 중 데이터 손실을 방지 하려면 다시 시도 정책을 강력 하 게 구성 해야 합니다. 재시도 정책 구성에 [대 한 Azure Functions 설명서](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md) 를 참조 하십시오. 

[샘플 리포지토리의](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) 예제 프로젝트에 대해 선택한 정책 설정은 데이터 손실을 방지 하기 위해 재시도 간격을 5 초에서 15 분으로 설정 하 여 지 수 백오프 전략을 구성 합니다. 

Service Bus의 경우 큐의 상호 작용 및 큐에 대해 정의 된 최대 배달 횟수를 이해 하려면 ["트리거 복원 력 맨 위에 있는 재시도 지원 사용"](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) 섹션을 검토 합니다.

### <a name="setting-up-a-replication-application-host"></a>복제 응용 프로그램 호스트 설정

복제 응용 프로그램은 하나 이상의 복제 태스크에 대 한 실행 호스트입니다. 

소비 계획에서 실행 되도록 구성 된 Azure Functions 응용 프로그램 이거나 Azure Functions 프리미엄 계획에 대 한 (권장)입니다. 모든 복제 응용 프로그램은 [시스템 또는 사용자 할당 관리 id](https://docs.microsoft.com/azure/app-service/overview-managed-identity.md)로 실행 해야 합니다. 

ARM (연결 된 Azure Resource Manager) 템플릿은 다음을 사용 하 여 복제 응용 프로그램을 만들고 구성 합니다.

* 복제 진행률 및 로그를 추적 하기 위한 Azure Storage 계정
* 시스템 할당 관리 id 
* 모니터링을 위한 Azure 모니터링 및 Application Insights 통합.

Azure VNet (가상 네트워크)에 바인딩된 Event Hubs에 액세스 해야 하는 복제 응용 프로그램은 Azure Functions Premium 요금제를 사용 해야 하며, 사용 가능한 옵션 중 하나인 동일한 VNet에 연결 하도록 구성 해야 합니다.


|       | 배포 | 시각화  
|-------|------------------|--------------|---------------|
| **Azure Functions 소비 계획** | [![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![시각화](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions 프리미엄 계획** |[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![시각화](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **VNet을 사용 하 여 프리미엄 계획 Azure Functions** | [![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![시각화](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>예

[샘플 리포지토리에](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) 는 Event Hubs 및/또는 Service Bus 엔터티 간에 이벤트를 복사 하는 복제 작업의 몇 가지 예제가 포함 되어 있습니다.

Event Hubs 간에 이벤트를 복사 하려면 이벤트 허브 출력 바인딩과 함께 이벤트 허브 트리거를 사용 합니다.

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Service Bus 엔터티 간에 메시지를 복사 하는 경우 Service Bus 트리거 및 출력 바인딩을 사용 합니다.

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

도우미 메서드를 사용 하면 Event Hubs와 Service Bus 간에 쉽게 복제할 수 있습니다.

| 원본      | 대상      | 진입점 
|-------------|-------------|------------------------------------------------------------------------
| 이벤트 허브   | 이벤트 허브   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 이벤트 허브   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | 이벤트 허브   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>모니터링

복제 앱을 모니터링 하는 방법에 대 한 자세한 내용은 Azure Functions 설명서의 [모니터링 섹션](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) 을 참조 하세요.

복제 작업을 모니터링 하기 위한 특히 유용한 시각적 도구는 캡처된 모니터링 정보에서 자동으로 생성 되 고 복제 작업 원본 및 대상 전송의 안정성과 성능을 탐색할 수 있도록 하는 Application Insights [응용 프로그램 맵](https://docs.microsoft.com/azure/azure-monitor/app/app-map)입니다.

즉각적인 진단 정보를 위해 로그 세부 정보를 낮은 대기 시간 시각화를 제공 하는 [라이브 메트릭](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) 포털 도구를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Functions 배포](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies.md)
* [Azure Functions 진단](https://docs.microsoft.com/azure/azure-functions/functions-diagnostics.md)
* [Azure Functions 네트워킹 옵션](https://docs.microsoft.com/azure/azure-functions/functions-networking-options.md)
* [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview.md)