---
title: "PowerShell을 사용하여 Azure VM에서 MSI를 구성하는 방법"
description: "PowerShell을 사용하여 Azure VM에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>PowerShell을 사용하여 VM MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 Azure Windows VM에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

또한 [Azure PowerShell 버전 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1)을 아직 설치하지 않은 경우 설치합니다.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 MSI를 사용하도록 설정

MSI가 사용하도록 설정된 새 Windows 가상 컴퓨터 리소스가 지정된 구성 매개 변수를 사용하여 새 리소스 그룹에 생성됩니다. 이러한 cmdlet 중 대부분은 결과를 반환할 때까지 30초 이상 실행될 수도 있으며 최종 VM 생성이 완료되려면 몇 분이 걸립니다.

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 배포하려는 Azure 구독과 연결된 계정을 사용하세요.

   ```powershell
   Login-AzureRmAccount
   ```

2. `New-AzureRmResourceGroup` cmdlet을 사용하여 VM 및 관련 리소스를 포함하고 배포하는 데 사용할 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. VM용 네트워킹 리소스를 만듭니다.

   a. 가상 네트워크, 서브넷 및 공용 IP 주소를 만듭니다. 이러한 리소스는 가상 컴퓨터에 네트워크 연결을 제공하고 가상 컴퓨터를 인터넷에 연결하는 데 사용됩니다.

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. 네트워크 보안 그룹 및 네트워크 보안 그룹 규칙을 만듭니다. 네트워크 보안 그룹은 인바운드 및 아웃바운드 규칙을 사용하여 가상 컴퓨터를 보호합니다. 이 경우 포트 3389에 대해 들어오는 원격 데스크톱 연결을 허용하는 인바운드 규칙이 만들어집니다. 그리고 포트 80에 대한 인바운드 규칙을 만들어서 들어오는 웹 트래픽을 허용하려고 합니다.

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. 가상 컴퓨터에 대한 가상 네트워크 카드를 만듭니다. 네트워크 카드는 서브넷, 네트워크 보안 그룹 및 공용 IP 주소에 가상 컴퓨터를 연결합니다.

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. VM을 만듭니다.

   a. 구성 가능한 VM 개체를 만듭니다. 가상 컴퓨터 이미지, 크기 및 인증 구성 등의 이러한 설정은 가상 컴퓨터를 배포할 때 사용됩니다. [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet에서 사용되는 `-IdentityType "SystemAssigned"` 매개 변수로 인해 VM이 MSI와 함께 프로비전됩니다. `Get-Credential` cmdlet은 자격 증명을 입력하라는 메시지를 표시합니다. 이 자격 증명은 가상 컴퓨터의 사용자 이름과 암호로 구성됩니다.

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. 새 VM을 프로비전합니다.

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type "ManagedIdentityExtensionForWindows"` 매개 변수를 사용하여 MSI VM 확장을 추가합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>기존 Azure VM에서 MSI를 사용하도록 설정

기존 가상 컴퓨터에서 MSI를 사용하도록 설정해야 하는 경우 다음을 수행합니다.

1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다. VM을 배포하려는 Azure 구독과 연결된 계정을 사용하세요.

   ```powershell
   Login-AzureRmAccount
   ```

2. 먼저 `Get-AzureRmVM` cmdlet을 사용하여 VM 속성을 검색합니다. 그런 후에 MSI를 사용하도록 설정하려면 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet에서 `-IdentityType` 스위치를 사용합니다.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet에서 `-Type "ManagedIdentityExtensionForWindows"` 매개 변수를 사용하여 MSI VM 확장을 추가합니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 끝점에서 사용하는 포트를 지정합니다. 기존 VM의 위치와 일치하는 올바른 `-Location` 매개 변수를 지정해야 합니다.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM에서 MSI 제거

MSI가 더 이상 필요하지 않은 가상 컴퓨터가 있으면 `RemoveAzureRmVMExtension` cmdlet을 사용하여 해당 VM에서 MSI를 제거할 수 있습니다.

1. `-Name "ManagedIdentityExtensionForWindows"` 스위치와 함께 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet을 사용합니다.

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>관련 콘텐츠

- [관리 서비스 ID 개요](msi-overview.md)
- 이 문서는 [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](../virtual-machines/windows/quick-create-powershell.md) 빠른 시작에서 조정되어 MSI 관련 지침을 포함하도록 수정된 것입니다. 

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

















