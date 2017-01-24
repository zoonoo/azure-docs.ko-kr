---
title: "Azure Queue Storage 및 Visual Studio 연결 서비스 시작(ASP.NET) | Microsoft Docs"
description: "Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio ASP.NET 프로젝트에서 Azure Queue Storage 사용을 시작하는 방법입니다."
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f58c2b522f81dc2dc86f0d2c6bc4872504cf7377
ms.openlocfilehash: 70875287e79aaf49e1b8802cf2953cd5381b97f4


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Azure Queue Storage 및 Visual Studio 연결 서비스 시작(ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요

Azure 큐 저장소는 HTTP 또는 HTTPS를 통해 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 큐 메시지의 크기는 최대 64KB일 수 있으며, 하나의 큐에 저장소 계정의 총 용량 제한까지 제한 없는 수의 메시지가 포함될 수 있습니다.

이 문서에서는 Azure Queue Storage 엔터티를 프로그래밍 방식으로 관리하여 Azure 큐 만들기, 큐 메시지 추가, 수정, 읽기 및 제거와 같은 일반적인 작업을 수행하는 방법을 설명합니다.

> [!NOTE]
> 
> 이 문서의 코드 섹션에서는 연결 서비스를 사용하여 Azure Storage 계정에 이미 연결되어 있다고 가정합니다. 연결 서비스는 Visual Studio 솔루션 탐색기를 열고, 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가->연결 서비스** 옵션을 선택하여 구성됩니다. 여기에서 대화 상자 지침에 따라 원하는 Azure Storage 계정에 연결합니다.      

## <a name="create-a-queue"></a>큐 만들기

다음 단계에서는 프로그래밍 방식으로 큐를 만드는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다.

1. 다음 *using* 지시문을 추가합니다.
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 원하는 큐 이름에 대한 참조를 나타내는 **CloudQueue** 개체를 가져옵니다. (*<queue-name>*을 만들려는 큐의 이름으로 변경합니다.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 큐가 아직 없으면 **CloudQueue.CreateIfNotExists** 메서드를 호출하여 만듭니다. 

        queue.CreateIfNotExists();


## <a name="add-a-message-to-a-queue"></a>큐에 메시지 추가

다음 단계에서는 프로그래밍 방식으로 큐에 메시지를 추가하는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다.

1. 다음 *using* 지시문을 추가합니다.
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 원하는 큐 이름에 대한 참조를 나타내는 **CloudQueue** 개체를 가져옵니다. (*<queue-name>*을 메시지를 추가하려는 큐의 이름으로 변경합니다.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 큐에 추가할 메시지를 나타내는 **CloudQueueMessage** 개체를 만듭니다. **CloudQueueMessage** 개체는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다. (*<queue-message>*를 추가하려는 메시지로 변경합니다.)

        CloudQueueMessage message = new CloudQueueMessage(<queue-message>);

6. **CloudQueue.AddMessage** 메서드를 호출하여 큐에 메시지를 추가합니다.

        queue.AddMessage(message);

## <a name="read-a-message-from-a-queue-without-removing-it"></a>메시지를 제거하지 않고 큐에서 읽기

다음 단계에서는 대기 중인 메시지를 프로그래밍 방식으로 미리 확인하는 방법을 설명합니다(첫 번째 메시지를 제거하지 않고 읽음). ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다. 

1. 다음 *using* 지시문을 추가합니다.
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 큐에 대한 참조를 나타내는 **CloudQueue** 개체를 가져옵니다. (*<queue-name>*을 메시지를 읽어올 큐의 이름으로 변경합니다.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. **CloudQueue.PeekMessage** 메서드를 호출하여 큐에서 메시지를 제거하지 않고도 큐의 맨 앞에서 메시지를 읽습니다.

        CloudQueueMessage message = queue.PeekMessage();

6. **CloudQueueMessage.AsBytes** 또는 **CloudQueueMessage.AsString** 속성을 사용하여 **CloudQueueMessage** 개체의 값에 액세스합니다.

        string messageAsString = message.AsString;
        byte[] messageAsBytes = message.AsBytes;

## <a name="read-and-remove-a-message-from-a-queue"></a>큐에서 메시지 읽기 및 제거

다음 단계에서는 프로그래밍 방식으로 대기된 메시지를 읽은 후 삭제하는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다. 

1. 다음 *using* 지시문을 추가합니다.
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 큐에 대한 참조를 나타내는 **CloudQueue** 개체를 가져옵니다. (*<queue-name>*을 메시지를 읽어올 큐의 이름으로 변경합니다.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. **CloudQueue.GetMessage** 메서드를 호출하여 큐의 첫 번째 메시지를 읽습니다. **CloudQueue.GetMessage** 메서드는 메시지를 읽는 다른 코드에 해당 메시지가 30초(기본값) 동안 보이지 않도록 하여 사용자가 메시지를 처리하는 동안 다른 코드가 메시지를 수정하거나 삭제할 수 없게 합니다. 메시지가 보이지 않는 기간을 변경하려면 **CloudQueue.GetMessage** 메서드에 전달되는 **visibilityTimeout** 매개 변수를 수정합니다.

        // This message will be invisible to other code for 30 seconds.
        CloudQueueMessage message = queue.GetMessage();     

6. **CloudQueueMessage.Delete** 메서드를 호출하여 큐에서 메시지를 삭제합니다.

        queue.DeleteMessage(message);

## <a name="get-the-queue-length"></a>큐 길이 가져오기

다음 단계에서는 프로그래밍 방식으로 큐 길이(메시지 수)를 가져오는 방법을 보여 줍니다. ASP.NET MVC 앱에서 이 코드를 실행하면 컨트롤러로 전환됩니다. 

1. 다음 *using* 지시문을 추가합니다.
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 큐에 대한 참조를 나타내는 **CloudQueue** 개체를 가져옵니다. (*<queue-name>*을 길이를 쿼리 중인 큐의 이름으로 변경합니다.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. **CloudQueue.FetchAttributes** 메서드를 호출하여 큐의 특성(길이 포함)을 검색합니다. 

        queue.FetchAttributes();

6. **CloudQueue.ApproximateMessageCount** 속성에 액세스하여 큐의 길이를 가져옵니다.
 
        int? nMessages = queue.ApproximateMessageCount;

## <a name="delete-a-queue"></a>큐 삭제
다음 단계에서는 프로그래밍 방식으로 큐를 삭제하는 방법을 보여 줍니다. 

1. 다음 *using* 지시문을 추가합니다.
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*<storage-account-name>*을 액세스하려는 Azure Storage 계정의 이름으로 변경합니다.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 큐에 대한 참조를 나타내는 **CloudQueue** 개체를 가져옵니다. (*<queue-name>*을 길이를 쿼리 중인 큐의 이름으로 변경합니다.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. **CloudQueue.Delete** 메서드를 호출하여 **CloudQueue** 개체로 나타내는 큐를 삭제합니다.

        messageQueue.Delete();

## <a name="next-steps"></a>다음 단계
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Dec16_HO2-->


