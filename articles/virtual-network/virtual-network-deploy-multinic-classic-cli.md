<properties 
   pageTitle="클래식 배포 모델에서 Azure CLI를 사용하여 여러 NIC VM 배포 | Microsoft Azure"
   description="클래식 배포 모델에서 Azure CLI를 사용하여 여러 NIC VM을 배포하는 방법을 알아봅니다."
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
   ms.date="11/12/2015"
   ms.author="telmos" />

#Azure CLI를 사용하여 다중 NIC VM(클래식) 배포

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

현재는 단일 NIC가 사용되는 VM과 여러 NIC가 사용되는 VM을 같은 클라우드 서비스에서 사용할 수 없기 때문에 시나리오의 다른 모든 구성 요소와 다른 클라우드 서비스에 백 엔드 서버를 구현하게 됩니다. 다음 단계에서는 기본 리소스에 *IaaSStory* 라는 클라우드 서비스를, 백 엔드 서버에는 *IaaSStory-BackEnd* 를 사용합니다.

## 필수 조건

백 엔드 서버를 배포하려면 먼저 이 시나리오에 필요한 모든 리소스로 기본 클라우드 서비스를 배포해야 합니다. 최소한, 백 엔드에 대한 서브넷으로 가상 네트워크를 만들어야 합니다. 가상 네트워크를 배포하는 방법을 알아보려면 [Azure CLI를 사용하여 가상 네트워크를 만들기](virtual-networks-create-vnet-classic-cli.md)를 참조하세요.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 백 엔드 VM 배포

백 엔드 VM은 만드는 리소스에 따라 아래와 같이 다릅니다.

- **데이터 디스크용 저장소 계정**. 성능 향상을 위해 데이터베이스 서버의 데이터 디스크는 SSD(반도체 드라이브) 기술을 사용하며, 이 기술에는 프리미엄 저장소 계정이 필요합니다. 배포할 Azure 위치에서 프리미엄 저장소가 지원되는지 확인하세요.
- **NIC**. 각 VM에 데이터베이스 액세스용으로 하나, 그리고 관리용으로 하나씩, 두 개의 NIC가 사용됩니다.
- **가용성 집합**. 모든 데이터베이스 서버가 단일 가용성 집합에 추가되어, 유지 관리 도중에 하나 이상의 VM이 실행 중이도록 합니다. 

### 1단계 - 스크립트 시작

사용되는 전체 bash 스크립트를 [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.sh)에서 다운로드할 수 있습니다. 다음 단계에 따라 스크립트를 사용자 환경에서 작동하도록 변경합니다.

1. 위의 [필수 조건](#Prerequisites)에서 배포한 기존 리소스 그룹을 기반으로 다음 변수 값을 변경합니다.

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. 백 엔드 배포에 사용하려는 값을 기반으로 다음 변수 값을 변경합니다.

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### 2단계 - VM에 필요한 리소스 만들기

1. 모든 백 엔드 VM에 대해 새 클라우드 서비스를 만듭니다. 리소스 그룹 이름에 `$backendCSName` 변수가, Azure 지역에 대해서는 `$location`이 사용되었습니다.

		azure service create --serviceName $backendCSName \
		    --location $location

2. VM에서 사용할 OS 및 데이터 디스크에 대해 프리미엄 저장소 계정을 만듭니다.

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS 

### 3단계 - 여러 NIC를 사용하여 VM 만들기

1. `numberOfVMs` 변수를 기반으로 하여 여러 VM을 만드는 루프를 시작합니다.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. 각 VM에 대해 NIC 두 개의 이름과 IP 주소를 각각 지정합니다.

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		
		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. VM을 만듭니다. 이름, 서브넷, IP 주소와 함께 모든 NIC 목록을 포함하는 `--nic-config` 매개 변수 사용에 유의합니다.

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. 각 VM에 대해 두 개의 데이터 디스크를 만듭니다.

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd
		
		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### 4단계 - 스크립트 실행

스크립트를 다운로드하여 요구에 맞게 변경했으므로, 이제 이 스크립트를 실행하여 여러 NIC를 사용하여 백 엔드 데이터베이스 VM을 만듭니다.

1. 스크립트를 저장하고 **Bash** 터미널에서 실행합니다. 아래와 같이 초기 출력에 표시됩니다.

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. 몇 분 후에 실행이 종료되고 아래와 같이 출력의 나머지 부분이 표시됩니다.

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!----HONumber=Nov15_HO4-->
