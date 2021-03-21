---
title: AKS (Azure Kubernetes Service) 노드 풀의 노드 구성 사용자 지정 (미리 보기)
description: AKS (Azure Kubernetes Service) 클러스터 노드 및 노드 풀에서 구성을 사용자 지정 하는 방법에 대해 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 7b39242a7d7208b33a070e86088b25e9414ead04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714632"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>AKS (Azure Kubernetes Service) 노드 풀의 노드 구성 사용자 지정 (미리 보기)

노드 구성을 사용자 지정 하면 워크 로드의 요구 사항에 맞게 OS (운영 체제) 설정 또는 kubelet 매개 변수를 구성 하거나 튜닝할 수 있습니다. AKS 클러스터를 만들거나 클러스터에 노드 풀을 추가 하는 경우 일반적으로 사용 되는 OS 및 kubelet 설정의 하위 집합을 사용자 지정할 수 있습니다. 이 하위 집합을 벗어난 설정을 구성 하려면 [데몬 집합을 사용 하 여 노드에 대 한 끊어질 AKS 지원 없이 필요한 구성을 사용자 지정](support-policies.md#shared-responsibility)합니다.

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>`CustomNodeConfigPreview`미리 보기 기능 등록

Kubelet 매개 변수 또는 OS 설정을 사용자 지정할 수 있는 AKS 클러스터 또는 노드 풀을 만들려면 `CustomNodeConfigPreview` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다.

`CustomNodeConfigPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Kubelet 매개 변수 또는 OS 설정을 사용자 지정할 수 있는 AKS 클러스터 또는 노드 풀을 만들려면 최신 *AKS-미리 보기* Azure CLI 확장이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>사용자 지정 노드 구성 사용

### <a name="kubelet-custom-configuration"></a>Kubelet 사용자 지정 구성

지원 되는 Kubelet 매개 변수 및 허용 되는 값은 다음과 같습니다.

| 매개 변수 | 허용 되는 값/간격 | 기본값 | 설명 |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | 없음, 정적 | 없음 | 정적 정책을 통해 [보장 된 pod](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) 의 컨테이너는 노드의 배타 cpu에 대 한 액세스를 정수 CPU로 요청할 수 있습니다. |
| `cpuCfsQuota` | true, false | true |  CPU 제한을 지정 하는 컨테이너에 대해 CPU CFS 할당량 적용을 사용 하거나 사용 하지 않도록 설정 합니다. | 
| `cpuCfsQuotaPeriod` | 간격 (밀리초) (밀리초) | `100ms` | CPU CFS 할당량 기간 값을 설정 합니다. | 
| `imageGcHighThreshold` | 0-100 | 85 | 이미지 가비지 수집이 항상 실행 되는 디스크 사용의 백분율입니다. 가비지 수집을 트리거하는 최소 디스크 **사용량입니다.** 이미지 가비지 수집을 사용 하지 않도록 설정 하려면를 100으로 설정 합니다. | 
| `imageGcLowThreshold` | 0-100, 다음 보다 높음 `imageGcHighThreshold` | 80 | 이미지 가비지 수집을 실행 하지 않는 디스크 사용의 백분율입니다. 가비지 수집을 트리거할 **수** 있는 최소 디스크 사용량입니다. |
| `topologyManagerPolicy` | 없음, 최고 노력, 제한 됨, 단일 numa 노드 | 없음 | NUMA 노드 맞춤 최적화에 대 한 자세한 내용은 [여기](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/)를 참조 하세요. Kubernetes v 1.18 +만 해당 합니다. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | 없음 | 안전 하지 않은 sysctls 또는 unsafe sysctl 패턴의 허용 되는 목록입니다. | 

### <a name="linux-os-custom-configuration"></a>Linux OS 사용자 지정 구성

지원 되는 OS 설정 및 허용 되는 값은 다음과 같습니다.

#### <a name="file-handle-limits"></a>파일 핸들 제한

많은 트래픽을 처리 하는 경우 서비스 트래픽이 많은 로컬 파일에서 제공 되는 것이 일반적입니다. 일부 시스템 메모리의 비용으로 더 많은 기능을 처리할 수 있도록 아래의 커널 설정과 기본 제공 제한을 조정할 수 있습니다.

| 설정 | 허용 되는 값/간격 | 기본값 | 설명 |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192-12000500 | 709620 | Linux 커널이 할당할 최대 파일 핸들 수입니다 .이 값을 늘리면 허용 되는 열려 있는 파일의 최대 수를 늘릴 수 있습니다. |
| `fs.inotify.max_user_watches` | 781250-2097152 | 1048576 | 시스템에서 허용 하는 최대 파일 감시 수입니다. 각 *조사식* 은 32 비트 커널의 약 90 바이트 및 64 비트 커널의 약 160 바이트입니다. | 
| `fs.aio-max-nr` | 65536-6553500 | 65536 | Nr에는 현재 시스템 차원의 비동기 io 요청 수가 표시 됩니다. nr-nr가 증가할 수 있는 최대 값을 변경할 수 있습니다. |
| `fs.nr_open` | 8192-20000500 | 1048576 | 프로세스에서 할당할 수 있는 파일 핸들의 최대 수입니다. |


#### <a name="socket-and-network-tuning"></a>소켓 및 네트워크 튜닝

매우 많은 수의 동시 세션을 처리할 것으로 예상 되는 에이전트 노드의 경우, 아래와 같은 TCP 및 네트워크 옵션의 하위 집합을 사용 하 여 노드 풀 별로 조정할 수 있습니다. 

| 설정 | 허용 되는 값/간격 | 기본값 | 설명 |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096-324만 | 16384 | 지정 된 수신 소켓에 대해 큐에 대기할 수 있는 최대 연결 요청 수입니다. [Listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) 함수에 전달 된 백로그 매개 변수의 값에 대 한 상한입니다. 백로그 인수가 보다 크면 `somaxconn` 자동으로이 제한으로 잘립니다.
| `net.core.netdev_max_backlog` | 1000-324만 | 1000 | 패킷이 커널을 처리할 수 있는 것 보다 빨리 패킷을 수신 하는 경우 입력 쪽에서 큐에 대기 중인 최대 패킷 수입니다. |
| `net.core.rmem_max` | 212992-134217728 | 212992 | 최대 수신 소켓 버퍼 크기 (바이트)입니다. |
| `net.core.wmem_max` | 212992-134217728 | 212992 | 최대 송신 소켓 버퍼 크기 (바이트)입니다. | 
| `net.core.optmem_max` | 20480-4194304 | 20480 | 소켓 당 최대 보조 버퍼 크기 (옵션 메모리 버퍼)를 허용 합니다. 소켓 옵션 메모리는 소켓 사용량과 관련 된 추가 구조를 저장 하기 위해 몇 가지 경우에 사용 됩니다. | 
| `net.ipv4.tcp_max_syn_backlog` | 128-324만 | 16384 | 연결 하는 클라이언트에서 아직 승인을 받지 않은 대기 중인 최대 연결 요청 수입니다. 이 수를 초과 하면 커널이 요청 삭제를 시작 합니다. |
| `net.ipv4.tcp_max_tw_buckets` | 8000-144만 | 32768 | `timewait`시스템에서 동시에 보유 한 최대 소켓 수입니다. 이 값이 초과 되 면 시간 대기 소켓이 즉시 제거 되 고 경고가 출력 됩니다. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | 분리 된 응용 프로그램에서 더 이상 참조 하지 않는 연결의 시간 길이는 로컬 끝에서 중단 되기 전에 FIN_WAIT_2 상태로 유지 됩니다. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | `keepalive` `keepalive` 가 설정 된 경우 TCP가 메시지를 보내는 빈도입니다. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | `keepalive`연결이 끊어지기를 결정할 때까지 전송 되는 프로브 TCP 수 |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | 프로브를 전송 하는 빈도입니다. 를 곱하여 검색 `tcp_keepalive_probes` 을 시작한 후 응답 하지 않는 연결을 종료 하는 시간을 만듭니다. | 
| `net.ipv4.tcp_tw_reuse` | 0 또는 1 | 0 | `TIME-WAIT`프로토콜 관점에서 안전 하 게 새 연결에 대 한 소켓을 다시 사용 하도록 허용 합니다. | 
| `net.ipv4.ip_local_port_range` | 첫 번째: 1024-60999 및 Last: 32768-65000] | 첫 번째: 32768 및 Last: 60999 | 로컬 포트를 선택 하기 위해 TCP 및 UDP 트래픽에 사용 되는 로컬 포트 범위입니다. 두 숫자로 구성 됩니다. 첫 번째 숫자는 에이전트 노드에서 TCP 및 UDP 트래픽을 허용 하는 첫 번째 로컬 포트이 고, 두 번째는 마지막 로컬 포트 번호입니다. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128-8만 | 4096 | ARP 캐시에 있을 수 있는 최소 항목 수입니다. 항목 수가이 설정 보다 작은 경우 가비지 수집이 트리거되지 않습니다. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512-9만 | 8192 | ARP 캐시에 있을 수 있는 소프트 최대 항목 수입니다. 이 설정은이 소프트 최대값에 도달한 후 약 5 초 후에 ARP 가비지 수집이 트리거되면 가장 중요 합니다. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024-10만 | 16384 | ARP 캐시의 하드 최대 항목 수입니다. |
| `net.netfilter.nf_conntrack_max` | 131072-589824 | 131072 | `nf_conntrack` 는 Linux 내에서 NAT에 대 한 연결 항목을 추적 하는 모듈입니다. 이 `nf_conntrack` 모듈은 해시 테이블을 사용 하 여 TCP 프로토콜의 *설정 된 연결* 레코드를 기록 합니다. `nf_conntrack_max` 해시 테이블에 있는 최대 노드 수, 즉 모듈에서 지 원하는 최대 연결 수 `nf_conntrack` 또는 연결 추적 테이블의 크기입니다. | 
| `net.netfilter.nf_conntrack_buckets` | 65536-147456 | 65536 | `nf_conntrack` 는 Linux 내에서 NAT에 대 한 연결 항목을 추적 하는 모듈입니다. 이 `nf_conntrack` 모듈은 해시 테이블을 사용 하 여 TCP 프로토콜의 *설정 된 연결* 레코드를 기록 합니다. `nf_conntrack_buckets` 해시 테이블의 크기입니다. | 

#### <a name="worker-limits"></a>작업자 제한

파일 설명자 제한과 마찬가지로 프로세스에서 만들 수 있는 작업자 또는 스레드 수는 커널 설정과 사용자 제한에 의해 제한 됩니다. AKS에 대 한 사용자 제한은 무제한입니다. 

| 설정 | 허용 되는 값/간격 | 기본값 | 설명 |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | 프로세스는 작업자 스레드를 실행 합니다. 생성할 수 있는 모든 스레드의 최대 수는 커널 설정을 사용 하 여 설정 됩니다 `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>가상 메모리

아래 설정을 사용 하 여 Linux 커널의 VM (가상 메모리) 하위 시스템 및 더티 데이터의 작업을 디스크로 튜닝할 수 있습니다 `writeout` .

| 설정 | 허용 되는 값/간격 | 기본값 | 설명 |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530-262144 | 65530 | 이 파일은 프로세스에 포함 될 수 있는 최대 메모리 맵 영역 수를 포함 합니다. 메모리 맵 영역은 호출의 부작용으로 사용 되며, 및에서 `malloc` 직접 `mmap` , `mprotect` `madvise` 공유 라이브러리를 로드 하는 경우에도 사용 됩니다. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | 이 백분율 값은 디렉터리 및 inode 개체의 캐싱에 사용 되는 메모리를 확보 하기 위한 커널의 추세를 제어 합니다. |
| `vm.swappiness` | 0 - 100 | 60 | 이 컨트롤은 커널이 메모리 페이지를 교체 하는 정도를 정의 하는 데 사용 됩니다. 값이 높을수록 강도가 증가 하 고, 값이 작을수록 교환 양이 감소 합니다. 값이 0 이면 사용 가능한 페이지 양과 파일 지원 페이지가 영역에 있는 상위 워터 마크 보다 적을 때까지 커널이 교환을 시작 하지 않습니다. | 
| `swapFileSizeMB` | 1mb- [임시 디스크](../virtual-machines/managed-disks-overview.md#temporary-disk) 의 크기 (/sv/sv/ps) | 없음 | SwapFileSizeMB 스왑 파일의 크기 (MB)를 지정 합니다 .이 노드 풀에서 에이전트 노드에 생성 됩니다. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) 는 프로세서의 메모리 매핑 하드웨어를 보다 효율적으로 사용 하 여 성능을 개선 하기 위한 Linux 커널 기능입니다. 이 기능을 사용 하도록 설정 하면 가능한 경우 커널에서 할당을 시도 하 `hugepages` 고, 모든 Linux 프로세스가 2mb의 자연스럽 게 맞춰진 경우 2mb 페이지를 받게 됩니다 `mmap` . 에서 `hugepages` 시스템 전체를 사용 하도록 설정 하는 경우 응용 프로그램에서 더 많은 메모리 리소스를 할당 하 게 될 수 있습니다. 응용 프로그램은 `mmap` 용량이 매우 클 수 있지만 1 바이트를 터치 하는 것이 좋습니다 .이 경우 적절 하지 않은 경우 4k 페이지 대신 2mb 페이지가 할당 될 수 있습니다. 이 시나리오는 시스템 전체를 사용 하지 않도록 설정 `hugepages` 하거나 지역 내 에서만 사용할 수 있는 이유입니다 `MADV_HUGEPAGE madvise` . | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | 이 값은 커널이 메모리 압축을 적극적으로 사용 하 여 사용 가능 하도록 할지 여부를 제어 합니다 `hugepages` . | 

> [!IMPORTANT]
> 쉽게 검색 하 고 읽을 수 있도록 OS 설정은 해당 이름으로이 문서에 표시 되지만 [camelCase 대/소문자 규칙](/dotnet/standard/design-guidelines/capitalization-conventions)을 사용 하 여 configuration json 파일 또는 AKS API에 추가 되어야 합니다.

`kubeletconfig.json`다음 내용으로 파일을 만듭니다.

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
`linuxosconfig.json`다음 내용으로 파일을 만듭니다.

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

이전 단계에서 만든 JSON 파일을 사용 하 여 kubelet 및 OS 구성을 지정 하는 새 클러스터를 만듭니다. 

> [!NOTE]
> 클러스터를 만들 때 kubelet 구성, OS 구성 또는 둘 다를 지정할 수 있습니다. 클러스터를 만들 때 구성을 지정 하는 경우 초기 노드 풀의 노드에만 해당 구성이 적용 됩니다. JSON 파일에서 구성 되지 않은 모든 설정은 기본값을 유지 합니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

만든 JSON 파일을 사용 하 여 Kubelet 매개 변수를 지정 하는 새 노드 풀을 추가 합니다.

> [!NOTE]
> 기존 클러스터에 노드 풀을 추가 하는 경우 kubelet 구성, OS 구성 또는 둘 다를 지정할 수 있습니다. 노드 풀을 추가할 때 구성을 지정 하는 경우 새 노드 풀의 노드에만 해당 구성이 적용 됩니다. JSON 파일에서 구성 되지 않은 모든 설정은 기본값을 유지 합니다.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>다음 단계

- [AKS 클러스터를 구성 하는 방법](cluster-configuration.md)에 대해 알아봅니다.
- 클러스터에서 [노드 이미지를 업그레이드](node-image-upgrade.md) 하는 방법을 알아봅니다.
- 클러스터를 최신 버전의 Kubernetes로 업그레이드하는 방법을 알아보려면 [AKS(Azure Kubernetes Service) 클러스터 업그레이드](upgrade-cluster.md)를 참조하세요.
- 몇 가지 일반적인 AKS 질문에 대한 답변을 확인하려면 [AKS에 대한 질문과 대답](faq.md) 목록을 참조하세요.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why