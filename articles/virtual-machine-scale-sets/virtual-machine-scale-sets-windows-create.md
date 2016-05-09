<properties
	pageTitle="가상 컴퓨터 규모 집합 만들기 | Microsoft Azure"
	description="Powershell을 사용하여 가상 컴퓨터 크기 집합 만들기"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Azure PowerShell을 사용하여 Windows 가상 컴퓨터 크기 집합 만들기

다음 단계에서는 빈칸을 채우는 방식을 따라 Azure 가상 컴퓨터 크기 집합을 만듭니다. 이 문서에서 값이 필요한 변수는 사용자가 입력해야 합니다. 따옴표 안의 모든 값을 사용자의 구독 및 응용 프로그램에 적합한 값으로 바꿉니다.

## 1단계: Azure PowerShell 설치

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## 2단계: 구독 설정

1. PowerShell 프롬프트를 시작합니다.

2. 계정에 로그인합니다.

        Login-AzureRmAccount

3. 구독을 가져옵니다.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. 현재 구독으로 사용할 구독을 선택합니다.

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## 2단계: 리소스 만들기

새 가상 컴퓨터 크기 집합에 필요한 리소스를 만듭니다.

### 리소스 그룹

가상 컴퓨터 크기 집합은 리소스 그룹에 포함되어야 합니다.

1.  이 명령을 실행하여 사용 가능한 위치와 지원되는 서비스 목록을 가져옵니다.

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    다음과 유사한 결과가 표시됩니다.

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

    가장 적합한 위치를 선택한 다음 따옴표 안의 텍스트를 해당 위치 이름으로 바꿉니다.

        $locName = "location name from the list, such as Central US"

4. 따옴표 안의 텍스트를 새 리소스 그룹에 사용하려는 이름으로 바꾼 다음 이전에 설정한 위치에 해당 변수를 만듭니다.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    다음과 유사한 결과가 표시됩니다.

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### 저장소 계정

크기 집합에 만드는 가상 컴퓨터는 저장소 계정에서 관련 디스크를 저장해야 합니다.

1. 따옴표 안의 텍스트를 저장소 계정에 사용하려는 이름으로 바꾼 다음 고유한지 여부를 테스트합니다.

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    답이 **False**인 경우에는 제안한 이름이 고유한 것입니다.

2. 따옴표 안의 텍스트를 저장소 계정 유형으로 바꾼 다음 이전에 설정한 이름과 위치로 계정을 만듭니다. 가능한 값은 Standard\_LRS, Standard\_GRS, Standard\_RAGRS 또는 Premium\_LRS입니다.

        $saType = "storage account type"
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    다음과 유사한 결과가 표시됩니다.

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### 가상 네트워크

크기 집합의 가상 컴퓨터에 가상 네트워크가 필요합니다.

1. 따옴표의 텍스트를 가상 네트워크 서브넷에 사용할 이름으로 바꾼 다음 구성을 만듭니다.

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. 따옴표 안의 텍스트를 가상 네트워크에 사용하려는 이름으로 바꾼 다음 이전에 정의한 정보와 리소스를 사용하여 만듭니다.

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 공용 IP 주소

네트워크 인터페이스를 만들기 전에 공용 IP 주소를 만들어야 합니다.

1. 따옴표 안의 텍스트를 공용 IP 주소에 사용할 도메인 이름 레이블로 바꾼 다음 이름이 고유한지 여부를 테스트합니다. 레이블에는 문자, 숫자, 하이픈만 사용할 수 있으며 마지막 문자는 문자 또는 숫자여야 합니다.

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    답이 **False**인 경우에는 제안한 이름이 고유한 것입니다.

2. 따옴표 안의 텍스트를 공용 IP 주소에 사용하려는 이름으로 바꾼 다음 해당 변수를 만듭니다.

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### 네트워크 인터페이스

이제 공용 IP 주소가 있으므로 네트워크 인터페이스를 만들 수 있습니다.

1. 따옴표 안의 텍스트를 네트워크 인터페이스에 사용하려는 이름으로 바꾼 다음 이전에 만든 리소스를 사용하여 해당 변수를 만듭니다.

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### 가상 컴퓨터 크기 집합 만들기

필요한 모든 리소스가 있으므로 이제 크기 집합을 만들어야 합니다.

1. 따옴표 안의 텍스트를 IP 구성에 사용하려는 이름으로 바꾼 다음 해당 변수를 만듭니다.

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. 따옴표 안의 텍스트를 크기 집합 구성에 사용하려는 이름으로 바꾼 다음 해당 변수를 만듭니다. 이 단계에서는 집합에 있는 가상 컴퓨터의 크기(SkuName이라고 함)를 설정합니다. 요구 사항에 맞는 크기를 찾으려면 [가상 컴퓨터의 크기](..\virtual-machines\virtual-machines-windows-sizes.md)를 참조하세요. 이 예에서는 Standard\_A0을 사용하는 것이 좋습니다.

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    다음과 유사한 결과가 표시됩니다.

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. 따옴표 안의 텍스트를 사용하려는 컴퓨터 이름의 접두사, 가상 컴퓨터의 관리자 계정 이름, 계정 암호로 바꾼 다음 운영 체제 프로필을 만듭니다.

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    osProfile 섹션이 다음과 유사하게 표시됩니다.

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. 따옴표 안의 텍스트를 저장소 프로필에 사용하려는 이름, 이미지 정보, 가상 컴퓨터가 저장된 디스크 위치에 대한 저장소 경로로 바꾼 다음 프로필을 만듭니다. 필요한 이미지를 찾으려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md)을 참조하세요.

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    storageProfile 섹션에 다음과 유사한 결과가 표시됩니다.

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. 따옴표 안의 텍스트를 가상 컴퓨터 크기 집합의 이름으로 바꾼 다음 해당 변수를 만듭니다.

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    배포가 성공했음을 나타내는 내용이 다음과 유사하게 표시됩니다.

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## 3단계: 리소스 탐색

다음과 같은 리소스를 사용하여 방금 만든 가상 컴퓨터 크기 집합을 탐색합니다.

- Azure 포털 - 포털을 사용하여 제한된 양의 정보를 얻을 수 있습니다.
- [Azure 리소스 탐색기](https://resources.azure.com/) - 크기 집합의 현재 상태를 탐색할 수 있는 최상의 도구입니다.
- Azure Powershell - 이 명령을 사용하여 정보를 가져옵니다.

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## 다음 단계

1. 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md)를 참조하십시오.

2. [자동 크기 조정 및 가상 컴퓨터 크기 집합](virtual-machine-scale-sets-autoscale-overview.md)의 정보를 사용하여 크기 집합의 자동 크기 조정을 설정해 보십시오.

<!---HONumber=AcomDC_0427_2016-->