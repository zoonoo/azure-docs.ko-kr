---
title: Azure 빠른 시작 - PowerShell을 사용하여 이벤트 허브 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 이벤트 허브를 만든 다음, .NET 표준 SDK를 사용하여 이벤트를 수신하는 방법을 설명합니다.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: a8a29484af7c95e9cffa1c7048c0f7b8d642c868
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42023984"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 이벤트 허브 만들기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하고 처리할 수 있는 확장성이 뛰어난 데이터 스트리밍 플랫폼 및 수집 서비스입니다. 이 빠른 시작에서는 Azure PowerShell을 사용하여 이벤트 허브를 만든 다음, .NET Standard SDK를 사용하여 이벤트 허브에서 송수신하는 방법을 보여 줍니다.

이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요][].

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

- [Visual Studio 2017 업데이트 3(버전 15.3, 26730.01)](http://www.visualstudio.com/vs) 이상
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 사용 중인 경우이 빠른 시작을 완료하려면 PowerShell의 최신 버전을 실행해야 합니다. 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)을 참조하세요.

## <a name="provision-resources"></a>리소스 프로비전

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스의 논리적 컬렉션으로, 이벤트 허브를 만들려면 리소스 그룹이 필요합니다. 

다음 예제에서는 미국 동부 지역의 리소스 그룹을 만듭니다. 사용할 리소스 그룹의 이름과 위치로 `myResourceGroup`을 바꿉니다.

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

리소스 그룹을 만든 후에는 해당 리소스 그룹 안에 Event Hubs 네임스페이스를 만듭니다. Event Hubs 네임스페이스는 이벤트 허브를 만들 수 있는 정규화된 고유의 도메인 이름을 제공합니다. `namespace_name`을 네임스페이스의 고유한 이름으로 바꿉니다.

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

Event Hubs 네임스페이스가 있으므로 이제 해당 네임스페이스 안에 이벤트 허브를 만듭니다.

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>이벤트 처리기 호스트에 대한 저장소 계정 만들기

이벤트 처리기 호스트는 검사점과 병렬 수신기를 관리하여 Event Hubs로부터의 이벤트 수신을 간소화합니다. 검사점 지정을 위해 이벤트 처리기 호스트에는 저장소 계정이 필요합니다. 저장소 계정을 만들고 키를 가져오려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

연결 문자열은 이벤트 허브 및 프로세스 이벤트에 연결하기 위해 필요합니다. 연결 문자열을 가져오려면 다음을 실행합니다.

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Event Hubs로 스트림

이제 Event Hubs로의 스트리밍을 시작할 수 있습니다. 샘플은 [Event Hubs 리포지토리](https://github.com/Azure/azure-event-hubs)에서 Git 복제하거나 다운로드할 수 있습니다.

### <a name="ingest-events"></a>이벤트 수집

스트리밍 이벤트를 시작하려면 GitHub에서 [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender)를 다운로드하거나, 다음 명령을 실행하여 [Event Hubs GitHub 리포지토리](https://github.com/Azure/azure-event-hubs)를 복제합니다.

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

\azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender 폴더로 이동한 다음, Visual Studio에 SampleSender.sln 파일을 로드합니다.

이제[Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) Nuget 패키지를 프로젝트에 추가합니다.

Program.cs 파일에서 이벤트 허브 이름과 연결 문자열로 다음 자리 표시자를 바꿉니다.

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

이제 샘플을 빌드하고 실행합니다. 이벤트가 이벤트 허브에 수집되는 것을 볼 수 있습니다.

![][3]

### <a name="receive-and-process-events"></a>이벤트 수신 및 처리

이제 방금 보낸 메시지를 수신하는 이벤트 처리기 호스트 수신기 샘플을 다운로드합니다. GitHub에서 [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)를 다운로드하거나, 다음 명령을 실행하여 [Event Hubs GitHub 리포지토리](https://github.com/Azure/azure-event-hubs)를 복제합니다.

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

\azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver 폴더로 이동한 다음, Visual Studio에 SampleEphReceiver.sln 솔루션 파일을 로드합니다.

이제 [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 및 [Microsoft.Azure.EventHubs.Process또는 ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) Nuget 패키지를 프로젝트에 추가합니다.

Program.cs 파일에서 해당 값으로 다음 상수를 바꿉니다.

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

이제 샘플을 빌드하고 실행합니다. 샘플 응용 프로그램에 이벤트가 수신되는 것을 확인할 수 있습니다.

![][4]

다음과 같이 Azure Portal에서 지정된 Event Hubs 네임스페이스에 대해 이벤트가 처리되는 속도를 확인할 수 있습니다.

![][5]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료하면 리소스 그룹과 네임스페이스, 저장소 계정 및 그 안의 이벤트 허브를 삭제할 수 있습니다. 만든 리소스 그룹의 이름으로 `myResourceGroup`을 바꿉니다. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이벤트 허브에서 이벤트를 보내고 받는 데 필요한 Event Hubs 네임스페이스 및 기타 리소스를 만들었습니다. 자세히 알아보려면 다음 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Event Hubs 데이터 스트림에서 잘못된 데이터 시각화](event-hubs-tutorial-visualize-anomalies.md)

[계정을 만드세요]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
