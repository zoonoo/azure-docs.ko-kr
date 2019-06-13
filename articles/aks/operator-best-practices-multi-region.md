---
title: 고가용성 및 재해 복구 Azure Kubernetes Service (AKS)에서
description: 고가용성을 제공 하 고 재해 복구 Azure Kubernetes Service (AKS)에서 준비 응용 프로그램에 대 한 최대 가동 시간을 달성 하기 위해 클러스터 운영자의 모범 사례에 알아봅니다.
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 4afc1231e6c9fa49c04c7bf6dfe26ee5eb87cc31
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475174"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 비즈니스 연속성 및 재해 복구 모범 사례

AKS(Azure Kubernetes Services)에서 클러스터를 관리할 때 애플리케이션 가동 시간이 중요합니다. AKS는 가용성 집합에 여러 노드를 사용하여 고가용성을 제공합니다. 하지만 이러한 여러 노드 영역 장애 로부터 시스템을 보호 하지 않습니다. 작동 시간을 최대화 하려면 비즈니스 연속성을 유지 관리 하 고 재해 복구를 위해 준비 하도록 미리 계획 합니다.

이 문서에서는 AKS에서 재해 복구 및 비즈니스 연속성 계획 하는 방법에 중점을 둡니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 여러 지역에서 AKS 클러스터에 대해 계획 합니다.
> * Azure Traffic Manager를 사용 하 여 여러 클러스터 간에 트래픽을 라우팅합니다.
> * 지역 복제를 사용 하 여 컨테이너 이미지 레지스트리에 대 한 합니다.
> * 여러 클러스터에서 응용 프로그램 상태에 대 한 계획입니다.
> * 여러 지역에서 저장소를 복제 합니다.

## <a name="plan-for-multiregion-deployment"></a>다중 지역 배포에 대 한 계획

**모범 사례**: 여러 AKS 클러스터를 배포할 때 AKS를 사용할 수 있는 지역을 선택 하 고 쌍을 이루는 지역을 사용 합니다.

AKS 클러스터는 단일 Azure 지역에 배포됩니다. 지역에서 시스템을 보호 하려면 다른 지역에 걸쳐 여러 AKS 클러스터에 응용 프로그램을 배포 합니다. AKS 클러스터를 배포 하는 위치를 계획할 때 고려해 야 합니다.

* [**AKS 지역 가용성**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): 사용자에게 가까운 Azure 지역을 선택합니다. AKS는 지속적으로 새로운 지역으로 확장합니다.
* [**Azure 쌍을 이루는 지역**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): 지리적 영역으로는 서로 쌍을 이루는 두 Azure 지역을 선택합니다. 플랫폼 업데이트를 조정 하 고 복구 작업 우선 순위를 지정 하는 쌍을 이루는 지역 필요한 경우.
* **서비스 가용성**: 쌍을 이루는 지역에 핫/핫, 웜/핫, 또는 핫/콜드 되어야 하는지 여부를 결정 합니다. 두 지역 모두 하나의 영역을 사용 하 여 동시에 실행 하 시겠습니까 *준비* 트래픽을 제공 하려면? 또는 한 지역 시간이 트래픽을 처리 하기 위해 준비 하 시겠습니까?

AKS 지역 가용성 및 쌍을 이루는 지역에 공동 고려 사항이 있습니다. 지역 재해 복구를 함께 관리하도록 설계된 쌍을 이루는 지역에 AKS 클러스터를 배포합니다. 예를 들어, AKS는 미국 동부 및 미국 서 부에 제공 됩니다. 이러한 지역 쌍을 이루는 됩니다. AKS BC/DR 전략을 만들 때 이러한 두 영역을 선택 합니다.

응용 프로그램을 배포 하는 경우 이러한 여러 AKS 클러스터를 배포 하는 CI/CD 파이프라인에 다른 단계를 추가 합니다. 배포 파이프라인을 업데이트 하지 않으면, 하나만 지역 및 AKS 클러스터에 응용 프로그램을 배포할 수 있습니다. 고객 트래픽을 보조 지역으로 전송 되는 최신 코드 업데이트를 받지 않습니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

**모범 사례**: Azure Traffic Manager는 고객에 게 가장 가까운 AKS 클러스터 및 응용 프로그램 인스턴스에 전달할 수 있습니다. 최상의 성능 및 중복 AKS 클러스터를 이동 하기 전에 모든 응용 프로그램 트래픽을 Traffic Manager를 통해 직접.

다른 지역의 여러 AKS 클러스터가 있는 경우 각 클러스터에서 실행 되는 응용 프로그램에 트래픽이 흐르는 방식을 제어 하려면 Traffic Manager를 사용 합니다. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/)는 네트워크 트래픽을 여러 Azure 지역에 분산할 수 있는 DNS 기반 트래픽 부하 분산 장치입니다. 사용자를 라우팅할 Traffic Manager를 사용 하 여 클러스터 응답 시간을 기준으로 또는 지리를 기반으로 합니다.

![Traffic Manager를 사용 하 여 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

단일 AKS 클러스터를 일반적으로 있는 고객은 지정된 된 응용 프로그램의 서비스 IP 또는 DNS 이름에 연결 합니다. Multicluster 배포의 경우 고객은 각 AKS 클러스터에서 서비스를 가리키는 Traffic Manager DNS 이름에 연결 해야 합니다. Traffic Manager 끝점을 사용 하 여 이러한 서비스를 정의 합니다. 각 끝점은는 *부하 분산 장치 IP 서비스*합니다. 다른 지역의 끝점으로 하나의 지역에서 Traffic Manager 끝점에서 네트워크 트래픽을 전달 하려면이 구성을 사용 합니다.

![Traffic Manager를 통해 라우팅 지리적](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager DNS 조회를 수행 하 고 사용자의 가장 적절 한 끝점을 반환 합니다. 중첩된 프로필을 기본 위치 우선 순위를 지정할 수 있습니다. 예를 들어, 사용자는 가장 가까운 지리적 지역에 일반적으로 연결 해야 합니다. 해당 지역에 문제가 있는 경우 Traffic Manager는 보조 지역으로 사용자를 대신 보냅니다. 이 방법을 사용 하면 해당 가장 가까운 지리적 지역을 사용할 수 없는 경우에 고객 응용 프로그램 인스턴스를 연결할 수 있도록 합니다.

끝점 및 라우팅 설정 방법에 대 한 자세한 내용은 [Traffic Manager를 사용 하 여 지리적 트래픽 라우팅 방법 구성](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)합니다.

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Azure 프런트 도어 서비스를 사용 하 여 계층 7 응용 프로그램 라우팅

Traffic Manager는 셰이프 트래픽에 DNS (계층 3)을 사용 합니다. [Azure 프런트 도어 서비스](https://docs.microsoft.com/azure/frontdoor/front-door-overview) HTTP/HTTPS (계층 7) 라우팅 옵션을 제공 합니다. Azure 프런트 도어 서비스의 추가 기능에는 SSL 종료, 사용자 지정 도메인, 웹 응용 프로그램 방화벽, URL 재작성 및 세션 선호도 포함 됩니다. 애플리케이션 트래픽 요구 사항을 검토하여 어떤 솔루션이 가장 적합한지 알아보세요.

## <a name="enable-geo-replication-for-container-images"></a>컨테이너 이미지에 지역 복제 사용

**모범 사례**: 각 AKS 지역에 레지스트리를 지리적 복제 Azure Container Registry에 컨테이너 이미지를 저장 합니다.

AKS에 애플리케이션을 배포하고 실행하려면 컨테이너 이미지를 저장하고 끌어오는 방법이 필요합니다. Container Registry에 컨테이너 이미지 또는 Helm 차트를 안전 하 게 저장할 수 있게 AKS와 통합 됩니다. Container Registry는 다중 마스터 지역 간 복제는 전 세계 Azure 지역에 이미지를 자동으로 복제를 지원 합니다. 

성능 및 가용성 향상을 위해 AKS 클러스터에 있는 각 지역에 레지스트리를 만들려면 컨테이너 레지스트리 지역에서 복제를 사용 합니다. 각 AKS 클러스터를 동일한 지역에 로컬 컨테이너 레지스트리에서 컨테이너 이미지를 끌어옵니다.

![컨테이너 레지스트리 지역에서 복제 컨테이너 이미지에 대 한](media/operator-best-practices-bc-dr/acr-geo-replication.png)

이미지를 끌어올 컨테이너 레지스트리 지역에서 복제를 사용 하 여 동일한 지역에서 결과 다음과 같습니다.

* **더 빠르게**: 동일한 Azure 지역 내에서 대기 시간이 짧은 고속 네트워크 연결에서 이미지를 끌어옵니다.
* **안정적**: 지역을 사용할 수 없는 경우 AKS 클러스터를 사용할 수 있는 컨테이너 레지스트리에서 이미지를 끌어옵니다.
* **저렴 한**: 데이터 센터 간 네트워크 송신 요금이 발생하지 않습니다.

지역에서 복제의 기능은 *Premium* 컨테이너 레지스트리 SKU입니다. 지역 복제를 구성 하는 방법에 대 한 자세한 내용은 [컨테이너 레지스트리 지역에서 복제](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)합니다.

## <a name="remove-service-state-from-inside-containers"></a>컨테이너 내부에서 서비스 상태 제거

**모범 사례**: 가능한 경우 컨테이너 내에서 서비스 상태를 저장 하지 마십시오. 대신 Azure 플랫폼을 사용 하 여 다중 지역 복제를 지 원하는 서비스 (PaaS).

*서비스 상태* 함수에는 서비스가 필요한 메모리 내 또는 디스크에 데이터를 나타냅니다. 상태의 예로는 서비스에서 읽고 쓰는 데이터 구조 및 멤버 변수를 들 수 있습니다. 서비스를 구성 하는 방법에 따라 상태 파일 또는 디스크에 저장 되어 있는 기타 리소스를 포함할 수도 있습니다. 예를 들어, 상태 데이터 및 트랜잭션 로그를 저장할 데이터베이스를 사용 하 여 파일을 포함할 수 있습니다.

상태는 구체화 되거나 상태를 조작 하는 코드와 함께 있습니다. 일반적으로 데이터베이스 또는 네트워크를 통해 서로 다른 컴퓨터에서 실행 되는 또는 동일한 컴퓨터에서 out-of-process를 실행 하는 다른 데이터 저장소를 사용 하 여 상태를 외부화 합니다.

컨테이너 및 마이크로 서비스는 내부에 실행 되는 프로세스 상태를 유지 하지 않습니다 하는 경우 가장 복원 합니다. 응용 프로그램 상태를 거의 항상 포함 PostgreSQL 또는 Azure SQL Database에 대 한 MySQL, Azure Database에 대 한 Azure Database와 같은 PaaS 솔루션을 사용 합니다.

이식 가능한 응용 프로그램을 작성 하려면 다음 지침을 참조 하세요.

* [12 개 요소 앱 방법론](https://12factor.net/)
* [여러 Azure 지역에 웹 응용 프로그램을 실행 합니다.](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>스토리지 마이그레이션 계획 작성

**모범 사례**: Azure Storage를 사용 하는 경우 준비 하 고 백업 지역에 주 지역에서 저장소를 마이그레이션하는 방법을 테스트 합니다.

응용 프로그램 데이터에 대 한 Azure Storage를 사용할 수 있습니다. 응용 프로그램은 서로 다른 지역의 여러 AKS 클러스터 분산으로, 때문에 동기화 저장소 유지 해야 합니다. 저장소를 복제 하도록 두 가지 일반적인 방법은 다음과 같습니다.

* 인프라 기반 비동기 복제
* 애플리케이션 기반 비동기 복제

### <a name="infrastructure-based-asynchronous-replication"></a>인프라 기반 비동기 복제

Pod를 삭제 한 후에 응용 프로그램에서 영구 저장소를 필요할 수 있습니다. Kubernetes에서 영구 데이터 저장소에 영구 볼륨을 사용할 수 있습니다. 영구적 볼륨 노드 VM에 탑재 되며 pod에 공개 됩니다. 영구적 볼륨 pod는 동일한 클러스터 내의 다른 노드로 이동 하는 경우에 pod를 수행 합니다.

사용할 복제 전략 저장소 솔루션에 따라 달라 집니다. 와 같은 일반적인 저장소 솔루션 [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)를 [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/)를 [루크](https://rook.io/docs/rook/master/disaster-recovery.html), 및 [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 재해 복구에 대 한 고유한 지침을 제공 하 고 복제 합니다.

일반적인 전략은 응용 프로그램이 해당 데이터를 쓸 수 있는 공용 저장소 지점을 제공 하는 것입니다. 이 데이터는 여러 Azure 지역에 복제된 후 로컬로 액세스됩니다.

![인프라 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure Managed Disks를 사용 하는 경우에 복제와 같은 DR 솔루션을 선택할 수 있습니다.

* [Azure에서 Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>애플리케이션 기반 비동기 복제

Kubernetes는 현재 응용 프로그램 기반 비동기 복제에 대 한 기본 구현이 제공합니다. 컨테이너 및 Kubernetes 느슨하게 결합 되므로 때문에 모든 기존 응용 프로그램 또는 언어 방법은 작동 합니다. 일반적으로 응용 프로그램 자체는 다음 각 클러스터의 기본 데이터 저장소에 기록 되는 저장소 요청을 복제 합니다.

![애플리케이션 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 비즈니스 연속성 및 AKS 클러스터에 대 한 재해 복구 고려 사항에 중점을 둡니다. AKS 클러스터 작업에 대 한 자세한 내용은 모범 사례에 대 한 이러한 문서를 참조 하세요.

* [클러스터 및 다중 테 넌 트 격리][aks-best-practices-cluster-isolation]
* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
