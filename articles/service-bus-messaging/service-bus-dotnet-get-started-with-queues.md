---
title: "Azure Service Bus 큐 시작 | Microsoft Docs"
description: "Service Bus 메시징 큐를 사용하는 C# 콘솔 응용 프로그램을 작성합니다."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 99a377db6341d90d263b98e14227db61dd9beabd
ms.contentlocale: ko-kr
ms.lasthandoff: 08/15/2017

---
# <a name="get-started-with-service-bus-queues"></a>Service Bus 큐 시작
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>수행될 작업
이 자습서에서 다루는 단계는 다음과 같습니다.

1. Azure Portal을 사용하여 Service Bus 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 Service Bus 큐를 만듭니다.
3. 메시지를 보내는 콘솔 응용 프로그램을 작성합니다.
4. 이전 단계에서 보낸 메시지를 수신하는 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건
1. [Visual Studio 2015 이상](http://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2017을 사용합니다.
2. Azure 구독.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Azure Portal을 사용하여 네임스페이스 만들기
Service Bus 메시징 네임스페이스를 이미 만든 경우 [Azure Portal을 사용하여 큐 만들기](#2-create-a-queue-using-the-azure-portal) 섹션으로 이동합니다.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Azure Portal을 사용하여 큐 만들기
Service Bus 큐를 이미 만든 경우 [큐에 메시지 보내기](#3-send-messages-to-the-queue) 섹션으로 이동합니다.

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. 큐에 메시지 보내기
큐에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio를 시작하고 **콘솔 앱(.NET Framework)** 프로젝트를 만듭니다.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가
1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **Microsoft Azure Service Bus**를 검색한 다음 **WindowsAzure.ServiceBus** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
   
    ![NuGet 패키지 선택][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>큐에 메시지를 보내는 코드 작성
1. Program.cs 파일 위쪽에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. `Main` 메서드에 다음 코드를 추가합니다. `connectionString` 변수를 네임스페이스를 만들 때 가져온 연결 문자열에 설정하고 `queueName`을 큐를 만들 때 사용된 큐 이름에 설정합니다.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs 파일은 다음과 같아야 합니다.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. 프로그램을 실행하고 Azure Portal을 확인합니다. 네임스페이스 **개요** 블레이드에서 큐 이름을 클릭합니다. 큐 **Essentials** 블레이드가 표시됩니다. **활성 메시지 수** 값이 1이어야 합니다. 메시지를 검색하지 않고 보낸 사람 응용 프로그램을 실행할 때마다 이 값이 1씩 증가합니다. 또한 큐의 현재 크기는 앱이 큐에 메시지를 추가할 때마다 증가합니다.
   
      ![메시지 크기][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. 큐에서 메시지 받기

1. 방금 보낸 메시지를 받으려면 새 콘솔 응용 프로그램을 만들고 이전의 보낸 사람 응용 프로그램과 유사한 Service Bus NuGet 패키지에 참조를 추가합니다.
2. Program.cs 파일 위쪽에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. `Main` 메서드에 다음 코드를 추가합니다. `connectionString` 변수를 네임스페이스를 만들 때 가져온 연결 문자열에 설정하고 `queueName`을 큐를 만들 때 사용된 큐 이름에 설정합니다.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs 파일은 다음과 같아야 합니다.
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. 프로그램을 실행하고 포털을 다시 확인합니다. **활성 메시지 수**와 **현재** 값이 이제 0이 됩니다.
   
    ![큐 길이][queue-message-receive]

축하합니다. 이제 큐를 만들고 메시지를 보내고 메시지를 받았습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징의 많은 고급 기능 중 일부를 보여 주는 [샘플이 있는 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

