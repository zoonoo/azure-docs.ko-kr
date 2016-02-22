<properties 
   pageTitle="리소스 관리자에서 Azure CLI를 사용하여 여러 NIC VM 배포 | Microsoft Azure"
   description="리소스 관리자에서 Azure CLI를 사용하여 여러 NIC VM을 배포하는 방법에 대해 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Azure CLI를 사용하여 다중 NIC VM 배포

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

현재는 단일 NIC가 사용되는 VM과 여러 NIC가 사용되는 VM을 동일 리소스 그룹에서 사용할 수 없기 때문에 다른 모든 구성 요소와 다른 리소스 그룹에 백 엔드 서버를 구현하게 됩니다. 다음 단계에서는 기본 리소스 그룹에 *IaaSStory*라는 리소스 그룹을, 백 엔드 서버에는 *IaaSStory-BackEnd*를 사용합니다.

## 필수 조건

백 엔드 서버를 배포하려면 먼저 이 시나리오에 필요한 모든 리소스로 기본 리소스 그룹을 배포해야 합니다. 이러한 리소스를 배포하려면 다음 단계를 수행하세요.

1. [템플릿 페이지](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)로 이동합니다.
2. 서식 파일 페이지에서 **Parent resource group(부모 리소스 그룹)** 오른쪽에 있는 **Azure에 배포**를 클릭합니다.
3. 필요한 경우 매개 변수 값을 변경한 다음 Azure Preview 포털의 단계에 따라 리소스 그룹을 배포합니다.

> [AZURE.IMPORTANT] 저장소 계정 이름이 고유한지 확인합니다. Azure에서 중복된 저장소 계정 이름을 사용할 수 없습니다.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 백 엔드 VM 배포

백 엔드 VM은 만드는 리소스에 따라 아래와 같이 다릅니다.

- **데이터 디스크용 저장소 계정**. 성능 향상을 위해 데이터베이스 서버의 데이터 디스크는 SSD(반도체 드라이브) 기술을 사용하며, 이 기술에는 프리미엄 저장소 계정이 필요합니다. 배포할 Azure 위치에서 프리미엄 저장소가 지원되는지 확인하세요.
- **NIC**. 각 VM에 데이터베이스 액세스용으로 하나, 그리고 관리용으로 하나씩, 두 개의 NIC가 사용됩니다.
- **가용성 집합**. 모든 데이터베이스 서버가 단일 가용성 집합에 추가되어, 유지 관리 도중에 하나 이상의 VM이 실행 중이도록 합니다. 

### 1단계 - 스크립트 시작

사용되는 전체 bash 스크립트를 [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-cli.sh)에서 다운로드할 수 있습니다. 다음 단계에 따라 스크립트를 사용자 환경에서 작동하도록 변경합니다.

1. 위의 [필수 조건](#Prerequisites)에서 배포한 기존 리소스 그룹을 기반으로 다음 변수 값을 변경합니다.

		existingRGName="IaaSStory"
		location="westus"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"
		remoteAccessNSGName="NSG-RemoteAccess"
		
2. 백 엔드 배포에 사용하려는 값을 기반으로 다음 변수 값을 변경합니다.

		backendRGName="IaaSStory-Backend"
		prmStorageAccountName="wtestvnetstorageprm"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		publisher="Canonical"
		offer="UbuntuServer"
		sku="14.04.2-LTS"
		version="latest"
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskName="datadisk"
		nicNamePrefix="NICDB"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

3. VM을 만들 `BackEnd` 서브넷의 ID를 검색합니다. 이 단계를 수행해야 하는 이유는 이 서브넷에 연결할 NIC이 다른 리소스 그룹에 속해 있기 때문입니다.

		subnetId="$(azure network vnet subnet show --resource-group $existingRGName \
		                --vnet-name $vnetName \
		                --name $backendSubnetName|grep Id)"
		subnetId=${subnetId#*/}

>[AZURE.TIP] 위의 첫 번째 명령은 [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) 및 [문자열 조작](http://tldp.org/LDP/abs/html/string-manipulation.html)(구체적으로 말하면, 하위 문자열 제거)을 사용합니다.

4. `NSG-RemoteAccess` NSG의 ID를 검색합니다. 이 단계를 수행해야 하는 이유는 이 NSG에 연결할 NIC이 다른 리소스 그룹에 속해 있기 때문입니다.

		nsgId="$(azure network nsg show --resource-group $existingRGName \
		                --name $remoteAccessNSGName|grep Id)"
		nsgId=${nsgId#*/}

### 2단계 - VM에 필요한 리소스 만들기

1. 모든 백 엔드 리소스를 위한 새 리소스 그룹을 만듭니다. 리소스 그룹 이름에 `$backendRGName` 변수가, Azure 지역에 대해서는 `$location`이 사용되었습니다.

		azure group create $backendRGName $location

2. VM에서 사용할 OS 및 데이터 디스크에 대해 프리미엄 저장소 계정을 만듭니다.

		azure storage account create $prmStorageAccountName \
		    --resource-group $backendRGName \
		    --location $location \
			--type PLRS 

3. VM의 가용성 집합을 만듭니다.

		azure availset create --resource-group $backendRGName \
		    --location $location \
		    --name $avSetName

### 3단계 - NIC 및 백 엔드 VM 만들기

1. `numberOfVMs` 변수를 기반으로 하여 여러 VM을 만드는 루프를 시작합니다.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. 각 VM에 대해 데이터베이스 액세스를 위한 NIC를 만듭니다.

		    nic1Name=$nicNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		    azure network nic create --name $nic1Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress1 \
		        --subnet-id $subnetId

3. 각 VM에 대해 원격 액세스를 위한 NIC를 만듭니다. NIC를 NSG에 연결하는 데 `--network-security-group` 매개 변수가 사용되었습니다.

		    nic2Name=$nicNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x
		    azure network nic create --name $nic2Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress2 \
		        --subnet-id $subnetId $vnetName \
		        --network-security-group-id $nsgId

4. VM을 만듭니다.

		    azure vm create --resource-group $backendRGName \
		        --name $vmNamePrefix$suffixNumber \
		        --location $location \
		        --vm-size $vmSize \
		        --subnet-id $subnetId \
		        --availset-name $avSetName \
		        --nic-names $nic1Name,$nic2Name \
		        --os-type linux \
		        --image-urn $publisher:$offer:$sku:$version \
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --os-disk-vhd $osDiskName$suffixNumber.vhd \
		        --admin-username $username \
		        --admin-password $password

5. 각 VM에 대해 두 개의 데이터 디스크를 만들고 `done` 명령을 사용하여 루프를 종료합니다.

		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-1.vhd \
		        --size-in-gb $diskSize \
		        --lun 0
		
		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-2.vhd \
		        --size-in-gb $diskSize \
		        --lun 1
		done


### 4단계 - 스크립트 실행

스크립트를 다운로드하여 요구에 맞게 변경했으므로, 이제 이 스크립트를 실행하여 여러 NIC를 사용하여 백 엔드 데이터베이스 VM을 만듭니다.

1. 스크립트를 저장하고 **Bash** 터미널에서 실행합니다. 아래와 같이 초기 출력에 표시됩니다.

		info:    Executing command group create
		info:    Getting resource group IaaSStory-Backend
		info:    Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command availset create
		info:    Looking up the availability set "ASDB"
		info:    Creating availability set "ASDB"
		info:    availset create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-DA"
		info:    Creating network interface "NICDB1-DA"
		info:    Looking up the network interface "NICDB1-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-DA
		data:    Name                            : NICDB1-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-RA"
		info:    Creating network interface "NICDB1-RA"
		info:    Looking up the network interface "NICDB1-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-RA
		data:    Name                            : NICDB1-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.54
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB1"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB1-DA"
		info:    Looking up the NIC "NICDB1-RA"
		info:    Creating VM "DB1"

2. 몇 분 후에 실행이 종료되고 아래와 같이 출력의 나머지 부분이 표시됩니다.

		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-1.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-2.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-DA"
		info:    Creating network interface "NICDB2-DA"
		info:    Looking up the network interface "NICDB2-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-DA
		data:    Name                            : NICDB2-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.5
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-RA"
		info:    Creating network interface "NICDB2-RA"
		info:    Looking up the network interface "NICDB2-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-RA
		data:    Name                            : NICDB2-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.55
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB2"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB2-DA"
		info:    Looking up the NIC "NICDB2-RA"
		info:    Creating VM "DB2"
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-1.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-2.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0211_2016-->