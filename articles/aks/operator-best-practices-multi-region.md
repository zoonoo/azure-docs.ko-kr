---
title: AKS 비즈니스 연속성 및 재해 복구를 위한 모범 사례
description: 클러스터 운영자의 모범 사례를 통해 애플리케이션에 대한 최대 가동 시간을 달성하고, 고가용성을 제공하고, AKS(Azure Kubernetes Service)에서 재해 복구를 준비합니다.
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 7aa93d8ba21cafddc5511e16fa430b76942b1a6d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668300"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 비즈니스 연속성 및 재해 복구 모범 사례

AKS(Azure Kubernetes Services)에서 클러스터를 관리할 때 애플리케이션 가동 시간이 중요합니다. 기본적으로 AKS는 [VMSS(가상 시스템 확장 집합)에서](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)여러 노드를 사용하여 고가용성을 제공합니다. 그러나 이러한 여러 노드는 지역 오류로부터 시스템을 보호하지 못합니다. 가동 시간을 최대화하려면 비즈니스 연속성을 유지하고 재해 복구에 대비하기 위해 미리 계획을 세워야 합니다.

이 문서에서는 AKS에서 비즈니스 연속성 및 재해 복구를 계획하는 방법에 대해 중점합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 여러 리전에서 AKS 클러스터를 계획합니다.
> * Azure 트래픽 관리자를 사용하여 여러 클러스터에서 트래픽을 라우팅합니다.
> * 컨테이너 이미지 레지스트리에 지역 복제를 사용합니다.
> * 여러 클러스터에서 응용 프로그램 상태를 계획합니다.
> * 여러 리전에서 스토리지를 복제합니다.

## <a name="plan-for-multiregion-deployment"></a>다중 지역 배포 계획

**모범 사례:** 여러 AKS 클러스터를 배포할 때 AKS를 사용할 수 있는 지역을 선택하고 쌍을 이루는 지역을 사용합니다.

AKS 클러스터는 단일 Azure 지역에 배포됩니다. 지역 오류로부터 시스템을 보호하려면 응용 프로그램을 여러 지역에 걸쳐 여러 AKS 클러스터에 배포합니다. AKS 클러스터를 배포할 위치를 계획할 때는 다음을 고려하십시오.

* [**AKS 지역 가용성**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): 사용자와 가까운 지역을 선택합니다. AKS는 지속적으로 새로운 지역으로 확장.
* [**Azure 페어링 지역**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): 지리적 영역의 경우 서로 페어링된 두 지역을 선택합니다. 쌍을 이루는 지역은 플랫폼 업데이트를 조정하고 필요한 경우 복구 노력의 우선 순위를 지정합니다.
* **서비스 이용 가능 여부**: 페어링된 지역이 뜨겁거나 뜨겁거나 따뜻하거나 따뜻해야 하는지 또는 뜨겁거나 차가운지 결정합니다. 한 리전이 트래픽 서비스를 시작할 *준비가 된* 두 지역을 동시에 실행하시겠습니까? 또는 한 지역에서 트래픽을 제공할 준비를 할 시간을 갖기를 원하십니까?

AKS 지역 가용성 및 페어링된 영역은 공동 고려 사항입니다. 지역 재해 복구를 함께 관리하도록 설계된 쌍을 이루는 지역에 AKS 클러스터를 배포합니다. 예를 들어 AKS는 미국 동부 및 미국 서부에서 사용할 수 있습니다. 이러한 영역은 쌍을 이룹니다. AKS BC/DR 전략을 만들 때 이 두 지역을 선택합니다.

응용 프로그램을 배포할 때 CI/CD 파이프라인에 다른 단계를 추가하여 이러한 여러 AKS 클러스터에 배포합니다. 배포 파이프라인을 업데이트하지 않으면 응용 프로그램이 지역 및 AKS 클러스터 중 하나에만 배포될 수 있습니다. 보조 지역으로 향하는 고객 트래픽은 최신 코드 업데이트를 받지 못합니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

**모범 사례**: Azure 트래픽 관리자는 고객을 가장 가까운 AKS 클러스터 및 응용 프로그램 인스턴스로 안내할 수 있습니다. 최상의 성능과 중복성을 위해 AKS 클러스터로 이동하기 전에 트래픽 관리자를 통해 모든 응용 프로그램 트래픽을 지시합니다.

여러 지역에 AKS 클러스터가 여러 개 있는 경우 트래픽 관리자를 사용하여 각 클러스터에서 실행되는 응용 프로그램에 트래픽이 흐르는 방식을 제어합니다. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/)는 네트워크 트래픽을 여러 Azure 지역에 분산할 수 있는 DNS 기반 트래픽 부하 분산 장치입니다. 트래픽 관리자를 사용하여 클러스터 응답 시간 또는 지역에 따라 사용자를 라우팅합니다.

![교통 관리자가 있는 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

단일 AKS 클러스터를 가진 고객은 일반적으로 지정된 응용 프로그램의 서비스 IP 또는 DNS 이름에 연결합니다. 다중 클러스터 배포에서 고객은 각 AKS 클러스터의 서비스를 가리키는 트래픽 관리자 DNS 이름에 연결해야 합니다. 트래픽 관리자 끝점을 사용하여 이러한 서비스를 정의합니다. 각 끝점은 *서비스 로드 밸런서 IP입니다.* 이 구성을 사용하여 한 지역의 트래픽 관리자 끝점에서 다른 지역의 끝점으로 네트워크 트래픽을 안내합니다.

![트래픽 관리자를 통한 지리적 라우팅](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

트래픽 관리자는 DNS 조회를 수행하고 사용자의 가장 적합한 끝점을 반환합니다. 중첩된 프로파일은 기본 위치의 우선 순위를 지정할 수 있습니다. 예를 들어 사용자는 일반적으로 가장 가까운 지리적 지역에 연결해야 합니다. 해당 지역에 문제가 있는 경우 트래픽 관리자는 사용자를 보조 지역으로 안내합니다. 이 방법을 사용하면 가장 가까운 지리적 지역을 사용할 수 없는 경우에도 고객이 응용 프로그램 인스턴스에 연결할 수 있습니다.

끝점 및 라우팅을 설정하는 방법에 대한 자세한 내용은 [트래픽 관리자를 사용하여 지리적 트래픽 라우팅 방법 구성을](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)참조하십시오.

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Azure 정문 서비스를 사용 하 고 계층 7 응용 프로그램 라우팅

트래픽 관리자는 DNS(계층 3)를 사용하여 트래픽을 셰이프합니다. [Azure 정문 서비스는](https://docs.microsoft.com/azure/frontdoor/front-door-overview) HTTP/HTTPS(계층 7) 라우팅 옵션을 제공합니다. Azure 정문 서비스의 추가 기능에는 TLS 종료, 사용자 지정 도메인, 웹 응용 프로그램 방화벽, URL 다시 쓰기 및 세션 선호도가 포함됩니다. 애플리케이션 트래픽 요구 사항을 검토하여 어떤 솔루션이 가장 적합한지 알아보세요.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>글로벌 가상 네트워크 피어링과 상호 연결 지역

클러스터가 서로 통신해야 하는 경우 가상 네트워크 [피어링을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)통해 두 가상 네트워크를 서로 연결할 수 있습니다. 이 기술은 가상 네트워크를 서로 상호 연결하여 서로 다른 지리적 지역에서도 Microsoft의 백본 네트워크 전반에 걸쳐 높은 대역폭을 제공합니다.

AKS 클러스터가 실행 중인 가상 네트워크를 피어링하기 위한 전제 조건은 AKS 클러스터의 표준 로드 밸런서를 사용하여 Kubernetes 서비스가 가상 네트워크 피어링 에서 연결할 수 있도록 하는 것입니다.

## <a name="enable-geo-replication-for-container-images"></a>컨테이너 이미지에 지역 복제 사용

**모범 사례:** 컨테이너 이미지를 Azure 컨테이너 레지스트리에 저장하고 레지스트리를 각 AKS 리전에 지리적복제합니다.

AKS에 애플리케이션을 배포하고 실행하려면 컨테이너 이미지를 저장하고 끌어오는 방법이 필요합니다. 컨테이너 레지스트리는 AKS와 통합되므로 컨테이너 이미지 또는 Helm 차트를 안전하게 저장할 수 있습니다. 컨테이너 레지스트리는 멀티마스터 지역 복제를 지원하여 이미지를 전 세계 Azure 지역으로 자동으로 복제합니다. 

성능 및 가용성을 향상 하려면 컨테이너 레지스트리 지역 복제를 사용 하 여 AKS 클러스터가 있는 각 리전에서 레지스트리를 만듭니다. 그런 다음 각 AKS 클러스터는 동일한 지역의 로컬 컨테이너 레지스트리에서 컨테이너 이미지를 가져옵니다.

![컨테이너 이미지에 대한 컨테이너 레지스트리 지역 복제](media/operator-best-practices-bc-dr/acr-geo-replication.png)

컨테이너 레지스트리 지역 복제를 사용하여 동일한 리전에서 이미지를 가져오는 경우 결과는 다음과 같습니다.

* **더 빠른 속도:** 동일한 Azure 리전 내에서 고속, 대기 시간이 짧은 네트워크 연결에서 이미지를 가져옵니다.
* **신뢰성:** 지역을 사용할 수 없는 경우 AKS 클러스터는 사용 가능한 컨테이너 레지스트리에서 이미지를 가져옵니다.
* **저렴**: 데이터 센터 간에 네트워크 송신 요금이 부과되지 않습니다.

지리적 복제는 *프리미엄* SKU 컨테이너 레지스트리의 기능입니다. 지역 복제를 구성하는 방법에 대한 자세한 내용은 [컨테이너 레지스트리 지역 복제](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)를 참조하십시오.

## <a name="remove-service-state-from-inside-containers"></a>컨테이너 내부에서 서비스 상태 제거

**모범 사례:** 가능한 경우 컨테이너 내부에 서비스 상태를 저장하지 마십시오. 대신 다중 지역 복제를 지원하는 PaaS(서비스)로 Azure 플랫폼을 사용합니다.

*서비스 상태는* 서비스가 작동하는 데 필요한 메모리 내 또는 디스크 내 데이터를 나타냅니다. 상태의 예로는 서비스에서 읽고 쓰는 데이터 구조 및 멤버 변수를 들 수 있습니다. 서비스를 설계하는 방법에 따라 상태에 디스크에 저장된 파일이나 기타 리소스도 포함될 수 있습니다. 예를 들어 상태에는 데이터베이스가 데이터 및 트랜잭션 로그를 저장하는 데 사용하는 파일이 포함될 수 있습니다.

상태를 외부화하거나 상태를 조작하는 코드와 함께 배치할 수 있습니다. 일반적으로 네트워크를 통해 다른 컴퓨터에서 실행하거나 동일한 컴퓨터에서 프로세스가 부족한 데이터베이스 또는 기타 데이터 저장소를 사용하여 상태를 외부화합니다.

컨테이너 및 마이크로 서비스는 내부에서 실행되는 프로세스가 상태를 유지하지 않는 경우 가장 복원력이 뛰어다. 응용 프로그램에는 거의 항상 일부 상태가 포함되어 있으므로 MySQL용 Azure 데이터베이스, PostgreSQL용 Azure 데이터베이스 또는 Azure SQL 데이터베이스와 같은 PaaS 솔루션을 사용합니다.

이식 가능한 응용 프로그램을 빌드하려면 다음 지침을 참조하십시오.

* [12단계 앱 방법론](https://12factor.net/)
* [여러 Azure 지역에서 웹 애플리케이션 실행](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>스토리지 마이그레이션 계획 작성

**모범 사례:** Azure Storage를 사용하는 경우 기본 리전에서 백업 지역으로 저장소를 마이그레이션하는 방법을 준비하고 테스트합니다.

응용 프로그램에서 데이터에 Azure 저장소를 사용할 수 있습니다. 응용 프로그램은 서로 다른 지역의 여러 AKS 클러스터에 분산되어 있으므로 저장소를 동기화된 상태로 유지해야 합니다. 저장소를 복제하는 두 가지 일반적인 방법은 다음과 같습니다.

* 인프라 기반 비동기 복제
* 애플리케이션 기반 비동기 복제

### <a name="infrastructure-based-asynchronous-replication"></a>인프라 기반 비동기 복제

응용 프로그램에는 pod가 삭제된 후에도 영구 저장소가 필요할 수 있습니다. Kubernetes에서는 영구 볼륨을 사용하여 데이터 저장소를 지속할 수 있습니다. 영구 볼륨은 노드 VM에 탑재된 다음 포드에 노출됩니다. 영구 볼륨은 포드가 동일한 클러스터 내의 다른 노드로 이동되더라도 포드를 따릅니다.

사용하는 복제 전략은 저장소 솔루션에 따라 다릅니다. [Gluster,](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/) [Ceph,](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/) [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)및 [Portworx와](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 같은 일반적인 저장소 솔루션은 재해 복구 및 복제에 대한 자체 지침을 제공합니다.

일반적인 전략은 응용 프로그램이 데이터를 쓸 수 있는 공통 저장소 지점을 제공하는 것입니다. 이 데이터는 여러 Azure 지역에 복제된 후 로컬로 액세스됩니다.

![인프라 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure 관리 디스크를 사용하는 경우 다음과 같은 복제 및 DR 솔루션을 선택할 수 있습니다.

* [벨레로 온 Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>애플리케이션 기반 비동기 복제

Kubernetes는 현재 응용 프로그램 기반 비동기 복제에 대한 기본 구현을 제공하지 않습니다. 컨테이너와 Kubernetes가 느슨하게 결합되어 있기 때문에 기존의 응용 프로그램이나 언어 접근 방식이 작동해야 합니다. 일반적으로 응용 프로그램 자체는 저장소 요청을 복제한 다음 각 클러스터의 기본 데이터 저장소에 기록됩니다.

![애플리케이션 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 대한 비즈니스 연속성 및 재해 복구 고려 사항에 대해 중점합니다. AKS의 클러스터 운영에 대한 자세한 내용은 다음 모범 사례에 대한 문서를 참조하세요.

* [멀티테넌시 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
