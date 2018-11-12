---
title: .NET Standard 라이브러리를 사용하여 Azure Event Hubs에서 이벤트 수신 | Microsoft Docs
description: .NET Standard에서 EventProcessorHost를 사용하여 메시지 수신 시작
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 9adbd8b9e7934ebe454d14ac6e47fe96898c9184
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234394"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>.NET Standard에서 이벤트 프로세서 호스트를 사용하여 메시지 수신 시작
Event Hubs는 연결된 장치 및 응용 프로그램에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. Event Hubs에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 응용 프로그램 아키텍처의 핵심 구성 요소입니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 [이벤트 프로세서 호스트](event-hubs-event-processor-host.md)를 사용하여 이벤트 허브의 메시지를 수신하는 .NET Core 콘솔 응용 프로그램을 작성하는 방법을 보여 줍니다. [이벤트 프로세서 호스트](event-hubs-event-processor-host.md)는 영구적 검사점을 관리하여 Event Hubs의 이벤트 수신을 간소화하고 이러한 Event Hubs에서 병렬 수신하는 .NET 클래스입니다. 이벤트 프로세서 호스트를 사용하면 다른 노드에 호스트된 수신기를 비롯한 여러 수신기 간에 이벤트를 분할할 수 있습니다. 이 예제에서는 단일 수신기에 대해 이벤트 프로세서 호스트를 사용하는 방법을 보여 줍니다. [이벤트 프로세싱 규모 확장][Event Hubs를 사용하여 이벤트 처리 규모 확장] 샘플에서는 여러 수신기에서 이벤트 프로세서 호스트를 사용하는 방법을 보여 줍니다.

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName`, `StorageAccountName`, `StorageAccountKey`, `StorageContainerName` 문자열을 이벤트 허브 값으로 대체한 후 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* [Microsoft Visual Studio 2015 또는 2017](https://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2017을 사용하지만 Visual Studio 2015도 지원됩니다.
* [.NET Core Visual Studio 2015 또는 2017 도구](https://www.microsoft.com/net/core).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 이벤트 허브 만들기
첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 진행하세요.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio를 시작합니다. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다. .NET Core 콘솔 응용 프로그램을 만듭니다.

![새 프로젝트][2]

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

[**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 및 [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET 표준 라이브러리 NuGet 패키지를 다음 단계를 따라 사용자 프로젝트에 추가합니다. 

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **Microsoft.Azure.EventHubs**를 검색한 다음 **Microsoft.Azure.EventHubs** 패키지를 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
3. 1단계와 2단계를 반복하고 **Microsoft.Azure.EventHubs.Processor** 패키지를 설치합니다.

## <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor 인터페이스 구현

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 새 클래스 이름을 **SimpleEventProcessor**로 지정합니다.

2. SimpleEventProcessor.cs 파일을 열고 다음 `using` 문을 파일의 맨 위에 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. `IEventProcessor` 인터페이스를 구현합니다. `SimpleEventProcessor` 클래스의 전체 내용을 다음 코드로 바꿉니다.

    ```csharp
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
    ```

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>SimpleEventProcessor를 사용하도록 Main 메서드 업데이트

1. Program.cs 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. 이벤트 허브 연결 문자열, 이벤트 허브 이름, 저장소 계정 컨테이너 이름, 저장소 계정 이름, 저장소 계정 키의 `Program` 클래스에 상수를 추가합니다. 자리 표시자를 해당 값으로 바꾸어 다음 코드를 추가합니다.

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 다음과 같이 `MainAsync`라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` 메서드에 다음 코드 줄을 추가합니다.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 파일은 다음과 같아야 합니다.

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
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
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. 프로그램을 실행하고 오류가 없는지 확인합니다.

축하합니다! 이제 이벤트 프로세서 호스트를 사용하여 이벤트 허브에서 메시지를 수신했습니다.

> [!NOTE]
> 이 자습서에서는 [EventProcessorHost](event-hubs-event-processor-host.md)의 단일 인스턴스를 사용합니다. 처리량을 늘리려면 [확장된 이벤트 처리](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) 샘플에 표시된 대로 [EventProcessorHost](event-hubs-event-processor-host.md)의 여러 인스턴스를 실행하는 것이 좋습니다. 이러한 경우 여러 인스턴스가 자동으로 서로 조정되어 받은 이벤트의 부하를 분산시킵니다. 

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 이벤트 허브에서 메시지를 받은 .NET 표준 응용 프로그램을 만들었습니다. .NET 표준을 사용하여 이벤트 허브에 이벤트를 보내는 방법을 알아보려면 [이벤트 허브에서 이벤트 보내기 - .NET 표준](event-hubs-dotnet-standard-getstarted-send.md)을 참조하세요.

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
