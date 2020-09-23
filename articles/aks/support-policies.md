---
title: AKS(Azure Kubernetes Service)에 대한 지원 정책
description: AKS(Azure Kubernetes Service) 지원 정책, 공유 책임 및 미리 보기(또는 알파 또는 베타) 상태인 기능에 대해 자세히 알아봅니다.
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 188416d9ef6f8e7568b10e8ccbb405be0bff315d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888988"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대한 지원 정책

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 기술 지원 정책 및 제한 사항에 대해 자세히 설명합니다. 또한이 문서에서는 에이전트 노드 관리, 관리 되는 제어 평면 구성 요소, 타사 오픈 소스 구성 요소 및 보안 또는 패치 관리에 대해 자세히 설명 합니다.

## <a name="service-updates-and-releases"></a>서비스 업데이트 및 릴리스

* 릴리스 정보는 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases)를 참조하세요.
* 미리 보기 상태인 기능에 대한 자세한 내용은 [AKS 미리 보기 기능 및 관련 프로젝트](https://awesomeopensource.com/projects/aks?categoryPage=11)를 참조하세요.

## <a name="managed-features-in-aks"></a>AKS의 관리 기능

기본 IaaS (infrastructure as a service) 클라우드 구성 요소 (예: 계산 또는 네트워킹 구성 요소)를 사용 하 여 하위 수준 컨트롤 및 사용자 지정 옵션에 액세스할 수 있습니다. 이와 대조적으로 AKS는 클러스터에 필요한 구성 및 기능에 대 한 공통 집합을 제공 하는 턴키 Kubernetes 배포를 제공 합니다. AKS 사용자는 제한 된 사용자 지정 및 배포 옵션을 사용할 수 있습니다. Exchange에서는 Kubernetes 클러스터를 직접 걱정 하거나 관리할 필요가 없습니다.

AKS를 사용 하면 완전히 관리 되는 *제어 평면*을 얻을 수 있습니다. 제어 평면에는 작동 하는 데 필요한 모든 구성 요소 및 서비스가 포함 되며 Kubernetes 클러스터를 최종 사용자에 게 제공 합니다. 모든 Kubernetes 구성 요소는 Microsoft에서 유지 관리하고 운영합니다.

Microsoft는 제어 창에서 다음 구성 요소를 관리하고 모니터링합니다.

* Kubelet 또는 Kubernetes API 서버
* QoS(서비스 품질), 확장성 및 런타임을 제공하는 etcd 또는 호환되는 키-값 저장소
* DNS 서비스(예: kube-dns 또는 CoreDNS)
* Kubernetes 프록시 또는 네트워킹
* Kube 네임 스페이스에서 실행 되는 추가 addon 또는 system 구성 요소

AKS는 PaaS (Platform as a Service) 솔루션이 아닙니다. 에이전트 노드와 같은 일부 구성 요소에는 사용자가 AKS 클러스터를 유지 관리 하는 데 도움이 되어야 하는 *공유 책임이*있습니다. 사용자 입력은 예를 들어, 에이전트 노드 운영 체제 (OS) 보안 패치를 적용 하는 데 필요 합니다.

Microsoft와 AKS 팀이 서비스 가용성 및 기능을 배포하고 운영하고 이에 대한 책임을 진다는 점에서 서비스는 ‘관리형’입니다. 고객은 이러한 관리형 구성 요소를 변경할 수 없습니다. Microsoft는 일관되고 확장성 있는 사용자 환경을 보장하기 위해 사용자 지정을 제한합니다. 완전히 사용자 지정 가능한 솔루션은 [AKS 엔진](https://github.com/Azure/aks-engine)을 참조하세요.

## <a name="shared-responsibility"></a>공동 책임

클러스터가 생성 되 면 AKS에서 생성 하는 Kubernetes agent 노드를 정의 합니다. 작업은 이러한 노드에서 실행 됩니다.

에이전트 노드는 개인 코드를 실행 하 고 중요 한 데이터를 저장 하기 때문에 매우 제한 된 방법 으로만 액세스할 수 Microsoft 지원. Express 권한이 나 지원 없이에 로그인 하거나, 명령을 실행 하거나, 이러한 노드에 대 한 로그를 볼 수 Microsoft 지원.

IaaS Api를 사용 하 여 에이전트 노드를 직접 수정 하면 클러스터 unsupportable 렌더링 됩니다. 에이전트 노드를 수정 하는 작업은 같은 kubernetes 메커니즘을 사용 하 여 수행 해야 합니다 `Daemon Sets` .

마찬가지로, 클러스터에 메타 데이터를 추가 하 고 태그 및 레이블과 같은 노드에 메타 데이터를 추가할 수 있는 경우 시스템에서 만든 메타 데이터를 변경 하면 클러스터가 지원 되지 않는 상태로 렌더링 됩니다.

## <a name="aks-support-coverage"></a>AKS 지원 범위

Microsoft는 다음 예제에 대 한 기술 지원을 제공 합니다.

* Kubernetes 서비스가 제공하고 지원하는 모든 Kubernetes 구성 요소(예 API 서버)에 대한 연결
* Kubernetes 제어 평면 서비스 (예: Kubernetes 제어 평면, API 서버, etcd 및 coreDNS)의 관리, 작동 시간, QoS 및 작업입니다.
* Etcd 데이터 저장소입니다. 지원에는 재해 계획 및 클러스터 상태 복원에 대해 30분마다 모든 etcd 데이터의 자동화된 투명 백업이 포함됩니다. 이러한 백업은 사용자나 사용자가 직접 사용할 수 없습니다. 데이터 안정성 및 일관성을 보장합니다. Etcd. 주문형 롤백 또는 복원은 기능으로 지원 되지 않습니다.
* Kubernetes용 Azure 클라우드 공급자 드라이버의 모든 통합 요소. 여기에는 부하 분산 장치, 일관된 볼륨 또는 네트워킹(Kubernetes 및 Azure CNI)과 같은 다른 Azure 서비스에 대한 통합이 포함됩니다.
* Kubernetes API 서버, etcd 및 coreDNS와 같은 제어 평면 구성 요소의 사용자 지정에 대 한 질문이 나 문제
* 네트워킹(예: Azure CNI, kubenet 또는 기타 네트워크 액세스)에 관한 문제 및 기능 문제 문제에는 DNS 확인, 패킷 손실, 라우팅 등이 포함될 수 있습니다. Microsoft는 다음과 같은 다양한 네트워킹 시나리오를 지원합니다.
  * Kubenet 및 Azure CNI를 사용 하 여 관리 되는 Vnet을 사용 하거나 사용자 지정 (사용자 고유의 서브넷 가져오기)을 사용 합니다.
  * 다른 Azure 서비스 및 애플리케이션에 대한 연결
  * 수신 컨트롤러 및 수신 또는 부하 분산 장치 구성
  * 네트워크 성능 및 대기 시간


> [!NOTE]
> Microsoft/AKS에서 수행 하는 모든 클러스터 작업은 기본 제공 Kubernetes 역할 `aks-service` 및 기본 제공 역할 바인딩에서 사용자 동의를 사용 하 여 수행 됩니다 `aks-service-rolebinding` . 이 역할을 통해 AKS는 클러스터 문제를 해결 하 고 진단할 수 있지만 권한을 수정 하거나 역할 또는 역할 바인딩 또는 기타 높은 권한 작업을 만들 수 없습니다. 역할 액세스는 JIT (just-in-time) 액세스를 사용 하는 활성 지원 티켓 에서만 사용 하도록 설정 됩니다.

Microsoft는 다음 예제에 대 한 기술 지원을 제공 하지 않습니다.

* Kubernetes 사용 방법에 대한 질문. 예를 들어 Microsoft 지원은 사용자 지정 수신 컨트롤러를 만들거나, 애플리케이션 워크로드를 사용하거나, 타사 또는 오픈 소스 소프트웨어 패키지 또는 도구를 적용하는 방법에 대한 조언을 제공하지 않습니다.
  > [!NOTE]
  > Microsoft 지원은 AKS 클러스터 기능, 사용자 지정 및 튜닝(예: Kubernetes 작업 문제 및 절차)에 대해 조언할 수 있습니다.
* Kubernetes 컨트롤 플레인의 일부로 제공되거나 AKS 클러스터와 함께 배포되지 않은 타사 오픈 소스 프로젝트. 이러한 프로젝트에는 Istio, Helm, Envoy 등이 포함될 수 있습니다.
  > [!NOTE]
  > Microsoft는 투구와 같은 타사 오픈 소스 프로젝트에 대 한 최상의 지원을 제공할 수 있습니다. 타사 오픈 소스 도구가 Kubernetes Azure 클라우드 공급자 또는 다른 AKS 관련 버그와 통합되는 경우 microsoft는 Microsoft 설명서의 예제 및 애플리케이션을 지원합니다.
* 타사의 비공개 소스 소프트웨어. 이 소프트웨어에는 보안 검사 도구와 네트워킹 디바이스 또는 소프트웨어가 포함될 수 있습니다.
* [AKS 설명서](./index.yml)에 나열 되지 않은 네트워크 사용자 지정.


## <a name="aks-support-coverage-for-agent-nodes"></a>에이전트 노드에 대 한 AKS 지원 검사

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>AKS agent 노드에 대 한 Microsoft 책임

Microsoft 및 사용자는 다음과 같은 Kubernetes agent 노드에 대 한 책임을 공유 합니다.

* 기본 OS 이미지에는 모니터링 및 네트워킹 에이전트와 같은 추가 요구 사항이 있습니다.
* 에이전트 노드가 자동으로 OS 패치를 수신 합니다.
* 에이전트 노드에서 실행 되는 Kubernetes 제어 평면 구성 요소와 관련 된 문제는 자동으로 재구성 됩니다. 이러한 구성 요소는 다음과 같습니다.
  * `Kube-proxy`
  * Kubernetes 마스터 구성 요소에 대한 통신 경로를 제공하는 네트워킹 터널
  * `Kubelet`
  * `Moby` 또는 `ContainerD`

> [!NOTE]
> 에이전트 노드가 작동 하지 않는 경우 AKS는 개별 구성 요소나 전체 에이전트 노드를 다시 시작할 수 있습니다. 이러한 다시 시작 작업은 자동화 되 고 일반적인 문제에 대 한 자동 수정 기능을 제공 합니다. 자동 수정 메커니즘에 대해 자세히 알아보려면 [노드 자동 복구](node-auto-repair.md) 를 참조 하세요.

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>AKS agent 노드에 대 한 고객 책임

Microsoft는 이미지 노드에 대 한 패치 및 새 이미지를 기본적으로 제공 하지만 자동으로 패치를 제공 하지는 않습니다. 에이전트 노드 OS 및 런타임 구성 요소를 패치 된 상태로 유지 하려면 일반 [노드 이미지 업그레이드](node-image-upgrade.md) 일정을 유지 하거나 자동화 해야 합니다.

마찬가지로 AKS는 새로운 kubernetes 패치와 부 버전을 정기적으로 릴리스 합니다. 이러한 업데이트에는 Kubernetes에 대 한 보안 또는 기능 향상이 포함 될 수 있습니다. 클러스터의 kubernetes 버전이 업데이트 되 고 [AKS Kubernetes 지원 버전 정책](supported-kubernetes-versions.md)에 따라 유지 됩니다.

#### <a name="user-customization-of-agent-nodes"></a>에이전트 노드의 사용자 지정
> [!NOTE]
> AKS 에이전트 노드는 Azure Portal 일반 Azure IaaS 리소스로 표시 됩니다. 그러나 이러한 가상 머신은 사용자 지정 Azure 리소스 그룹에 배포 됩니다 (일반적으로 MC_ 접두사가 붙은 \* ). IaaS Api 또는 리소스를 사용 하 여 기본 OS 이미지를 변경 하거나 이러한 노드에 대 한 직접 사용자 지정을 수행할 수 없습니다. AKS API를 통해 수행 되지 않는 모든 사용자 지정 변경 내용은 업그레이드, 크기 조정, 업데이트 또는 다시 부팅이 지속 되지 않습니다. Microsoft 지원 변경 하도록 지시 하지 않는 한 에이전트 노드에 대 한 변경 작업을 수행 하지 않습니다.

AKS는 사용자를 대신해 에이전트 노드의 수명 주기 및 작업을 관리 합니다. 에이전트 노드와 연결 된 IaaS 리소스 수정은 **지원 되지 않습니다**. 지원 되지 않는 작업의 예는 가상 머신 확장 집합 포털 또는 API를 통해 수동으로 구성을 변경 하 여 노드 풀 가상 머신 확장 집합을 사용자 지정 하는 것입니다.
 
워크 로드 관련 구성 또는 패키지의 경우 AKS는 [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)를 사용 하는 것이 좋습니다.

Kubernetes 권한 `daemon sets` 및 init 컨테이너를 사용 하면 클러스터 에이전트 노드에 타사 소프트웨어를 조정/수정 하거나 설치할 수 있습니다. 이러한 사용자 지정의 예로는 사용자 지정 보안 검사 소프트웨어 추가 또는 sysctl 설정 업데이트가 포함됩니다.

위의 요구 사항이 적용 되는 경우이 경로를 지정 하는 것이 좋지만, AKS 엔지니어링 및 지원은 사용자 지정 배포로 인해 노드를 사용할 수 없게 렌더링 하는 수정 문제를 해결 하거나 문제를 진단할 수 없습니다 `daemon set` .

### <a name="security-issues-and-patching"></a>보안 문제 및 패치

AKS의 관리 되는 구성 요소 중 하나 이상에 보안 결함이 발견 되 면 AKS 팀은 영향을 받는 모든 클러스터에 패치를 적용 하 여 문제를 완화 합니다. 또는 팀에서 사용자에게 업그레이드 지침을 제공합니다.

보안 결함의 영향을 받는 에이전트 노드의 경우 Microsoft는 보안 문제를 해결 하거나 완화 하기 위한 영향 및 단계에 대 한 세부 정보 (일반적으로 노드 이미지 업그레이드 또는 클러스터 패치 업그레이드)를 알려 줍니다.

### <a name="node-maintenance-and-access"></a>노드 유지 관리 및 액세스

에 로그인 하 여 에이전트 노드를 변경할 수 있지만이 작업을 수행 하면 클러스터가 unsupportable 수 있으므로이 작업을 수행 하지 않는 것이 좋습니다.

## <a name="network-ports-access-and-nsgs"></a>네트워크 포트, 액세스 및 NSG

사용자 지정 서브넷 에서만 NSGs를 사용자 지정할 수 있습니다. 관리 되는 서브넷 또는 에이전트 노드의 NIC 수준에서 NSGs를 사용자 지정할 수 없습니다. AKS는 특정 끝점에 대 한 송신 요구 사항을 포함 하 여 송신을 제어 하 고 필요한 연결을 보장 합니다. [송신 트래픽 제한](limit-egress-traffic.md)을 참조 하세요.

## <a name="stopped-or-de-allocated-clusters"></a>중지 또는 할당 취소 된 클러스터

위에서 설명한 대로 IaaS Api/CLI/포털을 통해 모든 클러스터 노드를 수동으로 할당 취소 하면 클러스터가 지원 되지 않는 상태로 렌더링 됩니다.
모든 노드를 중지/할당 취소 하는 데 지원 되는 유일한 방법은 [최대 12 개월 동안 클러스터 상태를 유지 하는 aks 클러스터를 중지 하는 것입니다.

12 개월 이상 중지 된 클러스터는 더 이상 상태를 유지 하지 않습니다. 

AKS Api 외부에서 할당 취소 된 클러스터는 상태 유지를 보장 하지 않습니다. 이 상태에서 클러스터의 제어 평면은 30 일 후에 보관 되 고 12 개월 후에 삭제 됩니다.

AKS는 30일 및 그 이상의 연장된 기간 동안 지원 지침과 다르게 구성된 컨트롤 플레인을 보관할 권리를 보유합니다. AKS는 클러스터 etcd 메타데이터의 백업을 유지 관리하고 클러스터를 언제든 다시 할당할 수 있습니다. 이 재할당은 클러스터를 다시 지원(예: 활성 에이전트 노드에 대한 업그레이드 또는 스케일링) 상태로 돌리는 PUT 작업에 의해 시작될 수 있습니다.

구독이 일시 중지 되거나 삭제 되 면 클러스터의 제어 평면과 상태가 90 일 후에 삭제 됩니다.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>지원되지 않는 알파 및 베타 Kubernetes 기능

AKS는 업스트림 Kubernetes 프로젝트 내에서 안정적이 고 베타 기능만 지원 합니다. 다른 설명이 없는 한 AKS은 업스트림 Kubernetes 프로젝트에서 사용할 수 있는 모든 알파 기능을 지원 하지 않습니다.

## <a name="preview-features-or-feature-flags"></a>미리 보기 기능 또는 기능 플래그

확장 된 테스트 및 사용자 피드백이 필요한 기능을 위해 Microsoft는 기능 플래그 뒤에 새로운 미리 보기 기능이 나 기능을 릴리스 합니다. 이러한 기능을 시험판 또는 베타 기능으로 간주합니다.

미리 보기 기능 또는 기능 플래그 기능은 프로덕션용이 아닙니다. API 및 동작의 지속적인 변경, 버그 수정 및 기타 변경 사항은 클러스터 및 가동 중지 시간을 불안정하게 만들 수 있습니다.

공개 미리 보기 상태의 기능은 해당 기능이 미리 보기 상태이고 프로덕션용이 아니기 때문에 ‘최선의 노력을 기울이는’ 지원에 속하며 업무 시간에만 AKS 기술 지원 팀에서 지원합니다. 자세한 내용은 다음을 참조하세요.

* [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>업스트림 버그 및 문제

업스트림 Kubernetes 프로젝트에서는 개발 속도 때문에 버그가 항상 발생합니다. 이러한 버그 중 일부는 AKS 시스템 내에서 패치되거나 해결되지 못할 수 있습니다. 대신, 버그 수정에는 업스트림 프로젝트 (예: Kubernetes, 노드, 에이전트 운영 체제 및 커널)에 대 한 더 큰 패치가 필요 합니다. Microsoft에서 소유하는 구성 요소(예: Azure 클라우드 공급자)의 경우 AKS 및 Azure 담당자는 커뮤니티에서 업스트림 문제를 해결하기 위해 최선을 다하고 있습니다.

하나 이상의 업스트림 버그가 기술 지원 문제에 대한 근본 원인이 되는 경우 AKS 지원 및 엔지니어링 팀은 다음을 수행합니다.

* 업스트림 버그를 식별하고 이 문제가 클러스터 또는 워크로드에 영향을 주는 이유를 설명하는 뒷받침되는 세부 정보를 연결합니다. 고객은 필요한 리포지토리에 대한 링크를 받아서 문제를 감시하고 새 릴리스가 픽스를 제공할 시기를 확인할 수 있습니다.
* 잠재적 해결 방법 또는 완화 기능을 제공 합니다. 문제를 완화할 수 있는 경우 [알려진 문제](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)는 AKS 리포지토리에 기록됩니다. 알려진 문제 기록은 다음을 설명합니다.
  * 업스트림 버그에 대한 링크를 포함한 문제
  * 업그레이드 또는 솔루션의 다른 지속성에 대한 해결 방법 및 세부 정보
  * 업스트림 릴리스 흐름을 기준으로 할 때 문제가 포함되는 대략적인 타임라인
