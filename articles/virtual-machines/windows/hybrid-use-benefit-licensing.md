---
title: "Window Server 및 Windows 클라이언트에 대한 Azure 하이브리드 사용 혜택 | Microsoft Docs"
description: "Azure에 온-프레미스 라이선스를 가져오기 위해 Windows Software Assurance 혜택을 최대화하는 방법에 대해 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: george-moore
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 3/10/2017
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 76459acd75cc09a315b0dad219990a830a6ad111
ms.lasthandoff: 03/31/2017


---
# <a name="azure-hybrid-use-benefit-for-windows-server-and-windows-client"></a>Window Server 및 Windows 클라이언트에 대한 Azure 하이브리드 사용 혜택
Software Assurance 고객은 Azure 하이브리드 사용 혜택을 통해 온-프레미스 Windows Server 및 Windows 클라이언트 라이선스를 사용하고 Azure에서 Windows 가상 컴퓨터를 실행하여 비용을 절감할 수 있습니다. Windows Server에 대한 Azure 하이브리드 사용 혜택에는 Windows Server 2008R2, Windows Server 2012, Windows Server 2012R2 및 Windows Server 2016이 포함됩니다. Windows 클라이언트에 대한 Azure 하이브리드 사용 혜택에는 Windows 10이 포함됩니다. 자세한 내용은 [Azure 하이브리드 사용 혜택 라이선싱 페이지](https://azure.microsoft.com/pricing/hybrid-use-benefit/)를 참조하세요.

>[!IMPORTANT]
>Windows 클라이언트에 대한 Azure 하이브리드 사용 혜택은 현재 미리 보기로 제공됩니다. 사용자별 Windows 10 Enterprise E3/E5 또는 사용자별 Windows VDA(사용자 구독 라이선스 또는 추가 기능 사용자 구독 라이선스)를 보유한 엔터프라이즈 고객("적격 라이선스")만 혜택을 누릴 수 있습니다.
>
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Azure 하이브리드 사용 혜택을 사용하는 방법
Azure 하이브리드 사용 혜택을 사용하여 Windows VM을 배포하는 몇 가지 다른 방법이 있습니다.

1. 기업계약 구독이 있는 경우 Azure 하이브리드 사용 혜택으로 미리 구성되어 있는 [특정 Marketplace 이미지에서 VM을 배포](#deploy-a-vm-using-the-azure-marketplace)할 수 있습니다.
2. 기업계약이 없는 경우 [사용자 지정 VM을 업로드](#upload-a-windows-vhd)하거나 [Resource Manager 템플릿](#deploy-a-vm-via-resource-manager) 또는 [Azure PowerShell을 사용하여 배포](#detailed-powershell-deployment-walkthrough)할 수 있습니다.

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Azure Marketplace를 사용하여 VM 배포
[기업계약 구독](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)을 사용하는 고객은 Azure 하이브리드 사용 혜택으로 미리 구성된 Marketplace에서 이미지를 사용할 수 있습니다. 이러한 이미지는 Azure Portal, Resource Manager 템플릿 또는 Azure PowerShell 등에서 직접 배포할 수 있습니다. Marketplace의 이미지는 다음과 같이 `[HUB]` 이름으로 표시됩니다.

![Azure Marketplace에서 Azure 하이브리드 사용 혜택 이미지](./media/hybrid-use-benefit-licensing/ahub-images-portal.png)

Azure Portal에서 직접 이러한 이미지를 배포할 수 있습니다. Resource Manager 템플릿 및 Azure PowerShell에서 사용하려면 다음과 같은 이미지 목록을 봅니다.

Windows Server:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "WindowsServer-HUB"
```

Windows 클라이언트:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

기업계약 구독이 없는 경우 계속해서 사용자 지정 VM을 업로드하고 Azure 하이브리드 사용 혜택으로 배포하는 방법에 대한 지침을 읽습니다.


## <a name="upload-a-windows-vhd"></a>Windows VHD 업로드
Azure에서 Windows VM을 배포하려면 먼저 기본 Windows 빌드를 포함하는 VHD를 만들어야 합니다. 이 VHD는 Azure에 업로드하기 전에 Sysprep을 통해 적절하게 준비되어야 합니다. 자세한 내용은 [VHD 요구 사항 및 Sysprep 프로세스](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요. Sysprep를 실행하기 전에 VM을 백업합니다. 

먼저 [최신 Azure PowerShell을 설치 및 구성](/powershell/azureps-cmdlets-docs)했는지 확인합니다. VHD를 준비했으면 다음과 같이 `Add-AzureRmVhd` cmdlet을 사용하여 Azure 저장소 계정에 VHD를 업로드합니다.

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint 서버 및 Dynamics는 또한 Software Assurance 라이선스를 활용할 수 있습니다. Windows Server 이미지를 준비하려면 응용 프로그램 구성 요소를 설치하고 라이선스 키를 적절하게 제공한 다음, 디스크 이미지를 Azure에 업로드합니다. 응용 프로그램과 함께 [Sysprep를 사용하여 SQL Server 설치에 대한 고려 사항](https://msdn.microsoft.com/library/ee210754.aspx) 또는 [SharePoint Server 2016 참조 이미지 빌드(Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)와 같은 Sysprep 실행에 대한 적합한 설명서를 검토합니다.
>
>

자세한 내용은 [Azure에 VHD 업로드 프로세스](upload-image.md#upload-the-vhd-to-your-storage-account)를 참조하세요.


## <a name="deploy-an-uploaded-vm-via-resource-manager"></a>Resource Manager를 통해 업로드된 VM 배포
Resource Manager 템플릿 내에서 `licenseType` 에 대한 추가 매개 변수를 지정할 수 있습니다. [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)에 대해 자세히 알아볼 수 있습니다. Azure에 VHD를 업로드하고 나면 Resource Manager 템플릿을 편집하여 계산 공급자의 일부로 라이선스 유형을 포함하고 정상적으로 템플릿을 배포합니다.

Windows Server:
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Windows 클라이언트:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-an-uploaded-vm-via-powershell-quickstart"></a>PowerShell 빠른 시작을 통해 업로드된 VM 배포
PowerShell을 통해 Windows Server VM을 배포할 때는 `-LicenseType`에 대한 추가 매개 변수가 있습니다. Azure에 VHD를 업로드하고 나면 다음과 같이 `New-AzureRmVM`을 사용하여 VM을 만들고 라이선싱 형식을 지정합니다.

Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Windows 클라이언트:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

아래에서 [PowerShell을 통해 Azure에서 VM을 배포하는 보다 자세한 연습을 확인](hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough)하거나, [Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행하는 다른 단계에 대해 보다 자세히 설명하는 더 자세한 지침을 확인하세요.


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM이 라이선싱 혜택을 사용하고 있는지 확인
PowerShell 또는 Resource Manager 배포 메서드를 통해 VM을 배포한 후 다음과 같이 `Get-AzureRmVM` 을 사용하여 라이선스 형식을 확인합니다.

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Windows Server의 경우 다음 예제와 유사하게 출력됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

이 출력은 Azure 갤러리에서 바로 배포된 VM과 같이 Azure 하이브리드 사용 혜택 라이선싱 없이 배포된 다음 VM과는 대조됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>자세한 PowerShell 배포 연습
다음 자세한 PowerShell 단계는 VM의 전체 배포를 보여 줍니다. [Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 만든 실제 cmdlet 및 다른 구성 요소에 대해 자세한 컨텍스트를 읽어볼 수 있습니다. 단계별로 리소스 그룹, 저장소 계정 및 가상 네트워킹을 만든 다음 VM을 정의하고 마지막으로 VM을 만듭니다.

먼저 안전하게 자격 증명을 얻고, 위치를 설정하고, 리소스 그룹 이름을 지정합니다.

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

공용 IP 만들기:

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

서브넷, NIC 및 VNET을 정의합니다.

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

VM 이름을 지정하고 VM 구성을 만듭니다.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

OS 정의:

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

VM에 NIC를 추가합니다.

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

사용할 저장소 계정 정의:

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

VHD를 업로드하고, 적절하게 준비하고, 사용을 위해 VM에 연결합니다.

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

마지막으로, VM을 만들고 Azure 하이브리드 사용 혜택을 사용하기 위한 라이선싱 유형을 정의합니다.

Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

Windows 클라이언트:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Client"
```

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Azure 하이브리드 사용 혜택 라이선싱](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 참조하세요.

[Resource Manager 템플릿 사용](../../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.

