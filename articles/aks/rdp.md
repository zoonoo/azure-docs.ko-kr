---
title: Azure Kubernetes Service (AKS) 클러스터 Windows Server 노드에 대 한 RDP
description: 문제 해결 및 유지 관리 작업에 대 한 Windows 서버 노드에서 Azure Kubernetes Service (AKS) 클러스터를 사용 하 여 RDP 연결을 만드는 방법에 알아봅니다.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: twhitney
ms.openlocfilehash: 11f6869d4d5a2ee0ef2e986ee8268c7a001ea015
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688640"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Azure Kubernetes Service (AKS)를 Windows Server 클러스터 유지 관리 또는 문제 해결에 대 한 RDP를 사용 하 여 연결

Azure Kubernetes Service (AKS) 클러스터의 수명 주기 동안 Windows Server AKS 노드에 액세스 해야 합니다. 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 이 액세스를 사용할 수 있습니다. RDP를 사용 하 여 AKS Windows 서버 노드에서 액세스할 수 있습니다. 또는 Windows Server AKS 노드에 액세스 하려면 SSH를 사용 하려는 경우 클러스터를 만드는 동안 사용 된 동일한 쌍에 액세스할 수 있는 수는의 단계를 수행 [Azure Kubernetes Service (AKS) 클러스터 노드에 SSH] [ssh-steps]. 보안을 위해 AKS 노드는 인터넷에 노출되지 않습니다.

Windows Server 노드 지원은 현재 AKS에서 미리 보기로 제공 됩니다.

이 문서에서는 개인 IP 주소를 사용 하 여 AKS 노드를 사용 하 여 RDP 연결을 만드는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Windows 서버 노드를 사용 하 여 기존 AKS 클러스터 있다고 가정 합니다. AKS 클러스터에 필요한 경우 문서를 참조 [Azure CLI를 사용 하 여 Windows 컨테이너를 사용 하 여 AKS 클러스터를 만들][aks-windows-cli]합니다. 문제를 해결 하려면 Windows Server 노드에 대 한 Windows 관리자 사용자 이름 및 암호가 필요 합니다. 또한 RDP 클라이언트가 필요와 같은 [Microsoft 원격 데스크톱][rdp-mac]합니다.

또한 Azure cli 버전 2.0.61 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>클러스터와 동일한 서브넷에 가상 컴퓨터를 배포 합니다.

Windows Server에 대 한 노드의 AKS 클러스터 IP 주소 외부에서 액세스할 수 없습니다. RDP 연결을 위해 Windows 서버 노드를 동일한 서브넷에 공개적으로 액세스 가능한 IP 주소를 사용 하 여 가상 컴퓨터를 배포할 수 있습니다.

다음 예제에서는 명명 된 가상 머신을 만듭니다 *myVM* 에 *myResourceGroup* 리소스 그룹입니다.

먼저 Windows Server 노드 풀에서 사용 하는 서브넷을 가져옵니다. 서브넷 id를 가져오려면 서브넷의 이름을 알아야 합니다. 서브넷의 이름을 가져오려면 vnet의 이름이 필요 합니다. 클러스터 네트워크 목록에 대 한 쿼리를 통해 vnet 이름을 가져옵니다. 클러스터를 쿼리하려면 해당 이름이 필요 합니다. Azure Cloud Shell에서 다음을 실행 하 여 이러한 모든를 가져올 수 있습니다.

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

SUBNET_ID 했으므로 VM을 만드는 동일한 Azure Cloud Shell 창에서 다음 명령을 실행 합니다.

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

다음 예제 출력에서는 VM에 성공적으로 만들어졌으며 가상 머신의 공용 IP 주소를 표시를 보여 줍니다.

```console
13.62.204.18
```

가상 머신의 공용 IP 주소를 기록 합니다. 이후 단계에서이 주소를 사용 합니다.

## <a name="get-the-node-address"></a>노드 주소 가져오기

Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli][az-aks-install-cli] 명령을 사용합니다.
    
```azurecli-interactive
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

사용 하 여 Windows Server 노드의 내부 IP 주소를 나열 합니다 [kubectl get] [ kubectl-get] 명령:

```console
kubectl get nodes -o wide
```

다음 예제 출력에는 Windows 서버 노드를 포함 하 여 클러스터의 모든 노드의 내부 IP 주소를 보여 줍니다.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

문제를 해결 하려면 Windows Server 노드의 내부 IP 주소를 적어둡니다. 이후 단계에서이 주소를 사용 합니다.

## <a name="connect-to-the-virtual-machine-and-node"></a>가상 머신 및 노드 연결

앞서와 같은 RDP 클라이언트가 사용 하는 것이 만든 가상 머신의 공용 IP 주소에 연결 [Microsoft 원격 데스크톱][rdp-mac]합니다.

![RDP 클라이언트를 사용 하 여 가상 머신에 연결 하는 이미지](media/rdp/vm-rdp.png)

가상 컴퓨터에 연결한 후에 연결 합니다 *내부 IP 주소* 가상 컴퓨터 내에서 RDP 클라이언트를 사용 하 여 문제를 해결 하려면 Windows Server 노드.

![RDP 클라이언트를 사용 하 여 Windows Server 노드로 연결 이미지](media/rdp/node-rdp.png)

이제 Windows Server 노드로 연결 되어 있습니다.

![Windows Server 노드에 cmd 창의 이미지](media/rdp/node-session.png)

이제 문제 해결 명령에서 실행할 수는 *cmd* 창입니다. Windows 서버 노드에서 Windows Server Core를 사용 하므로 않습니다 전체 GUI 또는 기타 GUI 도구 RDP를 통해 Windows Server 노드에 연결한 경우.

## <a name="remove-rdp-access"></a>RDP 액세스를 제거 합니다.

을 완료 한 후 Windows Server 노드로 RDP 연결을 종료 한 후 가상 머신에 대 한 RDP 세션을 종료 합니다. RDP 세션을 모두를 종료 한 후 사용 하 여 가상 컴퓨터를 삭제 합니다 [az vm 삭제] [ az-vm-delete] 명령:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>다음 단계

추가 문제 해결 데이터를 해야 할 수 있습니다 [Kubernetes 마스터 노드 로그 보기] [ view-master-logs] 하거나 [Azure Monitor][azure-monitor-containers]합니다.

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
