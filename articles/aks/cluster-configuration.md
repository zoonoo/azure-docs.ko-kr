---
title: AKS(Azure Kubernetes Service)의 클러스터 구성
description: AKS(Azure Kubernetes Service)에서 클러스터를 구성하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eaf512915532b482c25e830cd9f2e01d61aa4524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572780"
---
# <a name="configure-an-aks-cluster"></a>AKS 클러스터 구성

AKS 클러스터를 만드는 과정에서 필요에 따라 클러스터 구성을 사용자 지정해야 할 수 있습니다. 이 문서에서는 AKS 클러스터를 사용자 지정하기 위한 몇 가지 옵션을 소개합니다.

## <a name="os-configuration"></a>OS 구성

AKS는 이제 1.18 1.18 보다 낮은 kubernetes 버전의 클러스터에 대 한 GA (일반 공급)에서 기본 노드 (운영 체제)로 Ubuntu 18.04를 지원 합니다. AKS Ubuntu 16.04은 여전히 기본 기본 이미지입니다. Kubernetes v 1.18 이상에서 기본 기본은 AKS Ubuntu 18.04입니다.

> [!IMPORTANT]
> Kubernetes v 1.18에 생성 되는 노드 풀은 노드 이미지에 대 한 기본값 보다 높습니다 `AKS Ubuntu 18.04` . 1.18 미만의 지원 되는 Kubernetes 버전의 노드 풀은 노드 `AKS Ubuntu 16.04` 이미지로 수신 하지만 `AKS Ubuntu 18.04` 노드 풀 Kubernetes 버전이 v 1.18 이상으로 업데이트 되 면 업데이트 됩니다.
> 
> 1.18 이상에서 클러스터를 사용 하기 전에 AKS Ubuntu 18.04 노드 풀에서 워크 로드를 테스트 하는 것이 좋습니다.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>새 클러스터에서 GA (AKS Ubuntu 18.04) 사용

Kubernetes v 1.18에 생성 되는 클러스터는 노드 이미지에 대 한 기본값 보다 높습니다 `AKS Ubuntu 18.04` . 1.18 보다 작은 지원 되는 Kubernetes 버전의 노드 풀은 여전히 `AKS Ubuntu 16.04` 노드 이미지로 수신 되지만 `AKS Ubuntu 18.04` 클러스터 또는 노드 풀 Kubernetes 버전이 v 1.18 이상으로 업데이트 되 면로 업데이트 됩니다.

1.18 이상에서 클러스터를 사용 하기 전에 AKS Ubuntu 18.04 노드 풀에서 워크 로드를 테스트 하는 것이 좋습니다.

노드 이미지를 사용 하 여 클러스터를 만들려면 `AKS Ubuntu 18.04` 아래와 같이 kubernetes v 1.18 이상을 실행 하는 클러스터를 만듭니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>기존 클러스터에서 GA (AKS Ubuntu 18.04) 사용

Kubernetes v 1.18에 생성 되는 클러스터는 노드 이미지에 대 한 기본값 보다 높습니다 `AKS Ubuntu 18.04` . 1.18 보다 작은 지원 되는 Kubernetes 버전의 노드 풀은 여전히 `AKS Ubuntu 16.04` 노드 이미지로 수신 되지만 `AKS Ubuntu 18.04` 클러스터 또는 노드 풀 Kubernetes 버전이 v 1.18 이상으로 업데이트 되 면로 업데이트 됩니다.

1.18 이상에서 클러스터를 사용 하기 전에 AKS Ubuntu 18.04 노드 풀에서 워크 로드를 테스트 하는 것이 좋습니다.

클러스터 또는 노드 풀이 노드 이미지에 대해 준비 된 경우 `AKS Ubuntu 18.04` 아래와 같이 v 1.18 이상으로 업그레이드할 수 있습니다.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

하나의 노드 풀만 업그레이드 하려는 경우:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>기존 클러스터의 GA (AKS Ubuntu 18.04) 테스트

Kubernetes v 1.18에 생성 되는 노드 풀은 노드 이미지에 대 한 기본값 보다 높습니다 `AKS Ubuntu 18.04` . 1.18 보다 작은 지원 되는 Kubernetes 버전의 노드 풀은 여전히 `AKS Ubuntu 16.04` 노드 이미지로 수신 되지만 `AKS Ubuntu 18.04` 노드 풀 Kubernetes 버전이 v 1.18 이상으로 업데이트 되 면로 업데이트 됩니다.

프로덕션 노드 풀을 업그레이드 하기 전에 AKS Ubuntu 18.04 노드 풀에서 워크 로드를 테스트 하는 것이 좋습니다.

노드 이미지를 사용 하 여 노드 풀을 만들려면 `AKS Ubuntu 18.04` kubernetes v 1.18 이상을 실행 하는 노드 풀을 만듭니다. 클러스터 제어 평면은 적어도 v 1.18 이상 이어야 하지만 다른 노드 풀은 이전 kubernetes 버전에 남아 있을 수 있습니다.
아래에서는 먼저 제어 평면을 업그레이드 한 다음 새 노드 이미지 OS 버전을 받는 v 1.18를 사용 하 여 새 노드 풀을 만듭니다.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>컨테이너 런타임 구성

컨테이너 런타임은 컨테이너를 실행 하 고 노드의 컨테이너 이미지를 관리 하는 소프트웨어입니다. 런타임은 sys 호출 또는 OS (운영 체제) 특정 기능을 추상화 하 여 Linux 또는 Windows에서 컨테이너를 실행 하는 데 도움이 됩니다. Kubernetes 버전 1.19 노드 풀을 사용 하 여 클러스터를 AKS 하 고 컨테이너 런타임으로 더 많이 사용 `containerd` 합니다. 노드 풀에 대해 v 1.19 이전에 Kubernetes를 사용 하는 AKS 클러스터는 [Moby](https://mobyproject.org/) (업스트림 docker)를 컨테이너 런타임으로 사용 합니다.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) 는 노드에서 컨테이너를 실행 하 고 이미지를 관리 하는 데 필요한 최소 기능 집합을 제공 하는 [OCI](https://opencontainers.org/) (Open Container 이니셔티브) 규격 핵심 컨테이너 런타임입니다. 2017 년 3 월의 CNCF (Cloud Native Compute Foundation)로 [기증](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) 되었습니다. AKS에서 사용 하는 현재 Moby 버전은 `containerd` 위에 표시 된 것 처럼를 기반으로 합니다.

기반 노드 및 노드 풀을 사용 하 여와 통신 하는 `containerd` 대신, `dockershim` KUBELET는 `containerd` CRI (container runtime interface) 플러그 인을 통해 직접 통신 하므로 Docker CRI 구현과 비교할 때 흐름에서 추가 홉을 제거 합니다. 따라서 pod 시작 대기 시간 및 리소스 (CPU 및 메모리) 사용이 더 나은 것을 볼 수 있습니다.

AKS 노드에 대해를 사용 하 여 `containerd` pod 시작 대기 시간이 향상 되 고 컨테이너 런타임에의 한 노드 리소스 소비가 감소 합니다. 이러한 향상 된 기능은 kubelet이 새 아키텍처에서 사용 하도록 설정 됩니다 .이 아키텍처는 `containerd` Moby/docker 아키텍처 kubelet에서 CRI 플러그 인을 통해 직접 통신 하는 반면 `dockershim` `containerd` , 흐름에 추가 홉이 발생 하기 전에 및 docker 엔진과 통신 합니다.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` AKS에 있는 Kubernetes의 모든 GA 버전 및 v 1.19 위의 모든 업스트림 Kubernetes 버전에서 작동 하며 모든 Kubernetes 및 AKS 기능을 지원 합니다.

> [!IMPORTANT]
> Kubernetes v 1.19 이상에서 노드 풀을 만든 클러스터 `containerd` 는 해당 컨테이너 런타임에 대해로 기본 설정 됩니다. 지원 되는 Kubernetes 버전의 노드 풀을 포함 하는 클러스터는 해당 컨테이너 런타임에 대해 수신 1.19 개 미만으로 `Moby` 업데이트 되지만 `ContainerD` 노드 풀 Kubernetes 버전을 v 1.19 이상으로 업데이트 하면로 업데이트 됩니다. 지원 `Moby` 되지 않는 이전 버전에서 노드 풀 및 클러스터를 계속 사용할 수 있습니다.
> 
> `containerD`1.19 이상에서 클러스터를 사용 하기 전에를 사용 하 여 AKS node 풀에서 워크 로드를 테스트 하는 것이 좋습니다.

### <a name="containerd-limitationsdifferences"></a>`Containerd` 제한 사항/차이점

* 를 `containerd` 컨테이너 런타임으로 사용 하려면 기본 OS 이미지로 AKS Ubuntu 18.04을 사용 해야 합니다.
* Docker 도구 집합은 여전히 노드에 있지만 Kubernetes는 컨테이너 런타임으로를 사용 `containerd` 합니다. 따라서 Moby/Docker는 노드의 Kubernetes 생성 컨테이너를 관리 하지 않으므로 Docker 명령 (예: `docker ps` ) 또는 DOCKER API를 사용 하 여 컨테이너를 보거나 조작할 수 없습니다.
* 의 경우 `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) Kubernetes 노드의 pod, 컨테이너 및 컨테이너 이미지 **문제를 해결** 하기 위해 DOCKER cli 대신 대체 cli로를 사용 하는 것이 좋습니다 (예: `crictl ps` ). 
   * Docker CLI의 완전 한 기능을 제공 하지 않습니다. 문제 해결을 위한 용도로만 사용 됩니다.
   * `crictl` pod 등과 같은 개념을 포함 하 여 컨테이너에 대 한 보다 kubernetes 보기를 제공 합니다.
* `Containerd` 표준화 된 `cri` 로깅 형식 (현재 docker의 json 드라이버에서 가져온 것과 다름)을 사용 하 여 로깅을 설정 합니다. 로깅 솔루션은 `cri` [컨테이너에 대 한 Azure Monitor](../azure-monitor/containers/container-insights-enable-new-cluster.md)와 같은 로깅 형식을 지원 해야 합니다.
* 더 이상 docker 엔진에 액세스 하거나 docker (docker)를 사용할 수 없습니다 `/var/run/docker.sock` .
  * 현재 Docker 엔진에서 응용 프로그램 로그 또는 모니터링 데이터를 추출 하는 경우 [컨테이너에 Azure Monitor](../azure-monitor/containers/container-insights-enable-new-cluster.md) 와 같은 항목을 대신 사용 하세요. 또한 AKS는 에이전트 노드에서 모든 대역외 명령이 실행 되는 것을 지원 하지 않습니다 .이로 인해 불안정 해질 수 있습니다.
  * Moby/docker를 사용 하는 경우에도 위의 방법으로 이미지를 빌드하고 docker 엔진을 직접 활용 하는 것은 권장 되지 않습니다. Kubernetes는 소비 된 리소스를 완전히 인식 하지 못하므로 이러한 접근 방식은 [여기](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) 및 [여기](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)에서 자세히 설명 하는 다양 한 문제를 제공 합니다.
* 이미지 빌드-AKS 클러스터 내에서 이미지를 빌드하지 않는 한 현재 docker 빌드 워크플로를 정상적으로 계속 사용할 수 있습니다. 이 경우 [ACR 작업](../container-registry/container-registry-quickstart-task-cli.md)을 사용 하 여 이미지를 빌드하기 위한 권장 접근 방법으로 전환 하거나 [docker buildx](https://github.com/docker/buildx)와 같은 좀 더 안전한 클러스터 내 옵션으로 전환 하는 것을 고려해 보세요.

## <a name="generation-2-virtual-machines"></a>2세대 가상 컴퓨터

Azure는 [Gen2 (2 세대) vm (가상 머신)](../virtual-machines/generation-2.md)을 지원 합니다. 2 세대 Vm은 1 세대 Vm (Gen1)에서 지원 되지 않는 주요 기능을 지원 합니다. 이러한 기능에는 메모리 증가, Intel SGX(Software Guard Extensions) 및 vPMEM(가상화된 영구 메모리)이 포함됩니다.

2세대 VM은 1세대 VM에서 사용되는 BIOS 기반 아키텍처 대신 새 UEFI 기반 부팅 아키텍처를 사용합니다.
특정 Sku 및 크기만 Gen2 Vm을 지원 합니다. [지원 되는 크기 목록을](../virtual-machines/generation-2.md#generation-2-vm-sizes)확인 하 여 SKU가 Gen2을 지원 하거나 요구 하는지 확인 합니다.

또한 모든 VM 이미지가 Gen2을 지 원하는 것은 아닙니다. AKS Gen2 Vm은 새로운 [AKS Ubuntu 18.04 이미지](#os-configuration)를 사용 합니다. 이 이미지는 모든 Gen2 Sku 및 크기를 지원 합니다.

## <a name="ephemeral-os"></a>임시 OS

기본적으로 Azure는 가상 머신에 대 한 운영 체제 디스크를 자동으로 Azure storage에 복제 하 여 데이터 손실을 방지 하 고 VM을 다른 호스트에 재배치 해야 할 필요가 있습니다. 그러나 컨테이너는 로컬 상태를 유지 하도록 설계 되지 않았으므로이 동작은 제한 된 값을 제공 하는 반면, 느린 노드 프로 비전 및 더 높은 읽기/쓰기 대기 시간을 비롯 한 몇 가지 단점을 제공 합니다.

이와 반대로 삭제 된 OS 디스크는 임시 디스크와 마찬가지로 호스트 컴퓨터에만 저장 됩니다. 이를 통해 더 빠른 노드 크기 조정 및 클러스터 업그레이드와 함께 읽기/쓰기 대기 시간이 줄어듭니다.

임시 디스크와 마찬가지로, 임시 OS 디스크는 가상 컴퓨터의 가격에 포함 되어 있으므로 추가 저장소 비용이 발생 하지 않습니다.

> [!IMPORTANT]
>사용자가 OS에 대해 관리 디스크를 명시적으로 요청 하지 않으면 지정 된 nodepool 구성에 대해 가능 하면 AKS는 기본적으로 사용 후 삭제 OS로 설정 됩니다.

사용 후 삭제 OS를 사용 하는 경우 OS 디스크는 VM 캐시에 맞아야 합니다. VM 캐시의 크기는 IO 처리량 ("GiB의 캐시 크기") 옆의 괄호 안에 [Azure 설명서](../virtual-machines/dv3-dsv3-series.md) 에서 사용할 수 있습니다.

AKS 기본 VM Standard_DS2_v2 크기를 100GB의 기본 OS 디스크 크기 (예: 100GB)로 사용 하는 경우이 VM 크기는 사용 후 삭제 OS를 지원 하지만 캐시 크기의 86GB 있습니다. 사용자가 명시적으로 지정 하지 않은 경우이 구성은 기본적으로 관리 디스크로 설정 됩니다. 사용자가 임시 OS를 명시적으로 요청한 경우 유효성 검사 오류가 표시 됩니다.

사용자가 60GB OS 디스크를 사용 하 여 동일한 Standard_DS2_v2를 요청 하는 경우이 구성은 기본적으로 사용 후 삭제 OS로 설정 됩니다. 요청 된 60GB 크기는 86GB의 최대 캐시 크기 보다 작습니다.

100GB OS 디스크와 Standard_D8s_v3 사용 하 여이 VM 크기는 사용 후 삭제 OS 및 200GB의 캐시 공간을 지원 합니다. 사용자가 OS 디스크 유형을 지정 하지 않으면 nodepool은 기본적으로 사용 후 삭제 OS를 수신 합니다. 

사용 후 삭제 OS에는 Azure CLI 버전 2.15.0 이상이 필요 합니다.

### <a name="use-ephemeral-os-on-new-clusters"></a>새 클러스터에서 사용 후 삭제 OS 사용

클러스터를 만들 때 사용 후 삭제 OS 디스크를 사용 하도록 클러스터를 구성 합니다. `--node-osdisk-type`새 클러스터에 대 한 os 디스크 유형으로 사용 후 삭제 os를 설정 하려면 플래그를 사용 합니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

네트워크에 연결 된 OS 디스크를 사용 하 여 일반 클러스터를 만들려는 경우를 지정 하 여이 작업을 수행할 수 있습니다 `--node-osdisk-type=Managed` . 또한 아래와 같이 더 많은 임시 OS 노드 풀을 추가 하도록 선택할 수 있습니다.

### <a name="use-ephemeral-os-on-existing-clusters"></a>기존 클러스터에서 사용 후 삭제 OS 사용
새 노드 풀을 구성 하 여 사용 후 삭제 OS 디스크를 사용 합니다. 플래그를 사용 `--node-osdisk-type` 하 여 os 디스크 유형으로 해당 노드 풀의 os 디스크 유형으로 설정 합니다.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> 사용 후 삭제 OS를 사용 하면 vm 캐시 크기까지 VM 및 인스턴스 이미지를 배포할 수 있습니다. AKS의 경우 기본 노드 OS 디스크 구성은 128GB을 사용 합니다. 즉, 캐시가 128GB 보다 큰 VM 크기가 필요 합니다. 기본 Standard_DS2_v2의 캐시 크기는 86GB이 고 크기가 크지 않습니다. Standard_DS3_v2의 캐시 크기가 172GB 이며이는 크기가 커야 합니다. 또한를 사용 하 여 OS 디스크의 기본 크기를 줄일 수 있습니다 `--node-osdisk-size` . AKS 이미지의 최소 크기는 30GB입니다. 

네트워크에 연결 된 OS 디스크를 사용 하 여 노드 풀을 만들려면를 지정 하 여이 작업을 수행할 수 있습니다 `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>사용자 지정 리소스 그룹 이름

Azure에서 Azure Kubernetes Service 클러스터를 배포하면 작업자 노드에 대한 두 번째 리소스 그룹이 생성됩니다. 기본적으로 AKS는 노드 리소스 그룹 이름을 `MC_resourcegroupname_clustername_location`으로 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 aks-preview Azure CLI 확장 버전 0.3.2 이상을 설치합니다. Azure CLI 사용하여 `az aks create` 명령의 `--node-resource-group` 매개 변수를 사용하여 리소스 그룹의 사용자 지정 이름을 지정합니다. Azure Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포하는 경우 `nodeResourceGroup` 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

자체 구독에서 Azure 리소스 공급자가 자동으로 보조 리소스 그룹을 만듭니다. 클러스터가 생성될 때만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다. 

노드 리소스 그룹으로 작업할 때는 다음을 수행할 수 없습니다.

- 노드 리소스 그룹에 기존 리소스 그룹을 지정합니다.
- 노드 리소스 그룹에 다른 구독을 지정합니다.
- 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 이름을 지정합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 Azure에서 만든 태그를 수정하거나 삭제합니다.

## <a name="next-steps"></a>다음 단계

- 클러스터에서 [노드 이미지를 업그레이드](node-image-upgrade.md) 하는 방법을 알아봅니다.
- 클러스터를 최신 버전의 Kubernetes로 업그레이드하는 방법을 알아보려면 [AKS(Azure Kubernetes Service) 클러스터 업그레이드](upgrade-cluster.md)를 참조하세요.
- [ `containerd` 및 Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/) 에 대해 자세히 알아보세요.
- 몇 가지 일반적인 AKS 질문에 대한 답변을 확인하려면 [AKS에 대한 질문과 대답](faq.md) 목록을 참조하세요.
- [임시 OS 디스크](../virtual-machines/ephemeral-os-disks.md)에 대해 자세히 알아보세요.


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
