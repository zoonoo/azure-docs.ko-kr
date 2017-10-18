---
title: "Azure Service Bus 항목 및 구독 시작 | Microsoft Docs"
description: "Service Bus 메시징 항목 및 구독을 사용하는 C# 콘솔 응용 프로그램을 작성합니다."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 3646d14be662af0fdf80790cb53ddc581b33a146
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-topics"></a>Service Bus 큐 항목 시작

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 자습서에서 다루는 단계는 다음과 같습니다.

1. Azure Portal을 사용하여 Service Bus 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 Service Bus 항목을 만듭니다.
3. Azure Portal을 사용하여 해당 항목에 Service Bus 구독을 만듭니다.
4. 메시지를 항목으로 보내는 콘솔 응용 프로그램을 작성합니다.
5. 구독으로부터 해당 메시지를 받을 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건

1. [Visual Studio 2015 이상](http://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2017을 사용합니다.
2. Azure 구독.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Azure Portal을 사용하여 네임스페이스 만들기

Service Bus 메시징 네임스페이스를 이미 만든 경우 [Azure Portal을 사용하여 큐 만들기](#2-create-a-topic-using-the-azure-portal) 섹션으로 이동합니다.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Azure Portal을 사용하여 항목 만들기

1. [Azure Portal][azure-portal]에 로그인합니다.
2. 포털의 왼쪽 탐색 창에서 **Service Bus**(**Service Bus**가 표시되지 않으면 **더 많은 서비스** 클릭)를 클릭합니다.
3. 항목을 만들려는 네임스페이스를 클릭합니다. 네임스페이스 개요 블레이드가 나타납니다.
   
    ![토픽 만들기][createtopic1]
4. **Service Bus 네임스페이스** 블레이드에서 **항목**을 선택한 다음 **항목 추가**를 클릭합니다.
   
    ![항목 선택][createtopic2]
5. 항목의 이름을 입력하고 **분할 사용** 옵션을 선택 취소합니다. 다른 옵션은 기본값 그대로 둡니다.
   
    ![새로 만들기 선택][createtopic3]
6. 블레이드 하단에서 **만들기**를 클릭합니다.

## <a name="3-create-a-subscription-to-the-topic"></a>3. 항목에 대한 구독 만들기

1. 포털 리소스 창에서 1단계에서 만든 네임스페이스를 클릭한 다음 2단계에서 만든 항목의 이름을 클릭합니다.
2. 개요 창의 맨 위에서 **구독** 옆에 있는 더하기 기호를 클릭하여 이 항목에 구독을 추가합니다.

    ![구독 만들기][createtopic4]

3. 구독의 이름을 입력합니다. 다른 옵션은 기본값 그대로 둡니다.

## <a name="4-send-messages-to-the-topic"></a>4. 토픽에 메시지 보내기

항목에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio를 시작하고 **콘솔 앱(.NET Framework)** 프로젝트를 만듭니다.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **WindowsAzure.ServiceBus**를 검색한 다음 **WindowsAzure.ServiceBus** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
   
    ![NuGet 패키지 선택][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>항목에 메시지를 보내는 코드 작성

1. Program.cs 파일 위쪽에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. `Main` 메서드에 다음 코드를 추가합니다. `connectionString` 변수는 네임스페이스를 만들 때 가져온 연결 문자열에 설정하고, `topicName`은 항목을 만들 때 사용된 이름에 설정합니다.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. 프로그램을 실행하고 Azure Portal을 확인합니다. 네임스페이스 **개요** 블레이드에서 항목 이름을 클릭합니다. 항목 **Essentials** 블레이드가 표시됩니다. 블레이드 맨 아래 가까이 나열된 구독에서 각 구독에 대한 **메시지 수** 값이 이제 1임을 확인합니다. 메시지를 검색하지 않고 보낸 사람 응용 프로그램을 실행할 때마다(다음 섹션에서 설명) 이 값이 1씩 증가합니다. 또한 앱이 항목/구독에 메시지를 추가할 때마다 항목의 현재 크기는 **Essentials** 블레이드에 있는 **현재** 값을 증가시킵니다.
   
      ![메시지 크기][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. 구독에서 메시지 받기

1. 방금 보낸 메시지를 받으려면 새 콘솔 응용 프로그램을 만들고 이전의 보낸 사람 응용 프로그램과 유사한 Service Bus NuGet 패키지에 참조를 추가합니다.
2. Program.cs 파일 위쪽에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. `Main` 메서드에 다음 코드를 추가합니다. `connectionString` 변수는 네임스페이스를 만들 때 가져온 연결 문자열에 설정하고, `topicName`은 항목을 만들 때 사용된 이름에 설정합니다. 또한 `<your subscription name>`을 3단계에서 만든 구독의 이름으로 바꿔야 합니다. 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. 프로그램을 실행하고 포털을 다시 확인합니다. **메시지 수**와 **현재** 값이 이제 0이 됩니다.
   
    ![항목 길이][topic-message-receive]

축하합니다. 이제 토픽 및 구독을 만들고, 메시지를 보내고 해당 메시지를 받았습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징의 많은 고급 기능 중 일부를 보여 주는 [샘플이 있는 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
