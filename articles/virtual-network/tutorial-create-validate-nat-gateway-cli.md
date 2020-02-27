---
title: '자습서: NAT 게이트웨이 만들기 및 테스트 - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: 이 자습서에서는 Azure CLI를 사용하여 NAT 게이트웨이를 만들고 NAT 서비스를 테스트하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 26a427baddc99fa702b638c36b5378750364c849
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587025"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>자습서: Azure CLI를 사용하여 NAT 게이트웨이 만들기 및 NAT 서비스 테스트

이 자습서에서는 Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. NAT 게이트웨이를 테스트하려면 원본 및 대상 가상 머신을 배포해야 합니다. 공용 IP 주소에 대한 아웃바운드 연결을 설정하여 NAT 게이트웨이를 테스트합니다. 이러한 연결은 원본 가상 머신에서 대상으로 제공됩니다. 이 자습서에서는 간소화하기 위해 원본 및 대상을 동일한 리소스 그룹의 서로 다른 두 가상 네트워크에 배포합니다.

>[!NOTE] 
>Azure Virtual Network NAT는 현재 공개 미리 보기로 제공되며, 제한된 [지역](./nat-overview.md#region-availability) 세트에서 사용할 수 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms)을 참조하세요.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell을 사용하여 이 자습서를 완료하거나 각 명령을 로컬로 실행할 수 있습니다.  Azure Cloud Shell을 사용하지 않은 경우 [지금 로그인](https://shell.azure.com)해야 합니다.

이러한 명령을 로컬로 실행하도록 선택하는 경우 CLI를 설치해야 합니다.  이 자습서를 수행하려면 Azure CLI 버전 2.0.71 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 **myResourceGroupNAT**라는 리소스 그룹을 **eastus2** 위치에 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT Gateway 만들기

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

공용 인터넷에 액세스 하려면 NAT 게이트웨이에 대 한 공용 IP 주소가 하나 이상 필요 합니다. [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)를 사용하여 **myPublicIPsource**라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>공용 IP 접두사 만들기

하나 이상의 공용 IP 주소 리소스, 공용 IP 접두사 또는 둘 모두는 NAT 게이트웨이에서 사용할 수 있습니다. 공용 IP 접두사 리소스를 이 시나리오에 추가하여 보여 줍니다.   [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix)를 사용하여 **myPublicIPprefixsource**라는 공용 IP 접두사를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT 게이트웨이 리소스 만들기

이 섹션에서는 NAT 게이트웨이 리소스를 사용하여 NAT 서비스의 다음 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - NAT 게이트웨이 리소스에서 변환하는 아웃바운드 흐름에 사용할 공용 IP 풀 및 공용 IP 접두사입니다.
  - 유휴 시간 제한을 4분(기본값)에서 10분으로 변경합니다.

[az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest)를 사용하여 **myNATgateway**라는 글로벌 Azure NAT 게이트웨이를 만듭니다. 이 명령은 **myPublicIP** 공용 IP 주소와 **myPublicIPprefix**공용 IP 접두사를 모두 사용합니다. 또한 유휴 시간 제한을 10분으로 변경합니다.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

이 시점에서 NAT 게이트웨이가 작동하며, 이제 가상 네트워크의 서브넷을 사용해야 하는 서브넷만 구성하면 됩니다.

## <a name="prepare-the-source-for-outbound-traffic"></a>아웃바운드 트래픽에 대한 원본 준비

전체 테스트 환경을 설정하는 과정을 안내합니다. 오픈 소스 도구를 사용하여 NAT 게이트웨이를 확인하는 테스트를 설정합니다. 먼저 이전에 만든 NAT 게이트웨이 리소스를 사용하는 원본으로 시작합니다.

### <a name="configure-virtual-network-for-source"></a>원본에 대한 가상 네트워크 구성

VM을 배포하기 전에 NAT 게이트웨이를 테스트하려면 먼저 가상 네트워크를 만들어야 합니다.

[az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet)를 사용하여 **myResourceGroupNAT**의 서브넷 이름이 **mySubnetsource**인 **myVnetsource**라는 가상 네트워크를 만듭니다.  가상 네트워크의 IP 주소 공간은 **192.168.0.0/16**입니다. 가상 네트워크 내의 서브넷은 **192.168.0.0/24**입니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>원본 서브넷에 대한 NAT 서비스 구성

[az network Microsoft Azure Virtual Network subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)를 사용하여 특정 **myNATgateway** NAT 게이트웨이 리소스를 사용하도록 **myVnetsource** 가상 네트워크의 **mySubnetsource** 원본 서브넷을 구성합니다. 이 명령은 지정된 서브넷에서 NAT 서비스를 활성화합니다.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

이제 인터넷 대상으로의 모든 아웃바운드 트래픽에서 NAT 서비스를 사용합니다.  UDR을 구성할 필요가 없습니다.

NAT 게이트웨이를 테스트하려면 먼저 원본 VM을 만들어야 합니다.  외부에서 이 VM에 액세스할 수 있도록 공용 IP 주소 리소스를 인스턴스 수준 공용 IP로 할당합니다. 이 주소는 테스트를 위해 액세스하는 데만 사용됩니다.  NAT 서비스를 다른 아웃바운드 옵션보다 우선적으로 적용하는 방법을 설명합니다.

또한 공용 IP 없이 이 VM을 만들고, 연습용으로 공용 IP가 없는 점프 박스로 사용할 다른 VM을 만들 수도 있습니다.

### <a name="create-public-ip-for-source-vm"></a>원본 VM에 대한 공용 IP 만들기

원본 VM에 액세스하는 데 사용할 공용 IP를 만듭니다. [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)를 사용하여 **myPublicIPsourceVM**이라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>원본 VM에 대한 NSG 만들기

표준 공용 IP 주소는 '기본적으로 보안'되므로 ssh 액세스에 대한 인바운드 액세스를 허용하는 NSG를 만들어야 합니다.  Azure NAT 서비스는 흐름 방향을 인식합니다. NAT 게이트웨이가 동일한 서브넷에 구성되면 이 NSG는 아웃바운드에 사용되지 않습니다. [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)를 사용하여 **myNSGsource**라는 NSG 리소스를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>원본 VM에서 SSH 엔드포인트 공개

NSG에서 원본 VM에 대한 SSH 액세스 규칙을 만듭니다. [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)를 사용하여 **ssh**라는 NSG 규칙을 만듭니다. 이 규칙은 **myResourceGroupNAT** 리소스 그룹의 **myNSGsource**라는 NSG에 만들어집니다.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>원본 VM에 대한 NIC 만들기

[az network nic create](/cli/azure/network/nic#az-network-nic-create)를 사용하여 네트워크 인터페이스를 만들고 공용 IP 주소 및 네트워크 보안 그룹에 연결합니다. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>원본 VM 만들기

[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 가상 머신을 만듭니다.  이 VM에 대한 ssh 키를 생성하고 나중에 사용할 프라이빗 키를 저장합니다.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

명령이 즉시 반환되지만 VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="prepare-destination-for-outbound-traffic"></a>아웃바운드 트래픽에 대한 대상 준비

이제 NAT 서비스에서 변환하는 아웃바운드 트래픽의 대상을 만들어 테스트할 수 있습니다.

### <a name="configure-virtual-network-for-destination"></a>대상에 대한 가상 네트워크 구성

 대상 가상 머신이 있는 가상 네트워크를 만들어야 합니다.  이러한 명령은 대상 엔드포인트를 공개하기 위해 약간 변경되는 원본 VM에 적용되는 단계와 동일합니다.

[az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet)를 사용하여 **myResourceGroupNAT**의 서브넷 이름이 **mySubnetdestination**인 **myVnetdestination**이라는 가상 네트워크를 만듭니다.  가상 네트워크의 IP 주소 공간은 **192.168.0.0/16**입니다. 가상 네트워크 내의 서브넷은 **192.168.0.0/24**입니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>대상 VM에 대한 공용 IP 만들기

원본 VM에 액세스하는 데 사용할 공용 IP를 만듭니다. [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)를 사용하여 **myPublicIPdestinationVM**이라는 공용 IP 주소 리소스를 **myResourceGroupNAT**에 만듭니다. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>대상 VM에 대한 NSG 만들기

표준 공용 IP 주소는 '기본적으로 보안'되므로 ssh에 대한 인바운드 액세스를 허용하는 NSG를 만들어야 합니다. Azure NAT 서비스는 흐름 방향을 인식합니다. NAT 게이트웨이가 동일한 서브넷에 구성되면 이 NSG는 아웃바운드에 사용되지 않습니다. [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)를 사용하여 **myNSGdestination**이라는 NSG 리소스를 **myResourceGroupNAT**에 만듭니다.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>대상 VM에서 SSH 엔드포인트 공개

NSG에서 대상 VM에 대한 SSH 액세스 규칙을 만듭니다. [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)를 사용하여 **ssh**라는 NSG 규칙을 만듭니다. 이 규칙은 **myResourceGroupNAT** 리소스 그룹의 **myNSGdestination**이라는 NSG에 만들어집니다.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>대상 VM에서 HTTP 엔드포인트 공개

NSG에서 대상 VM에 대한 HTTP 액세스 규칙을 만듭니다. [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)를 사용하여 **http**라는 NSG 규칙을 **myResourceGroupNAT**의 **myNSGdestination**이라는 NSG에 만듭니다.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>대상 VM에 대한 NIC 만들기

[az network nic create](/cli/azure/network/nic#az-network-nic-create)를 사용하여 네트워크 인터페이스를 만들고 **myPublicIPdestinationVM** 공용 IP 주소 및 **myNSGdestination** 네트워크 보안 그룹에 연결합니다. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>대상 VM 만들기

[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 가상 머신을 만듭니다.  이 VM에 대한 ssh 키를 생성하고 나중에 사용할 프라이빗 키를 저장합니다.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
명령이 즉시 반환되지만 VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>대상 VM에서 웹 서버 및 테스트 페이로드 준비

먼저 대상 VM의 IP 주소를 검색해야 합니다.  대상 VM의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 명령을 사용합니다. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이후 단계에서 사용할 수 있도록 메모장에 붙여넣습니다. 대상 가상 머신임을 나타냅니다.

### <a name="sign-in-to-destination-vm"></a>대상 VM에 로그인

SSH 자격 증명은 이전 작업의 Cloud Shell에 저장해야 합니다.  브라우저에서 [Azure Cloud Shell](https://shell.azure.com)을 엽니다. 이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다. 

```bash
ssh <ip-address-destination>
```

로그인하면 다음 명령을 복사하여 붙여넣습니다.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

이러한 명령은 가상 머신을 업데이트하고, nginx를 설치하며, 100KB 파일을 만듭니다. 이 파일은 NAT 서비스를 사용하여 원본 VM에서 검색됩니다.

대상 VM을 사용하여 SSH 세션을 닫습니다.

## <a name="prepare-test-on-source-vm"></a>원본 VM에서 테스트 준비

먼저 원본 VM의 IP 주소를 검색해야 합니다.  원본 VM의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 명령을 사용합니다. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이후 단계에서 사용할 수 있도록 메모장에 붙여넣습니다. 원본 가상 머신임을 나타냅니다.

### <a name="sign-in-to-source-vm"></a>원본 VM에 로그인

다시 한 번 SSH 인증 정보가 Cloud Shell에 저장됩니다. 브라우저에서 [Azure Cloud Shell](https://shell.azure.com)에 대한 새 탭을 엽니다.  이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다. 

```bash
ssh <ip-address-source>
```

다음 명령을 복사하고 붙여넣어 NAT 서비스 테스트를 준비합니다.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

이 명령은 가상 머신을 업데이트하고, go를 설치하고, GitHub에서 [hey](https://github.com/rakyll/hey)를 설치하고, 셸 환경을 업데이트합니다.

이제 NAT 서비스를 테스트할 준비가 되었습니다.

## <a name="validate-nat-service"></a>NAT 서비스 유효성 검사

원본 VM에 로그인한 상태에서 **curl** 및 **hey**를 사용하여 대상 IP 주소에 대한 요청을 생성할 수 있습니다.

curl을 사용하여 100KB 파일을 검색합니다.  아래 예제의 **\<ip-address-destination>** 을 이전에 복사한 대상 IP 주소로 바꿉니다.  **--output** 매개 변수는 검색된 파일이 삭제됨을 나타냅니다.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

또한 **hey**를 사용하여 일련의 요청을 생성할 수도 있습니다. 다시 한 번 **\<ip-address-destination>** 을 이전에 복사한 대상 IP 주소로 바꿉니다.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

이 명령은 30초의 시간 제한에서 동시에 100개의 요청을 10회 생성합니다. TCP 연결은 다시 사용되지 않습니다.  각 요청에서 100KB를 검색합니다.  실행이 완료되면 **hey**에서 수행된 NAT 서비스의 작동 상태에 대한 몇 가지 통계를 보고합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 NAT 게이트웨이를 만들고, 원본 및 대상 VM을 만든 다음, NAT 게이트웨이를 테스트했습니다.

Azure Monitor에서 메트릭을 검토하여 NAT 서비스의 작동 상태를 확인합니다. 사용 가능한 SNAT 포트의 리소스 소모와 같은 문제를 진단합니다.  이러한 SNAT 포트의 리소스 소모는 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 모두를 추가하여 쉽게 해결할 수 있습니다.

- [Virtual Network NAT](./nat-overview.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스](./nat-gateway-resource.md)에 대한 자세한 정보
- [Azure CLI를 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-cli.md)를 배포하기 위한 빠른 시작.
- [Azure PowerShell을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-powershell.md)를 배포하기 위한 빠른 시작.
- [Azure Portal을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-portal.md)를 배포하기 위한 빠른 시작.
- [공개 미리 보기에 대한 피드백을 제공합니다](https://aka.ms/natfeedback).

> [!div class="nextstepaction"]

