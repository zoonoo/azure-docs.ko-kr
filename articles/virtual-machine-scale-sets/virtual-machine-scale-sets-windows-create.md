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
	ms.date="06/10/2016"
	ms.author="davidmu"/>

# Azure PowerShell을 사용하여 Windows 가상 컴퓨터 크기 집합 만들기

다음 단계에서는 빈칸을 채우는 방식을 따라 Azure 가상 컴퓨터 크기 집합을 만듭니다. 크기 집합에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md)를 참조하십시오.

이 문서의 단계를 수행하려면 약 30분이 걸립니다.

## 1단계: Azure PowerShell 설치

최신 버전의 Azure PowerShell을 설치하는 방법, 사용할 구독을 선택하는 방법, Azure 계정에 로그인하는 방법은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 2단계: 리소스 만들기

새 가상 컴퓨터 크기 집합에 필요한 리소스를 만듭니다.

### 리소스 그룹

가상 컴퓨터 크기 집합은 리소스 그룹에 포함되어야 합니다.

1.  사용 가능한 위치와 지원되는 서비스 목록을 가져옵니다.

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

2. 가장 적합한 위치를 선택하고 **$locName** 값을 해당 위치 이름으로 바꾼 다음 변수를 만듭니다.

        $locName = "location name from the list, such as Central US"

3. **$rgName** 값을 새 리소스 그룹에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다.

        $rgName = "resource group name"
        
4. 리소스 그룹을 만듭니다.
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    다음과 유사한 결과가 표시됩니다.

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### 저장소 계정

저장소 계정은 가상 컴퓨터에서 운영 체제 디스크 및 크기 조정에 사용되는 진단 데이터를 저장하기 위해 사용합니다. 가능하면 규모 집합에서 만들어진 각 가상 컴퓨터에 대해 저장소 계정을 갖는 것이 가장 좋습니다. 가능하지 않다면 저장소 계정당 VM을 20개 이하로 계획하세요. 이 문서의 예제는 규모 집합에서 가상 컴퓨터 3대에 대해 만들어진 저장소 계정 3개를 보여줍니다.

1. **saName** 값을 새 저장소 계정에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 

        $saName = "storage account name"
        
2. 선택한 이름이 고유한지 테스트합니다.
    
        Test-AzureName -Storage $saName

    답이 **False**인 경우에는 제안한 이름이 고유한 것입니다.

3. **$saType** 값을 저장소 계정 유형으로 바꾼 다음 변수를 만듭니다.

        $saType = "storage account type"
        
    가능한 값은 Standard\_LRS, Standard\_GRS, Standard\_RAGRS 또는 Premium\_LRS입니다.
        
4. 계정을 만듭니다.
    
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

5. 예를 들어 1부터 4까지 단계를 반복하여 저장소 계정을 myst1, myst2, myst3와 같이 3개를 만듭니다.

### 가상 네트워크

크기 집합의 가상 컴퓨터에 가상 네트워크가 필요합니다.

1. **$subName** 값을 가상 네트워크의 서브넷에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 

        $subName = "subnet name"
        
2. 서브넷 구성을 만듭니다.
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24
        
    주소 접두사는 가상 네트워크와 다를 수 있습니다.

3. **$netName** 값을 가상 네트워크에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다.

        $netName = "virtual network name"
        
4. 가상 네트워크를 만듭니다.
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### 공용 IP 주소

네트워크 인터페이스를 만들기 전에 공용 IP 주소를 만들어야 합니다.

1. **$domName** 값을 공용 IP 주소에 사용하려는 도메인 이름 레이블로 바꾼 다음 변수를 만듭니다.  

        $domName = "domain name label"
        
    레이블에는 문자, 숫자, 하이픈만 사용할 수 있으며 마지막 문자는 문자 또는 숫자여야 합니다.
    
2. 이름이 고유한지 여부를 테스트합니다.
    
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    답이 **True**인 경우에는 제안한 이름이 고유한 것입니다.

3. **$pipName** 값을 공용 IP 주소에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다.

        $pipName = "public ip address name"
        
4. 공용 IP 주소를 만듭니다.
    
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### 네트워크 인터페이스

이제 공용 IP 주소가 있으므로 네트워크 인터페이스를 만들 수 있습니다.

1. **$nicName** 값을 네트워크 인터페이스에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 

        $nicName = "network interface name"
        
2. 네트워크 인터페이스를 만듭니다.
    
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 크기 집합 구성

크기 집합 구성에 필요한 모든 리소스가 있으므로 크기 집합을 만들겠습니다.

1. **$ipName** 값을 IP 구성에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 

        $ipName = "IP configuration name"
        
2. IP 구성을 만듭니다.

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. **$vmssConfig** 값을 크기 집합 구성에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다.

        $vmssConfig = "Scale set configuration name"
        
3. 크기 집합에 대한 구성을 만듭니다.

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    이 예제는 3개의 가상 컴퓨터로 크기 집합을 만드는 방법을 보여줍니다. 크기 집합의 용량에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md)를 참조하십시오. 이 단계에서는 집합에 있는 가상 컴퓨터의 크기(SkuName이라고 함)도 설정합니다. 요구 사항에 맞는 크기를 찾으려면 [가상 컴퓨터의 크기](../virtual-machines/virtual-machines-windows-sizes.md)를 참조하세요.
    
4. 크기 집합 구성에 네트워크 인터페이스 구성을 추가합니다.
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    다음과 유사한 결과가 표시됩니다.

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### 운영 체제 프로필

1. **$computerName** 값을 사용하려는 컴퓨터 이름 접두사로 바꾼 다음 변수를 만듭니다. 

        $computerName = "computer name prefix"
        
2. **$adminName** 값을 가상 컴퓨터의 관리자 계정 이름으로 바꾼 다음 변수를 만듭니다.

        $adminName = "administrator account name"
        
3. **$adminPassword** 값을 계정 암호로 바꾼 다음 변수를 만듭니다.

        $adminPassword = "password for administrator accounts"
        
4. 운영 체제 프로필을 만듭니다.

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### 저장소 프로필

1. **$storageProfile** 값을 저장소 프로필에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다.  

        $storeProfile = "storage profile name"
        
2. 사용할 이미지를 정의하는 변수를 만듭니다.
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    사용할 이미지에 대한 자세한 내용은 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md)을 참조하세요.
        
3. **$vhdContainer** 값을 가상 하드 디스크가 저장된 경로가 들어 있는 목록과 바꾼 다음(예: "https://mystorage.blob.core.windows.net/vhds") 변수를 만듭니다.
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. 저장소 프로필을 만듭니다.

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### 가상 컴퓨터 크기 집합

마지막으로 크기 집합을 만들 수 있습니다.

1. **$vmssName** 값을 가상 컴퓨터 크기 집합의 이름으로 바꾼 다음 변수를 만듭니다.

        $vmssName = "scale set name"
        
2. 크기 집합을 만듭니다.

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    배포가 성공했음을 나타내는 내용이 다음과 유사하게 표시됩니다.

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                               ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## 3단계: 리소스 탐색

다음과 같은 리소스를 사용하여 방금 만든 가상 컴퓨터 크기 집합을 탐색합니다.

- Azure 포털 - 포털을 사용하여 제한된 양의 정보를 얻을 수 있습니다.
- [Azure 리소스 탐색기](https://resources.azure.com/) - 크기 집합의 현재 상태를 탐색하는 데 가장 적합한 도구입니다.
- Azure Powershell - 이 명령을 사용하여 정보를 가져옵니다.

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## 다음 단계

- 방금 [가상 컴퓨터 크기 집합의 가상 컴퓨터 관리](virtual-machine-scale-sets-windows-manage.md)의 정보에 따라 만든 크기 집합을 관리합니다.
- [자동 크기 조정 및 가상 컴퓨터 크기 집합](virtual-machine-scale-sets-autoscale-overview.md)의 정보를 사용하여 크기 집합의 자동 크기 조정을 설정해 보십시오.
- [가상 컴퓨터 크기 집합을 사용하여 수직 자동 크기 조정](virtual-machine-scale-sets-vertical-scale-reprovision.md)을 검토하여 수직 크기 조정에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0615_2016-->