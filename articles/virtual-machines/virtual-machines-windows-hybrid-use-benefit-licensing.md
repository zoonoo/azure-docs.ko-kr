<properties
   pageTitle="Window Server에 대한 Azure 하이브리드 사용 혜택 | Microsoft Azure"
   description="Azure에 온-프레미스 라이선스를 가져오기 위해 Windows Server Software Assurance 혜택을 최대화하는 방법에 대해 알아봅니다."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# Windows Server에 대한 Azure 하이브리드 사용 혜택

Software Assurance와 함께 Windows Server를 사용하는 고객의 경우, 온-프레미스 Windows Server 라이선스를 Azure로 가져올 수 있으며 Azure에서 절감된 비용으로 Windows Server VM을 실행할 수 있습니다. Azure 하이브리드 사용 혜택을 사용하면 기본 계산 요금으로 Azure에서 Windows Server VM을 실행할 수 있습니다. 자세한 내용은 [Azure 하이브리드 사용 혜택 라이선싱 페이지](https://azure.microsoft.com/pricing/hybrid-use-benefit/)를 참조하세요. 이 문서에서는 이 라이선싱 혜택을 사용하기 위해 Azure에서 Windows Server VM을 배포하는 방법을 설명합니다.

> [AZURE.NOTE] Azure 하이브리드 사용 혜택을 사용하는 Windows Server VM을 배포하는 데 Azure 마켓플레이스 이미지를 사용할 수 없습니다. 기본 계산 요금을 할인 받을 수 있도록 VM을 올바르게 등록하려면 PowerShell 또는 리소스 관리자 템플릿을 사용하여 VM을 배포해야 합니다.

## 필수 구성 요소
Azure에서 Windows Server VM에 대한 Azure 하이브리드 사용 혜택을 사용하려면 몇 가지 필수 조건이 있습니다.

- Azure PowerShell 모듈이 설치되어 있어야 합니다.
- Windows Server VHD를 Azure 저장소에 업로드해야 합니다.

### Azure PowerShell 설치
먼저 [최신 Azure PowerShell을 설치 및 구성](../powershell-install-configure.md)했는지 확인합니다. Resource Manager 템플릿을 사용하여 VM을 배포하려는 경우에도 Windows Server VHD를 업로드하려면 Azure PowerShell을 설치해야 합니다(다음 단계 참조).

### Windows Server VHD 업로드

Azure에서 Windows Server VM을 배포하려면 먼저 기본 Windows Server 빌드를 포함하는 VHD를 만들어야 합니다. 이 VHD는 Azure에 업로드하기 전에 Sysprep을 통해 적절하게 준비되어야 합니다. 자세한 내용은 [VHD 요구 사항 및 Sysprep 프로세스](./virtual-machines-windows-upload-image.md) 및 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요. Sysprep를 실행하기 전에 VM을 백업합니다. VHD를 준비했으면 다음과 같이 `Add-AzureRmVhd` cmdlet을 사용하여 Azure 저장소 계정에 VHD를 업로드합니다.

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, SharePoint 서버 및 Dynamics는 또한 Software Assurance 라이선스를 활용할 수 있습니다. Windows Server 이미지를 준비하려면 응용 프로그램 구성 요소를 설치하고 라이선스 키를 적절하게 제공한 다음, 디스크 이미지를 Azure에 업로드합니다. 응용 프로그램과 함께 [SysPrep을 사용하여 SQL Server 설치에 대한 고려 사항](https://msdn.microsoft.com/library/ee210754.aspx) 또는 [SharePoint Server 2016 참조 이미지 빌드(SysPrep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)와 같은 Sysprep 실행에 대한 적합한 설명서를 검토합니다.

자세한 내용은 [Azure에 VHD 업로드 프로세스](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account)를 참조하세요.

> [AZURE.TIP] 이 문서에서는 Windows Server VM을 배포하는 방법을 중점적으로 설명합니다. 같은 방식으로 Windows 클라이언트 VM도 배포할 수 있습니다. 다음 예제에서 `Server`를 `Client`로 적절하게 바꿉니다.

## PowerShell 빠른 시작을 통해 VM 배포
PowerShell을 통해 Windows Server VM을 배포할 때는 `-LicenseType`에 대한 추가 매개 변수가 있습니다. Azure에 VHD를 업로드하고 나면 다음과 같이 `New-AzureRmVM`을 사용하여 새 VM을 만들고 라이선싱 형식을 지정합니다.

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

아래에서 [PowerShell을 통해 Azure에서 VM을 배포하는 보다 자세한 연습을 확인](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough)하거나, [Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기](./virtual-machines-windows-ps-create.md)를 수행하는 다른 단계에 대해 보다 자세히 설명하는 더 자세한 지침을 확인하세요.

## 리소스 관리자를 통해 VM 배포
Resource Manager 템플릿 내에서 `licenseType`에 대한 추가 매개 변수를 지정할 수 있습니다. [Azure Resource Manager 템플릿 작성](../resource-group-authoring-templates.md)에 대해 자세히 알아볼 수 있습니다. Azure에 VHD를 업로드하고 나면 Resource Manager 템플릿을 편집하여 계산 공급자의 일부로 라이선스 유형을 포함하고 정상적으로 템플릿을 배포합니다.

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## VM이 라이선싱 혜택을 사용하고 있는지 확인
PowerShell 또는 Resource Manager 배포 메서드를 통해 VM을 배포한 후 다음과 같이 `Get-AzureRmVM`을 사용하여 라이선스 형식을 확인합니다.
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

다음과 유사하게 출력됩니다.

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

이는 Azure 갤러리에서 바로 배포된 VM과 같이 Azure 하이브리드 사용 혜택 라이선싱 없이 배포된 다음 VM과는 대조됩니다.

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## 자세한 PowerShell 연습

다음 자세한 PowerShell 단계는 VM의 전체 배포를 보여 줍니다. [Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기](./virtual-machines-windows-ps-create.md)에서 만든 실제 cmdlet 및 다른 구성 요소에 대해 자세한 컨텍스트를 읽어볼 수 있습니다. 단계별로 리소스 그룹, 저장소 계정 및 가상 네트워킹을 만든 다음 VM을 정의하고 마지막으로 VM을 만듭니다.
 
먼저 안전하게 자격 증명을 얻고, 위치를 설정하고, 리소스 그룹 이름을 지정합니다.

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

공용 IP 만들기:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

서브넷, NIC 및 VNET을 정의합니다.

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

VM 이름을 지정하고 VM 구성을 만듭니다.

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

OS 정의:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

VM에 NIC를 추가합니다.

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

사용할 저장소 계정 정의:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

VHD를 업로드하고, 적절하게 준비하고, 사용을 위해 VM에 연결합니다.

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

마지막으로, VM을 만들고 Azure 하이브리드 사용 혜택을 사용하기 위한 라이선싱 유형을 정의합니다.

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## 다음 단계

자세한 내용은 [Azure 하이브리드 사용 혜택 라이선싱](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 참조하세요.

[Resource Manager 템플릿 사용](../resource-group-overview.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0831_2016-->