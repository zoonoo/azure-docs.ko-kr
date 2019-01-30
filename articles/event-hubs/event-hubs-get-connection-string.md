---
title: 연결 문자열 가져오기 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 클라이언트가 Azure Event Hubs에 연결하는 데 사용할 수 있는 연결 문자열을 가져오기 위한 지침을 제공합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 31220002f8529fd31407470e7650a4c97b62f2b4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535277"
---
# <a name="get-an-event-hubs-connection-string"></a>Event Hubs 연결 문자열 가져오기

Event Hubs를 사용하려면 Event Hubs 네임스페이스를 만들어야 합니다. 네임스페이스는 여러 Event Hubs/Kafka 토픽을 저장할 수 있는 범위 지정 컨테이너로 사용됩니다. 이 네임스페이스는 고유한 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)을 제공합니다. 네임스페이스가 만들어지면 Event Hubs와 통신하는 데 필요한 연결 문자열을 가져올 수 있습니다.

Azure Event Hubs에 대한 연결 문자열에 포함되는 구성 요소는 다음과 같습니다.

* FQDN = 만든 EventHubs 네임스페이스의 FQDN(여기에는 EventHubs 네임스페이스 이름 뒤에 servicebus.windows.net이 포함됨)
* SharedAccessKeyName = 애플리케이션의 SAS 키에 대해 선택한 이름
* SharedAccessKey = 키에 대해 생성된 값

연결 문자열 템플릿은 다음과 같습니다.
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

연결 문자열의 예제는 `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`와 같을 수 있습니다.

이 문서에서는 연결 문자열을 가져오는 다양한 방법을 안내합니다.

## <a name="get-connection-string-from-the-portal"></a>포털에서 연결 문자열 가져오기

Event Hubs 네임스페이스가 있으면 아래와 같이 포털의 개요 섹션에서 연결 문자열을 제공할 수 있습니다.

![Event Hubs 연결 문자열](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

개요 섹션에서 연결 문자열 링크를 클릭하면 아래 그림과 같이 SAS 정책 탭이 열립니다.

![Event Hubs SAS 정책](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

새 SAS 정책을 추가하여 연결 문자열을 가져오거나 이미 만들어진 기본 정책을 사용할 수 있습니다. 정책이 열리면 아래 그림과 같이 연결 문자열을 가져옵니다.

![Event Hubs에서 연결 문자열 가져오기](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Azure PowerShell을 사용하여 연결 문자열 가져오기
Get-AzureRmEventHubNamespaceKey를 사용하여 아래와 같이 특정 정책/규칙 이름에 대한 연결 문자열을 가져올 수 있습니다.

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

자세한 내용은 [Azure Event Hubs PowerShell 모듈](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey)을 참조하세요.

## <a name="getting-the-connection-string-with-azure-cli"></a>Azure CLI를 사용하여 연결 문자열 가져오기
다음을 사용하여 네임스페이스에 대한 연결 문자열을 가져올 수 있습니다.

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

자세한 내용은 [Event Hubs용 Azure CLI](https://docs.microsoft.com/cli/azure/eventhubs)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
