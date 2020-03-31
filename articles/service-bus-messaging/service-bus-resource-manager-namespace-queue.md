---
title: Azure 템플릿을 사용하여 Azure Service Bus 네임스페이스 및 큐 만들기
description: '빠른 시작: Azure Resource Manager 템플릿을 사용하여 Service Bus 네임스페이스 및 큐 만들기'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 8605dae05b7f5270513b645367248090006c04a8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384911"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>빠른 시작: Azure Resource Manager 템플릿을 사용하여 Service Bus 네임스페이스 및 큐 만들기

이 문서에서는 해당 네임스페이스 내에 Service Bus 네임스페이스 및 큐를 만드는 Azure Resource Manager 템플릿을 사용하는 방법을 보여 줍니다. 이 문서는 어떤 리소스를 배포할지 지정하는 방법 및 배포를 실행할 때 지정되는 매개 변수를 정의하는 방법을 설명합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

None

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Service Bus 네임스페이스 및 큐 만들기

### <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

템플릿에 정의된 리소스는 다음과 같습니다.

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다.
>
> * [큐 및 권한 부여 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-auth-rule.md)
> * [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace.md)
> * [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic-with-rule.md)

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)에서 추가 템플릿을 찾을 수 있습니다.

### <a name="deploy-the-template"></a>템플릿 배포

이 템플릿으로 큐가 있는 Service Bus 네임스페이스를 배포합니다.

[Service Bus 큐](service-bus-queues-topics-subscriptions.md#queues)는 하나 이상의 경쟁 소비자에게 FIFO(선입선출) 메시지 배달을 제공합니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="next-steps"></a>다음 단계

네임스페이스/큐에 대한 권한 부여 규칙을 만드는 방법을 보여주는 다음 토픽을 참조하세요.

[Azure Resource Manager 템플릿을 사용하여 네임스페이스 및 큐에 대한 Service Bus 권한 부여 규칙 만들기](service-bus-resource-manager-namespace-auth-rule.md)

다음 문서를 검토하여 이러한 리소스를 관리하는 방법을 알아보세요.

* [PowerShell을 사용하여 Service Bus 관리](service-bus-manage-with-ps.md)
* [Service Bus 탐색기로 Service Bus 리소스 관리](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
