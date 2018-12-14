---
title: .NET Standard를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs
description: .NET Standard를 사용하여 Event Hubs로 이벤트 전송 시작
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
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 633d29d3e2e8a8ab0b746549f126ad45ea781d6e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227895"
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>.NET Standard를 사용하여 Azure Event Hubs로 메시지 전송 시작
Event Hubs는 연결된 디바이스 및 응용 프로그램에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. Event Hubs에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 응용 프로그램 아키텍처의 핵심 구성 요소입니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서는 .NET Core를 사용하여 C#로 작성된 콘솔 응용 프로그램을 사용하여 이벤트 허브로 이벤트를 전송하는 방법을 보여 줍니다. 

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체한 후 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* [Microsoft Visual Studio 2015 또는 2017](https://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2017을 사용하지만 Visual Studio 2015도 지원됩니다.
* [.NET Core Visual Studio 2015 또는 2017 도구](https://www.microsoft.com/net/core). 

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 이벤트 허브 만들기
첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 진행하세요.

## <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio를 시작합니다. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다. .NET Core 콘솔 응용 프로그램을 만듭니다.

![새 프로젝트][1]

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

다음 단계에 따라 [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET 표준 라이브러리 NuGet 패키지를 프로젝트에 추가합니다. 

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭한 다음 "Microsoft.Azure.EventHubs"를 검색하고 **Microsoft.Azure.EventHubs** 패키지를 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

## <a name="write-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

1. Program.cs 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Event Hubs 연결 문자열 및 엔터티 경로(개별 이벤트 허브 이름)에 대한 `Program` 클래스에 상수를 추가합니다. 대괄호 안의 자리 표시자를 이벤트 허브를 만들 때 얻은 적절한 값으로 바꿉니다. `{Event Hubs connection string}`이 네임스페이스 수준 연결 문자열이며 이벤트 허브 문자열이 아님을 확인합니다. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. 다음과 같이 `MainAsync`라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. 다음과 같이 `SendMessagesToEventHub`라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. `Program` 클래스의 `Main` 메서드에 다음 코드를 추가합니다.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs는 다음과 같아야 합니다.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. 프로그램을 실행하고 오류가 없는지 확인합니다.

축하합니다! 이제 Event Hub에 메시지를 보냈습니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 .NET 표준을 사용하여 이벤트 허브로 메시지를 전송했습니다. .NET 표준을 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 알아보려면 [이벤트 허브에서 이벤트 수신 - .NET 표준](event-hubs-dotnet-standard-getstarted-receive-eph.md)을 참조하세요.

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png
