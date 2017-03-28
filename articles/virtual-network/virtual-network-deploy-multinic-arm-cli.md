---
title: "다중 NIC가 있는 VM 만들기 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 다중 NIC가 있는 VM을 만드는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 4f5eaf5f6ba56709b69d97c1f646f71396fd031b
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 다중 NIC이 있는 VM 만들기

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 [클래식 배포 모델](virtual-network-deploy-multinic-classic-cli.md) 대신 이 모델을 사용하도록 권장합니다.
>

## <a name="create"></a>VM 만들기

Azure CLI 2.0(이 문서) 또는 [Azure CLI 1.0](virtual-network-deploy-multinic-cli-nodejs.md)을 사용하여 이 태스크를 완료할 수 있습니다. 다음 단계에서 변수에 대한 ""의 값은 시나리오의 설정을 사용하여 리소스를 만듭니다. 사용자 환경에 적절한 값으로 변경합니다.

1. [Azure CLI 2.0](/cli/azure/install-az-cli2)을 아직 설치하지 않은 경우 설치합니다.
2. [Linux VM에 SSH 공용 및 개인 키 쌍 만들기](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 단계를 완료하여 Linux VM에 SSH 공용 및 개인 키 쌍을 만듭니다.
3. 명령 셸에서 `az login` 명령을 사용하여 로그인합니다.
4. Linux 또는 Mac 컴퓨터에서 다음에 나오는 스크립트를 실행하여 VM을 만듭니다. 스크립트는 리소스 그룹, 두 개의 서브넷이 있는 하나의 VNet(가상 네트워크), 두 개의 NIC 및 연결된 두 개의 NIC가 있는 VM을 만듭니다. NIC 중 하나는 하나의 서브넷에 연결되고 고정 공용 IP 주소 및 개인 IP 주소가 할당됩니다. 다른 NIC는 다른 서브넷에 연결되고 고정 개인 IP 주소가 할당되지만 공용 IP 주소는 할당되지 않습니다. NIC, 공용 IP 주소, 가상 네트워크 및 VM 리소스는 모두 동일한 위치 및 구독에 있어야 합니다. 리소스가 모두 동일한 리소스 그룹에 위치할 필요는 없습니다. 하지만 다음 스크립트에서는 모두 동일한 리소스 그룹에 위치합니다.

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

스크립트는 두 개의 NIC를 가진 VM 외에도 다음 항목을 만듭니다.
- 기본적으로 단일 프리미엄이 디스크를 관리했지만 만들 수 있는 디스크 유형에 대한 다른 옵션을 사용할 수 있습니다. 자세한 내용은 [Azure CLI 2.0을 사용하여 Linux VM 만들기](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- 두 개의 서브넷 및 하나의 공용 IP 주소를 가진 가상 네트워크입니다. 또는 *기존* 가상 네트워크, 서브넷, NIC 또는 공용 IP 주소 리소스를 사용할 수 있습니다. 추가 리소스를 만드는 것이 아니라 기존 네트워크 리소스를 사용하는 방법을 알아보려면 `az vm create -h`을 입력합니다.

## <a name = "validate"></a>VM 생성 및 NIC 유효성 검사

1. `az resource list --resouce-group Multi-NIC-VM --output table` 명령을 입력하여 스크립트로 만든 리소스의 목록을 볼 수 있습니다. 반환된 출력에 두 개의 NIC, 하나의 디스크, 하나의 공용 IP 주소, 하나의 가상 네트워크 및 가상 컴퓨터와 같은 6개의 리소스가 있어야 합니다.
2. `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table` 명령을 입력합니다. 반환된 출력에서 **IpAddress** 값을 확인하고 **PublicIpAllocationMethod** 값이 *고정*인지 확인합니다.
3. 다음 명령을 실행하기 전에 <>를 제거하고 *사용자 이름*을 스크립트의 **사용자 이름** 변수에 사용된 이름으로 바꾸고 *ipAddress*를 이전 단계의 **ipAddress**로 바꿉니다. `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>` 명령을 실행하여 VM에 연결합니다. 
4. VM에 연결되면 `sudo ifconfig` 명령을 실행하여 *eth0* 및 *eth1* 인터페이스를 확인합니다. 각 NIC에는 Azure DHCP 서버에서 스크립트에 지정된 고정 개인 IP 주소가 할당되었습니다. NIC에 할당된 IP 및 MAC 주소는 VM이 삭제될 때까지 변경되지 않습니다. 컴퓨터에 대한 연결이 끊어질 수 있기 때문에 운영 체제 내에서 IP 주소 지정을 변경하지 않는 것이 좋습니다. Azure 인프라에서 개인 IP 주소 간에 변환되는 네트워크 주소인 공용 IP 주소는 운영 체제 내에서 표시되지 않습니다.

## <a name= "clean-up"></a>VM 및 관련된 리소스 제거

프로덕션에서 VM을 사용하지 않는 경우 이 연습에서 만든 리소스를 삭제하는 것이 좋습니다. VM, 공용 IP 주소 및 디스크 리소스를 프로비전하는 경우 요금이 발생합니다. 이 연습에서 만든 리소스를 제거하려면 다음 단계를 완료합니다.
1. 리소스 그룹의 리소스를 보려면 `az resource list --resource-group Multi-NIC-VM` 명령을 실행합니다.
2. 리소스 그룹에 이 문서의 스크립트에서 만든 리소스 외에 다른 리소스가 없는지 확인합니다. 
3. 이 연습에서 만든 모든 리소스를 삭제하려면 `az group delete --name Multi-NIC-VM` 명령을 실행합니다. 이 명령은 리소스 그룹과 그 속에 포함된 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 만든 VM 간에 네트워크 트래픽을 전달할 수 있습니다. 네트워크 인터페이스, 서브넷 각각 또는 둘 간에 전달할 수 있는 트래픽을 제한하는 NSG 내에서 인바운드 및 아웃바운드 규칙을 정의할 수 있습니다. NSG에 대해 자세히 알아보려면 [NSG 개요](virtual-networks-nsg.md) 문서를 읽어보세요.
