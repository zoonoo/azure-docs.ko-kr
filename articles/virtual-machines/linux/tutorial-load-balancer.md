---
title: "Azure의 Linux 가상 컴퓨터 부하 분산 방법 | Microsoft Docs"
description: "Azure Load Balancer를 사용하여 3개의 Linux VM에서 고가용성 및 보안 응용 프로그램을 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 105ff3614a05926d2bc2f236837bb4d5d95fcf32
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Azure의 Linux 가상 컴퓨터 부하를 분산하여 고가용성 응용 프로그램을 만드는 방법
이 자습서에서는 트래픽을 분산하고 고가용성을 제공하는 Azure Load Balancer의 여러 다른 구성 요소에 대해 알아봅니다. 부하 분산 장치를 작동하려면 3개의 Linux VM(가상 컴퓨터)에서 실행되는 Node.js 앱을 빌드합니다.

이 자습서의 단계는 최신 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 사용하여 완료할 수 있습니다.


## <a name="azure-load-balancer-overview"></a>Azure Load Balancer 개요
Azure Load Balancer는 들어오는 트래픽을 정상 VM 간에 분산하여 고가용성을 제공하는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치 상태 프로브가 각 VM에서 지정된 포트를 모니터링하고 작동하는 VM으로만 트래픽을 분산합니다.

하나 이상의 공용 IP 주소를 포함하는 프런트 엔드 IP 구성을 정의할 수 있습니다. 이 프런트 엔드 IP 구성을 사용하여 인터넷을 통해 부하 분산 장치 및 응용 프로그램에 액세스하도록 할 수 있습니다. 

가상 컴퓨터는 가상 NIC(네트워크 인터페이스 카드)를 사용하여 부하 분산 장치에 연결합니다. VM으로 트래픽을 분산하기 위해 백 엔드 주소 풀에 부하 분산 장치에 연결된 가상(NIC)의 주소가 포함됩니다.

트래픽 흐름을 제어하려면 VM에 매핑되는 특정 포트 및 프로토콜에 대해 부하 분산 장치 규칙을 정의합니다.

이전 자습서를 따라 [가상 컴퓨터 크기 집합을 만든 경우](tutorial-create-vmss.md) 부하 분산 장치가 생성되었을 것입니다. 이러한 모든 구성 요소는 크기 집합의 일부로 구성되었습니다.


## <a name="create-azure-load-balancer"></a>Azure Load Balancer 만들기
이 섹션에서는 부하 분산 장치의 각 구성 요소를 만들고 구성하는 방법을 자세히 설명합니다. 부하 분산 장치를 만들려면 먼저 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `westus` 위치에 `myRGLoadBalancer`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myRGLoadBalancer --location westus
```

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
인터넷에서 앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. [az network public-ip create](/cli/azure/public-ip#create)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 `myRGLoadBalancer` 리소스 그룹에 `myPublicIP`라는 공용 IP 주소를 만듭니다.

```azurecli
az network public-ip create --resource-group myRGLoadBalancer --name myPublicIP
```

### <a name="create-a-load-balancer"></a>부하 분산 장치 만들기
[az network lb create](/cli/azure/network/lb#create)를 사용하여 부하 분산 장치를 만듭니다. 다음 예제에서는 `myLoadBalancer`라는 부하 분산 장치를 만들고 `myPublicIP` 주소를 프런트 엔드 IP 구성에 할당합니다.

```azurecli
az network lb create \
    --resource-group myRGLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>상태 프로브 만들기
부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 기본적으로 VM은 15초 간격으로 두 번의 연속 실패 후에 부하 분산 장치 분산에서 제거됩니다. 앱의 프로토콜 또는 특정 상태 확인 페이지에 따라 상태 프로브를 만듭니다. 

다음 예제에서는 TCP 프로브를 만듭니다. 좀 더 미세 조정된 상태 검사를 위해 사용자 지정 HTTP 프로브를 만들 수도 있습니다. 사용자 지정 HTTP 프로브를 사용할 경우 `healthcheck.js`와 같은 상태 확인 페이지를 만들어야 합니다. 부하 분산 상태가 호스트를 순환 상태를 유지하려면 프로브는 **HTTP 200 정상** 응답을 반환해야 합니다.

TCP 상태 프로브를 만들려면 [az network lb probe create](/cli/azure/network/lb/probe#create)를 사용합니다. 다음 예제에서는 `myHealthProbe`라는 상태 프로브를 만듭니다.

```azurecli
az network lb probe create \
    --resource-group myRGLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기
부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 VM만 트래픽을 수신하도록 하려면 사용할 상태 프로브도 정의합니다.

[az network lb rule create](/cli/azure/network/lb/rule#create)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 `myLoadBalancerRule`이라는 규칙을 만들고, `myHealthProbe` 상태 프로브를 사용하고, 포트 `80`에서 트래픽 부하를 분산합니다.

```azurecli
az network lb rule create \
    --resource-group myRGLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>가상 네트워크 구성
일부 VM을 배포하고 부하 분산 장치를 테스트하려면 지원하는 가상 네트워크 리소스를 만듭니다. 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 관리](tutorial-virtual-network.md) 자습서를 참조하세요.

### <a name="create-network-resources"></a>네트워크 리소스 만들기
[az network vnet create](/cli/azure/vnet#create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 `mySubnet`이라는 서브넷을 사용하여 `myVnet`이라는 가상 네트워크를 만듭니다.

```azurecli
az network vnet create --resource-group myRGLoadBalancer --name myVnet --subnet-name mySubnet
```

네트워크 보안 그룹을 추가하려면 [az network nsg create](/cli/azure/network/nsg#create)를 사용합니다. 다음 예제에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create --resource-group myRGLoadBalancer --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroupRule`이라는 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myRGLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

가상 NIC는 [az network nic create](/cli/azure/network/nic#create)를 사용하여 만듭니다. 다음 예제에서는 3개의 가상 NIC를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩) 언제든지 추가 가상 NIC 및 VM을 만든 후 부하 분산 장치에 추가할 수 있습니다.

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myRGLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-virtual-machines"></a>가상 컴퓨터 만들기

### <a name="create-cloud-init-config"></a>cloud-init 구성 만들기
[처음 부팅 시 Linux 가상 컴퓨터를 사용자 지정하는 방법](tutorial-automate-vm-deployment.md)에 대한 이전 자습서에서 cloud-init를 사용하여 VM 사용자 지정을 자동화하는 방법을 배웠습니다. 동일한 cloud-init 구성 파일을 사용하여 NGINX를 설치하고 간단한 'Hello World' Node.js 앱을 실행할 수 있습니다. `cloud-init.txt`라는 파일을 만들고 다음 구성을 붙여 넣습니다.

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
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-virtual-machines"></a>가상 컴퓨터 만들기
앱의 고가용성을 향상시키려면 VM을 가용성 집합에 배치합니다. 가용성 집합에 대한 자세한 내용은 이전 [고가용성 가상 컴퓨터를 만드는 방법](tutorial-availability-sets.md) 자습서를 참조하세요.

[az vm availability-set create](/cli/azure/vm/availability-set#create)를 사용하여 가용성 집합을 만듭니다. 다음 예제는 `myAvailabilitySet`라는 가용성 집합을 만듭니다.

```azurecli
az vm availability-set create \
    --resource-group myRGLoadBalancer \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```

이제 [az vm create](/cli/azure/vm#create)로 VM을 만들 수 있습니다. 다음 예제에서는 3개의 VM을 만들고 SSH 키가 아직 없으면 생성합니다.

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myRGLoadBalancer \
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


## <a name="test-load-balancer"></a>부하 분산 장치 테스트
[az network public-ip show](/cli/azure/network/public-ip#show)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 `myPublicIP`의 IP 주소를 가져옵니다.

```azurecli
az network public-ip show \
    --resource-group myRGLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

그런 다음 웹 브라우저에 공용 IP 주소를 입력할 수 있습니다. 다음 예제와 같이 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름을 포함하여 앱이 표시됩니다.

![Node.js 앱 실행](./media/tutorial-load-balancer/running-nodejs-app.png)

앱이 실행되는 3개의 모든 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인하기 위해 웹 브라우저를 강제로 새로 고칠 수 있습니다.


## <a name="add-and-remove-vms"></a>VM 추가 및 제거
앱이 실행되는 동안 OS 업데이트와 같은 유지 관리 작업을 VM에서 수행해야 할 수 있습니다. 앱에 대해 증가된 트래픽을 처리하기 위해 VM을 더 추가해야 할 수 있습니다. 이 섹션에서는 부하 분산 장치에서 VM을 추가 또는 제거하는 방법을 보여 줍니다.

### <a name="remove-a-vm-from-the-load-balancer"></a>부하 분산 장치에서 VM 제거
[az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove)를 사용하여 백 엔드 주소 풀에서 VM을 제거할 수 있습니다. 다음 예제에서는 `myLoadBalancer`에서 **myVM2**용 가상 NIC를 제거합니다.

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myRGLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

앱이 실행되는 나머지 2개의 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인하기 위해 웹 브라우저를 강제로 새로 고칠 수 있습니다. 이제 OS 업데이트 설치 또는 VM 다시 부팅을 수행 등의 유지 관리 작업을 VM에서 수행할 수 있습니다.

### <a name="add-a-vm-to-the-load-balancer"></a>부하 분산 장치에 VM 추가
VM 유지 관리를 수행한 이후 또는 용량을 확장해야 할 경우 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add)를 사용하여 백 엔드 주소 풀에 VM을 추가할 수 있습니다. 다음 예제에서는 `myLoadBalancer`에 **myVM2**용 가상 NIC를 추가합니다.

```azurecli
az network nic ip-config address-pool add \
    --resource-group myRGLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 가상 컴퓨터에 대한 부하 분산 장치를 만드는 과정을 알아보았습니다. 다음 자습서에서는 Azure Virtual Network 구성 요소에 대해 자세히 알아봅니다.

[VM 네트워킹 관리](tutorial-virtual-network.md)

