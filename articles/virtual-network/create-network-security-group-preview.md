---
title: Azure 네트워크 및 응용 프로그램 보안 그룹(미리 보기)을 사용하여 네트워크 트래픽 필터링 | Microsoft Docs
description: 네트워크 및 응용 프로그램 보안 그룹(미리 보기)을 만들어 가상 머신에서 네트워크 트래픽 유형을 제한하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2018
ms.locfileid: "30294226"
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>네트워크 및 응용 프로그램 보안 그룹(미리 보기)을 사용하여 네트워크 트래픽 필터링

이 자습서에서는 네트워크 보안 그룹을 만들어 응용 프로그램 보안 그룹이 있는 가상 머신 그룹 간의 네트워크 트래픽을 필터링하는 방법에 대해 알아봅니다. 네트워크 보안 그룹 및 응용 프로그램 보안 그룹에 대한 자세한 내용은 [보안 개요](security-overview.md)를 참조하세요. 

다음 섹션에는 Azure 명령줄 인터페이스([Azure CLI](#azure-cli)) 및 [Azure PowerShell](#powershell)을 사용하여 네트워크 보안 그룹을 만들 수 있는 단계가 나와 있습니다. 네트워크 보안 그룹을 만드는 데 어떤 도구를 선택하든지 간에 결과는 동일합니다. 도구 링크를 클릭하면 자습서의 해당 섹션으로 이동합니다. 

이 문서에서는 네트워크 보안 그룹을 만들 때 권장되는 배포 모델인 Resource Manager 배포 모델을 통해 네트워크 보안 그룹을 만드는 단계를 설명합니다. 네트워크 보안 그룹(클래식)을 만들어야 할 경우 [네트워크 보안 그룹(클래식) 만들기](virtual-networks-create-nsg-classic-ps.md)를 참조하세요. Azure 배포 모델에 대해 잘 모르는 경우에는 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

> [!NOTE]
> 이 자습서에서는 현재 미리 보기 릴리스 상태인 네트워크 보안 그룹 기능을 활용합니다. 미리 보기 릴리스의 기능은 일반 릴리스의 기능과 동일한 가용성 및 안정성을 제공하지 않습니다. 일반 릴리스의 기능만 사용하여 네트워크 보안 그룹을 구현하려는 경우 [네트워크 보안 그룹 만들기](virtual-networks-create-nsg-arm-pportal.md)를 참조하세요. 

## <a name="azure-cli"></a>Azure CLI

Azure CLI 명령은 Windows, Linux 또는 macOS에서 실행하는지 여부에 상관없이 동일합니다. 그러나 운영 체제 셸 간에 스크립팅 차이는 있습니다. 다음 단계의 스크립트는 Bash 셸에서 실행됩니다. 

1. [Azure CLI를 설치 및 구성합니다](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. `az --version` 명령을 입력하여 Azure CLI 버전 2.0.18 이상을 사용하고 있는지 확인합니다. 그렇지 않을 경우 최신 버전을 설치합니다.
3. `az login` 명령을 사용하여 Azure에 로그인합니다.
4. 다음 명령을 입력하여 미리 보기에 등록합니다.
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. 다음 명령을 입력하여 미리 보기에 등록되었는지 확인합니다.

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > 등록을 완료하는 데 최대 1시간이 걸릴 수 있습니다. 이전 명령에서 반환되는 출력의 **state**에 *Registered*가 표시되면 나머지 단계를 진행합니다. 등록되기 전에 진행하면 나머지 단계가 실패합니다.

6. 다음 bash스크립트를 실행하여 리소스 그룹을 만듭니다.

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. 각 서버 유형에 대해 하나씩 세 개의 응용 프로그램 보안 그룹을 만듭니다.

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. 네트워크 보안 그룹 만들기:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. NSG 내에 응용 프로그램 보안 그룹을 대상으로 설정하는 보안 규칙을 만듭니다.
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. 가상 네트워크 만들기: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. 네트워크 보안 그룹을 가상 네트워크의 서브넷에 연결합니다.

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. 각 서버 유형에 대해 하나씩 세 개의 네트워크 인터페이스를 만듭니다. 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    9단계에서 만든 해당 보안 규칙만 네트워크 인터페이스를 구성원으로 하는 응용 프로그램 보안 그룹을 기반으로 네트워크 인터페이스에 적용됩니다. 예를 들어 *AppRule*만 *myNic2*에 적용됩니다. 왜냐하면 네트워크 인터페이스는 *AppServers* 응용 프로그램 보안 그룹의 구성원이고 규칙에서 *AppServers* 응용 프로그램 보안 그룹을 대상으로 지정하기 때문입니다. *WebRule* 및 *DatabaseRule* 규칙은 *myNic2*에 적용되지 않습니다. 왜냐하면 네트워크 인터페이스는 *WebServers* 및 *DatabaseServers* 응용 프로그램 보안 그룹의 구성원이 아니기 때문입니다. 그러나 *WebRule* 및 *AppRule* 규칙은 *myNic1*에 적용됩니다. *myNic1* 네트워크 인터페이스가 *WebServers* 및 *AppServers* 응용 프로그램 보안 그룹 둘 다의 구성원이고, 규칙이 *WebServers* 및 *AppServers* 응용 프로그램 보안 그룹을 대상으로 지정하기 때문입니다. 

13. 각 서버 유형에 대해 하나의 가상 머신을 만들고 각 가상 머신에 해당 네트워크 인터페이스를 연결합니다. 이 예제에서는 Windows 가상 머신을 만들지만 *win2016datacenter*를 *UbuntuLTS*로 변경하면 Linux 가상 머신을 만들 수 있습니다.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **선택 사항:** [리소스 삭제](#delete-cli)의 단계를 완료하여 이 자습서에서 만든 리소스를 삭제합니다.

## <a name="powershell"></a>PowerShell

1. [PowerShell](/powershell/azure/install-azurerm-ps)을 설치 및 구성합니다.
2. 버전 4.4.0 이상의 AzureRm 모듈이 설치되어 있는지 확인합니다. `Get-Module -ListAvailable AzureRM` 명령을 입력하여 현재 설치된 버전을 확인할 수 있습니다. 설치 또는 업그레이드해야 할 경우 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureRM)에서 최신 버전의 AzureRM 모듈을 설치합니다.
3. PowerShell 세션에서 `login-azurermaccount` 명령을 사용하여 [Azure 계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 Azure에 로그인합니다.
4. 다음 명령을 입력하여 미리 보기에 등록합니다.
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. 다음 명령을 입력하여 미리 보기에 등록되었는지 확인합니다.

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > 등록을 완료하는 데 최대 1시간이 걸릴 수 있습니다. 이전 명령에서 반환되는 출력의 **RegistrationState**에 *Registered*가 표시되면 나머지 단계를 진행합니다. 등록되기 전에 진행하면 나머지 단계가 실패합니다.
        
6. 리소스 그룹 만들기:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. 각 서버 유형에 대해 하나씩 세 개의 응용 프로그램 보안 그룹을 만듭니다.

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. 각 서버 유형에 대한 보안 규칙을 만듭니다.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. 네트워크 보안 그룹 만들기:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. 서브넷 구성을 만들어 네트워크 보안 그룹과 연결합니다.
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. 가상 네트워크 만들기: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. 각 서버 유형에 대해 하나씩 세 개의 네트워크 인터페이스를 만듭니다. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    8단계에서 만든 해당 보안 규칙만 네트워크 인터페이스를 구성원으로 하는 응용 프로그램 보안 그룹을 기반으로 네트워크 인터페이스에 적용됩니다. 예를 들어 *AppRule*만 *myNic2*에 적용됩니다. 왜냐하면 네트워크 인터페이스는 *AppServers* 응용 프로그램 보안 그룹의 구성원이고 규칙에서 *AppServers* 응용 프로그램 보안 그룹을 대상으로 지정하기 때문입니다. *WebRule* 및 *DatabaseRule* 규칙은 *myNic2*에 적용되지 않습니다. 왜냐하면 네트워크 인터페이스는 *WebServers* 및 *DatabaseServers* 응용 프로그램 보안 그룹의 구성원이 아니기 때문입니다. 그러나 *WebRule* 및 *AppRule* 규칙은 *myNic1*에 적용됩니다. *myNic1* 네트워크 인터페이스가 *WebServers* 및 *AppServers* 응용 프로그램 보안 그룹 둘 다의 구성원이고, 규칙이 *WebServers* 및 *AppServers* 응용 프로그램 보안 그룹을 대상으로 지정하기 때문입니다. 

13. 각 서버 유형에 대해 하나의 가상 머신을 만들고 각 가상 머신에 해당 네트워크 인터페이스를 연결합니다. 이 예제에서는 Windows 가상 머신을 만들지만 스크립트를 실행하기 전에 *-Windows*를 *-Linux*로, *MicrosoftWindowsServer*를 *Canonical*로, *windowsServer*를 *UbuntuServer*로, *2016-Datacenter*를 *14.04.2-LTS*로 변경하면 Linux 가상 머신을 만들 수 있습니다.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **선택 사항:** [리소스 삭제](#delete-cli)의 단계를 완료하여 이 자습서에서 만든 리소스를 삭제합니다.

## <a name="remove-a-nic-from-an-asg"></a>ASG에서 NIC 제거
응용 프로그램 보안 그룹에서 네트워크 인터페이스를 제거하면, 응용 프로그램 보안 그룹을 지정하는 어떤 규칙도 제거된 네트워크 인터페이스에 적용되지 않습니다.

### <a name="azure-cli"></a>Azure CLI

모든 응용 프로그램 보안 그룹에서 *myNic3*를 제거하려면 다음 명령을 입력합니다.

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

모든 응용 프로그램 보안 그룹에서 *myNic3*를 제거하려면 다음 명령을 입력합니다.

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>리소스 삭제

이 자습서를 마친 후에는 사용 요금이 발생하지 않도록 작성했던 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하면 리소스 그룹에 있는 리소스도 모두 삭제됩니다.

### <a name="delete-portal"></a>Azure Portal

1. 포털 검색 상자에 **myResourceGroup**을 입력합니다. 검색 결과에서 **myResourceGroup**을 클릭합니다.
2. **myResourceGroup** 블레이드에서 **삭제** 아이콘을 클릭합니다.
3. 삭제를 확인하려면 **리소스 그룹 이름 입력** 상자에 **myResourceGroup**을 입력한 다음 **삭제**를 클릭합니다.

### <a name="delete-cli"></a>Azure CLI

CLI 세션에서 다음 명령을 입력합니다.

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

PowerShell 세션에서 다음 명령을 입력합니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

