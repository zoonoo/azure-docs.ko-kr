---
title: "Linux VM 이동 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 다른 Azure 구독 또는 리소스 그룹으로 Linux VM을 이동합니다."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 0dee2c3b92adc4098a51014dd7379aa3d53c1627
ms.openlocfilehash: 2978fee770b70378dfbffcbd97e57a6b82d2e2fa


---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Linux VM을 다른 구독 또는 리소스 그룹으로 이동
이 문서에서는 리소스 그룹 또는 구독 간에 Linux VM을 이동하는 방법을 안내합니다. 개인 구독에서 VM을 만들고 회사 구독으로 이동하려면 구독 간의 VM 이동이 편리할 수 있습니다.

> [!NOTE]
> 새 리소스 ID는 이동의 일부로 생성됩니다. VM을 이동하면 새 리소스 ID를 사용하도록 도구 및 스크립트를 업데이트해야 합니다. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Azure CLI를 사용하여 VM 이동
VM을 성공적으로 이동하려면 VM 및 모든 지원 리소스를 이동해야 합니다. **azure group show** 명령을 사용하여 리소스 그룹 및 해당 ID에서 모든 리소스를 나열합니다. 이후 명령에 ID를 복사하고 붙여넣을 수 있도록 파일에이 명령의 출력을 파이프할 수 있습니다.

    azure group show <resourceGroupName>

다른 리소스 그룹에 VM 또는 해당 리소스를 이동하려면 **azure resource move** CLI 명령을 사용합니다. 다음 예제에서는 필요한 VM 및 가장 일반적인 리소스를 이동하는 방법을 보여 줍니다. **-i** 매개 변수를 사용하고 이동할 리소스에 대한 ID의 쉼표로 구분된 목록에 공백 없이 전달합니다.

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

VM 및 해당 리소스를 다른 구독으로 이동하려는 경우 **--destination-subscriptionId &#60;destinationSubscriptionID&#62;** 매개 변수를 추가하여 대상 구독을 지정합니다.

Windows 컴퓨터의 명령 프롬프트에서 작업하는 경우 선언할 때 변수 이름 앞에 **$** 를 추가해야 합니다. Linux에서는 필요하지 않습니다.

지정한 리소스를 이동할 것인지 묻는 메시지가 나타납니다. **Y** 를 입력하여 리소스를 이동할 것인지 확인합니다.

[!INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>다음 단계
리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.    




<!--HONumber=Jan17_HO1-->


