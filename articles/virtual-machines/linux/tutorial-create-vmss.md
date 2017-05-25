---
title: "Azure에서 Linux용 가상 컴퓨터 확장 집합 만들기 | Microsoft Docs"
description: "가상 컴퓨터 확장 집합을 사용하여 Linux VM에 항상 사용 가능한 응용 프로그램을 만들고 배포합니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 05/02/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 972c6f60c8963cad6f92b228e795a5027b838f00
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017

---

# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>가상 컴퓨터 확장 집합 만들기 및 Linux에 항상 사용 가능한 앱 배포
가상 컴퓨터 확장 집합을 사용하면 동일한 자동 크기 조정 가상 컴퓨터 집합을 배포하고 관리할 수 있습니다. 확장 집합의 VM 수를 수동으로 조정하거나 CPU 사용률, 메모리 요구량 또는 네트워크 트래픽을 기반으로 자동으로 크기를 조정하는 규칙을 정의할 수도 있습니다. 이 자습서에서는 Azure에서 가상 컴퓨터 확장 집합을 배포합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * cloud-init를 사용하여 크기를 조정하는 앱 만들기
> * 가상 컴퓨터 확장 집합 만들기
> * 확장 집합의 인스턴스 수 증가 또는 감소
> * 확장 집합 인스턴스에 대한 연결 정보 보기
> * 확장 집합에 데이터 디스크 사용

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 또한 브라우저에서 [Cloud Shell](/azure/cloud-shell/quickstart)을 사용할 수도 있습니다.

## <a name="scale-set-overview"></a>확장 집합 개요
가상 컴퓨터 확장 집합을 사용하면 동일한 자동 크기 조정 가상 컴퓨터 집합을 배포하고 관리할 수 있습니다. 확장 집합은 이전의 [고가용성 VM 만들기](tutorial-availability-sets.md) 자습서에서 알아본 것과 동일한 구성 요소를 사용합니다. 확장 집합의 VM은 가용성 집합에 만들어지고 논리 장애 도메인 및 업데이트 도메인에 분산됩니다.

VM은 필요에 따라 확장 집합에 생성됩니다. 사용자는 확장 집합에서 VM이 추가되거나 제거되는 방법 및 시기를 제어하는 자동 크기 조정 규칙을 정의합니다. 이러한 규칙은 메트릭(예: CPU 부하, 메모리 사용량 또는 네트워크 트래픽)을 기반으로 트리거할 수 있습니다.

확장 집합은 Azure 플랫폼 이미지를 사용하는 경우 최대 1,000개의 VM을 지원합니다. 프로덕션 워크로드의 경우 [사용자 지정 VM 이미지 만들기](tutorial-custom-images.md) 작업이 필요할 수 있습니다. 사용자 지정 이미지를 사용하는 경우 확장 집합에 최대 100개의 VM을 만들 수 있습니다.


## <a name="create-an-app-to-scale"></a>크기를 조정하는 앱 만들기
프로덕션 사용을 위해 설치되고 구성된 응용 프로그램을 포함하는 [사용자 지정 VM 이미지 만들기](tutorial-custom-images.md) 작업이 필요할 수 있습니다. 이 자습서에서는 처음 부팅 시 VM을 사용자 지정하여 확장 집합의 실제 동작을 신속하게 확인합니다.

이전 자습서에서 cloud-init를 사용하여 [처음 부팅 시 Linux 가상 컴퓨터를 사용자 지정하는 방법](tutorial-automate-vm-deployment.md)을 배웠습니다. 동일한 cloud-init 구성 파일을 사용하여 NGINX를 설치하고 간단한 'Hello World' Node.js 앱을 실행할 수 있습니다. *cloud-init.txt*라는 파일을 만들고 다음 구성을 붙여넣습니다.

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


## <a name="create-a-scale-set"></a>확장 집합 만들기
확장 집합을 만들려면 먼저 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupScaleSet*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroupScaleSet --location eastus
```

이제 [az vmss create](/cli/azure/vmss#create)를 사용하여 가상 컴퓨터 확장 집합을 만듭니다. 다음 예제에서는 *myScaleSet*이라는 확장 집합을 만들고, cloud-init 파일을 사용하여 VM을 사용자 지정하고, SSH 키가 없는 경우 SSH 키를 생성합니다.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="allow-web-traffic"></a>웹 트래픽 허용
부하 분산 장치는 가상 컴퓨터 확장 집합의 일부로 자동으로 생성되었습니다. 부하 분산 장치는 부하 분산 장치 규칙을 사용하여 정의된 VM 집합 전역에 트래픽을 분산시킵니다. 다음 자습서 [Azure에서 Virtual Machines의 부하를 분산하는 방법](tutorial-load-balancer.md)에서 부하 분산 장치 개념 및 구성에 대해 자세히 알아볼 수 있습니다.

트래픽이 Web App에 도달하도록 허용하려면 [az network lb rule create](/cli/azure/network/lb/rule#create)를 사용하여 규칙을 만듭니다. 다음 예제는 *myLoadBalancerRuleWeb*이라는 규칙을 만듭니다.

```azurecli
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>앱 테스트
웹에서 Node.js 앱을 보려면 [az network public-ip show](/cli/azure/network/public-ip#show)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 확장 집합의 일부로 만든 *myScaleSetLBPublicIP*의 IP 주소를 가져옵니다.

```azurecli
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

웹 브라우저에 공용 IP 주소를 입력합니다. 앱이 표시되고 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름이 표시됩니다.

![Node.js 앱 실행](./media/tutorial-create-vmss/running-nodejs-app.png)

확장 집합의 실제 동작을 확인하려면 웹 브라우저에서 새로 고침을 실행하여 부하 분산 장치가 앱이 실행되는 모든 VM으로 트래픽을 분산시키는 것을 확인합니다.


## <a name="management-tasks"></a>관리 작업
확장 집합의 수명 주기 내내 하나 이상의 관리 작업을 실행해야 합니다. 또한 다양한 수명 주기 작업을 자동화하는 스크립트를 만들어야 하는 경우가 있습니다. Azure CLI 2.0은 이러한 작업을 수행할 수 있는 빠른 방법을 제공합니다. 다음은 몇 가지 일반적인 작업입니다.

### <a name="view-vms-in-a-scale-set"></a>확장 집합의 VM 보기
확장 집합에서 실행 중인 VM 목록을 보려면 다음과 같이 [az vmss list-instances](/cli/azure/vmss#list-instances)를 사용합니다.

```azurecli
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

다음 예제와 유사하게 출력됩니다.

```azurecli
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>VM 인스턴스 증가 또는 감소
현재 확장 집합의 인스턴스 수를 보려면 [az vmss show](/cli/azure/vmss#show)를 사용하여 *sku.capacity*를 쿼리합니다.

```azurecli
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

그런 다음[az vmss scale](/cli/azure/vmss#scale)을 사용하여 확장 집합의 Virtual Machines 수를 수동으로 증가 또는 감소시킬 수 있습니다. 다음 예제는 확장 집합의 VM 수를 *5*로 설정합니다.

```azurecli
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 5
```

자동 크기 조정 규칙을 사용하면 네트워크 트래픽 또는 CPU 사용량과 같은 수요에 대응하여 확장 집합의 VM 수를 확대하거나 축소하는 방법을 정의할 수 있습니다. 현재 이 규칙은 Azure CLI 2.0에서 설정할 수 없습니다. 자동 크기 조정을 구성하려면 [Azure Portal](https://portal.azure.com)을 사용하세요.

### <a name="get-connection-info"></a>연결 정보 가져오기
확장 집합의 VM에 대한 연결 정보를 가져오려면 [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info)를 사용합니다. 이 명령은 SSH와 연결할 수 있도록 각 VM의 공용 IP 주소 및 포트를 출력합니다.

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>확장 집합으로 데이터 디스크 사용
확장 집합으로 데이터 디스크를 사용할 수 있습니다. OS 디스크 대신 데이터 디스크에 앱을 빌드하는 모범 사례 및 성능 향상에 대해 설명하는 이전 자습서에서는 [Azure 디스크를 관리하는 방법](tutorial-manage-disks.md)을 배웠습니다.

### <a name="create-scale-set-with-data-disks"></a>데이터 디스크로 확장 집합 만들기
확장 집합을 만들고 데이터 디스크를 연결하려면 [az vmss create](/cli/azure/vmss#create) 명령에 `--data-disk-sizes-gb` 매개 변수를 추가합니다. 다음 예제에서는 각 인스턴스에 *50*Gb 데이터 디스크가 연결된 확장 집합을 만듭니다.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSetDisks \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50
```

확장 집합에서 인스턴스가 제거되면 연결된 데이터 디스크도 제거됩니다.

### <a name="add-data-disks"></a>데이터 디스크 추가
확장 집합의 인스턴스에 데이터 디스크를 추가하려면 [az vmss disk attach](/cli/azure/vmss/disk#attach) 명령을 사용합니다. 다음 예제는 각 인스턴스에 *50*Gb 디스크를 추가합니다.

```azurecli
az vmss disk attach `
    --resource-group myResourceGroupScaleSet `
    --name myScaleSet `
    --size-gb 50 `
    --lun 2
```

### <a name="detach-data-disks"></a>데이터 디스크 분리
확장 집합의 인스턴스에서 데이터 디스크를 제거하려면 [az vmss disk detach](/cli/azure/vmss/disk#detach) 명령을 사용합니다. 다음 예제는 각 인스턴스의 LUN *2*에서 데이터 디스크를 제거합니다.

```azurecli
az vmss disk detach `
    --resource-group myResourceGroupScaleSet `
    --name myScaleSet `
    --lun 2
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 가상 컴퓨터 확장 집합을 만들었습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * cloud-init를 사용하여 크기를 조정하는 앱 만들기
> * 가상 컴퓨터 확장 집합 만들기
> * 확장 집합의 인스턴스 수 증가 또는 감소
> * 확장 집합 인스턴스에 대한 연결 정보 보기
> * 확장 집합에 데이터 디스크 사용

Virtual Machines의 부하 분산 개념에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Virtual Machines 부하 분산](tutorial-load-balancer.md)
