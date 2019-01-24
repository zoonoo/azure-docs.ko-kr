---
title: Azure Event Grid 구독에 대한 배달 못한 편지 및 다시 시도 정책
description: Event Grid에 대한 이벤트 전송 옵션을 사용자 지정하는 방법을 설명합니다. 배달 못한 편지 대상을 설정하고 배달을 다시 시도하기 위한 기간을 지정합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 8660f09c41cf6226f2ffb173508d37c260522b80
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474254"
---
# <a name="dead-letter-and-retry-policies"></a>배달 못한 편지 및 다시 시도 정책

이벤트 구독을 만들 때 이벤트 전송 설정을 사용자 지정할 수 있습니다. 이 문서에서는 배달 못한 편지 위치를 설정하고 재시도 설정을 사용자 지정하는 방법을 보여 줍니다. 이 기능에 대한 자세한 내용은 [Event Grid 메시지 전송 및 재시도](delivery-and-retry.md)를 참조하세요.

## <a name="install-preview-feature"></a>미리 보기 기능 설치

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>배달 못한 편지 위치 설정

배달 못한 편지 위치를 설정하려면 엔드포인트로 전달할 수 없는 이벤트를 보유할 저장소 계정이 필요합니다. 이 예제는 기존 스토리지 계정의 리소스 ID를 가져옵니다. 배달 못한 편지 엔드포인트의 스토리지 계정에 있는 컨테이너를 사용하는 이벤트 구독을 만듭니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

배달 못한 편지를 해제하려면 이벤트 구독을 만드는 명령을 다시 실행하지만 `deadletter-endpoint`에 대한 값을 제공하지는 않습니다. 이벤트 구독을 삭제할 필요가 없습니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

배달 못한 편지를 해제하려면 이벤트 구독을 만드는 명령을 다시 실행하지만 `DeadLetterEndpoint`에 대한 값을 제공하지는 않습니다. 이벤트 구독을 삭제할 필요가 없습니다.

## <a name="set-retry-policy"></a>재시도 정책 설정

Event Grid 구독을 만들 때 Event Grid에서 이벤트 전송을 시도할 기간의 값을 설정할 수 있습니다. 기본적으로 Event Grid는 24시간(1440분) 동안 또는 30회 시도합니다. Event Grid 구독에 대해 이러한 값 중 하나를 설정할 수 있습니다. 이벤트 TTL(Time to Live)의 값은 1과 1440 사이의 정수여야 합니다. 최대 재시도 값은 1과 30 사이의 정수여야 합니다.

[재시도 일정](delivery-and-retry.md#retry-schedule-and-duration)을 구성할 수 없습니다.

### <a name="azure-cli"></a>Azure CLI

이벤트 TTL(Time to Live)을 1440분 이외의 값으로 설정하려면 다음을 사용합니다.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

최대 재시도 값을 30 이외의 값으로 설정하려면 다음을 사용합니다.

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

`event-ttl` 및 `max-deliver-attempts`를 모두 설정하면 Event Grid는 먼저 만료되는 것을 사용하여 이벤트 전송을 중지할 시기를 결정합니다.

### <a name="powershell"></a>PowerShell

이벤트 TTL(Time to Live)을 1440분 이외의 값으로 설정하려면 다음을 사용합니다.

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

최대 재시도 값을 30 이외의 값으로 설정하려면 다음을 사용합니다.

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

`EventTtl` 및 `MaxDeliveryAttempt`를 모두 설정하면 Event Grid는 먼저 만료되는 것을 사용하여 이벤트 전송을 중지할 시기를 결정합니다.

## <a name="next-steps"></a>다음 단계

* 배달 못한 편지 이벤트를 처리하기 위해 Azure 함수 앱을 사용하는 샘플 애플리케이션은 [.NET용 Azure Event Grid 배달 못한 편지 샘플](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)을 참조하세요.
* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
