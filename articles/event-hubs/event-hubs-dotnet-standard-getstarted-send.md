---
title: ".NET Standard를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs"
description: ".NET Standard를 사용하여 Event Hubs로 이벤트 전송 시작"
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
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: c549676b2126448d52ed43af509671d0b593a064

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>.NET Standard를 사용하여 Event Hubs로 메시지 전송 시작

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender)에서 사용할 수 있습니다.

## <a name="what-will-be-accomplished"></a>수행될 작업

이 자습서에서는 기존 솔루션 **SampleSender**(이 폴더에 있음)를 만드는 방법을 보여 줍니다. `EhConnectionString`, `EhEntityPath` 및 `StorageAccount` 문자열을 이벤트 허브 값으로 바꾸어 이 솔루션을 있는 그대로 실행하거나 이 자습서를 참조하여 직접 만들 수도 있습니다.

이 자습서에서는 이벤트 허브에 메시지를 전송하는 .NET Core 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건

1. [Visual Studio 2015](http://www.visualstudio.com)

2. [.NET Core Visual Studio 2015 도구](http://www.microsoft.com/net/core).

3. Azure 구독.

4. Event Hubs 네임스페이스

## <a name="send-messages-to-an-event-hub"></a>이벤트 허브에 메시지 전송

이벤트 허브에 메시지를 전송하려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

* Visual Studio를 시작하고 새 .NET Core 콘솔 응용 프로그램을 만듭니다.

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. **찾아보기** 탭을 클릭한 다음 "Microsoft Azure Event Hubs"를 검색하고 **Microsoft Azure Event Hubs** 항목을 선택합니다. **설치** 를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

1. Program.cs 파일 위쪽에 다음 `using` 문을 추가합니다.

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. Event Hubs 연결 문자열 및 엔터티 경로(개별 이벤트 허브 이름)에 대한 `Program` 클래스에 상수를 추가합니다. 대괄호 안의 자리 표시자를 이벤트 허브를 만들 때 얻은 적절한 값으로 바꿉니다.

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. 다음과 같이 `MainAsync`이라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```cs
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. 다음과 같이 `SendMessagesToEventHub`이라는 새 메서드를 `Program` 클래스에 추가합니다.

    ```cs
    // Creates an Event Hub client and sends 100 messages to the event hub.
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

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs는 다음과 같아야 합니다.

    ```cs
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
    
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
    
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
    
                await SendMessagesToEventHub(100);
    
                await eventHubClient.CloseAsync();
    
                Console.WriteLine("Press any key to exit.");
                Console.ReadLine();
            }
    
            // Creates an Event Hub client and sends 100 messages to the event hub.
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
  
6. 프로그램을 실행하고 발생한 오류가 없는지 확인합니다.
  
축하합니다. 이제 이벤트 허브에 메시지를 보냈습니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs에서 이벤트 수신](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [이벤트 허브 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


