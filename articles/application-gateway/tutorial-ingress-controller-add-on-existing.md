---
title: 기존 Azure 애플리케이션 Gateway를 사용 하 여 기존 AKS 클러스터에 대 한 수신 컨트롤러 추가 기능 사용
description: 이 자습서를 사용 하 여 기존 AKS 클러스터에 대 한 수신 컨트롤러 추가 기능을 기존 Application Gateway 사용 하도록 설정 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 42952e379b9f68008de23ee3b1717280d8dd6cb2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088124"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>자습서: Azure CLI (미리 보기)를 통해 기존 AKS Application Gateway 클러스터에 대 한 Application Gateway 수신 컨트롤러 추가 기능 사용

Azure CLI를 사용 하 여 [AKS (Azure Kubernetes Services)](https://azure.microsoft.com/services/kubernetes-service/) 클러스터에 대해 현재 미리 보기 상태인 [AGIC (Application Gateway 수신 컨트롤러)](ingress-controller-overview.md) 추가 기능을 사용 하도록 설정할 수 있습니다. 이 자습서에서는 AGIC 추가 기능을 사용 하 여 별도의 가상 네트워크에 배포 된 기존 Application Gateway를 통해 기존 AKS 클러스터에 Kubernetes 응용 프로그램을 노출 하는 방법에 대해 알아봅니다. 먼저 하나의 가상 네트워크에 AKS 클러스터를 만들고 별도의 가상 네트워크에 Application Gateway를 만들어 기존 리소스를 시뮬레이션 합니다. 그런 다음 AGIC 추가 기능을 사용 하도록 설정 하 고, 두 가상 네트워크를 함께 피어 링 하 고, AGIC 추가 기능을 사용 하 여 Application Gateway를 통해 노출 되는 샘플 응용 프로그램을 배포 합니다. 기존 Application Gateway에 대해 AGIC 추가 기능을 사용 하도록 설정 하 고 동일한 가상 네트워크에서 기존 AKS 클러스터에 대 한 추가 기능을 사용 하도록 설정 하는 경우 아래 피어 링 단계를 건너뛸 수 있습니다. 이 추가 기능은 이전에 AGIC를 [통해](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) AKS 클러스터에 대 한를 배포 하는 훨씬 빠른 방법을 제공 하며 완전히 관리 되는 환경도 제공 합니다.  

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 리소스 그룹 만들기 
> * 새 AKS 클러스터 만들기 
> * 새 Application Gateway 만들기 
> * 기존 Application Gateway를 사용 하 여 기존 AKS 클러스터에서 AGIC 추가 기능을 사용 하도록 설정 
> * AKS 클러스터 가상 네트워크를 사용 하 여 Application Gateway 가상 네트워크 피어 링
> * AKS 클러스터에서 수신에 AGIC를 사용 하 여 샘플 응용 프로그램 배포
> * Application Gateway를 통해 응용 프로그램에 연결할 수 있는지 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하기로 선택할 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음 예제와 같이 [az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 명령을 사용 하 여 *AKS-IngressApplicationGatewayAddon* 기능 플래그를 등록 합니다. 추가 기능이 미리 보기 상태인 동안에는 구독 당 한 번만이 작업을 수행 해야 합니다.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

상태를 등록 된 것으로 표시 하는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 명령을 사용하여 등록 상태를 확인할 수 있습니다.
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) 명령을 사용하여 Microsoft.ContainerService 리소스 공급자 등록을 새로 고칩니다.
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

이 자습서에 대 한 aks-preview 확장을 설치/업데이트 해야 합니다. 다음 Azure CLI 명령을 사용 합니다.
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *canadacentral* location (지역)에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다. 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>새 AKS 클러스터 배포

이제 새로운 AKS 클러스터를 배포 하 여에 대 한 AGIC 추가 기능을 사용 하도록 설정 하려는 기존 AKS 클러스터가 있는지 시뮬레이트할 수 있습니다.  

다음 예제에서는 만든 리소스 그룹에서 [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) 및 [관리 되는 id](https://docs.microsoft.com/azure/aks/use-managed-identity) 를 사용 하 여 *myCluster* 라는 새 AKS 클러스터를 배포 합니다. *myresourcegroup*.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

명령에 대 한 추가 매개 변수를 구성 하려면 `az aks create` [여기](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create)에서 참조를 참조 하세요. 

## <a name="deploy-a-new-application-gateway"></a>새 Application Gateway 배포 

이제 새 Application Gateway를 배포 하 여 AKS 클러스터, *myCluster*에 대 한 트래픽 부하를 분산 하는 데 사용 하려는 기존 Application Gateway를 시뮬레이션 합니다. Application Gateway 이름은 *Myapplicationgateway*이지만, 먼저 *MYPUBLICIP*라는 공용 IP 리소스를 만들고, 주소 공간이 11.0.0.0/8 인 *myvnet* 이라는 새 가상 네트워크를 만들고, *mysubnet*이라고 하는 주소 공간이 11.1.0.0/16 인 서브넷을 만들고, *myPublicIp*를 사용 하 여 *mysubnet* 에 Application Gateway를 배포 해야 합니다. 

AKS 클러스터를 사용 하 고 별도의 가상 네트워크에서 Application Gateway 하는 경우 두 가상 네트워크의 주소 공간이 겹치지 않아야 합니다. AKS 클러스터가 배포 하는 기본 주소 공간은 10.0.0.0/8 이므로 Application Gateway 가상 네트워크 주소 접두사를 11.0.0.0/8로 설정 합니다. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> AGIC (Application Gateway 수신 컨트롤러) 추가 기능이 Application Gateway v2 Sku (표준 및 WAF) **만** 지원 하 고 Application Gateway v1 sku는 지원 **하지 않습니다** . 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>기존 Application Gateway를 사용 하 여 기존 AKS 클러스터에서 AGIC 추가 기능을 사용 하도록 설정 

이제 만든 AKS 클러스터에서 AGIC 추가 기능을 사용 하도록 설정 하 고 *myCluster*를 만든 기존 Application Gateway를 사용 하도록 AGIC 추가 *기능을 지정 합니다.* 이 자습서의 시작 부분에서 aks-preview 확장을 추가/업데이트 했는지 확인 합니다. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>두 가상 네트워크를 함께 피어 링

AKS 클러스터를 자체 가상 네트워크에 배포 하 고 다른 가상 네트워크에 Application Gateway 배포 했으므로 트래픽이 Application Gateway에서 클러스터의 pod로 흐르도록 하려면 두 가상 네트워크를 함께 피어 링 해야 합니다. 두 가상 네트워크를 피어 링 하려면 두 번의 Azure CLI 명령을 실행 해야 합니다 .이 경우 연결이 양방향이 되도록 합니다. 첫 번째 명령은 Application Gateway 가상 네트워크에서 AKS 가상 네트워크로의 피어 링 연결을 만듭니다. 두 번째 명령은 다른 방향으로 피어 링 연결을 만듭니다. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>AGIC를 사용 하 여 샘플 응용 프로그램 배포 

이제 AKS 클러스터에 샘플 응용 프로그램을 배포 하 여 수신에 AGIC 추가 기능을 사용 하 고 Application Gateway를 AKS 클러스터에 연결 합니다. 먼저 명령을 실행 하 여 배포한 AKS 클러스터에 대 한 자격 증명을 가져옵니다 `az aks get-credentials` . 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

만든 클러스터에 대 한 자격 증명이 있는 경우 다음 명령을 실행 하 여 클러스터 수신에 AGIC를 사용 하는 샘플 응용 프로그램을 설정 합니다. AGIC은 이전에 해당 라우팅 규칙을 사용 하 여 설정한 Application Gateway를 배포한 새 샘플 응용 프로그램으로 업데이트 합니다.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>응용 프로그램에 연결할 수 있는지 확인

AKS 클러스터에 대 한 트래픽을 제공 하도록 Application Gateway 설정 되었으므로 이제 응용 프로그램에 연결할 수 있는지 확인 합니다. 먼저 수신의 IP 주소를 가져옵니다. 

```azurecli-interactive
kubectl get ingress
```

위의 명령을 실행 하거나를 사용 하 여 가져온 Application Gateway의 IP 주소를 방문 하 여 만든 예제 응용 프로그램이 실행 중인지 확인 `curl` 합니다. 업데이트를 가져오는 데 1 분이 Application Gateway 걸릴 수 있으므로 Application Gateway 포털에서 "업데이트 중" 상태인 경우에는 IP 주소에 도달 하기 전에 완료 되도록 합니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>다음 단계
* [AGIC 추가 기능을 사용 하지 않도록 설정 하는 방법에 대 한 자세한 정보](./ingress-controller-disable-addon.md)
* [AGIC에서 지원 되는 주석에 대 한 자세한 정보](./ingress-controller-annotations.md)
* [AGIC 관련 문제 해결](./ingress-controller-troubleshoot.md)

