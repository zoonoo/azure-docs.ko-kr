---
title: "자습서 - Azure VM에서 고가용성 응용 프로그램 빌드 | Microsoft Docs"
description: "Azure에서 부하 분산 장치를 사용하여 3개의 Linux VM에서 고가용성 및 보안 응용 프로그램을 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f24cca8230e30ca3862d7e7011bcea649c6aec52
ms.lasthandoff: 03/29/2017

---

# <a name="build-a-highly-available-application-on-linux-virtual-machines-in-azure"></a>Azure의 Linux 가상 컴퓨터에서 고가용성 응용 프로그램 빌드
이 자습서에서는 유지 관리 이벤트에 대해 복원력이 뛰어난 고가용성 응용 프로그램을 만듭니다. 앱은 부하 분산 장치, 가용성 집합 및 3개의 Linux VM(가상 컴퓨터)을 사용합니다. 이 자습서를 사용하여 동일한 고가용성 구성 요소 및 지침에 따라 다른 응용 프로그램 프레임워크를 배포할 수 있지만 이 자습서에서는 Node.js 앱을 빌드합니다.

## <a name="step-1---azure-prerequisites"></a>1단계 - Azure 필수 구성 요소
이 자습서를 완료하려면 터미널 창을 열고 최신 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치했는지 확인합니다. Azure 구독에 아직 로그인하지 않은 경우 [az login](/cli/azure/#login)으로 로그인한 후 화면의 지시를 따릅니다.

```azurecli
az login
```

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다른 Azure 리소스를 만들려면 먼저 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 `westus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>2단계 - 가용성 집합 만들기
논리적 장애 및 업데이트 도메인 간에 가상 컴퓨터를 만들 수 있습니다. 각 논리 도메인은 기본 Azure 데이터 센터의 하드웨어 부분을 나타냅니다. 둘 이상의 VM을 만들 경우 Compute 및 저장소 리소스가 이러한 도메인 간에 분산됩니다. 이러한 분산은 하드웨어 구성 요소가 유지 관리를 진행할 경우 앱의 가용성을 유지해 줍니다. 가용성 집합을 통해 이러한 논리적 장애 및 업데이트 도메인을 정의할 수 있습니다.

[az vm availability-set create](/cli/azure/vm/availability-set#create)를 사용하여 가용성 집합을 만듭니다. 다음 예제는 `myAvailabilitySet`라는 가용성 집합을 만듭니다.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>3단계 - 부하 분산 장치 만들기
Azure Load Balancer는 부하 분산 장치 규칙을 사용하여 정의된 VM 집합 간에 트래픽을 분산시킵니다. 상태 프로브가 각 VM에서 지정된 포트를 모니터링하고 작동하는 VM으로만 트래픽을 분산합니다.

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
인터넷에서 앱에 액세스하려면 공용 IP 주소를 부하 분산 장치에 할당합니다. [az network public-ip create](/cli/azure/public-ip#create)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 `myPublicIP`라는 공용 IP 주소를 만듭니다.

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>부하 분산 장치 만들기
[az network lb create](/cli/azure/network/lb#create)를 사용하여 부하 분산 장치를 만듭니다. 다음 예제는 `myPublicIP` 주소를 사용하여 `myLoadBalancer`라는 부하 분산 장치를 만듭니다.

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>상태 프로브 만들기
부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 기본적으로 VM은 15초 간격으로 두 번의 연속 실패 후에 부하 분산 장치 분산에서 제거됩니다. 앱의 프로토콜 또는 특정 상태 확인 페이지에 따라 상태 프로브를 만듭니다. 예를 들어 이 예제를 확장하여 `--path healthcheck.js`를 추가할 수 있지만 다음 예제에서는 TCP 프로브를 만듭니다. 부하 분산 상태가 호스트를 순환 상태를 유지하려면 `healthcheck.js`가 생성된 후 **HTTP 200 정상** 응답을 반환해야 합니다.

[az network lb probe create](/cli/azure/network/lb/probe#create)를 사용하여 상태 프로브를 만듭니다. 다음 예제에서는 각 VM을 모니터링하는 `myHealthProbe`라는 상태 프로브를 만듭니다.

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기
부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다.

[az network lb rule create](/cli/azure/network/lb/rule#create)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 `myLoadBalancerRule`이라는 규칙을 만들고, `myHealthProbe` 상태 프로브를 사용하고, 포트 `80`에서 트래픽 부하를 분산합니다.

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>4단계 - 네트워킹 구성
각 VM에는 가상 네트워크에 연결되는 하나 이상의 가상 NIC(네트워크 인터페이스 카드)가 있습니다. 이 가상 네트워크는 정의된 액세스 규칙에 따라 트래픽을 필터링함으로써 보안이 유지됩니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
VM에 네트워크 연결을 제공하려면 [az network vnet create](/cli/azure/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 `mySubnet`이라는 서브넷을 사용하여 `myVnet`이라는 가상 네트워크를 만듭니다.

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>네트워크 보안 구성
네트워크 보안 그룹은 VM으로/에서의 트래픽 흐름을 제어하기 위해 규칙을 사용합니다. 포트 80에서 트래픽을 허용하도록 하는 것과 같이 이러한 규칙을 정의합니다. 부하 분산 장치는 VM 간에 트래픽 부하를 분산하지만 네트워크 보안 그룹 규칙은 허용되는 트래픽만 허용되도록 합니다.

[az network nsg create](/cli/azure/network/nsg#create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

웹 트래픽이 앱에 도달하게 하려면 [az network nsg rule create](/cli/azure/network/nsg/rule#create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroupRule`이라는 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>가상 네트워크 인터페이스 카드 만들기 
부하 분산 장치는 실제 VM보다는 가상 NIC 리소스에 작동합니다. 가상 NIC는 부하 분산 장치에 연결된 후 VM에 연결됩니다.

[az network nic create](/cli/azure/network/nic#create)를 사용하여 가상 NIC를 만듭니다. 다음 예제에서는 3개의 가상 NIC를 만듭니다. (다음 단계에서는 앱에 대한 가상 NIC를 각 VM에 대해 하나씩 만듭니다.)

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>5단계 - 앱 빌드
**cloud-init**는 VM 사용자 지정을 위해 널리 사용되는 방법입니다. **cloud-init**를 사용하여 패키지를 설치하고 파일을 쓸 수 있습니다. 초기 배포 동안 **cloud_init**가 실행될 경우 앱을 실행하기 위한 추가 단계가 필요하지 않습니다. 부하 분산 장치는 VM이 배포를 완료하고 앱을 실행하게 되면 트래픽을 분산하기 시작합니다. **cloud-init** 사용 방법에 대한 자세한 내용은 [cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

다음 **cloud-init** 구성은 **nodejs** 및 **npm**을 설치한 다음 **nginx**를 앱의 웹 프록시로 설치 및 구성합니다. 또한 이 구성에서는 간단한 'Hello World' Node.js 앱을 만든 다음 **Express**를 사용하여 앱을 초기화하고 시작합니다. 다른 응용 프로그램 프레임워크를 사용하려는 경우 패키지 및 배포된 응용 프로그램을 그에 따라 조정합니다.

`cloud-init.txt`라는 파일을 만들고 다음 구성을 붙여 넣습니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-6---create-virtual-machines"></a>6단계 - 가상 컴퓨터 만들기
모든 기본 구성 요소가 구축되면 이제 앱을 실행하기 위한 고가용성 VM을 만들 수 있습니다.

### <a name="create-vms"></a>VM 만들기
[az vm create](/cli/azure/vm#create)로 VM을 만듭니다. 다음 예제에서는 3개의 VM을 만들고 SSH 키가 아직 없으면 생성합니다.

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

3개의 VM을 만들고 구성하는 데 몇 분 정도 걸립니다. 부하 분산 장치 상태 프로브는 각 VM에서 앱을 실행될 경우를 자동으로 검색합니다. 앱이 실행되면 부하 분산 장치 규칙은 트래픽을 분산하기 시작합니다.

### <a name="test-your-app"></a>앱 테스트
[az network public-ip show](/cli/azure/network/public-ip#show)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 `myPublicIP`의 IP 주소를 가져옵니다.

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

웹 브라우저에 공용 IP 주소를 입력합니다. 앱이 표시되고 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름이 표시됩니다.

![Node.js 앱 실행](./media/virtual-machines-linux-tutorial-load-balance-nodejs/running-nodejs-app.png)

웹 브라우저를 강제로 새로 고쳐 앱이 실행되는 3개의 모든 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인합니다.


## <a name="step-7---management-tasks"></a>7단계 - 관리 작업
앱이 실행되는 동안 OS 업데이트와 같은 유지 관리 작업을 VM에서 수행해야 할 수 있습니다. 앱에 대해 증가된 트래픽을 처리하기 위해 VM을 더 추가해야 할 수 있습니다. 이 섹션에서는 부하 분산 장치에서 VM을 추가 또는 제거하는 방법을 보여 줍니다.

### <a name="remove-a-vm-from-the-load-balancer"></a>부하 분산 장치에서 VM 제거
[az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove)를 사용하여 백 엔드 주소 풀에서 VM을 제거합니다. 다음 예제에서는 `myLoadBalancer`에서 **myVM2**용 가상 NIC를 제거합니다.

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

웹 브라우저를 강제로 새로 고쳐 앱이 실행되는 나머지 2개의 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인합니다. 이제 OS 업데이트 설치 또는 VM 다시 부팅을 수행 등의 유지 관리 작업을 VM에서 수행할 수 있습니다.

### <a name="add-a-vm-to-the-load-balancer"></a>부하 분산 장치에 VM 추가
VM 유지 관리를 수행한 이후 또는 용량을 확장해야 할 경우 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add)를 사용하여 백 엔드 주소 풀에 VM을 추가합니다. 다음 예제에서는 `myLoadBalancer`에 **myVM2**용 가상 NIC를 추가합니다.

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>다음 단계
이 자습서에 도입된 고가용성 기능 중 일부에 대한 자세한 내용은 다음 정보를 참조하세요.

- [Linux 가상 컴퓨터의 가용성 관리](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure Load Balancer개요](../load-balancer/load-balancer-overview.md)
- [네트워크 보안 그룹을 사용하여 네트워크 트래픽 흐름 제어](../virtual-network/virtual-networks-nsg.md)
- [Azure CLI 샘플 스크립트](virtual-machines-linux-cli-samples.md)
