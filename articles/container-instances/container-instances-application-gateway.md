---
title: 컨테이너 그룹에 대 한 고정 IP 주소
description: 가상 네트워크에서 컨테이너 그룹을 만들고 Azure application gateway를 사용 하 여 컨테이너 화 된 웹 앱에 정적 프런트 엔드 IP 주소를 노출 합니다.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: a27cf20b7d04fedb0b9e0ab408de24d37f2935c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84299165"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>컨테이너 그룹에 대 한 고정 IP 주소 노출

이 문서에서는 Azure [application gateway](../application-gateway/overview.md)를 사용 하 여 [컨테이너 그룹](container-instances-container-groups.md) 에 대 한 고정 공용 IP 주소를 노출 하는 한 가지 방법을 보여 줍니다. Azure Container Instances에서 실행 되는 외부 연결 컨테이너 화 된 앱에 대 한 정적 진입점이 필요한 경우 다음 단계를 수행 합니다. 

이 문서에서는 Azure CLI를 사용 하 여이 시나리오에 대 한 리소스를 만듭니다.

* Azure Virtual Network
* 소규모 웹 앱을 호스트 하는 [가상 네트워크에](container-instances-vnet.md) 배포 된 컨테이너 그룹
* 공용 프런트 엔드 IP 주소, 게이트웨이에서 웹 사이트를 호스트 하는 수신기 및 백 엔드 컨테이너 그룹에 대 한 경로를 포함 하는 응용 프로그램 게이트웨이

응용 프로그램 게이트웨이가 실행 되 고 컨테이너 그룹이 네트워크의 위임 된 서브넷에서 안정 된 개인 IP 주소를 노출 하는 한 컨테이너 그룹은이 공용 IP 주소에서 액세스할 수 있습니다.

> [!NOTE]
> Azure는 게이트웨이가 프로 비전 되 고 사용 가능한 시간 및 처리 하는 데이터의 양을 기준으로 응용 프로그램 게이트웨이에 대 한 요금을 청구 합니다. [가격 책정](https://azure.microsoft.com/pricing/details/application-gateway/)을 참조 하세요.

## <a name="create-virtual-network"></a>가상 네트워크 만들기

일반적인 경우에는 Azure 가상 네트워크가 이미 있을 수 있습니다. 아직 없는 경우 다음 예제 명령과 같이 만듭니다. 가상 네트워크에는 응용 프로그램 게이트웨이와 컨테이너 그룹에 대 한 별도의 서브넷이 필요 합니다.

필요한 경우 Azure 리소스 그룹을 만듭니다. 예를 들어:

```azureci
az group create --name myResourceGroup --location eastus
```

[az network vnet create][az-network-vnet-create] 명령을 사용하여 가상 네트워크를 만듭니다. 이 명령은 네트워크에 *Myagsubnet* 서브넷을 만듭니다.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

[Az network vnet subnet create][az-network-vnet-subnet-create] 명령을 사용 하 여 백 엔드 컨테이너 그룹에 대 한 서브넷을 만듭니다. 여기서는 *Myacisubnet*이라고 합니다.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

[Az network 공용-ip create][az-network-public-ip-create] 명령을 사용 하 여 고정 공용 ip 리소스를 만듭니다. 이후 단계에서이 주소는 application gateway의 프런트 엔드로 구성 됩니다.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>컨테이너 그룹 만들기

다음 [az container create][az-container-create] 를 실행 하 여 이전 단계에서 구성한 가상 네트워크에 컨테이너 그룹을 만듭니다. 

이 그룹은 *Myacisubnet* 서브넷에 배포 되 고 이미지를 가져오는 *appcontainer* 라는 단일 인스턴스를 포함 합니다 `aci-helloworld` . 설명서의 다른 문서에 나와 있는 것 처럼이 이미지는 정적 HTML 페이지를 제공 하는 Node.js으로 작성 된 작은 웹 앱을 패키지 합니다. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

성공적으로 배포 되 면 컨테이너 그룹에 가상 네트워크의 개인 IP 주소가 할당 됩니다. 예를 들어 다음 [az container show][az-container-show] 명령을 실행 하 여 그룹의 IP 주소를 검색 합니다.

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

출력은 `10.0.2.4`과 비슷합니다.

이후 단계에서 사용 하려면 IP 주소를 환경 변수로 저장 합니다.

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>애플리케이션 게이트웨이 만들기

[Application gateway 빠른](../application-gateway/quick-create-cli.md)시작의 단계에 따라 가상 네트워크에서 응용 프로그램 게이트웨이를 만듭니다. 다음 [az network application-gateway create][az-network-application-gateway-create] 명령은 공용 프런트 엔드 IP 주소와 백 엔드 컨테이너 그룹에 대 한 경로를 사용 하 여 게이트웨이를 만듭니다. 게이트웨이 설정에 대 한 자세한 내용은 [Application Gateway 설명서](/azure/application-gateway/) 를 참조 하세요.

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


Azure가 응용 프로그램 게이트웨이를 만드는 데 최대 15 분이 걸릴 수 있습니다. 

## <a name="test-public-ip-address"></a>공용 IP 주소 테스트
  
이제 응용 프로그램 게이트웨이 뒤에 있는 컨테이너 그룹에서 실행 되는 웹 앱에 대 한 액세스를 테스트할 수 있습니다.

[Az network public-ip show][az-network-public-ip-show] 명령을 실행 하 여 게이트웨이의 프런트 엔드 공용 ip 주소를 검색 합니다.

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

출력은와 같은 공용 IP 주소입니다 `52.142.18.133` .

성공적으로 구성 된 경우 실행 중인 웹 앱을 보려면 브라우저에서 게이트웨이의 공용 IP 주소로 이동 합니다. 성공적인 액세스는 다음과 유사 합니다.

![Azure 컨테이너 인스턴스에서 실행되는 애플리케이션을 보여주는 브라우저 스크린샷](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>다음 단계

* 응용 프로그램 게이트웨이 뒤에 백 엔드 서버로 WordPress container 인스턴스를 사용 하 여 컨테이너 그룹을 만들려면 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) 을 참조 하세요.
* SSL 종료를 위해 인증서를 사용 하 여 응용 프로그램 게이트웨이를 구성할 수도 있습니다. [개요](../application-gateway/ssl-overview.md) 및 [자습서](../application-gateway/create-ssl-portal.md)를 참조 하세요.
* 시나리오에 따라 Azure Container Instances를 사용 하 여 다른 Azure 부하 분산 솔루션을 사용 하는 것이 좋습니다. 예를 들어 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 를 사용 하 여 여러 컨테이너 인스턴스 및 여러 지역에 걸쳐 트래픽을 분산 합니다. 이 [블로그 게시물](https://aaronmsft.com/posts/azure-container-instances/)을 참조하세요.

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show