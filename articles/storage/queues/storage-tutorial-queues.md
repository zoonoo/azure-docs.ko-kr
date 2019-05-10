---
title: 자습서 - Azure 스토리지 큐 작업 - Azure Storage
description: Azure 큐 서비스를 사용하여 큐를 만들고 메시지를 삽입하고, 가져오고, 삭제하는 방법에 대한 자습서입니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 81d7572f800f191791158f2c1f99e1f072980116
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151059"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>자습서: Azure 스토리지 큐 작업

Azure Queue storage는 분산된 애플리케이션의 구성 요소 간에 통신을 가능하게 해주는 클라우드 기반 큐를 구현합니다. 각 큐는 발신자 구성 요소에서 추가하고 수신자 구성 요소에서 처리할 수 있는 메시지 목록을 유지합니다. 큐를 사용하면 수요에 따라 애플리케이션을 즉시 확장할 수 있습니다. 이 문서에서는 Azure 스토리지 큐를 작업하는 기본 단계를 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> - Azure 저장소 계정 만들기
> - 앱 만들기
> - 비동기 코드에 대한 지원 추가
> - 큐 만들기
> - 큐에 메시지 삽입
> - 큐에서 메시지 제거
> - 빈 큐 삭제
> - 명령줄 인수 확인
> - 앱 빌드 및 실행

## <a name="prerequisites"></a>필수 조건

- 플랫폼 간 [Visual Studio Code](https://code.visualstudio.com/download) 편집기의 무료 복사본을 가져옵니다.
- [.NET Core SDK](https://dotnet.microsoft.com/download)를 다운로드하여 설치합니다.
- 현재 Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기

먼저, Azure 스토리지 계정을 만듭니다. 스토리지 계정을 만드는 방법에 대한 단계별 지침은 [스토리지 계정 만들기](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) 빠른 시작을 참조하세요.

## <a name="create-the-app"></a>앱 만들기

**QueueApp**이라는 .NET Core 애플리케이션을 만듭니다. 간략한 설명을 위해, 이 앱에서는 큐를 통해 메시지를 보내고 받는 작업을 모두 처리하겠습니다.

1. CMD, PowerShell 또는 Azure CLI 같은 콘솔 창에서 `dotnet new` 명령을 사용하여 **QueueApp**이라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs**라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

   ```console
   dotnet new console -n QueueApp
   ```

2. 새로 만든 **QueueApp** 폴더로 전환하여 모두 정상인지 확인하는 앱을 빌드합니다.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   다음과 유사한 결과가 표시됩니다.

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>비동기 코드에 대한 지원 추가

앱에서 클라우드 리소스를 사용하므로 코드가 비동기적으로 실행됩니다. 그러나 C#의 **async** 및 **await**는 C# 7.1까지 **Main** 메서드에서 유효한 키워드가 아니었습니다. **csproj** 파일의 플래그를 통해 해당 컴파일러로 쉽게 전환할 수 있습니다.

1. 프로젝트 디렉터리의 명령줄에 `code .`를 입력하여 현재 디렉터리에서 Visual Studio Code를 엽니다. 명령줄 창을 계속 열어 둡니다. 이후에 몇 가지 명령을 더 실행해야 합니다. 빌드 및 디버그에 필요한 C# 자산을 추가하라는 메시지가 표시되면 **예** 단추를 클릭합니다.

2. 편집기에서 **QueueApp.csproj** 파일을 엽니다.

3. 빌드 파일의 첫 번째 **PropertyGroup**에 `<LangVersion>7.1</LangVersion>`을 추가합니다. 설치한 .NET 버전에 따라 **TargetFramework**가 다를 수 있으므로 **LangVersion** 태그만 추가해야 합니다.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. **QueueApp.csproj** 파일을 저장합니다.

5. **Program.cs** 원본 파일을 열고 **Main** 메서드를 비동기적으로 실행되도록 업데이트합니다. **void**를 **비동기 작업** 반환 값으로 바꿉니다.

   ```csharp
   static async Task Main(string[] args)
   ```

6. **Program.cs** 파일을 저장합니다.

## <a name="create-a-queue"></a>큐 만들기

1. **WindowsAzure. Storage** 패키지를 프로젝트에 설치합니다(`dotnet add package` 명령 사용). 콘솔 창의 프로젝트 폴더에서 다음 dotnet 명령을 실행합니다.

   ```console
   dotnet add package WindowsAzure.Storage
   ```

2. **Program.cs** 파일의 맨 위에서, `using System;` 문 바로 뒤에 다음 네임스페이스를 추가합니다. 이 앱은 이 네임스페이스의 형식을 사용하여 Azure Storage에 연결하고 큐를 사용합니다.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;
   ```

3. **Program.cs** 파일을 저장합니다.

### <a name="get-your-connection-string"></a>연결 문자열 가져오기

클라이언트 라이브러리는 연결 문자열을 사용하여 연결을 설정합니다. 연결 문자열은 Azure Portal에 있는 스토리지 계정의 **설정** 섹션에서 사용할 수 있습니다.

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 저장소 계정으로 이동합니다.

3. **액세스 키**를 선택합니다.

4. **연결 문자열** 필드의 오른쪽에서 **복사** 단추를 클릭합니다.

![연결 문자열](media/storage-tutorial-queues/get-connection-string.png)

연결 문자열은 다음 형식입니다.

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>앱에 연결 문자열 추가

앱이 스토리지 계정에 액세스할 수 있도록 앱에 연결 문자열을 추가합니다.

1. Visual Studio Code로 돌아갑니다.

2. **Program** 클래스에서, 연결 문자열을 보관할 `private const string connectionString =` 멤버를 추가합니다.

3. 앞에서 Azure Portal에서 복사한 문자열 값을 등호 기호 뒤에 붙여넣습니다. **connectionString** 값은 계정에서 고유하게 유지됩니다.

4. **Main**에서 "Hello World" 코드를 제거합니다. 다음과 비슷하지만 여러분의 고유한 연결 문자열 값으로 바뀐 코드가 표시될 것입니다.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. **CloudQueue** 개체를 만들도록 **Main**을 업데이트합니다. 이 개체는 나중에 send 및 receive 메서드에 전달됩니다.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. 파일을 저장합니다.

## <a name="insert-messages-into-the-queue"></a>큐에 메시지 삽입

큐에 메시지를 보내는 새 메서드를 만듭니다. **Program** 클래스에 다음 메서드를 추가합니다. 이 메서드는 큐 참조를 가져온 다음, 기존 큐가 없으면 [CreateIfNotExistsAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.createifnotexistsasync?view=azure-dotnet)를 호출하여 새 큐를 만듭니다. 그런 다음, [AddMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.addmessageasync?view=azure-dotnet)를 호출하여 메시지를 큐에 추가합니다.

1. **Program** 클래스에 다음 **SendMessageAsync** 메서드를 추가합니다.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. 파일을 저장합니다.

## <a name="dequeue-messages"></a>큐에서 메시지 제거

**ReceiveMessageAsync**라는 새 메서드를 만듭니다. 이 메서드는 [GetMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.getmessageasync?view=azure-dotnet)를 호출하여 큐에서 메시지를 받습니다. 메시지가 성공적으로 수신되면 메시지가 여러 번 처리되지 않도록 큐에서 삭제해야 합니다. 메시지가 수신된 후 [DeleteMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.deletemessageasync?view=azure-dotnet)를 호출하여 큐에서 메시지를 삭제합니다.

1. **Program** 클래스에 다음 **ReceiveMessageAsync** 메서드를 추가합니다.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. 파일을 저장합니다.

## <a name="delete-an-empty-queue"></a>빈 큐 삭제

프로젝트가 끝나면 여기서 만든 리소스가 계속 필요한지 확인하는 것이 가장 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 큐가 있지만 비어 있는 경우 사용자에게 큐를 삭제할 것인지 물어봅니다.

1. 빈 큐를 삭제하는 프롬프트를 포함하도록 **ReceiveMessageAsync** 메서드를 확장합니다.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. 파일을 저장합니다.

## <a name="check-for-command-line-arguments"></a>명령줄 인수 확인

앱에 전달된 명령줄 인수가 있는 경우 이러한 인수가 큐에 추가할 메시지라고 가정합니다. 인수를 조인하여 문자열을 만듭니다. 앞에서 추가한 **SendMessageAsync** 메서드를 호출하여 이 문자열을 메시지 큐에 추가합니다.

명령줄 인수가 없는 경우 검색 작업을 실행합니다. **ReceiveMessageAsync** 메서드를 호출하여 큐의 첫 번째 메시지를 검색합니다.

마지막으로, 사용자 입력을 기다렸다가 **Console.ReadLine**을 호출하여 종료합니다.

1. 명령줄 인수를 확인하고 사용자 입력을 기다리도록 **Main** 메서드를 확장합니다.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. 파일을 저장합니다.

## <a name="complete-code"></a>전체 코드

이 프로젝트의 전체 코드는 다음과 같습니다.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 프로젝트 디렉터리의 명령줄에서 다음 dotnet 명령을 실행하여 프로젝트를 빌드합니다.

   ```console
   dotnet build
   ```

2. 프로젝트가 성공적으로 빌드되면 다음 명령을 실행하여 첫 번째 메시지를 큐에 추가합니다.

   ```console
   dotnet run First queue message
   ```

다음 출력이 표시됩니다.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. 명령줄 인수 없이 앱을 실행하여 큐의 첫 번째 메시지를 검색하고 제거합니다.

   ```console
   dotnet run
   ```

4. 모든 메시지를 제거할 때까지 앱을 계속 실행합니다. 앱을 한 번 더 실행하면 큐가 비어 있으며 큐를 삭제하라는 메시지가 표시됩니다.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

1. 큐 만들기
2. 큐에서 메시지 추가 및 제거
3. Azure 스토리지 큐 삭제

자세한 내용은 Azure 큐 빠른 시작을 확인하세요.

> [!div class="nextstepaction"]
> [큐 빠른 시작](storage-quickstart-queues-portal.md)
