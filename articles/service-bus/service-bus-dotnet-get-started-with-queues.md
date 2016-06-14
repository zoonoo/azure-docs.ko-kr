<properties
   pageTitle="서비스 버스 큐 시작 | Microsoft Azure"
   description="서비스 버스 메시징에 대한 C# 콘솔 응용 프로그램을 작성하는 방법"
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#서비스 버스 큐 시작
[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

##수행될 작업
이 자습서에서는 다음이 완료됩니다.

1. Azure 포털을 사용하여 서비스 버스 네임스페이스를 만듭니다.

2. Azure 포털을 사용하여 서비스 버스 메시징 큐를 만듭니다.

3. 메시지를 보내는 콘솔 응용 프로그램을 작성합니다.

4. 메시지를 수신하는 콘솔 응용 프로그램을 작성합니다.

##필수 조건
1. [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com)

2. Azure 구독

##1\. Azure 포털을 사용하여 네임스페이스 만들기
서비스 버스 네임스페이스를 이미 만든 경우 [Azure 포털 섹션을 사용하여 큐 만들기](#2-create-a-queue-using-the-azure-portal)로 이동합니다.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

##2\. Azure 포털을 사용하여 큐 만들기
서비스 버스 큐를 이미 만든 경우 [큐에 메시지 보내기 섹션](#3-sending-messages-to-the-queue)으로 이동합니다.

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

##3\. 큐에 메시지 보내기
큐에 메시지를 보내기 위해 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

###콘솔 응용 프로그램 만들기
1. Visual Studio를 시작하고 새 콘솔 응용 프로그램을 만듭니다.

###서비스 버스 NuGet 패키지 추가
1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. "Microsoft Azure 서비스 버스"를 검색하고 **Microsoft Azure 서비스 버스** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

    ![NuGet 패키지 선택][1]

###큐에 메시지를 보내는 코드 작성
1. Program.cs 파일 위쪽에 다음 using 문을 추가합니다.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Main 메서드에 다음 코드를 추가하고 네임스페이스를 만들 때 가져온 연결 문자열로 **connectionString** 변수를 설정하고 큐를 만들 때 사용된 큐 이름으로 **queueName**을 설정합니다.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);

    ```

    Program.cs는 다음과 같아야 합니다.

    ```
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
  
3. 프로그램을 실행하고 Azure 포털을 확인합니다. **큐 길이**는 이제 1이 되어야 합니다.
    
      ![큐 길이][2]
    
##4\. 큐에서 메시지 받기
1. 새 콘솔 응용 프로그램을 만들고 위의 응용 프로그램 보내기에 대해 수행한 것처럼 서비스 버스 NuGet 패키지에 참조를 추가합니다.

2. Program.cs 파일 위쪽에 다음 using 문을 추가합니다.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Main 메서드에 다음 코드를 추가하고 네임스페이스를 만들 때 가져온 연결 문자열로 **connectionString** 변수를 설정하고 큐를 만들 때 사용된 큐 이름으로 **queueName**을 설정합니다.

    ```
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

    ```
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
  
4. 프로그램을 실행하고 Azure 포털을 확인합니다. **큐 길이**는 이제 0이 되어야 합니다.

    ![큐 길이][3]
  
축하합니다. 이제 큐를 만들고 메시지를 보내고 메시지를 받았습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##다음 단계

Azure 서비스 버스 메시징의 고급 기능 중 일부를 보여 주는 샘플로 GitHub 리포지토리를 확인하세요. [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

<!--Image references-->

[1]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[2]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-send.png
[3]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->