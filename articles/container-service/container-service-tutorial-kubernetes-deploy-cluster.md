---
title: "Azure Container Service 자습서 - 클러스터 배포 | Microsoft Docs"
description: "Azure Container Service 자습서 - 클러스터 배포"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 1d3006b2100e84920cdfeb7a0762213a93f9a8a5
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Azure Container Service에서 Kubernetes 클러스터 배포

Kubernetes는 컨테이너화된 최신 응용 프로그램 실행을 위한 분산 플랫폼을 제공합니다. Azure Container Service를 통해 프로덕션이 준비된 Kubernetes 클러스터를 프로비전하는 작업은 간단하고 빠릅니다. 이 빠른 시작에서는 Kubernetes 클러스터를 배포하는 데 필요한 기본 단계를 자세히 설명합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Kubernetes ACS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * kubectl 구성

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 컨테이너 이미지를 만들어 Azure Container Registry 인스턴스에 업로드했습니다. 이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

[이전 자습서](./container-service-tutorial-kubernetes-prepare-acr.md)에서 *myResourceGroup*이라는 리소스 그룹을 만들었습니다. 만들지 않은 경우 지금 이 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az acs create](/cli/azure/acs#create) 명령을 사용하여 Azure Container Service에서 Kubernetes 클러스터를 만듭니다. 

다음 예제에서는 하나의 Linux 마스터 노드와 세 개의 Linux 에이전트 노드가 있는 *myK8sCluster*라는 클러스터를 만듭니다. 아직 이러한 노드가 없는 경우 SSH 키가 만들어집니다. 기본 위치가 아닌 위치에 있는 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azurecli-interactive 
az acs create --orchestrator-type=kubernetes --resource-group myResourceGroup --name=myK8SCluster --generate-ssh-keys 
```

몇 분 후 명령이 완료되고 ACS 배포에 대한 정보가 반환됩니다.

## <a name="install-the-kubectl-cli"></a>kubectl CLI 설치

클라이언트 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)을 사용합니다. 

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 로컬로 설치하려면 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 명령을 사용합니다.

Linux 또는 macOS에서 실행하는 경우 sudo를 사용하여 실행해야 할 수 있습니다. Windows에서는 셸이 관리자 권한으로 실행되었는지 확인합니다.

```azurecli-interactive 
az acs kubernetes install-cli 
```

Windows에서 기본 설치는 *c:\program files (x86)\kubectl.exe*입니다. Windows 경로에 이 파일을 추가해야 할 수 있습니다. 

## <a name="connect-with-kubectl"></a>Kubectl로 연결

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 명령을 실행합니다.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8SCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 실행합니다.

```bash
kubectl get nodes
```

출력:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

이 자습서를 마치면 ACS Kubernetes 클러스터가 워크로드에 대해 준비됩니다. 이후 자습서에서는 다중 컨테이너 응용 프로그램이 이 클러스터에 배포, 규모 확장, 업데이트 및 모니터링됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Service Kubernetes 클러스터를 배포했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Kubernetes ACS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * kubectl 구성

다음 자습서로 이동하여 클러스터에서 응용 프로그램 실행에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes에서 응용 프로그램 배포](./container-service-tutorial-kubernetes-deploy-application.md)
