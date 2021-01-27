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
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901068"
---
Azure Functions를 사용 하 여 미리 작성 된 진입점에 대 한 구성 전용 복제 작업을 만들 수 있습니다. [Azure Functions에 대 한 구성 기반 복제 샘플](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) 에서는 [미리 작성 된 도우미](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 를 사용자 코드에서 활용 하거나 코드를 모두 처리 하지 않고 구성을 사용 하는 방법을 보여 줍니다.

## <a name="create-a-replication-task"></a>복제 태스크 만들기
이러한 복제 작업을 만들려면 먼저 프로젝트 폴더 아래에 새 폴더를 만듭니다. 새 폴더의 이름은 함수의 이름입니다 (예를 들어, 또는의 경우 `europeToAsia` ) `telemetry` . 이 이름에는 사용 중인 메시징 엔터티와의 직접적인 상관 관계가 없으며이를 식별 하는 데에만 사용 됩니다. 동일한 프로젝트에서 수십 개의 함수를 만들 수 있습니다.

다음으로 `function.json` 폴더에 파일을 만듭니다. 파일이 함수를 구성 합니다. 다음 내용으로 시작 합니다.

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

해당 파일에서 연결 하려는 엔터티에 종속 된 세 가지 구성 단계를 완료 해야 합니다.

1. [입력 방향 구성](#configure-the-input-direction)
2. [출력 방향 구성](#configure-the-output-direction)
3. [진입점 구성](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>입력 방향 구성

#### <a name="event-hub-input"></a>이벤트 허브 입력

이벤트 허브에서 이벤트를 수신 하려면를 설정 하는 "바인딩" 내의 위쪽 섹션에 구성 정보를 추가 합니다.

* **type** -"eventHubTrigger" 형식입니다.
* **connection** -이벤트 허브 연결 문자열에 대 한 앱 설정 값의 이름입니다. 
* **eventHubName** -연결 문자열에 의해 식별 되는 네임 스페이스 내에 있는 이벤트 허브의 이름입니다.
* **consumerGroup** -소비자 그룹의 이름입니다. 이름은 백분율 기호로 묶여 있으므로 앱 설정 값을 참조 하기도 합니다.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Service Bus 큐 입력

Service Bus 큐에서 이벤트를 수신 하려면를 설정 하는 "바인딩" 내의 맨 위 섹션에 구성 정보를 추가 합니다.

* **type** -"serviceBusTrigger" 형식입니다.
* **connection** -Service Bus 연결 문자열에 대 한 앱 설정 값의 이름입니다.
* **queueName** -연결 문자열에 의해 식별 되는 네임 스페이스 내의 Service Bus 큐 이름입니다.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Service Bus 토픽 입력

Service Bus 토픽에서 이벤트를 수신 하려면를 설정 하는 "바인딩" 내의 맨 위 섹션에 구성 정보를 추가 합니다.

* **type** -"serviceBusTrigger" 형식입니다.
* **connection** -Service Bus 연결 문자열에 대 한 앱 설정 값의 이름입니다. `{FunctionName}-source-connection`제공 된 스크립트를 사용 하려면이 값이 이어야 합니다.
* **topicName** -연결 문자열에 의해 식별 되는 네임 스페이스 내의 Service Bus 항목 이름입니다.
* **subscriptionName** -연결 문자열에 의해 식별 되는 네임 스페이스 내의 지정 된 항목에 대 한 Service Bus 구독의 이름입니다.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>출력 방향 구성

#### <a name="event-hub-output"></a>이벤트 허브 출력

이벤트 허브로 이벤트를 전달 하려는 경우 다음을 설정 하는 "바인딩" 내의 아래쪽 섹션에 구성 정보를 추가 합니다.

* **유형** -"eventHub" 유형입니다.
* **connection** -이벤트 허브 연결 문자열에 대 한 앱 설정 값의 이름입니다. 
* **eventHubName** -연결 문자열에 의해 식별 되는 네임 스페이스 내에 있는 이벤트 허브의 이름입니다.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>큐 출력 Service Bus

Service Bus 큐에 이벤트를 전달 하려면를 설정 하는 "바인딩" 내의 아래쪽 섹션에 구성 정보를 추가 합니다.

* **type** -"serviceBus" 형식입니다.
* **connection** -Service Bus 연결 문자열에 대 한 앱 설정 값의 이름입니다. 
* **queueName** -연결 문자열에 의해 식별 되는 네임 스페이스 내의 Service Bus 큐 이름입니다.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Service Bus 토픽 출력

Service Bus 토픽으로 이벤트를 전달 하려는 경우 "바인딩" 내의 아래쪽 섹션에 구성 정보를 추가 합니다.

* **type** -"serviceBus" 형식입니다.
* **connection** -Service Bus 연결 문자열에 대 한 앱 설정 값의 이름입니다. 
* **topicName** -연결 문자열에 의해 식별 되는 네임 스페이스 내의 Service Bus 항목 이름입니다.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>진입점 구성

진입점 구성은 표준 복제 작업 중 하나를 선택 합니다. 프로젝트를 수정 하는 경우 `Azure.Messaging.Replication` 작업을 추가 하 고 여기에서 참조할 수도 있습니다. 예를 들면 다음과 같습니다.

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

다음 표에서는 원본 및 대상의 조합에 대 한 올바른 값을 제공 합니다.

| 원본      | 대상      | 진입점 
|-------------|-------------|------------------------------------------------------------------------
| 이벤트 허브   | 이벤트 허브   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 이벤트 허브   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | 이벤트 허브   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>재시도 정책

재시도 정책 구성에 [대 한 Azure Functions 설명서](../articles/azure-functions/functions-bindings-error-pages.md) 를 참조 하십시오. 이 리포지토리의 프로젝트 전체에서 선택한 정책 설정은 데이터 손실을 방지 하기 위해 재시도 간격을 5 초에서 5 분으로 설정 하 여 지 수 백오프 전략을 구성 합니다.

Service Bus의 경우 큐의 상호 작용 및 큐에 대해 정의 된 최대 배달 횟수를 이해 하려면 ["트리거 복원 력 맨 위에 있는 재시도 지원 사용"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) 섹션을 검토 합니다.

### <a name="build-deploy-and-configure"></a>빌드, 배포 및 구성

구성에 집중 하는 동안 작업에는 배포 가능한 응용 프로그램을 구축 하 고, 지정 된 끝점에 연결 하는 데 필요한 모든 정보를 포함 하도록 Azure Functions 호스트를 구성 해야 합니다. 

이는 [Azure Functions에 대 한 구성 기반 복제 샘플](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)에서 재사용 가능한 스크립트와 함께 설명 됩니다.