---
title: RDP에서 AKS 윈도우 서버 노드로
titleSuffix: Azure Kubernetes Service
description: 문제 해결 및 유지 관리 작업을 위해 AKS(Azure Kubernetes) 클러스터 Windows 서버 노드를 사용하여 RDP 연결을 만드는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 140d59894b38c7f07f16b0ac3cf99316c201d120
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886792"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>유지 보수 또는 문제 해결을 위해 RDP와 AKS(Azure Kubernetes) 클러스터 Windows 서버 노드에 연결

AZURE Kubernetes 서비스(AKS) 클러스터의 수명 주기 동안 AKS Windows 서버 노드에 액세스해야 할 수 있습니다. 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 이 액세스를 사용할 수 있습니다. RDP를 사용하여 AKS Windows 서버 노드에 액세스할 수 있습니다. 또는 SSH를 사용하여 AKS Windows Server 노드에 액세스하고 클러스터 를 만드는 동안 사용된 것과 동일한 키 쌍에 액세스할 수 있는 경우 SSH의 단계를 따라 [Azure Kubernetes 서비스(AKS) 클러스터 노드로 변환할][ssh-steps]수 있습니다. 보안을 위해 AKS 노드는 인터넷에 노출되지 않습니다.

Windows 서버 노드 지원은 현재 AKS에서 미리 보기 상태입니다.

이 문서에서는 개인 IP 주소를 사용하여 AKS 노드와 RDP 연결을 만드는 방법을 보여 주었습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Windows Server 노드가 있는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 Azure [CLI를 사용하여 Windows 컨테이너를 사용하여 AKS 클러스터를 만드는][aks-windows-cli]방법에 대한 문서를 참조하세요. 문제 해결하려는 Windows Server 노드의 Windows 관리자 사용자 이름과 암호가 필요합니다. 또한 [Microsoft 원격 데스크톱과][rdp-mac]같은 RDP 클라이언트가 필요합니다.

또한 Azure CLI 버전 2.0.61 이상설치 및 구성이 필요합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>클러스터와 동일한 서브넷에 가상 머신 배포

AKS 클러스터의 Windows 서버 노드에는 외부에서 액세스할 수 있는 IP 주소가 없습니다. RDP 연결을 만들려면 공개적으로 액세스할 수 있는 IP 주소가 있는 가상 컴퓨터를 Windows Server 노드와 동일한 서브넷에 배포할 수 있습니다.

다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myVM이라는* 가상 컴퓨터를 만듭니다.

먼저 Windows Server 노드 풀에서 사용하는 서브넷을 가져옵니다. 서브넷 ID를 얻으려면 서브넷의 이름이 필요합니다. 서브넷의 이름을 얻으려면 vnet의 이름이 필요합니다. 클러스터의 네트워크 목록을 쿼리하여 vnet 이름을 가져옵니다. 클러스터를 쿼리하려면 해당 이름이 필요합니다. Azure 클라우드 셸에서 다음을 실행하여 이러한 모든 것을 얻을 수 있습니다.

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

이제 SUBNET_ID 동일한 Azure Cloud Shell 창에서 다음 명령을 실행하여 VM을 만듭니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

다음 예제 출력은 VM이 성공적으로 생성되었으며 가상 시스템의 공용 IP 주소를 표시합니다.

```console
13.62.204.18
```

가상 시스템의 공용 IP 주소를 기록합니다. 이후 단계에서 이 주소를 사용합니다.

## <a name="allow-access-to-the-virtual-machine"></a>가상 시스템에 대한 액세스 허용

AKS 노드 풀 서브넷은 기본적으로 NSG(네트워크 보안 그룹)로 보호됩니다. 가상 컴퓨터에 액세스하려면 NSG에서 액세스를 활성화해야 합니다.

> [!NOTE]
> NSG는 AKS 서비스에 의해 제어됩니다. NSG에 대한 모든 변경 사항은 언제든지 제어평면에 의해 덮어씁니다.
>

먼저 nsg의 리소스 그룹과 nsg 이름을 사용하여 규칙을 추가합니다.

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

그런 다음 NSG 규칙을 만듭니다.

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>노드 주소 받기

Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli][az-aks-install-cli] 명령을 사용합니다.
    
```azurecli-interactive
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

[kubectl get][kubectl-get] 명령을 사용하여 Windows Server 노드의 내부 IP 주소를 나열합니다.

```console
kubectl get nodes -o wide
```

다음 예제 출력은 Windows Server 노드를 포함하여 클러스터의 모든 노드의 내부 IP 주소를 보여 주며, 이 출력은 클러스터의 모든 노드의 내부 IP 주소를 보여 주며, 이 출력은 Windows Server 노드를 포함합니다.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

트러블슈팅을 원하는 Windows Server 노드의 내부 IP 주소를 기록합니다. 이후 단계에서 이 주소를 사용합니다.

## <a name="connect-to-the-virtual-machine-and-node"></a>가상 컴퓨터 및 노드에 연결

[Microsoft 원격 데스크톱과][rdp-mac]같은 RDP 클라이언트를 사용하여 이전에 만든 가상 시스템의 공용 IP 주소에 연결합니다.

![RDP 클라이언트를 사용하여 가상 시스템에 연결하는 이미지](media/rdp/vm-rdp.png)

가상 컴퓨터에 연결한 후 가상 시스템 내에서 RDP 클라이언트를 사용하여 문제를 해결하려는 Windows Server 노드의 *내부 IP 주소에* 연결합니다.

![RDP 클라이언트를 사용하여 Windows 서버 노드에 연결하는 이미지](media/rdp/node-rdp.png)

이제 Windows 서버 노드에 연결됩니다.

![Windows 서버 노드의 cmd 창 이미지](media/rdp/node-session.png)

이제 *cmd* 창에서 문제 해결 명령을 실행할 수 있습니다. Windows 서버 노드는 Windows 서버 코어를 사용하기 때문에 RDP를 통해 Windows 서버 노드에 연결할 때 전체 GUI 또는 기타 GUI 도구가 없습니다.

## <a name="remove-rdp-access"></a>RDP 액세스 제거

완료되면 Windows 서버 노드로 RDP 연결을 종료한 다음 RDP 세션을 가상 컴퓨터로 종료합니다. 두 RDP 세션을 모두 종료한 후 [az vm delete][az-vm-delete] 명령을 통해 가상 컴퓨터를 삭제합니다.

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

그리고 NSG 규칙 :

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>다음 단계

추가 문제 해결 데이터가 필요한 경우 [Kubernetes 마스터 노드 로그][view-master-logs] 또는 [Azure 모니터][azure-monitor-containers]를 볼 수 있습니다.

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
