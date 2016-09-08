<properties 
   pageTitle="리소스 관리자에서 Azure CLI를 사용하여 고정 공용 IP를 사용하는 VM 배포 | Microsoft Azure"
   description="리소스 관리자에서 Azure CLI를 사용하여 고정 공용 IP를 사용하는 VM을 배포하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# Azure CLI를 사용하여 고정 공용 IP를 사용하는 VM 배포

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] 클래식 배포 모델.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 1단계 - 스크립트 시작

사용되는 전체 bash 스크립트를 [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-cli.sh)에서 다운로드할 수 있습니다. 다음 단계에 따라 스크립트를 사용자 환경에서 작동하도록 변경합니다.

1. 배포에 사용하려는 값을 기반으로 아래 변수 값을 변경합니다. 아래 값은 이 문서에 사용되는 시나리오에 매핑됩니다.

		# Set variables for the new resource group
		rgName="IaaSStory"
		location="westus"
		
		# Set variables for VNet
		vnetName="TestVNet"
		vnetPrefix="192.168.0.0/16"
		subnetName="FrontEnd"
		subnetPrefix="192.168.1.0/24"
		
		# Set variables for storage
		stdStorageAccountName="iaasstorystorage"
		
		# Set variables for VM
		vmSize="Standard_A1"
		diskSize=127
		publisher="Canonical"
		offer="UbuntuServer"
		sku="14.04.2-LTS"
		version="latest"
		vmName="WEB1"
		osDiskName="osdisk"
		nicName="NICWEB1"
		privateIPAddress="192.168.1.101"
		username='adminuser'
		password='adminP@ssw0rd'
		pipName="PIPWEB1"
		dnsName="iaasstoryws1"

## 2단계 - VM에 필요한 리소스 만들기

VM을 만들기 전에 VM에서 사용할 리소스 그룹, VNet, 공용 IP 및 NIC가 필요합니다.

1. 새 리소스 그룹을 만듭니다.

		azure group create $rgName $location
		
2. VNet 및 서브넷을 만듭니다.
		
		azure network vnet create --resource-group $rgName \
		    --name $vnetName \
		    --address-prefixes $vnetPrefix \
		    --location $location
		azure network vnet subnet create --resource-group $rgName \
		    --vnet-name $vnetName \
		    --name $subnetName \
		    --address-prefix $subnetPrefix

3. 공용 IP 리소스를 만듭니다.

		azure network public-ip create --resource-group $rgName \
		    --name $pipName \
		    --location $location \
		    --allocation-method Static \
		    --domain-name-label $dnsName 

4. 공용 IP를 사용하여 위에서 만든 서브넷의 VM에 대한 NIC(네트워크 인터페이스)를 만듭니다. 명령의 첫 번째 집합을 사용하여 위에서 만든 서브넷의 **ID**를 검색합니다.

		subnetId="$(azure network vnet subnet show --resource-group $rgName \
		                --vnet-name $vnetName \
		                --name $subnetName|grep Id)"

		subnetId=${subnetId#*/}
		
		azure network nic create --name $nicName \
		    --resource-group $rgName \
		    --location $location \
		    --private-ip-address $privateIPAddress \
		    --subnet-id $subnetId \
		    --public-ip-name $pipName

	>[AZURE.TIP] 위의 첫 번째 명령은 [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) 및 [문자열 조작](http://tldp.org/LDP/abs/html/string-manipulation.html)(구체적으로 말하면, 하위 문자열 제거)을 사용합니다.

5. VM OS 드라이브를 호스트하는 저장소 계정을 만듭니다.

		azure storage account create $stdStorageAccountName \
		    --resource-group $rgName \
		    --location $location --type LRS 

## 3단계 - VM 만들기 

이제 필요한 모든 리소스를 배치했으므로 새 VM을 만들 수 있습니다.

1. VM을 만듭니다.

		azure vm create --resource-group $rgName \
		    --name $vmName \
		    --location $location \
		    --vm-size $vmSize \
		    --subnet-id $subnetId \
		    --nic-names $nicName \
		    --os-type linux \
		    --image-urn $publisher:$offer:$sku:$version \
		    --storage-account-name $stdStorageAccountName \
		    --storage-account-container-name vhds \
		    --os-disk-vhd $osDiskName.vhd \
		    --admin-username $username \
		    --admin-password $password

2. 스크립트 파일을 저장합니다.

## 4단계 - 스크립트 실행

필요한 내용을 변경하고 위에 표시된 스크립트를 파악한 후 실행합니다.

1. Bash 콘솔에서 위의 스크립트를 실행합니다.

		sh myscript.sh

2. 몇 분 후에 아래 출력이 표시되어야 합니다.

		info:    Executing command group create
		info:    Getting resource group IaaSStory
		info:    Creating resource group IaaSStory
		info:    Created resource group IaaSStory
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory
		data:    Name:                IaaSStory
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK
		info:    Executing command network vnet create
		info:    Looking up virtual network "TestVNet"
		info:    Creating virtual network "TestVNet"
		info:    Loading virtual network state
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : westus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK
		info:    Executing command network vnet subnet create
		info:    Looking up the subnet "FrontEnd"
		info:    Creating subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK
		info:    Executing command network public-ip create
		info:    Looking up the public ip "PIPWEB1"
		info:    Creating public ip address "PIPWEB1"
		info:    Looking up the public ip "PIPWEB1"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:    Name                            : PIPWEB1
		data:    Type                            : Microsoft.Network/publicIPAddresses
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Allocation method               : Static
		data:    Idle timeout                    : 4
		data:    IP Address                      : 40.78.63.253
		data:    Domain name label               : iaasstoryws1
		data:    FQDN                            : iaasstoryws1.westus.cloudapp.azure.com
		info:    network public-ip create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICWEB1"
		info:    Looking up the public ip "PIPWEB1"
		info:    Creating network interface "NICWEB1"
		info:    Looking up the network interface "NICWEB1"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkInterfaces/NICWEB1
		data:    Name                            : NICWEB1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:      Private IP address            : 192.168.1.101
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory2/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up the VM "WEB1"
		info:    Using the VM Size "Standard_A1"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account iaasstorystorage
		info:    Looking up the NIC "NICWEB1"
		info:    Creating VM "WEB1"
		info:    vm create command OK

<!---HONumber=AcomDC_0824_2016-->