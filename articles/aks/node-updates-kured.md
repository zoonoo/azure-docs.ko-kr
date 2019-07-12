---
title: 업데이트 및 kured Azure Kubernetes Service (AKS)에서 사용 하 여 Linux 노드를 다시 부팅
description: Linux 노드를 업데이트 하 고 자동으로 kured Azure Kubernetes Service (AKS)에서 사용 하 여 다시 부팅 하는 방법을 알아봅니다
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 580d1316c2bfc6514a148ed6fba78a8e77bd880e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614916"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Linux 노드에 Azure Kubernetes Service (AKS)에서 보안 및 커널 업데이트 적용

클러스터를 보호 하려면 보안 업데이트가 AKS의 Linux 노드를 자동으로 적용 됩니다. 이러한 업데이트는 OS 보안 수정 사항 또는 커널 업데이트를 포함합니다. 이러한 업데이트의 일부는 프로세스를 완료하도록 노드를 다시 부팅해야 합니다. AKS는 업데이트 프로세스를 완료 하려면 이러한 Linux 노드를 자동으로 재부팅 하지 않습니다.

(현재 AKS에서 미리 보기)는에서 Windows 서버 노드를 최신 상태로 유지 하는 프로세스는 약간 다릅니다. Windows Server 노드에 매일 업데이트를 수신 하지 않습니다. 대신 최신 기본 Windows Server 이미지 및 패치를 사용 하 여 새 노드를 배포 하는 AKS 업그레이드를 수행 합니다. Windows 서버 노드를 사용 하는 AKS 클러스터를 참조 하세요 [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.

이 문서에서는 오픈 소스를 사용 하는 방법을 보여 줍니다 [kured (KUbernetes 재부팅 디먼)][kured] pod 및 노드를 실행 하는 디스플레이 창를 자동으로 처리 한 다음를 다시 부팅 해야 하는 Linux 노드를 다시 부팅 프로세스를 시청 하려면.

> [!NOTE]
> `Kured`는 Weaveworks에서 제공되는 오픈 소스 프로젝트입니다. AKS에서 이 프로젝트에 대한 지원은 최상의 노력을 기준으로 제공됩니다. 추가 지원은 #weave 커뮤니티 slack 채널에서 찾을 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터에 필요한 경우 AKS 빠른 시작을 참조 하세요 [Azure CLI를 사용 하 여][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]합니다.

또한 Azure cli 버전 2.0.59 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드를 참조 해야 하는 경우 [Azure CLI 설치][install-azure-cli]합니다.

## <a name="understand-the-aks-node-update-experience"></a>AKS 노드 업데이트 환경 이해

AKS 클러스터에서 Kubernetes 노드는 Azure VM(가상 머신)으로 실행됩니다. 이러한 Linux 기반 VM은 매일 밤 업데이트를 자동으로 확인하도록 구성된 OS와 함께 Ubuntu 이미지를 사용합니다. 보안 또는 커널 업데이트를 사용할 수 있는 경우 자동으로 다운로드되고 설치됩니다.

![kured를 사용하여 AKS 노드 업데이트 및 프로세스 다시 부팅](media/node-updates-kured/node-reboot-process.png)

커널 업데이트와 같은 일부 보안 업데이트에서는 프로세스를 완료하기 위해 노드를 다시 부팅해야 합니다. 다시 부팅 해야 하는 Linux 노드 라는 파일을 만듭니다 */var/run/reboot-required*합니다. 이 다시 부팅 프로세스는 자동으로 발생하지 않습니다.

사용자 고유의 워크플로 및 프로세스를 사용하여 노드 다시 부팅을 처리하거나 `kured`를 사용하여 프로세스를 오케스트레이션할 수 있습니다. 사용 하 여 `kured`, [DaemonSet][DaemonSet] 배포 된 클러스터의 각 Linux 노드에서 pod를 실행 하는 합니다. DaemonSet의 이러한 Pod는 */var/run/reboot-required* 파일의 존재 여부를 살펴본 다음, 프로세스를 시작하여 노드를 다시 부팅합니다.

### <a name="node-upgrades"></a>노드 업그레이드

AKS에 클러스터를 *업그레이드*할 수 있는 추가 프로세스가 있습니다. 업그레이드는 일반적으로 노드 보안 업데이트를 적용하는 것 뿐만 아니라 Kubernetes의 최신 버전으로 이동하는 것입니다. AKS 업그레이드는 다음 작업을 수행합니다.

* 새 노드가 최신 보안 업데이트 및 적용된 Kubernetes 버전으로 배포됩니다.
* 이전 노드가 통제되고 드레이닝됩니다.
* Pod는 새 노드에서 예약됩니다.
* 이전 노드가 삭제됩니다.

업그레이드 이벤트 중 동일한 Kubernetes 버전에 있을 수 없습니다. Kubernetes의 최신 버전을 지정해야 합니다. Kubernetes의 최신 버전으로 업그레이드 하려면 [AKS 클러스터 업그레이드][aks-upgrade]합니다.

## <a name="deploy-kured-in-an-aks-cluster"></a>AKS 클러스터에서 kured 배포

`kured` DaemonSet을 배포하려면 해당 GitHub 프로젝트 페이지에서 다음 샘플 YAML 매니페스트를 적용합니다. 이 매니페스트는 역할 및 클러스터 역할, 바인딩 및 서비스 계정을 만든 다음, AKS 클러스터 1.9 이상을 지원하는 `kured` 버전 1.1.0을 사용하여 DaemonSet을 배포합니다.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.2.0/kured-1.2.0-dockerhub.yaml
```

Prometheus 또는 Slack과 통합과 같은 `kured`에 대한 추가 매개 변수를 구성할 수도 있습니다. 추가 구성 매개 변수에 대 한 자세한 내용은 참조는 [kured 설치 docs][kured-install]합니다.

## <a name="update-cluster-nodes"></a>클러스터 노드 업데이트

AKS의 Linux 노드는 기본적으로 매일 저녁 업데이트에 대 한 확인합니다. 기다리지 않으려는 경우 `kured`가 올바르게 실행되는지 확인하도록 업데이트를 수동으로 수행할 수 있습니다. 먼저 다음 단계에 따라 [AKS 노드 중 하나에 대 한 SSH][aks-ssh]합니다. Linux 노드에 SSH 연결을 만든 후 업데이트 확인 하 고 다음과 같이 적용 합니다.

```console
sudo apt-get update && sudo apt-get upgrade -y
```

노드를 재부팅해야 하는 업데이트가 적용된 경우 파일은 */var/run/reboot-required*에 작성됩니다. `Kured`는 다시 부팅해야 하는 노드를 기본적으로 60분마다 확인합니다.

## <a name="monitor-and-review-reboot-process"></a>다시 부팅 프로세스 모니터링 및 검토

DaemonSet의 복제본 중 하나가 노드 다시 부팅이 필요한 것을 감지한 경우 Kubernetes API를 통해 노드에 잠금이 배치됩니다. 이 잠금은 추가 Pod가 노드에서 예약되는 것을 방지합니다. 또한 잠금은 한 번에 하나의 노드만 다시 부팅되어야 함을 나타냅니다. 노드 통제가 꺼지면 실행 중인 Pod가 노드에서 드레이닝된 다음, 노드가 다시 부팅됩니다.

사용 하 여 노드의 상태를 모니터링할 수 있습니다 합니다 [kubectl get 노드][kubectl-get-nodes] 명령입니다. 다음 예제 출력은 노드가 다시 부팅 프로세스를 준비하는 동안 *SchedulingDisabled*의 상태로 노드를 보여줍니다.

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

사용 하 여 노드의 상태를 볼 수 업데이트 프로세스가 완료 되 면 합니다 [kubectl get 노드][kubectl-get-nodes] 명령과 `--output wide` 매개 변수입니다. 이 추가 출력을 통해 다음 예제 출력에 표시된 것처럼 기본 노드의 *KERNEL-VERSION*에서 차이점을 확인할 수 있습니다. 합니다 *nodepool1 28993262 0 aks* 표시 커널 버전을 이전 단계에서 업데이트 되었습니다 *4.15.0-1039-azure*합니다. 노드 *nodepool1 28993262 1 aks* 는 업데이트 된 표시 커널 버전 이야기가 *4.15.0-1037-azure*합니다.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>다음 단계

이 문서에 사용 하는 방법을 자세히 설명 `kured` Linux 노드를 보안 업데이트 프로세스의 일부로 자동으로 다시 부팅 합니다. Kubernetes의 최신 버전으로 업그레이드 하려면 [AKS 클러스터 업그레이드][aks-upgrade]합니다.

Windows 서버 노드를 사용 하는 AKS 클러스터를 참조 하세요 [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
