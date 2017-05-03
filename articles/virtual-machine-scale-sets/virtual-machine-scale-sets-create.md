---
title: "Azure 가상 컴퓨터 확장 집합 만들기 | Microsoft Docs"
description: "Azure CLI, PowerShell, 템플릿 또는 Visual Studio를 사용하여 Linux 또는 Microsoft Azure 가상 컴퓨터 확장 집합을 만들고 배포합니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/30/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 9c9135e200cd1676b01895af4611bc71d5d27c4a
ms.lasthandoff: 04/21/2017

---

# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>가상 컴퓨터 확장 집합 만들기 및 배포
가상 컴퓨터 크기 집합은 동일한 가상 컴퓨터를 집합으로 쉽게 배포하고 관리할 수 있습니다. 규모 집합은 대규모 응용 프로그램에 대한 높은 확장성과 사용자 지정 가능한 계산 계층을 제공하고 Windows 플랫폼 이미지, Linux 플랫폼 이미지, 사용자 지정 이미지 및 확장을 지원합니다. 확장 집합에 대한 자세한 내용은 [가상 컴퓨터 확장 집합](virtual-machine-scale-sets-overview.md)을 참조하세요.

이 자습서에서는 Azure Portal을 사용하지 **않고** 가상 컴퓨터 크기 집합을 만드는 방법을 보여 줍니다. Azure Portal을 사용하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 컴퓨터 확장 집합을 만드는 방법](virtual-machine-scale-sets-portal-create.md)을 참조하세요.

>[!NOTE]
>Azure Resource Manager 리소스에 대한 자세한 내용은 [Azure Resource Manager 및 클래식 배포](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure CLI 2.0 또는 Azure PowerShell을 사용하여 확장 집합을 만드는 경우 먼저 구독에 로그인해야 합니다.

Azure CLI 또는 PowerShell을 사용하여 Azure를 설치, 설정 및 로그인하는 방법에 대한 자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli.md) 또는 [Azure PowerShell cmdlet 시작](/powershell/resourcemanager/)을 참조하세요.

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

먼저 가상 컴퓨터 크기 집합이 연결된 리소스 그룹을 만들어야 합니다.

```azurecli
az group create --location westus2 --name vmss-test-1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name vmss-test-1
```

## <a name="create-from-azure-cli"></a>Azure CLI에서 만들기

Azure CLI를 사용하여 적은 노력으로 가상 컴퓨터 크기 집합을 만들 수 있습니다. 기본값을 생략한 경우 기본값이 제공됩니다. 예를 들어 가상 네트워크 정보를 지정하지 않는 경우 가상 네트워크가 만들어집니다. 다음 부분을 생략한 경우 자동으로 만들어집니다. 
- 부하 분산 장치
- 가상 네트워크
- 공용 IP 주소

가상 컴퓨터 확장 집합에 사용하려는 가상 컴퓨터 이미지를 선택할 경우 몇 가지 옵션이 있습니다.

- URN  
리소스의 식별자:  
**Win2012R2Datacenter**

- URN 별칭  
URN의 이름:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- 사용자 지정 리소스 ID  
Azure 리소스에 대한 경로:  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**

- 웹 리소스  
HTTP URI에 대한 경로:  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**

>[!TIP]
>`az vm image list`을 사용하여 사용 가능한 이미지 목록을 가져올 수 있습니다.

가상 컴퓨터 확장 집합을 만들려면 다음을 지정해야 합니다.

- 리소스 그룹 
- 이름
- 운영 체제 이미지
- 인증 정보 
 
다음 예제에서는 기본 가상 컴퓨터 확장 집합을 만듭니다(이 단계는 몇 분 정도 걸릴 수 있음).

```azurecli
az vmss create --resource-group vmss-test-1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

명령이 완료되면 이제 가상 컴퓨터 확장 집합이 만들어져 있을 것입니다. 가상 컴퓨터에 연결할 수 있도록 가상 컴퓨터의 IP 주소를 가져와야 할 수 있습니다. 다음 명령을 사용하여 가상 컴퓨터(IP 주소 포함)에 대한 수많은 다양한 정보를 얻을 수 있습니다. 

```azurecli
az vmss list-instance-connection-info --resource-group vmss-test-1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>PowerShell에서 만들기

PowerShell은 Azure CLI보다 사용하기가 복잡합니다. Azure CLI는 네트워킹 관련 리소스(부하 분산 장치, IP 주소, 가상 네트워크 등)의 기본값을 제공하는 반면 PowerShell은 제공하지 않습니다. PowerShell을 사용하여 이미지를 참조하는 작업은 약간 더 복잡합니다. 다음 cmdlet을 사용하여 이미지를 가져올 수 있습니다.

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

cmdlet은 작업 시퀀스에서 파이프될 수 있습니다. **microsoft**라는 이름이 포함된 게시자가 있는 **미국 서부 2** 지역에 대한 모든 이미지를 가져오는 방법의 예제는 다음과 같습니다.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

가상 컴퓨터 확장 집합을 만드는 워크플로는 다음과 같습니다.

1. 크기 집합에 대한 정보를 보유하는 구성 개체를 만듭니다.
2. 기본 OS 이미지를 참조합니다.
3. 운영 체제 설정 구성: 인증, VM 이름 접두사, 사용자/전달
4. 네트워킹을 구성합니다.
5. 크기 집합을 만듭니다.

이 예제에서는 Windows Server 2016이 설치된 컴퓨터에 2개의 인스턴스로 된 기본 확장 집합을 만듭니다.

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location WestUS2 -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName "vmss-test-1" -Location "westus2" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ip-address" -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName vmss-test-1 -Name my-scale-set -VirtualMachineScaleSet $vmssConfig
```

## <a name="create-from-a-template"></a>템플릿에서 만들기

Azure Resource Manager 템플릿을 사용하여 가상 컴퓨터 확장 집합을 배포할 수 있습니다. 고유한 템플릿을 만들거나 [템플릿 리포지토리](https://azure.microsoft.com/resources/templates/?term=vmss)에서 하나를 사용할 수 있습니다. 이러한 템플릿을 Azure 구독에 직접 배포할 수 있습니다.

>[!NOTE]
>고유한 템플릿을 만들려면 JSON 텍스트 파일을 만듭니다. 템플릿을 만들고 사용자 지정하는 방법에 대한 일반 정보는 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.

[GitHub에서](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set) 샘플 템플릿을 사용할 수 있습니다. 해당 샘플을 만들고 사용하는 방법에 대한 자세한 내용은 [실행 가능한 최소 확장 집합](.\virtual-machine-scale-sets-mvss-start.md)을 참조하세요.

## <a name="create-from-visual-studio"></a>Visual Studio에서 만들기

Visual Studio를 사용하여 Azure 리소스 그룹 프로젝트를 만들고 거기에 가상 컴퓨터 확장 집합 템플릿을 추가할 수 있습니다. GitHub 또는 Azure 웹 응용 프로그램 갤러리 중 어디에서 가져올 것인지를 선택할 수 있습니다. PowerShell 배포 스크립트도 생성됩니다. 자세한 내용은 [Visual Studio에서 가상 컴퓨터 확장 집합을 만드는 방법](virtual-machine-scale-sets-vs-create.md)을 참조하세요.

## <a name="create-from-the-azure-portal"></a>Azure Portal에서 만들기

Azure Portal은 크기 집합을 신속하게 만드는 편리한 방법을 제공합니다. 자세한 내용은 [Azure Portal에서 가상 컴퓨터 확장 집합을 만드는 방법](virtual-machine-scale-sets-portal-create.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[데이터 디스크](virtual-machine-scale-sets-attached-disks.md)에 대해 자세히 알아봅니다.

[앱을 관리](virtual-machine-scale-sets-deploy-app.md)하는 방법에 대해 알아봅니다.
