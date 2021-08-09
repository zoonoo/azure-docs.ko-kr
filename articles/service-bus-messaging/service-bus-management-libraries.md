---
title: Azure Service Bus 엔터티를 프로그래밍 방식으로 만들기 | Microsoft Docs
description: 이번 문서에서는 동적으로 또는 프로그래밍 방식으로 Service Bus 네임스페이스 및 엔터티를 프로비저닝하는 방법을 설명합니다.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539874"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Service Bus 네임스페이스 및 엔터티를 동적으로 프로비저닝 
Azure Service Bus 관리 라이브러리는 Service Bus 네임스페이스 및 엔터티를 동적으로 프로비전할 수 있습니다. 이를 통해 복잡한 배포 및 메시지 시나리오가 가능하며, 어떤 엔터티를 프로비전할 것인지 프로그래밍 방식으로 결정할 수 있습니다. 이러한 라이브러리는 현재 .NET에서 사용할 수 있습니다.

## <a name="overview"></a>개요
Service Bus 엔터티를 만들고 관리하는 데 사용할 수 있는 세 가지 관리 라이브러리가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- [Azure.Messaging.ServiceBus.Administration](#azuremessagingservicebusadministration)
- [Microsoft.Azure.ServiceBus.Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

모든 패키지는 **큐, 토픽, 구독** 에 대한 만들기, 가져오기, 나열, 삭제, 업데이트 작업을 지원합니다. 그러나 [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)만 **네임스페이스** 에 대한 만들기, 업데이트, 나열, 가져오기, 삭제 작업을 지원하며, SAS 키 등을 나열하고 다시 생성합니다. 

Microsoft.Azure.Management.ServiceBus 라이브러리는 Azure AD(Azure Active Directory) 인증에서만 작동하며 연결 문자열 사용을 지원하지 않습니다. 반면, 다른 두 라이브러리(Azure.Messaging.ServiceBus 및 Microsoft.Azure.ServiceBus)는 서비스 인증을 위해 연결 문자열 사용을 지원하며 사용하기가 더 쉽습니다. 두 라이브러리 중에 더 최신인 Azure.Messaging.ServiceBus를 사용하는 것이 좋습니다.

다음 섹션에서 해당 라이브러리에 대한 자세한 정보를 제공합니다. 

## <a name="azuremessagingservicebusadministration"></a>Azure.Messaging.ServiceBus.Administration
[Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration) 네임스페이스의 [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) 클래스를 사용하여 네임스페이스, 큐, 토픽, 구독을 관리할 수 있습니다. 다음은 샘플 코드입니다. 전체 예제는 [CRUD 예제](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs)를 참조하세요.

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft.Azure.ServiceBus.Management 
[Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management) 네임스페이스의 [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) 클래스를 사용하여 네임스페이스, 큐, 토픽, 구독을 관리할 수 있습니다. 다음은 샘플 코드입니다. 

> [!NOTE]
> 최신 SDK인 `Azure.Messaging.ServiceBus.Administration` 라이브러리의 `ServiceBusAdministrationClient` 클래스를 사용하는 것이 좋습니다. 자세한 내용은 [첫 번째 섹션](#azuremessagingservicebusadministration)을 참조하세요. 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
이 라이브러리는 Azure Resource Manager 기반 컨트롤 플레인 SDK의 일부입니다. 

### <a name="prerequisites"></a>필수 구성 요소

해당 라이브러리를 사용하려면 Azure AD(Azure Active Directory) 서비스로 인증해야 합니다. Azure AD를 사용하려면 Azure 리소스에 대한 액세스를 제공하는 서비스 주체로 인증해야 합니다. 서비스 주체 만들기에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.  

* [Azure Portal을 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)

이러한 자습서는 관리 라이브러리를 통해 인증에 사용되는 `AppId`(클라이언트 ID), `TenantId` 및 `ClientSecret`(인증 키)를 제공합니다. 실행하려는 리소스 그룹에 대한 [**Azure Service Bus 데이터 소유자**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 또는 [**Contributor**](../role-based-access-control/built-in-roles.md#contributor) 사용 권한이 최소한 필요합니다.

### <a name="programming-pattern"></a>프로그래밍 패턴

Service Bus 리소스를 조작하는 패턴은 일반 프로토콜을 따릅니다.

1. **Microsoft.IdentityModel.Clients.ActiveDirectory** 라이브러리를 사용하여 Azure AD에서 토큰을 가져옵니다.
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. `ServiceBusManagementClient` 개체를 만듭니다.

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. `CreateOrUpdate` 매개 변수를 지정된 값으로 설정합니다.

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. 호출을 실행합니다.

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

### <a name="complete-code-to-create-a-queue"></a>큐를 만드는 전체 코드
Service Bus 큐를 만드는 샘플 코드는 다음과 같습니다. 전체 예제는 [GitHub의 .NET 관리 샘플](https://github.com/Azure-Samples/service-bus-dotnet-management/)을 참조하세요. 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

## <a name="fluent-library"></a>흐름 라이브러리
흐름 라이브러리를 사용하여 Service Bus 엔터티를 관리하는 예제는 [이 샘플](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
아래 참조 토픽을 참조하세요. 

- [Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)