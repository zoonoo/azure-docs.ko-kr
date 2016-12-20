---
title: "Service Bus 큐 시작 | Microsoft Docs"
description: "서비스 버스 메시징에 대한 C# 콘솔 응용 프로그램을 작성하는 방법"
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 560e73eb7129e665d254c3e93b08f29cfb6b1aa9
ms.openlocfilehash: d80bedba1909de671ef96ac5372d9ff15a90764e


---
# <a name="get-started-with-service-bus-queues"></a>서비스 버스 큐 시작
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>수행될 작업
이 자습서에서는 다음이 완료됩니다.

1. Azure 포털을 사용하여 서비스 버스 네임스페이스를 만듭니다.
2. Azure 포털을 사용하여 서비스 버스 메시징 큐를 만듭니다.
3. 메시지를 보내는 콘솔 응용 프로그램을 작성합니다.
4. 메시지를 수신하는 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건
1. [Visual Studio 2013 또는 Visual Studio 2015](http://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2015를 사용합니다.
2. Azure 구독.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Azure 포털을 사용하여 네임스페이스 만들기
Service Bus 네임스페이스를 이미 만든 경우 [Azure Portal을 사용하여 큐 만들기](#2-create-a-queue-using-the-azure-portal) 섹션으로 이동합니다.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Azure 포털을 사용하여 큐 만들기
Service Bus 큐를 이미 만든 경우 [큐에 메시지 보내기](#3-send-messages-to-the-queue) 섹션으로 이동합니다.

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. 큐에 메시지 보내기
큐에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

- Visual Studio를 시작하고 새 콘솔 응용 프로그램을 만듭니다.

### <a name="add-the-service-bus-nuget-package"></a>서비스 버스 NuGet 패키지 추가
1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭한 다음 "Microsoft Azure Service Bus"를 검색하고 **Microsoft Azure Service Bus** 항목을 선택합니다. **설치** 를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
   
    ![NuGet 패키지 선택][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>큐에 메시지를 보내는 코드 작성
1. Program.cs 파일 위쪽에 다음 using 문을 추가합니다.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. `Main` 메서드에 다음 코드를 추가하고 **connectionString** 변수를 네임스페이스를 만들 때 가져온 연결 문자열로 설정하고 **queueName**을 큐를 만들 때 사용된 큐 이름으로 설정합니다.
   
    ```csharp
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```
   
    Program.cs는 다음과 같아야 합니다.
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";
   
                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");
   
                client.Send(message);
            }
        }
    }
    ```
3. 프로그램을 실행하고 Azure 포털을 확인합니다. 네임스페이스 **개요** 블레이드에서 큐의 이름을 클릭합니다. **활성 메시지 수** 값이 1이어야 합니다.
   
      ![메시지 수][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. 큐에서 메시지 받기
1. 새 콘솔 응용 프로그램을 만들고 이전의 응용 프로그램 보내기와 유사하게 서비스 버스 NuGet 패키지에 참조를 추가합니다.
2. Program.cs 파일 위쪽에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. `Main` 메서드에 다음 코드를 추가하고 **connectionString** 변수를 네임스페이스를 만들 때 가져온 연결 문자열로 설정하고 **queueName**을 큐를 만들 때 사용된 큐 이름으로 설정합니다.
   
    ```csharp
    var connectionString = "";
    var queueName = "samplequeue";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
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
          var connectionString = "";
          var queueName = "samplequeue";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
   
          Console.ReadLine();
        }
      }
    }
    ```
4. 프로그램을 실행하고 포털을 확인합니다. **큐 길이** 값은 이제 0이어야 합니다.
   
    ![큐 길이][queue-message-receive]

축하합니다. 이제 큐를 만들고 메시지를 보내고 메시지를 받았습니다.

## <a name="next-steps"></a>다음 단계
Azure Service Bus 메시징의 더 많은 고급 기능 중 일부를 보여 주는 [샘플이 있는 GitHub 리포지토리](https://github.com/Azure-Samples/azure-servicebus-messaging-samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Dec16_HO1-->


