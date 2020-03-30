---
title: Azure Kubernetes 서비스(AKS)로 마이그레이션
description: AZURE Kubernetes 서비스(AKS)로 마이그레이션합니다.
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624814"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)로 마이그레이션

이 문서에서는 AKS(Azure Kubernetes Service)로의 성공적인 마이그레이션을 계획하고 실행하는 데 도움이 됩니다. 주요 결정을 내리는 데 도움이 되도록 이 가이드에서는 AKS에 대한 현재 권장 구성에 대한 세부 정보를 제공합니다. 이 문서에는 모든 시나리오가 다루지 않으며 적절한 경우 이 문서에는 성공적인 마이그레이션을 계획하기 위한 자세한 정보에 대한 링크가 포함되어 있습니다.

이 문서는 다음 시나리오를 지원하는 데 사용할 수 있습니다.

* [가용성 집합에](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) 의해 지원되는 AKS 클러스터를 [가상 컴퓨터 규모 집합으로](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) 마이그레이션
* [표준 SKU 로드 밸런서를](https://docs.microsoft.com/azure/aks/load-balancer-standard) 사용하도록 AKS 클러스터 마이그레이션
* [AZURE 컨테이너 서비스(ACS)에서 마이그레이션 - 2020년 1월 31일 에서](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) AKS로 폐기
* [AKS 엔진에서 AKS로](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) 마이그레이션
* Azure 기반이 아닌 Kubernetes 클러스터에서 AKS로 마이그레이션

마이그레이션할 때 대상 Kubernetes 버전이 AKS의 지원되는 창 내에 있는지 확인합니다. 이전 버전을 사용하는 경우 지원되는 범위 내에 있지 않을 수 있으며 AKS에서 지원하기 위해 버전을 업그레이드해야 합니다. 자세한 내용은 [AKS 지원 Kubernetes 버전을](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) 참조하십시오.

최신 버전의 Kubernetes로 마이그레이션하는 경우 [Kubernetes 버전 및 버전 기울이기 지원 정책을](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)검토합니다.

시나리오에 따라 여러 오픈 소스 도구가 마이그레이션에 도움이 될 수 있습니다.

* [벨로](https://velero.io/) (쿠베르네테스 1.7 이상 필요)
* [Azure 쿠베 CLI 확장](https://github.com/yaron2/azure-kube-cli)
* [리시프터](https://github.com/mhausenblas/reshifter)

이 문서에서는 마이그레이션 세부 정보를 요약합니다.

> [!div class="checklist"]
> * 표준 로드 밸러블러 및 가상 머신 스케일 세트를 갖춘 AKS
> * 기존 연결된 Azure 서비스
> * 유효한 할당량 확인
> * 고가용성 및 비즈니스 연속성
> * 상태 비수기 응용 프로그램에 대한 고려 사항
> * 상태 충실 응용 프로그램에 대한 고려 사항
> * 클러스터 구성 배포

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>표준 로드 밸러블러 및 가상 머신 스케일 세트를 갖춘 AKS

AKS는 관리 오버헤드가 낮은 고유한 기능을 제공하는 관리형 서비스입니다. 관리되는 서비스인 경우 AKS가 지원하는 [지역](https://docs.microsoft.com/azure/aks/quotas-skus-regions) 집합중에서 선택해야 합니다. 기존 클러스터에서 AKS로 전환하려면 기존 응용 프로그램을 수정해야 AKS 관리 제어 평면에서 정상 상태를 유지할 수 있습니다.

[가상 시스템 규모 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets) 및 Azure 표준 로드 [밸런서에서](https://docs.microsoft.com/azure/aks/load-balancer-standard) 지원하는 AKS 클러스터를 사용하여 [여러 노드 풀,](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) [가용성 영역,](https://docs.microsoft.com/azure/availability-zones/az-overview) [인증된 IP 범위,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [클러스터 자동 크기 조정기,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [AKS용 Azure 정책](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)및 릴리스되는 다른 새로운 기능과 같은 기능을 얻을 수 있도록 하는 것이 좋습니다.

가상 시스템 가용성 [집합에서](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) 지원하는 AKS 클러스터는 이러한 많은 기능에 대한 지원이 부족합니다.

다음 예제는 가상 시스템 규모 집합에 의해 백업되는 단일 노드 풀이 있는 AKS 클러스터를 만듭니다. 표준 로드 밸러블러를 사용합니다. 또한 클러스터의 노드 풀에서 클러스터 자동 크기 조정기와 최소 *1개* 및 최대 *3개의* 노드를 설정합니다.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>기존 연결된 Azure 서비스

클러스터를 마이그레이션할 때 외부 Azure 서비스를 연결했을 수 있습니다. 리소스 레크리에이션이 필요하지는 않지만 기능을 유지하려면 이전 클러스터에서 새 클러스터로의 연결을 업데이트해야 합니다.

* Azure Container Registry
* Log Analytics
* 애플리케이션 정보
* Traffic Manager
* 스토리지 계정
* 외부 데이터베이스

## <a name="ensure-valid-quotas"></a>유효한 할당량 확인

마이그레이션 중에 추가 가상 머신이 구독에 배포되므로 할당량과 한도가 이러한 리소스에 충분한지 확인해야 합니다. [vCPU 할당량](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)증가를 요청해야 할 수 있습니다.

IP를 소진하지 않도록 [네트워크 할당량](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) 증가를 요청해야 할 수 있습니다. 자세한 내용은 [AKS의 네트워킹 및 IP 범위를](https://docs.microsoft.com/azure/aks/configure-kubenet) 참조하십시오.

자세한 내용은 [Azure 구독 및 서비스 제한을](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)참조하십시오. 현재 할당량을 확인하려면 Azure 포털에서 구독 [블레이드로](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)이동하여 구독을 선택한 다음 **사용량 + 할당량을 선택합니다.**

## <a name="high-availability-and-business-continuity"></a>고가용성 및 비즈니스 연속성

응용 프로그램에서 가동 중지 시간을 처리할 수 없는 경우 고가용성 마이그레이션 시나리오에 대한 모범 사례를 따라야 합니다.  복잡한 비즈니스 연속성 계획, 재해 복구 및 가동 시간 최대화를 위한 모범 사례는 이 문서의 범위를 벗어납니다.  자세한 내용은 [Azure Kubernetes 서비스(AKS)에서 비즈니스 연속성 및 재해 복구에 대한 모범 사례에](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) 대해 자세히 알아보십시오.

복잡한 애플리케이션의 경우 일반적으로 한 번에 마이그레이션되는 것이 아니라 시간이 지남에 따라 마이그레이션됩니다. 즉, 이전 환경과 새 환경은 네트워크를 통해 통신해야 할 수 있습니다. 이전에 통신하기 `ClusterIP` 위해 서비스를 사용했던 응용 프로그램은 `LoadBalancer` 유형으로 노출되고 적절하게 보호되어야 할 수 있습니다.

마이그레이션을 완료하려면 클라이언트를 AKS에서 실행 중인 새 서비스를 가리킬 수 있습니다. AKS 클러스터 앞에 있는 로드 밸런서를 가리키도록 DNS를 업데이트하여 트래픽을 리디렉션하는 것이 좋습니다.

[Azure 트래픽 관리자는](https://docs.microsoft.com/azure/traffic-manager/) 고객을 원하는 Kubernetes 클러스터 및 응용 프로그램 인스턴스로 안내할 수 있습니다.  트래픽 관리자는 여러 지역에 네트워크 트래픽을 분산할 수 있는 DNS 기반 트래픽 로드 밸러버입니다.  최상의 성능과 중복성을 위해 AKS 클러스터로 이동하기 전에 트래픽 관리자를 통해 모든 응용 프로그램 트래픽을 지시합니다.  다중 클러스터 배포에서 고객은 각 AKS 클러스터의 서비스를 가리키는 트래픽 관리자 DNS 이름에 연결해야 합니다. 트래픽 관리자 끝점을 사용하여 이러한 서비스를 정의합니다. 각 끝점은 *서비스 로드 밸런서 IP입니다.* 이 구성을 사용하여 한 지역의 트래픽 관리자 끝점에서 다른 지역의 끝점으로 네트워크 트래픽을 안내합니다.

![교통 관리자가 있는 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure 정문 서비스는](https://docs.microsoft.com/azure/frontdoor/front-door-overview) AKS 클러스터에 대한 트래픽을 라우팅하는 또 다른 옵션입니다.  Azure Front Door Service를 사용하면 최적의 성능과 고가용성을 지원하는 즉시 글로벌 장애 조치(failover)를 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리, 모니터링할 수 있습니다. 

### <a name="considerations-for-stateless-applications"></a>상태 비수기 응용 프로그램에 대한 고려 사항

상태 비저장 애플리케이션 마이그레이션은 가장 간단한 경우입니다. 리소스 정의(YAML 또는 Helm)를 새 클러스터에 적용하고, 모든 것이 예상대로 작동하는지 확인하고, 트래픽을 리디렉션하여 새 클러스터를 활성화합니다.

### <a name="considerations-for-stateful-applications"></a>상태 충실 응용 프로그램에 대한 고려 사항

데이터 손실이나 예기치 않은 가동 중지 시간을 방지하기 위해 상태 저장 응용 프로그램 마이그레이션을 신중하게 계획합니다.

Azure Files를 사용하는 경우 파일 공유를 새 클러스터에 볼륨으로 탑재할 수 있습니다.
* [정적 Azure 파일을 볼륨으로 마운트](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Azure 관리 디스크를 사용하는 경우 VM에 연결되지 않은 경우에만 디스크를 탑재할 수 있습니다.
* [정적 Azure 디스크를 볼륨으로 마운트](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

이러한 방법 중 어느 것도 작동하지 않으면 백업 및 복원 옵션을 사용할 수 있습니다.
* [벨레로 온 Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure 파일

디스크와 달리 Azure Files는 여러 호스트에 동시에 탑재할 수 있습니다. AKS 클러스터에서 Azure 및 Kubernetes는 ACS 클러스터가 여전히 사용하는 포드를 만드는 것을 방지하지 못합니다. 데이터 손실 및 예기치 않은 동작을 방지하려면 클러스터가 동시에 동일한 파일에 기록되지 않도록 합니다.

응용 프로그램이 동일한 파일 공유를 가리키는 여러 복제본을 호스트할 수 있는 경우 상태 비저장 마이그레이션 단계를 수행하고 YAML 정의를 새 클러스터에 배포합니다. 그렇지 않은 경우 한 가지 가능한 마이그레이션 방법에 다음 단계가 포함됩니다.

* 응용 프로그램이 제대로 작동하는지 확인합니다.
* 실시간 트래픽을 새 AKS 클러스터로 가리킵니다.
* 이전 클러스터의 연결을 끊습니다.

빈 공유로 시작하여 원본 데이터의 복사본을 만들려면 [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) 명령을 사용하여 데이터를 마이그레이션할 수 있습니다.


#### <a name="migrating-persistent-volumes"></a>영구 볼륨 마이그레이션

기존 영구 볼륨을 AKS로 마이그레이션하는 경우 일반적으로 다음 단계를 따릅니다.

* 키즈 응용 프로그램에 기록합니다. 이 단계는 선택 사항이며 가동 중지 시간이 필요합니다.
* 디스크의 스냅숏을 생성합니다.
* 스냅샷에서 새 관리 디스크를 만듭니다.
* AKS에서 영구 볼륨을 만듭니다.
* 영구 볼륨 클레임(정적 프로비저닝)이 아닌 [기존 볼륨을 사용하도록](https://docs.microsoft.com/azure/aks/azure-disk-volume) 포드 사양을 업데이트합니다.
* AKS에 응용 프로그램을 배포합니다.
* 응용 프로그램이 제대로 작동하는지 확인합니다.
* 실시간 트래픽을 새 AKS 클러스터로 가리킵니다.

> [!IMPORTANT]
> 쓰기를 정지하지 않도록 선택하면 새 배포에 데이터를 복제해야 합니다. 그렇지 않으면 디스크 스냅숏을 찍은 후 작성된 데이터가 누락됩니다.

일부 오픈 소스 도구를 사용하면 관리되는 디스크를 만들고 Kubernetes 클러스터 간에 볼륨을 마이그레이션할 수 있습니다.

* [Azure CLI 디스크 복사 확장은](https://github.com/noelbundick/azure-cli-disk-copy-extension) 리소스 그룹 및 Azure 리전에서 디스크를 복사하고 변환합니다.
* [Azure Kube CLI 확장은](https://github.com/yaron2/azure-kube-cli) ACS Kubernetes 볼륨을 연큐어하고 AKS 클러스터로 마이그레이션합니다.


### <a name="deployment-of-your-cluster-configuration"></a>클러스터 구성 배포

기존 CI(연속 통합) 및 CD(연속 제공) 파이프라인을 사용하여 AKS에 잘 알려진 구성을 배포하는 것이 좋습니다. Azure 파이프라인을 사용하여 [응용 프로그램을 빌드하고 AKS에 배포할](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops)수 있습니다. 기존 배포 작업을 복제하고 `kubeconfig` 새 AKS 클러스터를 가리키는지 확인합니다.

그렇지 않은 경우 기존 Kubernetes 클러스터에서 리소스 정의를 내보낸 다음 AKS에 적용합니다. `kubectl`을 사용하여 개체를 내보낼 수 있습니다.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>기존 리소스를 다른 리전으로 이동

AKS 클러스터를 AKS 에서 [지원하는 다른 지역으로][region-availability]이동할 수 있습니다. 다른 리전에서 새 클러스터를 만든 다음 리소스와 응용 프로그램을 새 클러스터에 배포하는 것이 좋습니다. 또한 AKS 클러스터에서 실행 중인 [Azure 개발자 공간과][azure-dev-spaces] 같은 서비스가 있는 경우 새 리전의 클러스터에 해당 서비스를 설치하고 구성해야 합니다.


이 문서에서는 다음에 대한 마이그레이션 세부 정보를 요약했습니다.

> [!div class="checklist"]
> * 표준 로드 밸러블러 및 가상 머신 스케일 세트를 갖춘 AKS
> * 기존 연결된 Azure 서비스
> * 유효한 할당량 확인
> * 고가용성 및 비즈니스 연속성
> * 상태 비수기 응용 프로그램에 대한 고려 사항
> * 상태 충실 응용 프로그램에 대한 고려 사항
> * 클러스터 구성 배포


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/