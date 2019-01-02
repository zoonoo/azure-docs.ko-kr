---
title: .NET Framework를 사용하여 이벤트 수신 - Azure Event Hubs | Microsoft Docs
description: .NET Framework를 사용하여 Azure Event Hubs에서 이벤트를 수신하려면 이 자습서를 따르세요.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 8052b797707f7913fdd678f4dd51822754623104
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077259"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>.NET Framework를 사용하여 Azure Event Hubs에서 이벤트 수신

## <a name="introduction"></a>소개

Event Hubs는 연결된 디바이스 및 응용 프로그램에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. Event Hubs에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 응용 프로그램 아키텍처의 핵심 구성 요소입니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 [이벤트 프로세서 호스트](event-hubs-event-processor-host.md)를 사용하여 이벤트 허브에서 메시지를 받는 .NET Framework 콘솔 애플리케이션을 작성하는 방법을 보여 줍니다. [이벤트 프로세서 호스트](event-hubs-event-processor-host.md)는 영구적 검사점을 관리하여 Event Hubs의 이벤트 수신을 간소화하고 이러한 Event Hubs에서 병렬 수신하는 .NET 클래스입니다. 이벤트 프로세서 호스트를 사용하면 다른 노드에 호스트된 수신기를 비롯한 여러 수신기 간에 이벤트를 분할할 수 있습니다. 이 예제에서는 단일 수신기에 대해 이벤트 프로세서 호스트를 사용하는 방법을 보여 줍니다. [확장된 이벤트 처리][Scale out Event Processing with Event Hubs] 샘플에서는 여러 수신기에서 이벤트 프로세서 호스트를 사용하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Microsoft Visual Studio 2017 이상](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 이벤트 허브 만들기
첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 진행하세요.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 응용 프로그램 프로젝트를 새로 만듭니다. 프로젝트 이름을 **Receiver**로 지정합니다.
   
![콘솔 응용 프로그램 만들기](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

1. 솔루션 탐색기에서 **Receiver** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **솔루션에 대한 NuGet 패키지 관리**를 클릭합니다.
2. **찾아보기** 탭을 클릭한 다음 `Microsoft Azure Service Bus Event Hub - EventProcessorHost`를 검색합니다. **설치**를 클릭하고 사용 약관에 동의합니다.
   
    ![이벤트 프로세서 호스트 NuGet 패키지 검색](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio는 [Azure Service Bus Event Hub - EventProcessorHost NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)에 대한 참조 및 해당하는 모든 종속성을 다운로드, 설치 및 추가합니다.

## <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor 인터페이스 구현

1. **Receiver** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다. 새 클래스의 이름을 **SimpleEventProcessor**로 지정하고 **추가**를 클릭하여 클래스를 생성합니다.
   
    ![SimpleEventProcessor 클래스 추가](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. SimpleEventProcessor.cs 파일의 맨 위에 다음 구문을 추가합니다.
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. 다음 코드를 클래스의 본문으로 대체합니다.
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      이 클래스는 Event Hub에서 받는 이벤트를 처리하기 위해 **EventProcessorHost**에 의해 호출됩니다. `SimpleEventProcessor` 클래스는 초시계를 사용하여 **EventProcessorHost** 컨텍스트에서 검사점 메서드를 주기적으로 호출합니다. 이 처리로 인해 수신기가 다시 시작되는 경우 5분 이하의 처리 작업은 손실됩니다.

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>SimpleEventProcessor를 사용하도록 Main 메서드 업데이트

1. **Program** 클래스에서 파일 맨 위에 다음 `using` 문을 추가합니다.
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. `Program` 클래스의 `Main` 메서드를 다음 코드로 바꿉니다. 이 코드는 이전에 저장한 이벤트 허브 이름 및 네임스페이스 수준 연결 문자열과 이전 섹션에서 복사한 저장소 계정 및 키를 대체합니다. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. 프로그램을 실행하고 오류가 없는지 확인합니다.
  
축하합니다! 이제 이벤트 프로세서 호스트를 사용하여 Event Hub에서 메시지를 받았습니다.


> [!NOTE]
> 이 자습서에서는 [EventProcessorHost](event-hubs-event-processor-host.md)의 단일 인스턴스를 사용합니다. 처리량을 늘리려면 [확장된 이벤트 처리](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) 샘플에 표시된 대로 [EventProcessorHost](event-hubs-event-processor-host.md)의 여러 인스턴스를 실행하는 것이 좋습니다. 이러한 경우 여러 인스턴스가 자동으로 서로 조정되어 받은 이벤트의 부하를 분산시킵니다. 

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 이벤트 허브에서 메시지를 받은 .NET Framework 애플리케이션을 만들었습니다. .NET Framework를 사용하여 이벤트 허브에 이벤트를 보내는 방법을 알아보려면 [이벤트 허브에서 이벤트 보내기 - .NET Framework](event-hubs-dotnet-framework-getstarted-send.md)를 참조하세요.

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
