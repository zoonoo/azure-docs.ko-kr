---
title: '빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure CLI | Microsoft Docs'
description: Azure CLI를 사용하여 백 엔드 풀의 가상 머신에 웹 트래픽을 보내는 Azure Application Gateway를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a4f6cc2af7b9e044e5a72767898f876932fbf973
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133930"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure CLI

이 빠른 시작에서는 Azure CLI를 사용하여 애플리케이션 게이트웨이를 만드는 방법을 보여줍니다.  Application Gateway를 만든 후 올바르게 작동하는지 테스트합니다. Azure Application Gateway를 통해 수신기를 포트에 할당하고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가하여 애플리케이션 웹 트래픽을 특정 리소스로 보냅니다. 간단히 나타내기 위해 이 문서에서는 공용 프런트 엔드 IP 1개, 이 Application Gateway에 단일 사이트를 호스트하는 기본 수신기 1개, 백 엔드 풀에 사용되는 가상 머신 2개, 기본 요청 라우팅 규칙 1개를 이용한 간단한 설정을 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 조건

### <a name="azure-cli"></a>Azure CLI

로컬로 CLI를 설치하여 사용하기로 선택하는 경우 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 찾으려면 **az --version**을 실행합니다. 설치 또는 업그레이드에 대한 자세한 내용은 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

### <a name="resource-group"></a>리소스 그룹

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만듭니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroupAG*라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>필요한 네트워크 리소스 

Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다.  애플리케이션 게이트웨이 서브넷은 애플리케이션 게이트웨이만 포함할 수 있습니다. 다른 리소스는 허용되지 않습니다.  Application Gateway에 대한 새 서브넷을 만들거나 기존 서브넷을 사용할 수 있습니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다. 사용 사례에 따라 Application Gateway의 프런트 엔드 IP를 공용 또는 프라이빗 IP로 구성할 수 있습니다. 이 예제에서는 공용 프런트 엔드 IP를 선택합니다.

가상 네트워크 및 서브넷을 만들려면 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)를 사용합니다. [az network public-ip create](/cli/azure/network/public-ip)를 실행하여 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

### <a name="backend-servers"></a>백 엔드 서버

백 엔드는 NIC, 가상 머신 확장 집합, 공용 IP, 내부 IP, FQDN(정규화된 도메인 이름) 및 다중 테넌트 백 엔드(예: Azure App Service)로 구성될 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이의 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다. 또한 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지 확인할 수 있도록 가상 머신에 IIS를 설치합니다.

#### <a name="create-two-virtual-machines"></a>두 개의 가상 머신 만들기

애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인할 수 있도록 가상 머신에 [NGINX 웹 서버](https://docs.nginx.com/nginx/)를 설치합니다. cloud-init 구성 파일을 사용하여 NGINX를 설치하고 Linux 가상 머신에서 "Hello World" Node.js 앱을 실행할 수 있습니다. cloud-init에 대한 자세한 내용은 [Azure의 가상 머신에 대한 Cloud-init 지원](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)을 참조하세요.

Azure Cloud Shell에서 다음 구성을 복사하여 *cloud-init.txt* 파일에 붙여넣습니다. *editor cloud-init.txt*를 입력하여 파일을 만듭니다.

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

[az network nic create](/cli/azure/network/nic#az-network-nic-create)를 사용하여 네트워크 인터페이스를 만듭니다. 가상 머신을 만들려면 [az vm create](/cli/azure/vm#az-vm-create)를 사용합니다.

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

[az network application-gateway create](/cli/azure/network/application-gateway)를 사용하여 애플리케이션 게이트웨이를 만듭니다. Azure CLI를 사용하여 애플리케이션 게이트웨이를 만들 때 용량, SKU, HTTP 설정 등의 구성 정보를 지정합니다. 그러면 Azure가 네트워크 인터페이스의 개인 IP 주소를 애플리케이션 게이트웨이의 백 엔드 풀에 서버로 추가합니다.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Azure가 애플리케이션 게이트웨이를 만들 때까지 최대 30분이 걸릴 수 있습니다. 만들어지면 **애플리케이션 게이트웨이** 페이지의 **설정** 섹션에서 다음 설정을 볼 수 있습니다.

- **appGatewayBackendPool**: **백 엔드 풀** 페이지에 있습니다. 필요한 백 엔드 풀을 지정합니다.
- **appGatewayBackendHttpSettings**: **HTTP 설정** 페이지에 있습니다. 애플리케이션 게이트웨이가 통신에 포트 80 및 HTTP 프로토콜을 사용하도록 지정합니다.
- **appGatewayHttpListener**: **수신기 페이지**에 있습니다. **appGatewayBackendPool**과 연결되는 기본 수신기를 지정합니다.
- **appGatewayFrontendIP**: **프런트 엔드 IP 구성** 페이지에 있습니다. *myAGPublicIPAddress*를 **appGatewayHttpListener**에 할당합니다.
- **rule1**: **규칙** 페이지에 있습니다. **appGatewayHttpListener**에 연결되는 기본 회람 규칙을 지정합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

Azure에서 애플리케이션 게이트웨이를 만들려면 반드시 NGINX 웹 서버가 필요한 것은 아니지만, 이 빠른 시작에서는 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지 확인하기 위해 설치했습니다. 새 애플리케이션 게이트웨이의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)를 사용합니다. 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.
    
![애플리케이션 게이트웨이 테스트](./media/quick-create-cli/application-gateway-nginxtest.png)

브라우저를 새로 고치면 보조 VM의 이름이 표시됩니다. 유효한 응답은 Application Gateway가 성공적으로 만들어졌는지와 백 엔드에 성고적으로 연결될 수 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 게이트웨이로 만든 리소스가 더 이상 필요 없으면 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹을 제거합니다. 리소스 그룹을 제거하면 애플리케이션 게이트웨이 및 모든 관련 리소스도 함께 제거됩니다.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)

