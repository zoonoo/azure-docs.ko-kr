---
title: AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기
description: 문제 해결 및 유지 관리 작업은 AKS(Azure Kubernetes Service) 클러스터 노드를 사용하여 SSH 연결을 만드는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 57eacca75d711c5125a2856a7b6219cd2ec5306b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242024"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>유지 관리 또는 문제 해결을 위해 AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH와 연결

AKS(Azure Kubernetes Service) 클러스터의 수명 주기 내내 AKS 노드에 액세스해야 합니다. 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 이 액세스를 사용할 수 있습니다. (현재 AKS에서 미리 보기)는에서 Windows 서버 노드를 포함 하 여 SSH를 사용 하 여 AKS 노드에 액세스할 수 있습니다. 할 수도 있습니다 [원격 데스크톱 프로토콜 (RDP) 연결을 사용 하 여 Windows 서버 노드에 연결할][aks-windows-rdp]합니다. 보안을 위해 AKS 노드는 인터넷에 노출되지 않습니다.

이 문서에서는 개인 IP 주소를 사용하여 AKS 노드와 SSH를 연결하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure cli 버전 2.0.64 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="add-your-public-ssh-key"></a>공용 SSH 키 추가

기본적으로 SSH 키를 가져올 또는 생성 됩니다 AKS 클러스터를 만들 때 노드를 추가 합니다. AKS 클러스터를 만들 때 사용 하는 것 보다 다른 SSH 키를 지정 하는 경우 Linux AKS 노드에 공용 SSH 키를 추가 합니다. 필요한 경우는 SSH 키를 사용 하 여 만들 수 있습니다 [macOS 또는 Linux] [ ssh-nix] 하거나 [Windows][ssh-windows]합니다. PuTTY Gen를 사용 하 여 키 쌍을 만드는 경우에 OpenSSH 키 쌍을 저장 형식 기본값 대신 PuTTy 개인 키 형식 (.ppk 파일).

> [!NOTE]
> SSH 키 수 현재만 추가할 수는 Azure CLI를 사용 하 여 Linux 노드. Windows Server 노드를 사용 하는 경우 AKS 클러스터를 만들 때 제공한 SSH 키를 사용 하 고의 단계로 건너뜁니다 [AKS 노드 주소 가져오기 방법](#get-the-aks-node-address)합니다. 또는 [원격 데스크톱 프로토콜 (RDP) 연결을 사용 하 여 Windows 서버 노드에 연결할][aks-windows-rdp]합니다.

AKS 노드의 개인 IP 주소를 가져오는 단계는 실행 하면 AKS 클러스터의 유형에 따라 다릅니다.

* 대부분의 AKS 클러스터의 경우 다음 단계에 따라 [일반 AKS 클러스터에 대 한 IP 주소를 가져올](#add-ssh-keys-to-regular-aks-clusters)합니다.
* 여러 노드 풀 또는 Windows Server 컨테이너 지원 기능 등의 가상 머신 확장 집합을 사용 하는 AKS에서 미리 보기 기능을 사용 하는 경우 [가상 머신 확장 집합 기반 AKS 클러스터에 대 한 단계](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters)합니다.

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>일반 AKS 클러스터에 SSH 키를 추가 합니다.

SSH 키에 Linux AKS 노드를 추가 하려면 다음 단계를 수행 합니다.

1. [az aks show][az-aks-show]를 사용하여 AKS 클러스터 리소스에 대한 리소스 그룹 이름을 가져옵니다. 사용자 고유의 핵심 리소스 그룹 및 AKS 클러스터 이름을 제공 합니다. 클러스터 이름을 라는 변수에 할당 됩니다 *CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. [az vm list][az-vm-list] 명령을 사용하여 AKS 클러스터 리소스 그룹에서 VM을 나열합니다. 이러한 VM은 AKS 노드입니다.

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    다음 예제 출력에서는 AKS 노드를 보여줍니다.

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. SSH 키를 노드에 추가하려면 [az vm user update][az-vm-user-update] 명령을 사용합니다. 이전 단계에서 가져온 리소스 그룹 이름 및 AKS 노드 중 하나를 차례로 입력합니다. AKS 노드에 대한 사용자 이름은 기본적으로 *azureuser*입니다. *~/.ssh/id_rsa.pub*와 같은 고유한 SSH 공용 키 위치를 입력하거나 SSH 공개 키의 내용을 붙여넣습니다.

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>가상 머신 확장 집합 기반 AKS 클러스터에 SSH 키를 추가 합니다.

SSH 키에는 가상 머신 확장 집합의 일부인 Linux AKS 노드를 추가 하려면 다음 단계를 수행 합니다.

1. [az aks show][az-aks-show]를 사용하여 AKS 클러스터 리소스에 대한 리소스 그룹 이름을 가져옵니다. 사용자 고유의 핵심 리소스 그룹 및 AKS 클러스터 이름을 제공 합니다. 클러스터 이름을 라는 변수에 할당 됩니다 *CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. 다음으로, 사용 하 여 AKS 클러스터에 대해 설정 하는 가상 머신 확장을 가져옵니다 합니다 [az vmss 목록] [ az-vmss-list] 명령입니다. 가상 머신 확장 집합 이름 이라는 변수에 할당 됩니다 *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. SSH 키에는 가상 머신 확장 집합의 노드를 추가 하려면 사용 합니다 [az vmss 확장 집합] [ az-vmss-extension-set] 명령입니다. 클러스터 리소스 그룹 및 가상 머신 확장 집합 이름은 이전 명령에서 제공 됩니다. AKS 노드에 대한 사용자 이름은 기본적으로 *azureuser*입니다. 필요한 경우 업데이트의 고유한 SSH 공용 키 위치, 위치와 같은 *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. SSH 키를 사용 하 여 노드를 적용 합니다 [az vmss update-인스턴스] [ az-vmss-update-instances] 명령:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>AKS 노드 주소 가져오기

AKS 노드는 공개적으로 인터넷에 노출되지 않습니다. AKS 노드에 SSH하려면 개인 IP 주소를 사용합니다. 다음 단계에서 만든 도우미 pod 수 있는 AKS 클러스터의 SSH 노드의이 개인 IP 주소. AKS 노드의 개인 IP 주소를 가져오는 단계는 실행 하면 AKS 클러스터의 유형에 따라 다릅니다.

* 대부분의 AKS 클러스터의 경우 다음 단계에 따라 [일반 AKS 클러스터에 대 한 IP 주소를 가져올](#ssh-to-regular-aks-clusters)합니다.
* 여러 노드 풀 또는 Windows Server 컨테이너 지원 기능 등의 가상 머신 확장 집합을 사용 하는 AKS에서 미리 보기 기능을 사용 하는 경우 [가상 머신 확장 집합 기반 AKS 클러스터에 대 한 단계](#ssh-to-virtual-machine-scale-set-based-aks-clusters)합니다.

### <a name="ssh-to-regular-aks-clusters"></a>일반 AKS 클러스터에 SSH

[az vm list-ip-addresses][az-vm-list-ip-addresses] 명령을 사용하여 AKS 클러스터 노드의 사설 IP 주소를 확인합니다. 이전 [az-aks-show][az-aks-show] 단계에서 가져온 고유한 AKS 클러스터 리소스 그룹 이름을 입력합니다.

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

다음 예제 출력에서는 AKS 노드의 개인 IP 주소를 보여줍니다.

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>가상 머신 확장 집합 기반 AKS 클러스터에 SSH

사용 하 여 노드의 내부 IP 주소를 나열 합니다 [kubectl get 명령][kubectl-get]:

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

문제를 해결 하려는 노드의 내부 IP 주소를 적어둡니다. 이후 단계에서이 주소를 사용 합니다.

## <a name="create-the-ssh-connection"></a>SSH 연결 만들기

AKS 노드에 SSH를 연결하려면 AKS 클러스터에서 도우미 Pod를 실행합니다. 이 도우미 Pod는 클러스터에 대한 SSH 액세스 및 추가 SSH 노드 액세스를 제공합니다. 이 도우미 Pod를 만들고 사용하려면 다음 단계를 수행합니다.

1. `debian` 컨테이너 이미지를 실행하고 여기에 터미널 세션을 연결합니다. 이 컨테이너를 사용하여 AKS 클러스터에서 노드가 있는 SSH 세션을 만들 수 있습니다.

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > (현재 AKS에서 미리 보기)는에서 Windows 서버 노드를 사용 하는 경우 다음과 같은 Linux 노드 상의 Debian 컨테이너를 예약 하는 명령 노드의 선택기를 추가 합니다.
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. 기본 Debian 이미지에는 SSH 구성 요소가 포함되지 않습니다. 터미널 세션이 컨테이너에 연결되면 다음과 같이 `apt-get`을 사용하여 SSH 클라이언트를 설치합니다.

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 컨테이너에 연결되지 않은 새 터미널 창에서 [kubectl get pods][kubectl-get] 명령을 사용하여 AKS 클러스터의 Pod를 나열합니다. 이전 단계에서 만든 Pod는 다음 예제와 같이 *aks-ssh* 이름으로 시작합니다.

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. 이 아티클의 첫 번째 단계에서는 AKS 노드에 공용 SSH 키를 추가했습니다. 이제 Pod에 프라이빗 SSH 키를 복사합니다. 이 프라이빗 키를 사용하여 AKS 노드에 SSH를 만듭니다.

    이전 단계에서 가져온 고유한 *aks-ssh* Pod 이름을 입력합니다. 필요에 따라 *~/.ssh/id_rsa*를 프라이빗 SSH 키의 위치로 변경합니다.

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. 컨테이너에 대한 터미널 세션으로 다시 돌아가서 사용자 읽기 전용이 되도록 복사된 `id_rsa` 프라이빗 SSH 키에 대한 사용 권한을 업데이트합니다.

    ```console
    chmod 0600 id_rsa
    ```

1. 이제 AKS 노드에 대한 SSH 연결을 만듭니다. AKS 노드에 대한 기본 사용자 이름은 *azureuser*입니다. SSH 키를 먼저 신뢰할 수 있다면 계속 연결하기 위한 프롬프트를 수락합니다. 그런 다음, AKS 노드의 bash 프롬프트와 함께 표시됩니다.

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
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
