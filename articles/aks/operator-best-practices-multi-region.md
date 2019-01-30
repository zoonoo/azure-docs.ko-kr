---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Services)의 고가용성 및 재해 복구
description: 애플리케이션의 가동 시간을 최대화하여 AKS(Azure Kubernetes Services)에서 고가용성을 제공하고 재해 복구 상황에 대비하기 위한 클러스터 운영자 모범 사례를 알아봅니다.
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 36e87550c90c1a5d4b05aa62f2fbe79b04b25445
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382442"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 비즈니스 연속성 및 재해 복구 모범 사례

AKS(Azure Kubernetes Services)에서 클러스터를 관리할 때 애플리케이션 가동 시간이 중요합니다. AKS는 가용성 집합에 여러 노드를 사용하여 고가용성을 제공합니다. 이러한 여러 노드는 Azure 지역 장애로부터 보호하지 않습니다. 가동 시간을 최대화하려면 비즈니스 연속성 및 재해 복구 기능을 구현해야 합니다.

이 모범 사례 문서에서는 AKS에서 비즈니스 연속성 및 재해 복구를 계획하는 데 도움이 되는 고려 사항을 집중적으로 다룹니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
* [여러 Azure 지역의 AKS 클러스터 계획 작성](#region-planning)
* [Azure Traffic Manager를 사용하여 여러 클러스터에 트래픽 라우팅](#ingress-traffic)
* [컨테이너 이미지 레지스트리에 지역 복제 사용](#container-registry)
* [여러 클러스터에서 애플리케이션 상태 계획 작성](#managing-application-state)
* [여러 Azure 지역에 스토리지 복제](#storage)

## <a name="plan-for-multi-region-deployment"></a>다중 지역 배포 계획 작성

**모범 사례 지침** - 여러 AKS 클러스터를 배포할 때 AKS를 사용할 수 있는 Azure 지역을 선택하고 쌍을 이루는 지역을 사용합니다.

AKS 클러스터는 단일 Azure 지역에 배포됩니다. Azure 지역 장애로부터 보호하려면 애플리케이션을 여러 Azure 지역의 여러 AKS 클러스터에 배포하세요. AKS 클러스터를 배포할 Azure 지역에 대한 계획을 작성할 때 다음 사항을 고려해야 합니다.

* [AKS 지역 가용성](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * 사용자에게 가까운 Azure 지역을 선택합니다. AKS는 지속적으로 새 Azure 지역으로 확장 중입니다.
* [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * 지리적 영역으로는 서로 쌍을 이루는 두 Azure 지역을 선택합니다. 이러한 Azure 지역은 플랫폼 업데이트를 조정하고, 필요한 경우 복구 활동의 우선 순위를 지정합니다.
* 서비스 가용성 수준(핫/핫, 웜/핫, 핫/콜드)
  * 두 Azure 지역을 동시에 실행하고 싶은데, 한 Azure 지역은 트래픽을 서비스할 *준비가 완료*되었고 다른 Azure 지역은 트래픽 서비스를 준비하기 위한 시간이 더 필요한가요?

AKS 지역 가용성 및 쌍을 이루는 지역은 같이 고려해야 합니다. 지역 재해 복구를 함께 관리하도록 설계된 쌍을 이루는 지역에 AKS 클러스터를 배포합니다. 예를 들어 AKS는 *미국 동부* 및 *미국 서부*에서 사용할 수 있습니다. 두 Azure 지역 역시 쌍을 이루는 지역입니다. 두 Azure 지역은 AKS BC/DR 전략을 작성할 때 권장됩니다.

애플리케이션을 배포할 때 이러한 여러 AKS 클러스터를 배포하는 또 다른 단계를 CI/CD 파이프라인에 추가해야 합니다. 배포 파이프라인을 업데이트하지 않으면 애플리케이션 배포 시 Azure 지역 및 AKS 클러스터 중 하나에만 배포될 수 있습니다. 보조 지역으로 전송되는 고객 트래픽은 최신 코드 업데이트를 수신하지 않습니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

**모범 사례 지침** - Azure Traffic Manager는 고객을 가장 가까운 AKS 클러스터 및 애플리케이션 인스턴스로 보낼 수 있습니다. 최상의 성능과 중복 제거를 얻으려면 모든 애플리케이션 트래픽이 Traffic Manager를 통해 AKS 클러스터로 이동해야 합니다.

여러 Azure 지역의 여러 AKS 클러스터를 사용하면 각 클러스터에서 실행되는 애플리케이션으로 트래픽이 전달되는 방법을 제어해야 합니다. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/)는 네트워크 트래픽을 여러 Azure 지역에 분산할 수 있는 DNS 기반 트래픽 부하 분산 장치입니다. 클러스터 응답 시간 또는 지리적 위치에 따라 사용자를 라우팅할 수 있습니다.

![Azure Traffic Manager와 함께 AKS 사용](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

단일 AKS 클러스터를 사용하는 고객은 일반적으로 지정된 애플리케이션의 *서비스 IP* 또는 DNS 이름에 연결합니다. 다중 클러스터 배포에서는 고객이 각 AKS 클러스터의 서비스를 가리키는 Traffic Manager DNS 이름에 연결해야 합니다. 이러한 서비스는 Traffic Manager 엔드포인트를 사용하여 정의됩니다. 각 엔드포인트는 *서비스 부하 분산 장치 IP*입니다. 이 구성은 한 Azure 지역의 Traffic Manager 엔드포인트에서 다른 Azure 지역의 엔드포인트로 네트워크 트래픽을 보낼 수 있습니다.

![Azure Traffic Manager를 사용한 지리적 라우팅](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager는 DNS 조회를 수행하여 사용자에게 가장 적합한 엔드포인트를 반환하는 데 사용됩니다. 기본 위치에 지정된 우선 순위에 따라 중첩된 프로필을 사용할 수 있습니다. 예를 들어 일반적으로 사용자는 지리적으로 가장 가까운 Azure 지역에 연결합니다. 해당 Azure 지역에 문제가 있는 경우 Traffic Manager는 보조 지역으로 전달합니다. 이 방식을 사용하면 지리적으로 가장 가까운 Azure 지역을 사용할 수 없더라도 고객이 항상 애플리케이션 인스턴스에 연결할 수 있습니다.

이러한 엔드포인트 및 라우팅을 설정하는 단계는 [Traffic Manager를 사용한 지리적 트래픽 라우팅 방법 구성](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)을 참조하세요.

### <a name="layer-7-application-routing-with-azure-front-door"></a>Azure Front Door를 사용한 레이어 7 애플리케이션 라우팅

Azure Traffic Manager는 DNS(레이어 3)를 사용하여 트래픽을 셰이핑합니다. [Azure Front Door(미리 보기)](https://docs.microsoft.com/azure/frontdoor/front-door-overview)는 HTTP/HTTPS(레이어 7) 라우팅 옵션을 제공합니다. Front Door의 추가 기능으로는 SSL 종료, 사용자 지정 도메인, 웹 애플리케이션 방화벽, URL 재작성 및 세션 선호도가 포함됩니다.

애플리케이션 트래픽 요구 사항을 검토하여 어떤 솔루션이 가장 적합한지 알아보세요.

## <a name="enable-geo-replication-for-container-images"></a>컨테이너 이미지에 지역 복제 사용

**모범 사례 지침** - Azure Container Registry에 컨테이너 이미지를 저장하고 레지스트리를 각 AKS 지역에 지역 복제합니다.

AKS에 애플리케이션을 배포하고 실행하려면 컨테이너 이미지를 저장하고 끌어오는 방법이 필요합니다. ACR(Azure Container Registry)은 AKS와 통합하여 컨테이너 이미지 또는 Helm 차트를 안전하게 저장할 수 있습니다. ACR은 전 세계 Azure 지역에 이미지를 자동으로 복제하는 다중 마스터 지역 복제를 지원합니다. 성능 및 가용성을 개선하려면 ACR 지역 복제를 사용하여 AKS 클러스터가 있는 각 Azure 지역에 레지스트리를 만듭니다. 그러면 각 AKS 클러스터가 동일한 Azure 지역의 로컬 ACR 레지스트리에서 컨테이너 이미지를 끌어옵니다.

![컨테이너 이미지용 Azure Container Registry 지역 복제](media/operator-best-practices-bc-dr/acr-geo-replication.png)

ACR 지역 복제를 사용하면 다음과 같은 이점이 있습니다.

* **동일한 Azure 지역에서 이미지를 더 빨리 끌어옵니다.** 동일한 Azure 지역에 있는 대기 시간이 짧은 고속 네트워크 연결에서 이미지를 끌어옵니다.
* **동일한 Azure 지역에서 이미지를 안정적으로 끌어옵니다.** 한 Azure 지역을 사용할 수 없는 경우 AKS 클러스터는 여전히 사용 가능한 다른 ACR 레지스트리에서 이미지를 끌어옵니다.
* **동일한 Azure 지역에서 이미지를 더 저렴하게 끌어옵니다.** 데이터 센터 간 네트워크 송신 요금이 발생하지 않습니다.

지역 복제는 *프리미엄* SKU ACR 레지스트리의 기능입니다. 복제를 구성하는 단계는 [Azure Container Registry 지역 복제](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)를 참조하세요.

## <a name="remove-service-state-from-inside-containers"></a>컨테이너 내부에서 서비스 상태 제거

**모범 사례 지침** - 되도록이면 컨테이너 내부에 서비스 상태를 저장하지 마세요. 다중 지역 복제를 지원하는 Azure PaaS 서비스를 대신 사용하세요.

서비스 상태는 서비스가 작동하기 위해 필요한 메모리 내 또는 디스크의 데이터를 나타냅니다. 상태의 예로는 서비스에서 읽고 쓰는 데이터 구조 및 멤버 변수를 들 수 있습니다. 서비스의 설계 방식에 따라 디스크에 저장되는 파일이나 기타 리소스도 상태에 포함될 수 있습니다. 예를 들어 데이터베이스가 데이터 및 트랜잭션 로그를 저장하는 데 사용하는 파일이 있습니다.

상태는 외장화할 수도 있고 상태를 조작하는 코드와 함께 배치할 수도 있습니다. 상태의 외장화는 네트워크를 통해 여러 머신에서 또는 같은 컴퓨터에서 out of process로 실행되는 데이터베이스 또는 기타 데이터 저장소를 사용하여 수행됩니다.

컨테이너 및 마이크로서비스는 내부에서 실행되는 프로세스가 상태를 유지하지 하는 경우에 가장 복원력이 우수합니다. 애플리케이션은 거의 항상 어떤 상태를 포함하고 있으므로 Azure Database for MySQL/Postgres 또는 Azure SQL 같은 Platform as a Service 솔루션을 사용하세요.

이식성이 우수한 애플리케이션을 빌드하는 방법에 대한 자세한 내용은 다음 지침을 참조하세요.

* [Twelve-Factor App 방법론](https://12factor.net/)
* [여러 Azure 지역에서 웹 애플리케이션 실행](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>스토리지 마이그레이션 계획 작성

**모범 사례 지침** - Azure Storage를 사용하는 경우 주 지역에서 백업 지역으로 스토리지를 마이그레이션하는 방법을 준비 및 테스트합니다.

애플리케이션이 데이터에 Azure Storage를 사용할 수 있습니다. 애플리케이션이 여러 Azure 지역의 여러 AKS 클러스터에 분산된 경우 스토리지를 동기화 상태로 유지해야 합니다. 스토리지를 복제하는 두 가지 일반적인 방법은 다음과 같습니다.

* 애플리케이션 기반 비동기 복제
* 인프라 기반 비동기 복제

### <a name="infrastructure-based-asynchronous-replication"></a>인프라 기반 비동기 복제

Pod를 삭제한 후에도 애플리케이션에서 영구 스토리지를 요구할 수 있습니다. Kubernetes에서는 영구 볼륨을 사용하여 데이터 스토리지를 유지할 수 있습니다. 이러한 영구 볼륨은 노드 VM에 탑재되며, Pod에 공개됩니다. Pod가 동일한 클러스터의 다른 노드로 이동되더라도 영구 볼륨은 Pod를 따릅니다.

솔루션에서 사용하는 스토리지에 따라 복제 전략이 달라질 수 있습니다. [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html), [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 같은 일반적인 스토리지 솔루션은 자체 지침을 제공합니다.

중심이 되는 방법은 애플리케이션이 데이터를 쓰는 공용 스토리지 지점입니다. 이 데이터는 여러 Azure 지역에 복제된 후 로컬로 액세스됩니다.

![인프라 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure Managed Disks를 사용하는 경우 다음 방법 중 하나를 복제 및 DR 솔루션으로 사용할 수 있습니다.

* [Azure 기반의 Ark](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>애플리케이션 기반 비동기 복제

현재 애플리케이션 기반 비동기 복제에 대한 Kubernetes 네이티브 구현이 없습니다. 컨테이너와 Kubernetes가 느슨하게 결합되는 특성상, 기존 애플리케이션 또는 언어 접근 방식이 작동할 것입니다. 중심이 되는 방법은 애플리케이션 자체에서 스토리지 요청을 복제하는 것입니다. 그러면 각 클러스터의 기본 데이터 스토리지에 요청이 기록됩니다.

![애플리케이션 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터의 비즈니스 연속성 및 재해 복구 고려 사항을 집중적으로 살펴보았습니다. AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [다중 테넌트 지원 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
