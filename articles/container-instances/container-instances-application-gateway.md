---
title: 컨테이너 그룹의 정적 IP 주소
description: 가상 네트워크에서 컨테이너 그룹을 만들고 Azure 애플리케이션 게이트웨이를 사용하여 컨테이너화된 웹앱에 정적 프런트 엔드 IP 주소를 노출합니다.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: c0401c0b6ec4aae124ee1954762d22c16cacb994
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110084226"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>컨테이너 그룹에 대한 정적 IP 주소 노출

이 문서에서는 Azure [애플리케이션 게이트웨이](../application-gateway/overview.md)를 사용하여 [컨테이너 그룹](container-instances-container-groups.md)에 대한 정적 공용 IP 주소를 노출하는 한 가지 방법을 보여 줍니다. Azure Container Instances에서 실행되는 외부 연결 컨테이너화된 앱에 대한 정적 진입점이 필요한 경우 다음 단계를 수행합니다.

이 문서에서는 Azure CLI를 사용하여 다음 시나리오를 위한 리소스를 만듭니다.

* Azure Virtual Network
* 소규모 웹앱을 호스트하는 [가상 네트워크에](container-instances-vnet.md) 배포된 컨테이너 그룹
* 공용 프런트 엔드 IP 주소, 게이트웨이에서 웹 사이트를 호스트하는 수신기 및 백 엔드 컨테이너 그룹에 대한 경로를 포함하는 애플리케이션 게이트웨이

애플리케이션 게이트웨이가 실행되고 컨테이너 그룹이 네트워크의 위임된 서브넷에서 안정된 개인 IP 주소를 노출하는 한 컨테이너 그룹은 이 공용 IP 주소에서 액세스할 수 있습니다.

> [!NOTE]
> Azure는 게이트웨이가 프로비전되고 사용 가능한 시간 및 처리하는 데이터의 양을 기준으로 애플리케이션 게이트웨이에 대한 요금을 청구합니다. [가격 책정](https://azure.microsoft.com/pricing/details/application-gateway/)을 참조하세요.

## <a name="create-virtual-network"></a>가상 네트워크 만들기

일반적인 경우 Azure 가상 네트워크가 이미 있을 수 있습니다. 아직 없는 경우 다음 예제 명령과 같이 가상 네트워크를 만듭니다. 가상 네트워크에는 애플리케이션 게이트웨이와 컨테이너 그룹에 대한 별도의 서브넷이 필요합니다.

필요한 경우 Azure 리소스 그룹을 만듭니다. 예를 들면 다음과 같습니다.

```azureci
az group create --name myResourceGroup --location eastus
```

[az network vnet create][az-network-vnet-create] 명령을 사용하여 가상 네트워크를 만듭니다. 이 명령은 네트워크에 *myAGSubnet* 서브넷을 만듭니다.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

[az network vnet subnet create][az-network-vnet-subnet-create] 명령을 사용하여 백 엔드 컨테이너 그룹에 대한 서브넷을 만듭니다. 여기서는 *Myacisubnet* 이라고 합니다.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

[az network public-ip create][az-network-public-ip-create] 명령을 사용하여 정적 공용 IP 리소스를 만듭니다. 이후 단계에서 이 주소는 애플리케이션 게이트웨이의 프런트 엔드로 구성됩니다.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>컨테이너 그룹 만들기

다음 [az container create][az-container-create]를 실행하여 이전 단계에서 구성한 가상 네트워크에 컨테이너 그룹을 만듭니다.

이 그룹은 *myACISubnet* 서브넷에 배포되며 `aci-helloworld` 이미지를 끌어오는 *appcontainer* 라는 단일 인스턴스를 포함합니다. 설명서의 다른 문서에 표시된 것처럼 이 이미지는 정적 HTML 페이지를 제공하는, Node.js로 작성된 작은 웹앱을 패키지합니다.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

성공적으로 배포되면 컨테이너 그룹에 가상 네트워크의 개인 IP 주소가 할당됩니다. 예를 들어 다음 [az container show][az-container-show] 명령을 실행하여 그룹의 IP 주소를 검색합니다.

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

출력은 `10.0.2.4`과 비슷합니다.

이후 단계에서 사용하려면 IP 주소를 환경 변수로 저장합니다.

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> 컨테이너 그룹을 중지, 시작 또는 다시 시작하면 컨테이너 그룹의 비공개 IP가 변경될 수 있습니다. 이런 경우 애플리케이션 게이트웨이 구성을 업데이트해야 합니다.

## <a name="create-application-gateway"></a>애플리케이션 게이트웨이 만들기

[애플리케이션 게이트웨이 빠른 시작](../application-gateway/quick-create-cli.md)의 단계에 따라 가상 네트워크에서 애플리케이션 게이트웨이를 만듭니다. 다음 [az network application-gateway create][az-network-application-gateway-create] 명령은 공용 프런트 엔드 IP 주소와 백 엔드 컨테이너 그룹에 대한 경로를 사용하여 게이트웨이를 만듭니다. 게이트웨이 설정에 대한 자세한 내용은 [Application Gateway 설명서](../application-gateway/index.yml)를 참조하세요.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP"
```


Azure가 애플리케이션 게이트웨이를 만들 때까지 최대 15분이 걸릴 수 있습니다.

## <a name="test-public-ip-address"></a>공용 IP 주소 테스트

이제 애플리케이션 게이트웨이 뒤에 있는 컨테이너 그룹에서 실행되는 웹앱에 대한 액세스를 테스트할 수 있습니다.

[Az network public-ip show][az-network-public-ip-show] 명령을 실행하여 게이트웨이의 프런트 엔드 공용 IP 주소를 검색합니다.

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

출력은 `52.142.18.133`와 같은 공용 IP 주소입니다.

성공적으로 구성된 후 실행 중인 웹앱을 보려면 브라우저에서 게이트웨이의 공용 IP 주소로 이동합니다. 성공적인 액세스는 다음과 유사합니다.

![Azure 컨테이너 인스턴스에서 실행되는 애플리케이션을 보여주는 브라우저 스크린샷](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>다음 단계

* 애플리케이션 게이트웨이 뒤에 백 엔드 서버로 WordPress container 인스턴스를 사용하여 컨테이너 그룹을 만들려면 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/wordpress/aci-wordpress-vnet)을 참조하세요.
* SSL 종료를 위한 인증서로 애플리케이션 게이트웨이를 구성할 수도 있습니다. [개요](../application-gateway/ssl-overview.md) 및 [자습서](../application-gateway/create-ssl-portal.md)를 참조하세요.
* 시나리오에 따라 다른 Azure 부하 분산 솔루션을 Azure Container Instances와 함께 사용하는 것이 좋습니다. 예를 들어 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 사용하여 여러 컨테이너 인스턴스 및 여러 지역에 걸쳐 트래픽을 배포합니다. 이 [블로그 게시물](https://aaronmsft.com/posts/azure-container-instances/)을 참조하세요.

[az-network-vnet-create]:  /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az_network-application-gateway-create
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
