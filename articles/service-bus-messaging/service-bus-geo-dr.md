---
title: "Azure Service Bus 지리적 재해 복구 | Microsoft Docs"
description: "지리적 지역을 사용하여 장애 조치(Failover)하고 Azure Service Bus에서 재해 복구를 수행하는 방법"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 2c509b56282ace92e536dc85f1a28f83a4701940
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Azure Service Bus 지리적 재해 복구(미리 보기)

지역 데이터 센터에서 가동 중지 시간이 발생하는 경우 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 따라서 *지리적 재해 복구* 및 *지리적 복제*는 기업에 중요한 기능입니다. Azure Service Bus는 네임스페이스 수준에서 지리적 재해 복구 및 지리적 복제를 둘 다 지원합니다. 

지리적 재해 복구 미리 보기는 현재 두 지역(**미국 중북부** 및 **미국 중남부**에서만 사용할 수 있습니다.

## <a name="outages-and-disasters"></a>중단 및 재해

[서비스 버스 가동 중단 및 재해로부터 응용 프로그램을 보호하기 위한 모범 사례](service-bus-outages-disasters.md) 문서에서는 "중단" 및 "재해"를 이해하기 쉽게 구분하고 있습니다. 이러한 구분은 매우 중요합니다. *중단*은 Azure Service Bus를 일시적으로 사용할 수 없는 것으로, 메시징 저장소와 같은 서비스의 일부 구성 요소나 전체 데이터 센터에 영향을 줄 수 있습니다. 그렇지만 문제가 해결되면 Service Bus가 다시 사용할 수 있는 상태가 됩니다. 일반적으로 중단으로 인해 메시지나 기타 데이터가 손실되지는 않습니다. 이러한 중단의 예로 데이터 센터의 전원 장애를 들 수 있습니다.

*재해*란 Service Bus [배율 단위](service-bus-architecture.md#service-bus-scale-units) 또는 데이터 센터의 영구적인 손실을 의미합니다. 데이터 센터는 다시 사용할 수 있게 될 수도, 그렇지 않을 수도 있고 몇 시간 또는 며칠 동안 다운될 수도 있습니다. 일반적으로 재해가 발생하면 메시지나 기타 데이터의 일부 또는 전체를 잃게 됩니다. 재해의 예로는 화재, 홍수 또는 지진이 있습니다.

Azure Service Bus의 지리적 재해 복구 기능은 재해 복구 솔루션입니다. 이 문서에 설명된 개념 및 워크플로는 재해 시나리오에 적용되며 일시적이거나 임시적인 중단이 아닙니다.  

## <a name="basic-concepts-and-terms"></a>기본 개념 및 용어

재해 복구 기능은 메타데이터 재해 복구를 구현하며 주 및 보조 재해 복구 네임스페이스에 의존합니다. 지리적 재해 복구 기능은 [프리미엄 네임스페이스](service-bus-premium-messaging.md)에만 사용할 수 있습니다. 별칭을 통해 연결이 수행되므로 연결 문자열을 변경할 필요가 없습니다.

이 문서에서는 다음 용어가 사용됩니다.

-  *별칭*: 기본 연결 문자열입니다.

-  *주/보조 네임스페이스*: 별칭에 해당하는 네임스페이스를 설명합니다. 주는 "활성" 상태이고 메시지를 수신하며, 보조는 "수동" 상태이고 메시지를 수신하지 않습니다. 둘 간의 메타데이터의 동기화되므로 응용 프로그램 코드 변경 없이도 둘 다 메시지를 원활하게 수락할 수 있습니다.

-  *메타데이터*: Azure Service Bus의 개체를 나타내는 것입니다. 현재 메타데이터만 지원합니다.

-  *장애 조치(Failover)*: 보조 네임스페이스를 활성화하는 프로세스입니다. 다시 사용할 수 있는 상태가 되면 이전의 주 네임스페이스에서 메시지를 끌어온 다음 네임스페이스를 삭제해야 합니다. 다른 장애 조치(Failover)를 만들려면 새 보조 네임스페이스를 페어링에 추가합니다.

## <a name="failover-workflow"></a>장애 조치 워크플로

다음 섹션은 초기 장애 조치(Failover)를 설정하는 전체 프로세스와 해당 지점에서 앞으로 이동하는 방법을 대략적으로 설명합니다.

![1][]

먼저 주 및 보조 네임스페이스를 설정한 후 페어링을 만듭니다. 이 페어링은 연결하는 데 사용할 수 있는 별칭을 제공합니다. 별칭을 사용하므로 연결 문자열을 변경할 필요가 없습니다. 새 네임스페이스에만 장애 조치(Failover) 페어링에 추가할 수 있습니다. 마지막으로 일부 트리거 논리를 추가해야 합니다(예를 들어 네임스페이스를 사용할 수 없음을 감지하고 장애 조치(Failover)를 시작하는 일부 비즈니스 논리). Service Bus의 [메시지 찾아보기](message-browsing.md) 기능을 사용하여 네임스페이스 가용성을 확인할 수 있습니다.

모니터링 및 재해 복구를 설정한 후 장애 조치(Failover) 프로세스를 살펴볼 수 있습니다. 트리거가 장애 조치(Failover)를 시작하거나 장애 조치(Failover)를 수동으로 시작하는 경우 다음 두 단계를 수행해야 합니다.

1. 다른 중단이 발생하면 다시 장애 조치(Failover)할 수 있기를 원합니다. 따라서 두 번째 수동 네임스페이스를 설정하고 페어링을 업데이트합니다. 
2. 새 네임스페이스를 사용할 수 있으면 이전 주 네임스페이스에서 메시지를 끌어옵니다. 그런 후 이전 주 네임스페이스를 다시 사용하거나 삭제합니다.

![2][]

## <a name="set-up-disaster-recovery"></a>재해 복구 설정

이 섹션에서는 Service Bus 지리적 재해 복구 코드를 작성하는 방법을 설명합니다. 이렇게 하려면 2개의 네임스페이스가 별도 위치(예: 미국 남부 및 미국 중북부)에 있어야 합니다. 다음 예제에서는 Visual Studio 2017을 사용합니다.

1.  Visual Studio에서 새 **콘솔 앱(.Net Framework)** 프로젝트를 만들고 이름을 지정합니다(예: **SBGeoDR**).

2.  다음 NuGet 패키지를 설치합니다.
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. 사용 중인 Newtonsoft.Json NuGet 패키지의 버전이 10.0.3인지 확인합니다.

3.  다음 `using` 문을 코드에 추가합니다.

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. `main()` 메서드를 수정하여 2개의 프리미엄 네임스페이스를 추가합니다.

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. 두 네임스페이스 간에 페어링을 사용하도록 설정하고 나중에 엔터티에 연결하는 데 사용할 수 있게 별칭을 가져옵니다.

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

두 개의 연결된 네임스페이스를 설정했습니다. 이제 엔터티를 만들어 메타데이터 동기화를 확인할 수 있습니다. 장애 조치(Failover)를 바로 수행하려는 경우 메타데이터가 동기화될 때까지 잠시 기다립니다. 예를 들어 다음과 같은 짧은 대기 시간을 추가할 수 있습니다.

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

이때 포털을 통해 또는 Azure Resource Manager를 통해 엔터티를 추가하고 동기화 방법을 확인할 수 있습니다. 수동으로 장애 조치(Failover)를 수행하려는 경우가 아니면, 주 네임스페이스를 모니터링하고 사용할 수 없는 경우 장애 조치(Failover)를 시작하는 앱을 만들어야 합니다. 

## <a name="initiate-a-failover"></a>장애 조치(Failover) 시작

다음 코드에서는 장애 조치(Failover)를 시작하는 방법을 보여 줍니다.

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

장애 조치(Failover)를 트리거한 후에 새 수동 네임스페이스를 추가하고 페어링을 다시 설정합니다. 새 페어링을 만들기 위한 코드는 이전 섹션에 나와 있습니다. 또한 장애 조치(Failover)가 완료되면 이전 주 네임스페이스에서 메시지를 제거해야 합니다. 큐에서 메시지를 수신하는 방법의 예제를 보려면 [큐 시작](service-bus-dotnet-get-started-with-queues.md)을 참조하세요.

## <a name="how-to-disable-geo-disaster-recovery"></a>지리적 재해 복구를 사용하지 않도록 설정하는 방법

다음 코드에서는 네임스페이스 페어링을 사용하지 않도록 설정하는 방법을 보여 줍니다.

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

다음 코드는 사용자가 만든 별칭을 삭제합니다.

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>장애 조치(Failback) 후 단계

장애 조치(Failback) 후에는 다음 두 단계를 수행하세요.

1.  새 수동 보조 네임스페이스를 만듭니다. 코드는 이전 섹션에 나와 있습니다.
2.  큐에서 나머지 메시지를 제거합니다.

## <a name="alias-connection-string-and-test-code"></a>별칭 연결 문자열 및 테스트 코드

장애 조치(Failback) 프로세스를 테스트하려는 경우 별칭을 사용하여 메시지를 주 네임스페이스로 푸시하는 샘플 응용 프로그램을 작성할 수 있습니다. 이렇게 하려면 활성는 네임스페이스에서 별칭 연결 문자열을 가져옵니다. 현재 미리 보기 릴리스를 사용하는 경우 이 연결 문자열을 직접 가져올 수 있는 인터페이스가 없습니다. 다음 코드 예제에서는 장애 조치(Failback) 전과 후의 관계를 보여 줍니다.

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>다음 단계

- 지리적 재해 복구를 참조합니다[REST API 참조](/rest/api/servicebus/disasterrecoveryconfigs).
- 지리적 재해 복구를 실행합니다[GitHub의 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- 지리적 재해 복구를 참조합니다[별칭으로 메시지를 전송하는 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Service Bus 메시징에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [서비스 버스 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
