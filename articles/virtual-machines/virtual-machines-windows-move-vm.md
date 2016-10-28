<properties
	pageTitle="Windows VM 이동 | Microsoft Azure"
	description="Resource Manager 배포 모델에서 다른 Azure 구독 또는 리소스 그룹으로 Windows VM을 이동합니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cynthn"/>

	


# 다른 Azure 구독 또는 리소스 그룹으로 Windows VM 이동 

이 문서에서는 리소스 그룹 또는 구독 간에 Windows VM을 이동하는 방법을 안내합니다. 개인 구독에서 원래 VM을 만들고 회사 구독으로 이동한 후 작업을 계속하려는 경우에 구독 간의 이동이 편리할 수 있습니다.

> [AZURE.NOTE] 새 리소스 ID는 이동의 일부로 생성됩니다. VM을 이동하면 새 리소스 ID를 사용하도록 도구 및 스크립트를 업데이트해야 합니다.


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## PowerShell을 사용하여 VM 이동

가상 컴퓨터를 다른 리소스 그룹에 이동하려면 모든 종속 리소스를 이동하는지 확인해야 합니다. AzureRMResource cmdlet을 사용하려면 리소스 이름과 리소스의 유형이 필요합니다. AzureRMResource cmdlet에서 둘 모두를 가져올 수 있습니다.

	Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
	

VM을 이동하려면 여러 리소스를 이동해야 합니다. 지금 막 각 리소스에 대한 별도의 변수를 만들고 나열했습니다. 이 예제는 VM에 대한 대부분의 기본 리소스를 포함하지만 필요에 따라 더 추가할 수 있습니다.

	$sourceRG = "<sourceResourceGroupName>"
	$destinationRG = "<destinationResourceGroupName>"
    
	$vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
	$diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
	$vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
	$nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
	$ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
	$nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
	
	Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

리소스를 다른 구독으로 이동하기 위해 **-DestinationSubscriptionId** 매개 변수를 포함합니다.

	Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



지정한 리소스를 이동할 것인지 묻는 메시지가 나타납니다. **Y**를 입력하여 리소스를 이동할 것인지 확인합니다.

  
## 다음 단계

리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../resource-group-move-resources.md)을 참조하세요.

<!---HONumber=AcomDC_0810_2016-->