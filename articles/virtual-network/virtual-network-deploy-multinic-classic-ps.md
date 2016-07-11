<properties
   pageTitle="클래식 배포 모델에서 PowerShell을 사용하여 여러 NIC VM 배포 | Microsoft Azure"
   description="클래식 배포 모델에서 PowerShell을 사용하여 여러 NIC VM을 배포하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#PowerShell을 사용하여 다중 NIC VM(클래식) 배포

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager 모델을 사용하여 이러한 단계를 수행](virtual-network-deploy-multinic-arm-ps.md)하는 방법을 알아봅니다.

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

현재는 단일 NIC가 사용되는 VM과 여러 NIC가 사용되는 VM을 같은 클라우드 서비스에서 포함할 수 없습니다. 따라서 시나리오의 다른 모든 구성 요소와 다른 클라우드 서비스에 백 엔드 서버를 구현해야 합니다. 다음 단계에서는 기본 리소스에 *IaaSStory*라는 클라우드 서비스를, 백 엔드 서버에는 *IaaSStory-BackEnd*를 사용합니다.

## 필수 조건

백 엔드 서버를 배포하려면 먼저 이 시나리오에 필요한 모든 리소스로 기본 클라우드 서비스를 배포해야 합니다. 최소한, 백 엔드에 대한 서브넷으로 가상 네트워크를 만들어야 합니다. 가상 네트워크를 배포하는 방법을 알아보려면 [PowerShell을 사용하여 가상 네트워크를 만들기](virtual-networks-create-vnet-classic-netcfg-ps.md)를 참조하세요.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 백 엔드 VM 배포

백 엔드 VM은 만드는 리소스에 따라 아래와 같이 다릅니다.

- **백 엔드 서브넷**. 트래픽을 격리하기 위해서 데이터베이스 서버는 별도의 서브넷에 속하게 됩니다. 아래 스크립트는 이 서브넷이 *WTestVnet*이라는 vnet에 존재한다고 추정합니다.
- **데이터 디스크용 저장소 계정**. 성능 향상을 위해 데이터베이스 서버의 데이터 디스크는 SSD(반도체 드라이브) 기술을 사용하며, 이 기술에는 프리미엄 저장소 계정이 필요합니다. 배포할 Azure 위치에서 프리미엄 저장소가 지원되는지 확인하세요.
- **가용성 집합**. 모든 데이터베이스 서버가 단일 가용성 집합에 추가되어, 유지 관리 도중에 하나 이상의 VM이 실행 중이도록 합니다.

### 1단계 - 스크립트 시작

사용되는 전체 PowerShell 스크립트를 [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)에서 다운로드할 수 있습니다. 다음 단계에 따라 스크립트를 사용자 환경에서 작동하도록 변경합니다.

1. 위의 [필수 조건](#Prerequisites)에서 배포한 기존 리소스 그룹을 기반으로 다음 변수 값을 변경합니다.

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. 백 엔드 배포에 사용하려는 값을 기반으로 다음 변수 값을 변경합니다.

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### 2단계 - VM에 필요한 리소스 만들기

새로운 클라우드 서비스, 모든 VM에 대한 데이터 디스크의 저장소 계정을 만들어야 합니다. VM에 대한 로컬 관리자 계정 및 이미지도 지정해야 합니다. 이러한 리소스를 만들려면 다음 단계를 실행합니다.

1. 새 클라우드 서비스 만들기

		New-AzureService -ServiceName $backendCSName -Location $location

2. 새 프리미엄 저장소 계정 만들기

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. 앞에서 만든 저장소 계정을 구독에 대한 현재 저장소 계정으로 설정합니다.

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. VM에 대한 이미지를 선택합니다.

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. 로컬 관리자 계정 자격 증명을 설정합니다.

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### 3단계: VM 만들기

루프를 사용하여 VM을 원하는 개수만큼 만들고 루프 내에서 필요한 NIC와 VM을 만듭니다. NIC와 VM을 만들려면 다음 단계를 실행합니다.

1. `$numberOfVMs` 변수 값을 기반으로 필요한 횟수만큼 VM 1개와 NIC 2개를 만드는 명령을 반복하는 `for` 루프를 시작합니다.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. VM의 이미지, 크기, 가용성 집합을 지정하는 `VMConfig` 개체를 만듭니다.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. VM을 Windows VM으로 프로비전합니다.

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. 기본 NIC를 설정하여 고정 IP 주소를 할당합니다.

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. 각 VM에 두 번째 NIC를 추가합니다.

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig

6. 각 VM에 데이터 디스크를 만듭니다.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       

		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. 각 VM을 만들고 루프를 끝냅니다.

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### 4단계 - 스크립트 실행

스크립트를 다운로드하여 요구에 맞게 변경했으므로, 이제 이 스크립트를 실행하여 여러 NIC와 백 엔드 데이터베이스 VM을 만듭니다.

1. 스크립트를 저장하여 **PowerShell** 명령 프롬프트 또는 **PowerShell ISE**에서 실행합니다. 아래와 같이 초기 출력에 표시됩니다.

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. 자격 증명 프롬프트에 필요한 정보를 입력하고 **확인**을 클릭합니다. 아래와 같은 출력이 표시됩니다.

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

<!---HONumber=AcomDC_0629_2016-->