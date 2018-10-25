---
title: Azure Event Grid 구독에 대한 배달 못한 편지 및 다시 시도 정책
description: Event Grid에 대한 이벤트 전송 옵션을 사용자 지정하는 방법을 설명합니다. 배달 못한 편지 대상을 설정하고 배달을 다시 시도하기 위한 기간을 지정합니다.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077785"
---
# <a name="dead-letter-and-retry-policies"></a>배달 못한 편지 및 다시 시도 정책

이벤트 구독을 만들 때 이벤트 전송 설정을 사용자 지정할 수 있습니다. 이 문서에서는 배달 못한 편지 위치를 설정하고 재시도 설정을 사용자 지정하는 방법을 보여 줍니다. 이 기능에 대한 자세한 내용은 [Event Grid 메시지 전송 및 재시도](delivery-and-retry.md)를 참조하세요.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>배달 못한 편지 위치 설정

배달 못한 편지 위치를 설정하려면 엔드포인트로 전달할 수 없는 이벤트를 보유할 저장소 계정이 필요합니다. 다음 스크립트는 기존 저장소 계정의 리소스 ID를 가져오고 배달 못한 편지 엔드포인트에 대해 해당 저장소 계정의 컨테이너를 사용하는 이벤트 구독을 만듭니다.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

배달 못한 편지를 해제하려면 이벤트 구독을 만드는 명령을 다시 실행하지만 `deadletter-endpoint`에 대한 값을 제공하지는 않습니다. 이벤트 구독을 삭제할 필요가 없습니다.

## <a name="set-retry-policy"></a>재시도 정책 설정

Event Grid 구독을 만들 때 Event Grid에서 이벤트 전송을 시도할 기간의 값을 설정할 수 있습니다. 기본적으로, Event Grid는 24시간(1440분) 동안 최대 30회 시도합니다. Event Grid 구독에 대해 이러한 값 중 하나를 설정할 수 있습니다. 이벤트 TTL(Time to Live)의 값은 1과 1440 사이의 정수여야 합니다. 배달 시도의 최댓값은 1과 30 사이의 정수여야 합니다.

[재시도 일정](delivery-and-retry.md#retry-schedule-and-duration)을 구성할 수 없습니다.

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

최대 재시도 횟수를 30 이외의 값으로 설정하려면 다음을 사용합니다.

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

`event-ttl` 및 `max-deliver-attempts`를 둘 다 설정하는 경우, Event Grid는 첫 번째 옵션을 사용하여 재시도 횟수를 만료합니다.

## <a name="next-steps"></a>다음 단계

* 배달 못한 편지 이벤트를 처리하기 위해 Azure 함수 앱을 사용하는 샘플 응용 프로그램은 [.NET용 Azure Event Grid 배달 못한 편지 샘플](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)을 참조하세요.
* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
