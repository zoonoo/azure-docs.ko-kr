---
title: Azure Resource Manager 템플릿을 사용하여 Service Bus 메시징 네임스페이스 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Service Bus 메시징 네임스페이스 만들기
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444764"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 Service Bus 네임 스페이스 만들기

Service Bus 네임 스페이스를 만드는 Azure Resource Manager 템플릿을 배포 하는 방법을 알아봅니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다. 템플릿을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Resource Manager 설명서](/azure/azure-resource-manager/)합니다.

다음 템플릿은 Service Bus 네임 스페이스를 만들기 위한 사용할 수도 있습니다.

* [큐가 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-queue.md)
* [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-topic.md)
* [큐 및 권한 부여 규칙이 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-auth-rule.md)
* [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

이 빠른 시작에서 사용 하는 [기존 Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) 에서 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

자세한 템플릿 샘플을 참조 하세요 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)합니다.

템플릿을 배포 하 여 service bus 네임 스페이스를 만들려면:

1. 선택 **사용해** 에서 다음 코드 블록을 및 Azure Cloud shell에 로그인 하 고 지침을 따릅니다.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    리소스 그룹 이름은 사용 하 여 service bus 네임 스페이스 이름 **rg** 추가 합니다.

2. **복사**를 선택하여 PowerShell 스크립트를 복사합니다.
3. 셸 콘솔을 마우스 오른쪽 단추로 클릭 한 다음 선택 **붙여넣기**합니다.

이벤트 허브를 만들려면 몇 분 정도 걸립니다.

## <a name="verify-the-deployment"></a>배포 확인

배포 된 service bus 네임 스페이스를 보려면 Azure portal에서 리소스 그룹을 엽니다. 또는 다음 Azure PowerShell 스크립트를 사용 합니다. Cloud shell가 계속 열려 있으면 다음 스크립트의 첫 번째와 두 번째 줄을 복사/실행할 필요가 없습니다.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

이 자습서에서 템플릿을 배포 하려면 azure PowerShell이 사용 됩니다. 다른 템플릿 배포 방법에 대 한 참조.

* [Azure portal을 사용 하 여](../azure-resource-manager/resource-group-template-deploy-portal.md)입니다.
* [Azure CLI를 사용 하 여](../azure-resource-manager/resource-group-template-deploy-cli.md)입니다.
* [REST API를 사용 하 여](../azure-resource-manager/resource-group-template-deploy-rest.md)입니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. Cloud shell가 계속 열려 있으면 다음 스크립트의 첫 번째와 두 번째 줄을 복사/실행할 필요가 없습니다.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Service Bus 네임스페이스를 만들었습니다. 다른 빠른 시작을 참조하여 큐, 토픽/구독을 만들고 사용하는 방법에 대해 알아봅니다.

* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 큐 토픽 시작](service-bus-dotnet-how-to-use-topics-subscriptions.md)
