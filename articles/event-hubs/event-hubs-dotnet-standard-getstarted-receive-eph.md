---
title: ".NET Standard를 사용하여 Azure Event Hubs에서 이벤트 수신 | Microsoft Docs"
description: ".NET Standard에서 EventProcessorHost를 사용하여 메시지 수신 시작"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 57175ddc53d5856cd3492d4c631a92d4bf9247c4
ms.openlocfilehash: 859f87356448041a320c2e126478aabf1efa0d44
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-receiving-messages-with-the-eventprocessorhost-in-net-standard"></a>.NET Standard에서 EventProcessorHost를 사용하여 메시지 수신 시작

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver)에서 사용할 수 있습니다.

## <a name="what-will-be-accomplished"></a>수행될 작업

이 자습서에서는 기존 솔루션 **SampleEphReceiver**(이 폴더에 포함)을 만드는 방법을 보여 줍니다. `EhConnectionString`, `EhEntityPath` 및 `StorageAccount` 문자열을 이벤트 허브 및 저장소 계정 값으로 바꾸어 이 솔루션을 있는 그대로 실행하거나 이 자습서를 참조하여 직접 만들 수도 있습니다.

이 자습서에서는 **EventProcessorHost**를 사용하여 이벤트 허브에서 메시지를 수신하는 .NET Core 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건

1. [Visual Studio 2015](http://www.visualstudio.com)

2. [.NET Core Visual Studio 2015 도구](http://www.microsoft.com/net/core).

3. Azure 구독.

4. Event Hubs 네임스페이스
    
## <a name="receive-messages-from-the-event-hub"></a>이벤트 허브에서 메시지 받기

### <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

1. Visual Studio를 시작하고 새 .NET Core 콘솔 응용 프로그램을 만듭니다.

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

* 다음 NuGet 패키지를 프로젝트에 추가합니다.
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

### <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor 인터페이스 구현

1. 'SimpleEventProcessor'라는 새 클래스를 만듭니다.

2. SimpleEventProcessor.cs 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. `IEventProcessor` 인터페이스를 구현합니다. 이 클래스는 다음과 같이 표시되어야 합니다.

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Collections.Generic;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class SimpleEventProcessor : IEventProcessor
        {
            public Task CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
                return Task.CompletedTask;
            }
    
            public Task OpenAsync(PartitionContext context)
            {
                Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
                return Task.CompletedTask;
            }
    
            public Task ProcessErrorAsync(PartitionContext context, Exception error)
            {
                Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
                return Task.CompletedTask;
            }
    
            public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (var eventData in messages)
                {
                    var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                    Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
                }
    
                return context.CheckpointAsync();
            }
        }
    }
    ```

### <a name="write-a-main-console-method-that-uses-simpleeventprocessor-to-receive-messages-from-an-event-hub"></a>`SimpleEventProcessor`를 사용하여 이벤트 허브에서 메시지를 수신하는 기본 콘솔 메서드 작성

1. Program.cs 파일 위에 다음 `using` 문을 추가합니다.
  
    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Event Hubs 연결 문자열, 이벤트 허브 경로, 저장소 컨테이너 이름, 저장소 계정 이름 및 저장소 계정 키에 대한 `Program` 클래스에 상수를 추가합니다. 자리 표시자를 해당 값으로 바꿉니다.

    ```cs
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 다음과 같이 `MainAsync`이라는 새 메서드를 `Program` 클래스에 추가합니다.
    ```cs
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` 메서드에 다음 코드 줄을 추가합니다.

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 파일은 다음과 같아야 합니다.

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";
    
            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");
    
                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);
    
                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
    
                Console.WriteLine("Receiving. Press enter key to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. 프로그램을 실행하고 오류가 없는지 확인합니다.
  
축하합니다. 이제 이벤트 허브에서 메시지를 받았습니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [이벤트 허브 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)
