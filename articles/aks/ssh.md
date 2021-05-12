---
title: AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기
description: 문제 해결 및 유지 관리 작업은 AKS(Azure Kubernetes Service) 클러스터 노드를 사용하여 SSH 연결을 만드는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 4c068dc1521d19793d42b6788d0439dd11499a91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605905"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>유지 관리 또는 문제 해결을 위해 AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH와 연결

AKS(Azure Kubernetes Service) 클러스터의 수명 주기 내내 AKS 노드에 액세스해야 합니다. 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 이 액세스를 사용할 수 있습니다. Windows Server 노드를 비롯해 SSH를 사용하여 AKS 노드에 액세스할 수 있습니다. [RDP(원격 데스크톱 프로토콜) 연결을 사용하여 Windows Server 노드에 연결][aks-windows-rdp]할 수도 있습니다. 보안을 위해 AKS 노드는 인터넷에 노출되지 않습니다. AKS 노드에 SSH하려면 개인 IP 주소를 사용합니다.

이 문서에서는 개인 IP 주소를 사용하여 AKS 노드와 SSH를 연결하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

기본적으로 SSH 키는 AKS 클러스터를 만들 때 가져오거나 생성된 다음 노드에 추가됩니다. 이 문서에서는 AKS 클러스터를 만들 때 사용된 SSH 키와는 다른 SSH 키를 지정하는 방법을 보여 줍니다. 또한 이 문서에서는 노드의 개인 IP 주소를 확인하고 SSH를 사용하여 연결하는 방법을 보여 줍니다. 다른 SSH 키를 지정할 필요가 없는 경우 노드에 SSH 공개 키를 추가하는 단계를 건너뛸 수 있습니다.

이 문서에서는 SSH 키가 있다고 가정합니다. [macOS, Linux][ssh-nix] 또는 [Windows][ssh-windows]를 사용하여 SSH 키를 만들 수 있습니다. PuTTY Gen을 사용하여 키 쌍을 만드는 경우 기본 PuTTy 프라이빗 키 형식(.ppk 파일) 대신 OpenSSH 형식으로 키 쌍을 저장합니다.

또한 Azure CLI 버전 2.0.64 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>SSH 액세스를 위한 가상 머신 확장 집합 기반 AKS 클러스터 구성

SSH 액세스를 위한 가상 머신 확장 집합 기반을 구성하려면 클러스터의 가상 머신 확장 집합의 이름을 찾고 SSH 공개 키를 해당 확장 집합에 추가합니다.

[az aks show][az-aks-show] 명령을 사용하여 AKS 클러스터의 리소스 그룹 이름을 가져온 다음 [az vmss list][az-vmss-list] 명령을 사용하여 확장 집합의 이름을 가져옵니다.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

위의 예제에서는 *CLUSTER_RESOURCE_GROUP* 에 *myResourceGroup* 의 *myAKSCluster* 에 대한 클러스터 리소스 그룹의 이름을 할당합니다. 그런 다음 해당 예제에서는 *CLUSTER_RESOURCE_GROUP* 을 사용하여 확장 집합 이름을 나열하고 *SCALE_SET_NAME* 에 할당합니다.

> [!IMPORTANT]
> 현재는 Azure CLI를 사용하여 가상 머신 확장 집합 기반 AKS 클러스터에 대한 SSH 키만 업데이트해야 합니다.
> 
> Linux 노드의 경우 현재 Azure CLI를 사용해야만 SSH 키를 추가할 수 있습니다. SSH를 사용하여 Windows Server 노드에 연결하려면 AKS 클러스터를 만들 때 제공된 SSH 키를 사용하고 SSH 공개 키를 추가하기 위한 다음 명령 집합을 건너뜁니다. 문제를 해결하려는 노드의 IP 주소가 여전히 필요합니다. 해당 주소는 이 섹션의 마지막 명령을 사용하면 표시됩니다. 또는 SSH를 사용하는 대신 [RDP(원격 데스크톱 프로토콜) 연결을 사용하여 Windows Server 노드에 연결][aks-windows-rdp]할 수 있습니다.

SSH 키를 가상 머신 확장 집합의 노드에 추가하려면 [az vmss extension set][az-vmss-extension-set] 및 [az vmss update-instances][az-vmss-update-instances] 명령을 사용합니다.

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

위의 예제에서는 이전 명령의 *CLUSTER_RESOURCE_GROUP* 변수와 *SCALE_SET_NAME* 변수를 사용합니다. 위의 예제에서는 SSH 공개 키의 위치로 *~/.ssh/id_rsa.pub* 도 사용합니다.

> [!NOTE]
> AKS 노드에 대한 사용자 이름은 기본적으로 *azureuser* 입니다.

확장 집합에 SSH 공개 키를 추가하면 가상 머신의 IP 주소를 사용하여 해당 확장 집합의 노드 가상 머신으로 SSH를 사용할 수 있습니다. [kubectl get 명령][kubectl-get]을 사용하여 AKS 클러스터 노드의 개인 IP 주소를 확인합니다.

```console
kubectl get nodes -o wide
```

다음 예제 출력값에서는 Windows Server 노드를 비롯한 클러스터에 있는 모든 노드의 내부 IP 주소를 보여 줍니다.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

문제 해결이 필요한 노드의 내부 IP 주소를 기록합니다.

SSH를 사용하여 노드에 액세스하려면 [SSH 연결 만들기](#create-the-ssh-connection)의 단계를 수행합니다.

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>SSH 액세스를 위한 가상 머신 가용성 집합 기반 AKS 클러스터 구성

SSH 액세스를 위한 가상 머신 가용성 집합 기반 AKS 클러스터를 구성하려면 클러스터의 Linux 노드 이름을 찾고 SSH 공개 키를 해당 노드에 추가합니다.

[az aks show][az-aks-show] 명령을 사용하여 AKS 클러스터의 리소스 그룹 이름을 가져온 다음 [az vm list][az-vm-list] 명령을 사용하여 클러스터의 Linux 노드에 대한 가상 머신 이름을 나열합니다.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

위의 예제에서는 *CLUSTER_RESOURCE_GROUP* 에 *myResourceGroup* 의 *myAKSCluster* 에 대한 클러스터 리소스 그룹의 이름을 할당합니다. 그런 다음 해당 예제에서는 *CLUSTER_RESOURCE_GROUP* 을 사용하여 가상 머신 이름을 나열합니다. 예제 출력값에는 가상 머신의 이름이 표시됩니다.

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

SSH 키를 노드에 추가하려면 [az vm user update][az-vm-user-update] 명령을 사용합니다.

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

위의 예제에서는 이전 명령의 *CLUSTER_RESOURCE_GROUP* 변수와 노드 가상 머신 이름을 사용합니다. 위의 예제에서는 SSH 공개 키의 위치로 *~/.ssh/id_rsa.pub* 도 사용합니다. 경로를 지정하는 대신 SSH 공개 키의 내용을 사용할 수도 있습니다.

> [!NOTE]
> AKS 노드에 대한 사용자 이름은 기본적으로 *azureuser* 입니다.

SSH 공개 키를 노드 가상 머신에 추가하면 가상 머신의 IP 주소를 사용하여 해당 가상 머신에 SSH를 사용할 수 있습니다. [az vm list-ip-addresses][az-vm-list-ip-addresses] 명령을 사용하여 AKS 클러스터 노드의 개인 IP 주소를 확인합니다.

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

위의 예제에서는 이전 명령에서 설정된 *CLUSTER_RESOURCE_GROUP* 변수를 사용합니다. 다음 예제 출력에서는 AKS 노드의 개인 IP 주소를 보여줍니다.

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>SSH 연결 만들기

AKS 노드에 SSH를 연결하려면 AKS 클러스터에서 도우미 Pod를 실행합니다. 이 도우미 Pod는 클러스터에 대한 SSH 액세스 및 추가 SSH 노드 액세스를 제공합니다. 이 도우미 Pod를 만들고 사용하려면 다음 단계를 수행합니다.

1. `debian` 컨테이너 이미지를 실행하고 여기에 터미널 세션을 연결합니다. 이 컨테이너를 사용하여 AKS 클러스터에서 노드가 있는 SSH 세션을 만들 수 있습니다.

    ```console
    kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
    ```

    > [!TIP]
    > Windows Server 노드를 사용하는 경우 명령에 노드 선택기를 추가하여 Linux 노드에서 Debian 컨테이너를 예약합니다.
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. 터미널 세션이 컨테이너에 연결되면 `apt-get`을 사용하여 SSH 클라이언트를 설치합니다.

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 컨테이너에 연결되지 않은 새 터미널 창을 열고 프라이빗 SSH 키를 도우미 Pod에 복사합니다. 해당 프라이빗 키를 사용하여 AKS 노드에 SSH를 만듭니다. 

   필요에 따라 *~/.ssh/id_rsa* 를 프라이빗 SSH 키의 위치로 변경합니다.

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. 컨테이너에 대한 터미널 세션으로 돌아가서 복사된 `id_rsa` 프라이빗 SSH 키에 대한 사용 권한을 업데이트해 사용자 읽기 전용이 되도록 합니다.

    ```console
    chmod 0400 id_rsa
    ```

1. AKS 노드에 대한 SSH 연결을 만듭니다. AKS 노드에 대한 기본 사용자 이름은 *azureuser* 입니다. SSH 키를 먼저 신뢰할 수 있다면 계속 연결하기 위한 프롬프트를 수락합니다. 그런 다음, AKS 노드의 bash 프롬프트와 함께 표시됩니다.

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH 액세스 제거

작업이 완료되면 SSH 세션을 `exit`한 다음, 대화형 컨테이너 세션을 `exit`합니다. 이 컨테이너 세션을 닫을 때 AKS 클러스터에서 SSH 액세스에 사용되는 Pod가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

추가 문제 해결 데이터가 필요하면 [kubelet 로그를 확인][view-kubelet-logs]하거나 [Kubernetes 마스터 노드 로그를 확인][view-master-logs]하면 됩니다.

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances