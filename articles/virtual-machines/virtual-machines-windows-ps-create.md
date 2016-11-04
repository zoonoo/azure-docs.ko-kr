---
title: PowerShell을 사용하여 Azure VM 만들기 | Microsoft Docs
description: Azure PowerShell 및 Azure Resource Manager를 사용하여 Windows Server를 실행하는 새 VM을 손쉽게 만들 수 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: davidmu

---
# 리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기
이 문서는 [리소스 관리자](../resource-group-overview.md) 및 PowerShell을 사용하여 Windows 서버를 실행하는 Azure 가상 컴퓨터 및 필요한 리소스를 빠르게 만드는 방법을 보여 줍니다.

이 문서의 모든 단계는 가상 컴퓨터를 만드는 데 필요하며 단계를 수행하려면 약 30분 정도가 걸립니다.

## 1단계: Azure PowerShell 설치
최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 2단계: 리소스 그룹 만들기
먼저, 리소스 그룹을 만듭니다.

1. 리소스를 만들 수 있는 사용 가능한 위치 목록을 가져옵니다.
   
        Get-AzureRmLocation | sort Location | Select Location
   
    다음 예제와 유사한 결과가 표시됩니다.
   
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus
2. **$locName** 값을 목록의 위치로 바꿉니다. 변수를 만듭니다.
   
        $locName = "centralus"
3. **$rgName** 값을 새 리소스 그룹 이름으로 바꿉니다. 변수 및 리소스 그룹을 만듭니다.
   
        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

## 3단계: 저장소 계정 만들기
만든 가상 컴퓨터에서 사용한 가상 하드 디스크를 저장하는 데 [저장소 계정](../storage/storage-introduction.md)이 필요합니다.

1. **$stName** 값을 저장소 계정 이름으로 바꿉니다. 이름의 고유성을 테스트합니다.
   
        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName
   
    이 명령에서 **True**가 반환되면 제안한 이름은 Azure 내에서 고유합니다. 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
2. 이제 명령을 실행하여 저장소 계정을 만듭니다.
   
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName

## 4단계: 가상 네트워크 만들기
모든 가상 컴퓨터는 [가상 네트워크](../virtual-network/virtual-networks-overview.md)의 일부입니다.

1. **$subnetName** 값을 서브넷 이름으로 바꿉니다. 변수 및 서브넷을 만듭니다.
   
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
2. **$vnetName** 값을 가상 네트워크 이름으로 바꿉니다. 서브넷으로 변수 및 가상 네트워크를 만듭니다.
   
        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
   
    응용 프로그램 및 환경에 적합한 값을 사용합니다.

## 5단계: 공용 IP 주소 및 네트워크 인터페이스 만들기
가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. **$ipName** 값을 공용 IP 주소의 이름으로 바꿉니다. 변수 및 공용 IP 주소를 만듭니다.
   
        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
2. **$nicName** 값을 네트워크 인터페이스의 이름으로 바꿉니다. 변수 및 네트워크 인터페이스를 만듭니다.
   
        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

## 6단계: 가상 컴퓨터 만들기
이제 모든 항목이 결합되었으므로 가상 컴퓨터를 만들 시간입니다.

1. 명령을 실행하여 가상 컴퓨터에 대한 관리자 계정 이름 및 암호를 설정합니다.
   
        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
   
    암호의 길이는 12-123자여야 하며 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자가 있어야 합니다.
2. **$vmName** 값을 가상 컴퓨터 이름으로 바꿉니다. 변수 및 가상 컴퓨터 구성을 만듭니다.
   
        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
   
    가상 컴퓨터에 사용 가능한 크기 목록은 [Azure에서 가상 컴퓨터에 대한 크기](virtual-machines-windows-sizes.md)를 참조하세요.
3. **$compName** 값을 가상 컴퓨터의 컴퓨터 이름으로 바꿉니다. 변수를 만들고 구성에 운영 체제 정보를 추가합니다.
   
        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
4. 가상 컴퓨터를 프로비전하는 데 사용할 이미지를 정의합니다.
   
        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
   
    사용할 이미지 선택에 대한 자세한 내용은 [Powershell 또는 CLI로 Azure의 Windows 가상 컴퓨터 이미지 이동 및 선택](virtual-machines-windows-cli-ps-findimage.md)을 참조하세요.
5. 만든 네트워크 인터페이스를 구성에 추가합니다.
   
        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
6. **$blobPath** 값을 가상 하드 디스크의 저장소에 있는 경로 및 파일 이름으로 바꿉니다. 가상 하드 디스크 파일은 일반적으로 컨테이너에 저장됩니다(예: **vhds/WindowsVMosDisk.vhd**). 변수를 만듭니다.
   
        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
7. **$diskName** 값을 운영 체제 디스크 이름으로 바꿉니다. 변수를 만들고 구성에 디스크 정보를 추가합니다.
   
        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
8. 마지막으로 가상 컴퓨터를 만듭니다.
   
        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm
   
    Azure 포털에서 리소스 그룹 및 모든 해당 리소스가 표시되고 PowerShell 창에 성공 상태가 표시됩니다.
   
        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## 다음 단계
* 배포에 문제가 있는 경우 다음 단계로서 [Azure Portal을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)을 살펴봅니다.
* [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.
* [Resource Manager 템플릿을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-windows-ps-template.md)의 정보를 사용하여 가상 컴퓨터를 만드는 데 템플릿을 활용합니다.

<!---HONumber=AcomDC_1005_2016-->