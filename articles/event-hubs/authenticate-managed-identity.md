---
title: Azure Active Directory를 사용 하 고 관리되는 ID 인증
description: 이 문서에서는 Azure Active Directory를 사용하여 관리되는 ID를 인증하여 Azure Event Hubs 리소스에 액세스하는 방법을 제공합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 672b663a9cab72d465ea00e0a5ade364eadbf64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251523"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Azure Active Directory를 사용하여 관리되는 ID를 인증하여 이벤트 허브 리소스에 액세스
Azure 이벤트 허브는 Azure [리소스에 대한 관리ID를](../active-directory/managed-identities-azure-resources/overview.md)통해 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스에 대한 관리되는 ID는 Azure 가상 시스템(VM), 기능 앱, 가상 시스템 확장 집합 및 기타 서비스에서 실행되는 응용 프로그램에서 Azure AD 자격 증명을 사용하여 Event Hubs 리소스에 대한 액세스를 승인할 수 있습니다. Azure AD 인증과 함께 Azure 리소스에 대해 관리되는 ID를 사용하면 클라우드에서 실행되는 응용 프로그램과 함께 자격 증명을 저장하지 않도록 할 수 있습니다.

이 문서에서는 Azure VM에서 관리되는 ID를 사용하여 이벤트 허브에 대한 액세스를 승인하는 방법을 보여 주며 있습니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용
Azure Resources에 대해 관리되는 ID를 사용하여 VM에서 이벤트 허브 리소스에 권한을 부여하려면 먼저 VM에서 Azure 리소스에 대해 관리되는 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure 포털](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure 파워쉘](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 리소스 관리자 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리되는 ID에 대한 사용 권한 부여
응용 프로그램에서 관리되는 ID에서 Event Hubs 서비스에 대한 요청을 승인하려면 먼저 해당 관리되는 ID에 대한 RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure Event Hubs는 이벤트 허브에서 보내고 읽을 수 있는 권한을 포함하는 RBAC 역할을 정의합니다. RBAC 역할이 관리되는 ID에 할당되면 관리되는 ID는 적절한 범위에서 이벤트 허브 데이터에 대한 액세스 권한을 부여합니다.

RBAC 역할 할당에 대한 자세한 내용은 [Azure Active Directory를 사용하여 인증하여 이벤트 허브 리소스에 액세스할 수](authorize-access-azure-active-directory.md)있는지 를 참조하십시오.

## <a name="use-event-hubs-with-managed-identities"></a>Event Hubs에서 관리 ID 사용
관리되는 ID와 함께 이벤트 허브를 사용하려면 ID에 역할과 적절한 범위를 할당해야 합니다. 이 섹션의 프로시저는 관리되는 ID로 실행되는 간단한 응용 프로그램을 사용하고 Event Hubs 리소스에 액세스합니다.

여기서Azure [앱 서비스에서](https://azure.microsoft.com/services/app-service/)호스팅되는 샘플 웹 응용 프로그램을 사용하고 있습니다. 웹 응용 프로그램을 만들기 위한 단계별 지침은 [Azure에서 ASP.NET Core 웹 앱 만들기를](../app-service/app-service-web-get-started-dotnet.md) 참조하십시오.

응용 프로그램이 만들어지면 다음 단계를 따르십시오. 

1. **설정으로** 이동하여 **ID**를 선택합니다. 
1. **을 사용할** **상태를** 선택합니다. 
1. **저장**을 선택하여 설정을 저장합니다. 

    ![웹 앱에 대한 관리되는 ID](./media/authenticate-managed-identity/identity-web-app.png)

이 설정을 활성화하면 Azure Active Directory(Azure AD)에서 새 서비스 ID가 만들어지고 앱 서비스 호스트에 구성됩니다.

이제 이벤트 허브 리소스의 필수 범위의 역할에 이 서비스 ID를 할당합니다.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Azure 포털을 사용하여 RBAC 역할을 할당하려면
이벤트 허브 리소스에 역할을 할당하려면 Azure 포털에서 해당 리소스로 이동합니다. 리소스에 대한 IAM(액세스 제어) 설정을 표시하고 다음 지침을 따라 역할 할당을 관리합니다.

> [!NOTE]
> 다음 단계에서는 이벤트 허브 네임스페이스에 서비스 ID 역할을 할당합니다. 동일한 단계를 수행하여 모든 Event Hubs 리소스에 범위가 지정된 역할을 할당할 수 있습니다. 

1. Azure 포털에서 이벤트 허브 네임스페이스로 이동하여 네임스페이스에 대한 **개요를** 표시합니다. 
1. 왼쪽 메뉴에서 **IAM(액세스 제어)을** 선택하여 이벤트 허브에 대한 액세스 제어 설정을 표시합니다.
1.  **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
3.  새 역할을 추가하려면 **추가를** 선택합니다.
4.  역할 **할당 추가** 페이지에서 할당할 이벤트 허브 역할을 선택합니다. 그런 다음 역할을 할당하기 위해 등록한 서비스 ID를 찾기 위해 검색합니다.
    
    ![역할 할당 페이지 추가](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  **저장**을 선택합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 서비스 ID에 이벤트 허브 데이터 소유자가 있음을 보여 주었습니다.
    
    ![역할에 할당된 ID](./media/authenticate-managed-identity/role-assigned.png)

역할을 할당하면 웹 응용 프로그램은 정의된 범위 내에서 이벤트 허브 리소스에 액세스할 수 있습니다. 

### <a name="test-the-web-application"></a>웹 애플리케이션 테스트
1. 이벤트 허브 네임스페이스 및 이벤트 허브를 만듭니다. 
2. Azure에 웹 앱을 배포합니다. GitHub의 웹 응용 프로그램에 대한 링크는 다음 탭 섹션을 참조하십시오. 
3. SendReceive.aspx가 웹 앱의 기본 문서로 설정되어 있는지 확인합니다. 
3. 웹 앱의 **ID를** 사용하도록 설정합니다. 
4. 네임스페이스 수준 또는 이벤트 허브 수준에서 **이벤트 허브 데이터 소유자** 역할에 이 ID를 할당합니다. 
5. 웹 응용 프로그램을 실행하고 네임스페이스 이름 및 이벤트 허브 이름, 메시지를 입력하고 **보내기를**선택합니다. 이벤트를 받으려면 **수신**을 선택합니다. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.메시징.EventHubs(최신)](#tab/latest)
이제 웹 응용 프로그램을 시작하고 브라우저를 샘플 aspx 페이지로 가리킬 수 있습니다. [GitHub 리포지토리에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)이벤트 허브 리소스에서 데이터를 보내고 받는 샘플 웹 응용 프로그램을 찾을 수 있습니다.

[NuGet에서](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)최신 패키지를 설치하고 **EventHubProducerClient를** 사용하여 이벤트 허브로 이벤트를 보내고 **EventHubConsumerClient를**사용하여 이벤트를 수신합니다. 

> [!NOTE]
> 관리되는 ID를 사용하여 이벤트를 이벤트 허브에 게시하는 Java 샘플의 경우 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)에서 Azure ID 샘플을 사용하여 이벤트 게시를 참조하십시오.

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs(레거시)](#tab/old)
이제 웹 응용 프로그램을 시작하고 브라우저를 샘플 aspx 페이지로 가리킬 수 있습니다. [GitHub 리포지토리에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)이벤트 허브 리소스에서 데이터를 보내고 받는 샘플 웹 응용 프로그램을 찾을 수 있습니다.

[NuGet에서](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)최신 패키지를 설치하고 다음 코드와 같이 EventHubClient를 사용하여 이벤트 허브에서 데이터를 보내고 받기 시작합니다. 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Kafka용 Event Hubs
Apache Kafka 응용 프로그램을 사용하여 관리되는 ID OAuth를 사용하여 Azure Event Hubs에서 메시지를 보내고 받을 수 있습니다. GitHub: Kafka에 대 한 이벤트 허브에 다음 샘플을 참조 [- 보고 관리되는 ID OAuth를 사용 하 여 메시지를 보내고 받을.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)

## <a name="samples"></a>샘플
- **Azure.메시징.EventHubs** 샘플
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft.Azure.EventHubs 샘플.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    이러한 샘플은 이전 **Microsoft.Azure.EventHubs 라이브러리를** 사용하지만 최신 **Azure.Messaging.EventHubs 라이브러리를** 사용하도록 쉽게 업데이트할 수 있습니다. 샘플을 이전 라이브러리를 새 라이브러리로 이동하려면 [Microsoft.Azure.EventHubs에서 Azure.Messaging.EventHubs로 마이그레이션하는 가이드를](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)참조하십시오.
    이 샘플은 최신 **Azure.Messaging.EventHubs 라이브러리를** 사용하도록 업데이트되었습니다.
- [카프카에 대한 이벤트 허브 - 관리 ID OAuth를 사용하여 메시지를 보내고받을](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>다음 단계
- Azure 리소스에 대한 관리ID에 대해 [What is managed identities for Azure resources?](../active-directory/managed-identities-azure-resources/overview.md) 알아보려면 다음 문서를 참조하세요.
- 다음 관련 문서를 참조하십시오.
    - [Azure Active Directory를 사용하여 응용 프로그램에서 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-application.md)
    - [공유 액세스 서명을 사용하여 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-shared-access-signature.md)
    - [Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)
    - [공유 액세스 서명을 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)