---
title: "Azure 가상 컴퓨터의 여러 IP 주소 - PowerShell | Microsoft Docs"
description: "PowerShell | Resource Manager를 사용하여 가상 컴퓨터에 여러 IP 주소를 할당하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: 394315f81cf694cc2bb3a28b45694361b11e0670
ms.openlocfilehash: 2a384c1a9af076205d4d0ae12e0a5f9e63b076d1


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>PowerShell을 사용하여 가상 컴퓨터에 여러 IP 주소 할당

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

이 문서에서는 PowerShell을 사용하여 Azure Resource Manager 배포 모델을 통해 VM(가상 컴퓨터)을 만드는 방법을 설명합니다. 클래식 배포 모델을 통해 생성된 리소스에 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

다음 단계는 시나리오에 설명된 대로 여러 IP 주소를 가진 예시 VM을 만드는 방법을 설명합니다. 변수 이름과 IP 주소 유형을 구현에 필요한 대로 변경합니다.

1. PowerShell 명령 프롬프트를 열고 단일 PowerShell 세션 내에서 이 섹션의 나머지 단계를 완료합니다. Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 문서의 단계를 완료합니다.
2. 로그인하고 적절한 구독을 선택한 후에 PowerShell에서 다음 명령을 실행하여 미리 보기에 등록합니다.
    ```
    Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

    Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
    ```Get-AzureRmProviderFeature``` 명령을 실행하면 다음과 같은 출력이 표시될 때까지 나머지 단계를 완료하지 마세요.
        
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------      
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >몇 분이 걸릴 수 있습니다.

3. [Windows VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md) 문서의 1-4단계를 완료합니다. 5단계(공용 IP 리소스 및 네트워크 인터페이스 만들기)를 완료하지 마세요. 이 문서에서 사용된 모든 변수의 이름을 변경하는 경우 나머지 단계의 변수 이름도 변경합니다. Linux VM을 만들려면 Windows 대신 Linux 운영 체제를 선택합니다.
4. 다음 명령을 입력하여 Windows VM 문서 만들기의 4단계(VNet 만들기)에서 만든 서브넷 개체를 저장하는 변수를 만듭니다.

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
5. NIC에 할당할 IP 구성을 정의합니다. 필요에 따라 구성을 추가, 제거 또는 변경할 수 있습니다. 다음 구성은 시나리오에 설명되어 있습니다.

    **IPConfig-1**

    다음을 만들기 위해 수행할 명령을 입력합니다.
    - 고정 공용 IP 주소가 있는 공용 IP 주소 리소스
    - 공용 IP 주소 리소스와 동적 개인 IP 주소가 있는 IP 구성

    ```powershell
    $myPublicIp1     = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    이전 명령의 `-Primary` 스위치를 기록합니다. NIC에 여러 IP 구성을 할당하는 경우 하나의 구성이 *기본*으로 할당되어야 합니다.

    > [!NOTE]
    > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
    >

    **IPConfig-2**

    만든 서브넷에서 사용할 수 있는 유효한 주소 뒤에 오는 **$IPAddress** 변수의 값을 변경합니다. 서브넷에서 주소 10.0.0.5를 사용할 수 있는지 확인하려면 명령 `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`을 입력합니다. 주소를 사용할 수 있는 경우 출력은 *True*를 반환합니다. 주소를 사용할 수 없는 경우 출력은 *False*와 사용할 수 있는 주소 목록을 반환합니다. 고정 공용 IP 주소와 고정 개인 IP 주소가 있는 새 공용 IP 주소 리소스와 새로운 IP 구성을 만들려면 다음 명령을 입력합니다.
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = 10.0.0.5
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    동적 개인 IP 주소가 있고 공용 IP 주소가 없는 IP 구성을 만들려면 다음 명령을 입력합니다.

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
6. 다음 명령을 입력하여 이전 단계에서 정의한 IP 구성을 사용하여 NIC를 만듭니다.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > 이 문서를 통해 모든 IP 구성을 단일 NIC에 할당하면 여러 IP 구성도 VM의 모든 NIC에 할당할 수 있습니다. 여러 NIC로 VM을 만드는 방법에 대해 자세히 알아보려면 [여러 NIC를 사용하여 VM 만들기](virtual-network-deploy-multinic-arm-ps.md) 문서를 참조하세요.

7. [VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md) 문서의 6단계를 완료합니다. 

    > [!WARNING]
    > 다음과 같은 경우 VM 만들기 문서의 6단계가 실패합니다.
    > - 이 문서의 6 단계에서 $myNIC라는 변수를 다른 것으로 변경한 경우
    > - 이 문서와 VM 만들기 문서의 이전 단계를 완료하지 않은 경우
    >
8. NIC에 할당된 개인 IP 주소 및 공용 IP 주소 리소스를 보려면 다음 명령을 입력합니다.

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 NIC에 추가할 수 있습니다. 다음 섹션의 예제는 이 문서의 [시나리오](#Scenario)에서 설명한&3;개의 IP로 구성된 VM이 이미 있다는 가정 하에 진행하되 필수 사항은 아닙니다.

1. PowerShell 명령 프롬프트를 열고 단일 PowerShell 세션 내에서 이 섹션의 나머지 단계를 완료합니다. Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 문서의 단계를 완료합니다.
2. 구독 ID 및 사용 목적을 적은 전자 메일을 [여러 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)로 보내어 미리 보기를 등록합니다. 다음 작업이 끝나기 전까지 나머지 단계를 완료하려 하지 마세요.
    - 미리 보기에 적용되었음을 알리는 전자 메일을 받을 때까지
    - 받은 전자 메일의 지침을 따르기 전까지
3. 다음 $Variables의 "값"을 IP 주소를 추가하려는 NIC의 이름과 NIC가 있는 리소스 그룹 및 위치로 변경합니다.

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    변경하려는 NIC의 이름을 잘 모르는 경우 다음 명령을 입력한 다음 이전 변수의 값을 변경합니다.

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
4. 다음 명령을 입력하여 변수를 만들고 기존 NIC로 설정합니다.

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
5. 다음 명령에서 *myVNet* 및 *mySubnet*을 NIC가 연결된 VNet 및 서브넷의 이름으로 변경합니다. NIC가 연결된 VNet 및 서브넷 개체를 검색하는 명령을 입력합니다.

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    NIC가 연결된 VNet 또는 서브넷 이름을 모르는 경우 다음 명령을 입력합니다.
    ```powershell
    $mynic.IpConfigurations
    ```
    반환된 출력에서 다음과 유사한 텍스트를 찾습니다.

        Subnet   : {
                     "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"

    이 출력의 경우 *myVnet*은 VNet, *mySubnet*은 NIC가 연결된 서브넷입니다.

6. 요구 사항에 따라 다음 섹션 중 하나의 단계를 완료합니다.

    **개인 IP 주소 추가**

    NIC에 개인 IP 주소를 추가하려면 IP 구성을 만들어야 합니다. 다음 명령은 10.0.0.7의 고정 IP 주소로 구성을 만듭니다. 동적 개인 IP 주소를 추가하려는 경우 명령을 입력하기 전에 `-PrivateIpAddress 10.0.0.7`을 제거합니다. 고정 IP 주소를 지정하는 경우 서브넷에 사용되지 않는 주소이어야 합니다. `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` 명령을 입력하여 먼저 주소를 사용할 수 있는지 테스트 하는 것이 좋습니다. IP 주소를 사용할 수 있는 경우 출력은 *True*를 반환합니다. IP 주소를 사용할 수 없는 경우 출력은 *False*와 사용할 수 있는 주소 목록을 반환합니다.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```

    고유한 구성 이름과 개인 IP 주소를 사용하여 필요한 만큼 구성을 만듭니다(정적 IP 주소를 가진 구성의 경우).

    이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다.

    **공용 IP 주소 추가**

    공용 IP 주소 리소스를 새 IP 구성 또는 기존 IP 구성에 연결하면 공용 IP 주소가 추가됩니다. 필요에 따라 이후 섹션 중 하나에 나와 있는 단계를 완료합니다.

    > [!NOTE]
    > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
    >

    **새 IP 구성에 공용 IP 주소 리소스 연결**
    
    새 IP 구성의 공용 IP 주소를 추가할 때마다 모든 IP 구성 시 개인 IP 주소가 있어야 하기 때문에 개인 IP 주소도 추가해야 합니다. 기존 공용 IP 주소 리소스를 추가하거나 새로 만들 수 있습니다. 새 파일을 만들려면 다음 명령을 입력합니다.
    
    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

     동적 개인 IP 주소 및 여기에 연결된 *myPublicIp3* 공용 IP 주소 리소스가 있는 새 IP 구성을 만들려면 다음 명령을 입력합니다.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

    **기존 IP 구성에 공용 IP 주소 리소스 연결**

    공용 IP 주소 리소스는 아직 연결한 리소스가 없는 IP 구성에만 연결될 수 있습니다. 다음 명령을 입력하면 IP 구성에 연결된 공용 IP 주소가 있는지 여부를 확인할 수 있습니다.

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

    반환된 출력에 다음과 비슷한 줄을 찾습니다.

        Name       PrivateIpAddress PublicIpAddress                                           Primary
        ----       ---------------- ---------------                                           -------
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False

    *IpConfig-3*에 대한 **PublicIpAddress** 열이 비어 있기 때문에 현재 공용 IP 주소 리소스가 여기에 연결되어 있지 않습니다. IpConfig-3에 기존 공용 IP 주소 리소스를 추가하거나 다음 명령을 입력하여 새로 만들 수 있습니다.

    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    *IpConfig-3*이라는 기존 IP 구성에 공용 IP 주소 리소스를 연결하려면 다음 명령을 입력합니다.
    
    ```powershell
    Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

7. 새로운 IP 구성으로 NIC를 설정하려면 다음 명령을 입력합니다.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

8. NIC에 할당된 개인 IP 주소 및 공용 IP 주소 리소스를 보려면 다음 명령을 입력합니다.

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]


<!--HONumber=Feb17_HO2-->


