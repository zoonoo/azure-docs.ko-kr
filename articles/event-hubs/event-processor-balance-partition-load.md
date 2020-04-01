---
title: 여러 인스턴스에서 파티션 로드 균형 조정 - Azure 이벤트 허브 | 마이크로 소프트 문서
description: 이벤트 프로세서와 Azure 이벤트 허브 SDK를 사용하여 응용 프로그램의 여러 인스턴스에서 파티션 로드의 균형을 맞추는 방법을 설명합니다.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: bf90120157bf64bd62a3b5ec9d8a6b2c6260e024
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398308"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>응용 프로그램의 여러 인스턴스에서 파티션 로드 균형 조정
이벤트 처리 응용 프로그램을 확장하려면 응용 프로그램의 여러 인스턴스를 실행하고 부하를 서로 분산시킬 수 있습니다. 이전 버전에서 [EventProcessorHost를](event-hubs-event-processor-host.md) 사용하면 프로그램의 여러 인스턴스와 수신 시 검사점 이벤트 간의 부하 균형을 조정할 수 있습니다. 최신 버전(5.0 이후), **EventProcessorClient(.NET** 및 Java) 또는 **EventHubConsumerClient(파이썬** 및 자바스크립트)에서 동일한 작업을 수행할 수 있습니다. 개발 모델은 이벤트를 사용하여 더 간단해지됩니다. 이벤트 처리기를 등록하여 관심 있는 이벤트를 구독합니다.

이 문서에서는 여러 인스턴스를 사용하여 이벤트 허브에서 이벤트를 읽은 다음 이벤트 프로세서 클라이언트의 기능에 대한 세부 정보를 제공하는 샘플 시나리오에 대해 설명하며, 이를 통해 한 번에 여러 파티션에서 이벤트를 수신하고 동일한 이벤트 허브 및 소비자 그룹을 사용하는 다른 소비자와의 로드 밸런스를 제공할 수 있습니다.

> [!NOTE]
> Event Hubs의 크기를 조정하는 핵심은 분할된 소비자라는 개념입니다. [경쟁하는 소비자](https://msdn.microsoft.com/library/dn568101.aspx) 패턴과 달리 분할된 소비자 패턴을 사용하면 경합 병목 상태를 제거하고 종단 간 병렬 처리를 용이하게 하여 대규모 크기 조정이 가능해집니다.

## <a name="example-scenario"></a>예제 시나리오

예제 시나리오로 100,000호의 집을 모니터링하는 보안 회사를 가정합니다. 1분마다 동작 감지기, 도어/창 개방 센서, 유리 파손 감지기 등과 같은 다양한 센서에서 데이터를 각 가정에 설치합니다. 회사는 거주자가 거의 실시간으로 해당 집의 활동을 모니터링하는 웹 사이트를 제공합니다.

각 센서는 데이터를 이벤트 허브로 푸시합니다. 이벤트 허브는 16개의 파티션으로 구성됩니다. 소비 끝에는 이러한 이벤트를 읽고 통합(필터, 집계 등)하고 집계를 저장소 Blob에 덤프한 다음 사용자 친화적인 웹 페이지에 투영할 수 있는 메커니즘이 필요합니다.

## <a name="write-the-consumer-application"></a>소비자 애플리케이션 작성

분산된 환경에서 소비자를 디자인할 때 시나리오는 다음 요구 사항을 처리해야 합니다.

1. **크기 조정:** 몇몇 Event Hubs 파티션에서 읽기 소유권을 가진 각 소비자를 사용하여 여러 소비자를 만듭니다.
2. **부하 분산:** 소비자를 동적으로 증가시키거나 감소시킵니다. 예를 들어, 새 센서 형식(예: 일산화 탐지기)이 각 집에 추가되면 이벤트 수가 증가합니다. 이 경우에 연산자(사람)는 소비자 인스턴스 수를 증가시킵니다. 그런 다음, 소비자 풀은 소유한 파티션의 수를 다시 조정하여 새로 추가된 소비자와 관련된 부하를 공유할 수 있습니다.
3. **오류 시 원활한 재개:** 소비자(소비자**A)가**실패하면(예: 소비자를 호스팅하는 가상 시스템이 갑자기 충돌함) 다른 소비자는 **소비자 A가** 소유한 파티션을 선택하고 계속할 수 있습니다. 또한 *검사점* 또는 *오프셋*이라는 연속 지점은 **소비자 A**가 실패한 정확한 지점 또는 약간 앞에 위치해야 합니다.
4. **이벤트 사용:** 앞의 세 가지 포인트는 소비자의 관리를 다루지만 이벤트를 사용하고 유용한 작업을 수행하는 코드가 있어야 합니다. 예를 들어 집계하여 Blob 저장소에 업로드합니다.

## <a name="event-processor-or-consumer-client"></a>이벤트 프로세서 또는 소비자 클라이언트

이러한 요구 사항을 충족하기 위해 자체 솔루션을 빌드할 필요가 없습니다. Azure 이벤트 허브 SDK는 이 기능을 제공합니다. .NET 또는 Java SDK에서는 이벤트 프로세서 클라이언트(EventProcessorClient)를 사용하고 파이썬 및 Java 스크립트 SDK에서는 EventHubConsumerClient를 사용합니다. 이전 버전의 SDK에서는 이러한 기능을 지원하는 이벤트 프로세서 호스트(EventProcessorHost)였습니다.

대부분의 프로덕션 시나리오에서는 이벤트를 읽고 처리하는 데 이벤트 프로세서 클라이언트를 사용하는 것이 좋습니다. 프로세서 클라이언트는 이벤트 허브의 모든 파티션에서 수행 및 내결함성 방식으로 이벤트를 처리하는 강력한 환경을 제공하는 동시에 진행 상황을 검사하는 수단을 제공하기 위한 것입니다. 이벤트 프로세서 클라이언트는 지정된 이벤트 허브에 대한 소비자 그룹의 컨텍스트 내에서 협력적으로 작업할 수도 있습니다. 클라이언트는 인스턴스를 사용할 수 없거나 그룹에 사용할 수 없게 되면 작업의 배포 및 균형 조정을 자동으로 관리합니다.

## <a name="partition-ownership-tracking"></a>파티션 소유권 추적

이벤트 프로세서 인스턴스는 일반적으로 하나 이상의 파티션에서 이벤트를 소유하고 처리합니다. 파티션의 소유권은 이벤트 허브 및 소비자 그룹 조합과 연결된 모든 활성 이벤트 프로세서 인스턴스 간에 균등하게 분산됩니다. 

각 이벤트 프로세서에는 고유 식별자가 부여되고 검사점 저장소의 항목을 추가하거나 업데이트하여 파티션의 소유권을 주장합니다. 모든 이벤트 프로세서 인스턴스는 이 저장소와 주기적으로 통신하여 자체 처리 상태를 업데이트하고 다른 활성 인스턴스에 대해 알아봅니다. 그런 다음 이 데이터를 사용하여 활성 프로세서 간의 부하 를 분산합니다. 새 인스턴스는 처리 풀에 조인하여 확장할 수 있습니다. 실패로 인해 인스턴스가 다운되거나 축소되면 파티션 소유권이 다른 활성 프로세서로 정상적으로 전송됩니다.

검사점 저장소의 파티션 소유권 레코드는 Event Hubs 네임스페이스, 이벤트 허브 이름, 소비자 그룹, 이벤트 프로세서 식별자(소유자라고도 함), 파티션 ID 및 마지막으로 수정된 시간을 추적합니다.



| Event Hubs 네임스페이스               | 이벤트 허브 이름 | **소비자 그룹** | 소유자                                | Partition ID | 마지막으로 수정된 시간  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | 마이이벤트허브     | 마이컨슈머 그룹    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | 마이이벤트허브     | 마이컨슈머 그룹    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | 마이이벤트허브     | 마이컨슈머 그룹    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | 마이이벤트허브     | 마이컨슈머 그룹    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

각 이벤트 프로세서 인스턴스는 파티션의 소유권을 획득하고 마지막으로 알려진 [검사점에서](# Checkpointing)파티션 처리를 시작합니다. 프로세서가 실패(VM종료)되면 다른 인스턴스는 마지막으로 수정된 시간을 확인하여 이를 감지합니다. 다른 인스턴스는 비활성 인스턴스가 이전에 소유한 파티션의 소유권을 얻으려고 시도하며 검사점 저장소는 인스턴스 중 하나만 파티션의 소유권을 주장하는 데 성공하도록 보장합니다. 따라서 특정 시점에 파티션에서 이벤트를 수신하는 프로세서가 한 개에 가있습니다.

## <a name="receive-messages"></a>메시지 받기

이벤트 프로세서를 만들 때 이벤트 및 오류를 처리하는 함수를 지정합니다. 이벤트를 처리하는 함수에 대한 각 호출은 특정 파티션에서 단일 이벤트를 제공합니다. 이 이벤트를 처리하는 것은 사용자의 책임입니다. 소비자가 모든 메시지를 적어도 한 번 이상 처리하도록 하려면 재시도 논리를 사용하여 고유한 코드를 작성해야 합니다. 하지만 포이즌 메시지에 대해 주의해야 합니다.

비교적 빠르게 수행하는 것이 좋습니다. 즉, 가능한 한 적은 처리를 수행합니다. 저장소에 쓰고 라우팅을 수행해야 하는 경우 두 개의 소비자 그룹을 사용하고 두 개의 이벤트 프로세서를 사용하는 것이 좋습니다.

## <a name="checkpointing"></a>검사점 설정

*검사점은* 이벤트 프로세서가 파티션 내에서 마지막으로 성공적으로 처리된 이벤트의 위치를 표시하거나 커밋하는 프로세스입니다. 검사점 표시는 일반적으로 이벤트를 처리하는 함수 내에서 수행되며 소비자 그룹 내에서 파티션별로 발생합니다. 

이벤트 프로세서가 파티션에서 연결이 끊어지면 다른 인스턴스가 해당 소비자 그룹의 해당 파티션의 마지막 프로세서에서 이전에 커밋된 검사점에서 파티션 처리를 다시 시작할 수 있습니다. 프로세서가 연결되면 오프셋을 이벤트 허브로 전달하여 읽기 시작할 위치를 지정합니다. 이러한 방식으로 검사점을 사용하여 다운스트림 응용 프로그램에서 이벤트를 "완료"로 표시하고 이벤트 프로세서가 다운될 때 복원력을 제공할 수 있습니다. 이 검사점 프로세스에서 더 낮은 오프셋을 지정하면 이전 데이터로 돌아갈 수 있습니다. 

이벤트를 처리된 것으로 표시하기 위해 검사점을 수행하면 검사점 저장소의 항목이 이벤트의 오프셋 및 시퀀스 번호로 추가되거나 업데이트됩니다. 사용자는 검사점 업데이트 빈도를 결정해야 합니다. 성공적으로 처리된 각 이벤트 후에 업데이트하면 기본 검사점 저장소에 쓰기 작업을 트리거할 때 성능및 비용에 영향을 미칠 수 있습니다. 또한 모든 단일 이벤트를 검사하는 것은 서비스 버스 큐가 이벤트 허브보다 더 나은 옵션이 될 수 있는 큐에 대기중인 메시징 패턴을 나타냅니다. Event Hubs의 기본 개념은 대규모로 "최소한 한 번" 제공받는다는 것입니다. 다운스트림 시스템 멱등원을 만들어서 쉽게 오류로부터 복구하거나 여러 번 수신되는 동일한 이벤트에서 해당 결과를 다시 시작할 수 있습니다.

> [!NOTE]
> Azure Blob Storage를 Azure에서 일반적으로 사용할 수 있는 버전보다 다른 버전의 저장소 Blob SDK를 지원하는 환경에서 검사점 저장소로 사용하는 경우 코드를 사용하여 저장소 서비스 API 버전을 해당 환경에서 지원하는 특정 버전으로 변경해야 합니다. 예를 들어 [Azure 스택 허브 버전 2002에서 이벤트 허브를](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)실행 하는 경우 저장소 서비스에 대 한 사용 가능한 가장 높은 버전은 버전 2017-11-09입니다. 이 경우 코드를 사용하여 저장소 서비스 API 버전을 2017-11-09로 지정해야 합니다. 특정 저장소 API 버전을 대상으로 지정하는 방법에 대한 예제는 GitHub에서 다음 샘플을 참조하십시오. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [자바 스크립트](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) 또는 [타이프 스크립트](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

## <a name="thread-safety-and-processor-instances"></a>스레드 안전성 및 프로세서 인스턴스

기본적으로 이벤트 프로세서 또는 소비자는 스레드가 안전하며 동기 방식으로 작동합니다. 파티션에 대해 이벤트가 도착하면 이벤트를 처리하는 함수가 호출됩니다. 메시지 펌프가 다른 스레드의 백그라운드에서 계속 실행됨에 따라 이 함수에 대한 후속 메시지와 호출이 백그라운드에서 큐에 대기합니다. 스레드 보안은 스레드로부터 안전한 컬렉션에 대한 필요성을 제거하고 성능을 크게 향상시킵니다.

## <a name="next-steps"></a>다음 단계
다음 빠른 시작을 참조하십시오.

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
