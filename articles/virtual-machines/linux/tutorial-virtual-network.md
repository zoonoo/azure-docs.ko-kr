---
title: "Azure VIrtual Network 및 Linux Virtual Machines | Microsoft Docs"
description: "자습서 - Azure CLI를 사용하여 Azure Virtual Network 및 Linux Virtual Machines 관리"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 1ac628b606a198bb437c02d00467d48462c34334
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure Virtual Network 및 Linux Virtual Machines 관리

이 자습서에서는 가상 네트워크에 여러 VM(가상 컴퓨터)을 만드는 방법을 알아보고 이러한 컴퓨터 간에 네트워크 연결을 구성합니다. 완료되면 SSH 연결의 경우 포트 22, HTTP 연결의 경우 포트 80을 통해 인터넷에서 '프런트 엔드' VM에 액세스할 수 있습니다. MySQL 데이터베이스가 있는 '백 엔드' VM은 격리되며, 포트 3306에서 프런트 엔드 VM을 통해서만 액세스할 수 있습니다.

이 자습서의 단계는 최신 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 사용하여 완료할 수 있습니다.

## <a name="create-vm-and-vnet"></a>VM 및 VNet 만들기

Azure VNet(Virtual Network)은 클라우드의 사용자 네트워크를 나타내는 표현입니다. 구독 전용 Azure 클라우드를 논리적으로 격리한 것이 VNet입니다. VNet 내에서 서브넷, 해당 서브넷에 대한 연결 규칙 및 VM에서 서브넷으로의 연결을 찾습니다. Azure CLI를 사용하면 VM에 대한 액세스를 지원하는 데 필요한 모든 네트워크 관련 리소스를 쉽게 만들 수 있습니다. 

다른 Azure 리소스를 만들려면 먼저 az group create를 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *westus* 위치에 *myRGNetwork*라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myRGNetwork --location westus
```

Azure CLI를 사용하여 가상 컴퓨터를 만들 때 필요한 네트워크 리소스가 동시에 자동으로 만들어집니다. [az vm create](https://docs.microsoft.com/cli/azure/vm#create) 명령을 사용하여 *myFrontendVM* 및 지원 네트워크 리소스를 만듭니다.

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM을 만든 후에 공용 IP 주소를 기록해 둡니다. 이 주소는 이 자습서의 이후 단계에서 사용됩니다.

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

다음과 같은 네트워크 리소스를 만들었습니다.

- **myFrontendVMNSG** – *myFrontendVM*으로 들어오는 트래픽의 보안을 설정하는 네트워크 보안 그룹입니다.
- **myVMPublicIP** – *myFrontendVM*에 대한 인터넷 액세스를 가능하게 하는 공용 IP 주소입니다.
- **myVMVMNic** – *myFrontendVM*에 대한 네트워크 연결을 제공하는 가상 네트워크 인터페이스입니다.
- **myVMVNET** – *myFrontendVM*이 연결되는 가상 네트워크입니다.

## <a name="install-web-server"></a>웹 서버 설치

*myFrontendVM*과의 SSH 연결을 만듭니다. 예제 IP 주소를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh 40.68.254.142
```

다음 명령을 실행하여 NGINX를 설치합니다.

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

SSH 세션을 종료합니다.

```bash
exit
```

## <a name="manage-internet-traffic"></a>인터넷 트래픽 관리

NSG(네트워크 보안 그룹)에는 VNet에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷 또는 VM에 연결된 개별 NIC에 연결될 수 있습니다. 포트를 통한 VM에 대한 액세스 열기 또는 닫기는 NSG 규칙을 사용하여 수행됩니다. *myFrontendVM*을 만들 때 SSH 연결에 대해 인바운드 포트 22가 자동으로 열렸습니다.

[az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) 명령을 사용하여 *myFrontendVM*에서 포트 80을 엽니다.

```azurecli
az vm open-port --resource-group myRGNetwork --name myFrontendVM --port 80
```

이제 VM의 공용 IP 주소로 이동하여 NGINX 사이트를 볼 수 있습니다.

![NGINX 기본 사이트](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>인터넷 트래픽 관리

VM의 내부 통신 또한 NSG를 사용하여 구성할 수 있습니다. 이 섹션에서는 네트워크에서 추가 서브넷을 만들고 NSG를 서브넷에 할당하여 포트 3306의 *myFrontendVM*에서 *myBackendVM*으로 연결할 수 있도록 하는 방법을 알아봅니다. 그러면 서브넷이 만들어질 때 VM에 할당됩니다.

[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) 명령을 사용하여 *myBackendNSG*라는 네트워크 보안 그룹을 추가합니다. 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

*myFrontendVM* 및 *myBackendVM*이 VNet에서 서로 통신할 수 있도록 포트를 설정합니다. [az network rule create](/cli/azure/network/rule#create) 명령을 사용하여 *myVMSubnet*에서 *myBackendSubnet*으로 오는 트래픽만 허용하는 NSG 규칙을 추가합니다.

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>백 엔드 서브넷 추가

서브넷은 VNet의 자식 리소스이며, IP 주소 접두사를 사용하여 CIDR 블록 내의 주소 공간 세그먼트를 정의하는 데 도움이 됩니다. NIC는 서브넷에 추가하고 VM에 연결하여 다양한 워크로드에 대한 연결을 제공할 수 있습니다.

[az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) 명령을 사용하여 *myBackEndSubnet*을 *myFrontendVMVNet*에 추가합니다.

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>백 엔드 VM 만들기

[az vm create](/cli/azure/vm#create) 명령을 통해 *myBackendSubnet*을 사용하여 *myBackendVM*을 만듭니다.

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>데이터베이스 설치

이 자습서에서는 개발 VM의 개인 키를 *myFrontendVM*으로 복사합니다. 프로덕션 환경에서는 VM을 만들 때 --generate-ssh-key를 사용하지 말고 VM에 사용할 특정 키를 만드는 것이 좋습니다. 

백 엔드 VM은 공개적으로 액세스하기 위한 것이 아닙니다. 이 섹션에서는 SSH를 사용하여 *myFrontendVM*에 로그인한 다음 SSH를 사용하여 *myFrontendVM*에서 *myBackendVM*에 로그인하는 방법을 알아봅니다.

예제의 IP 주소를 *myFrontendVM*의 공용 IP 주소로 바꿉니다.

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

*myFrontendVM*과의 SSH 연결을 만듭니다. 예제의 IP 주소를 *myFrontendVM*의 공용 IP 주소로 바꿉니다.

```bash
ssh 40.68.254.142
```

*myFrontendVM*에서 *myBackendVM*에 연결합니다.

```bash
ssh myBackendVM
```

다음 명령을 실행하여 MySQL을 설치합니다.

```bash
sudo apt-get -y install mysql-server
```

MySQL 설정을 위한 지침을 따릅니다.

SSH 세션을 종료합니다.

```bash
exit
```

응용 프로그램을 *myBackendVM*에 설치하는 방법을 보여 주기 위해 MySQL이 설치되지만 이 자습서에서 사용되지는 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 컴퓨터에 관련된 Azure 네트워크를 만들고 보호하는 방법을 배웠습니다. 다음 자습서로 이동하여 Azure Security Center를 통해 VM 보안을 모니터링하는 방법을 알아보세요.

[가상 컴퓨터 보안 관리](./tutorial-azure-security.md)
