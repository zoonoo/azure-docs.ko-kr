---
title: 개념 - AKS(Azure Kubernetes Service)의 보안
description: 마스터 및 노드 통신, 네트워크 정책 및 Kubernetes 비밀을 비롯한 AKS(Azure Kubernetes Service)의 보안에 대해 알아봅니다.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
ms.openlocfilehash: b3ad8fdce873b31c8ea6b1c8176ed41587b4b298
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507100"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 애플리케이션 및 클러스터에 대한 보안 개념

AKS(Azure Kubernetes Service)에서 애플리케이션 워크로드를 실행하는 경우 고객 데이터를 보호하려면 클러스터의 보안이 주요한 고려 사항입니다. Kubernetes에는 *네트워크 정책* 및 *비밀*과 같은 보안 구성 요소가 포함됩니다. Azure는 네트워크 보안 그룹 및 오케스트레이션된 클러스터 업그레이드와 같은 구성 요소를 추가합니다. 이러한 보안 구성 요소를 결합하여 AKS 클러스터에 최신 OS 보안 업데이트 및 Kubernetes 릴리스가 실행되고 안전한 Pod 트래픽 및 중요한 자격 증명에 액세스할 수 있도록 합니다.

이 문서에서는 AKS에서 애플리케이션을 보호하는 핵심 개념을 소개합니다.

- [AKS(Azure Kubernetes Service)의 애플리케이션 및 클러스터에 대한 보안 개념](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [마스터 보안](#master-security)
  - [노드 보안](#node-security)
    - [계산 격리](#compute-isolation)
  - [클러스터 업그레이드](#cluster-upgrades)
    - [차단 및 드레이닝](#cordon-and-drain)
  - [네트워크 보안](#network-security)
    - [Azure 네트워크 보안 그룹](#azure-network-security-groups)
  - [Kubernetes 비밀](#kubernetes-secrets)
  - [다음 단계](#next-steps)

## <a name="master-security"></a>마스터 보안

AKS에서 Kubernetes 마스터 구성 요소는 Microsoft에서 제공하는 관리 서비스에 속합니다. 각 AKS 클러스터에는 API 서버, 스케줄러 등을 제공 하는 고유한 단일 테 넌 트, 전용 Kubernetes 마스터가 있습니다. 이 마스터는 Microsoft에서 관리 하 고 유지 관리 합니다.

기본적으로 Kubernetes API 서버는 공용 IP 주소 및 FQDN (정규화 된 도메인 이름)을 사용 합니다. [권한 있는 IP 범위][authorized-ip-ranges]를 사용 하 여 API 서버 끝점에 대 한 액세스를 제한할 수 있습니다. 가상 네트워크에 대 한 API 서버 액세스를 제한 하는 완전 한 [개인 클러스터][private-clusters] 를 만들 수도 있습니다.

Kubernetes 역할 기반 액세스 제어 및 Azure Active Directory를 사용하여 API 서버에 대한 액세스를 제어할 수 있습니다. 자세한 내용은 [AKS와 Azure AD 통합][aks-aad]을 참조하세요.

## <a name="node-security"></a>노드 보안

AKS 노드는 사용자가 관리하고 유지하는 Azure 가상 머신입니다. Linux 노드는 Moby 컨테이너 런타임을 사용 하 여 최적화 된 Ubuntu 배포를 실행 합니다. Windows Server 노드는 최적화 된 Windows Server 2019 릴리스를 실행 하 고 Moby 컨테이너 런타임도 사용 합니다. AKS 클러스터가 생성되거나 강화되면 노드는 최신 OS 보안 업데이트 및 구성을 사용하여 자동으로 배포됩니다.

Azure 플랫폼은 매일 매일 Linux 노드에 OS 보안 패치를 자동으로 적용 합니다. Linux OS 보안 업데이트에서 호스트를 다시 부팅 해야 하는 경우 다시 부팅이 자동으로 수행 되지 않습니다. Linux 노드를 수동으로 다시 부팅 하거나, Kubernetes에 대 한 오픈 소스 재부팅 디먼 인 [Kured][kured]를 사용 하는 것이 일반적인 방법입니다. Kured는 [DaemonSet][aks-daemonsets]으로 실행하며 다시 부팅해야 함을 표시하는 파일의 존재에 대한 각 노드를 모니터링합니다. 다시 부팅은 업그레이드와 동일한 [cordon 및 드레이닝 프로세스](#cordon-and-drain)를 사용하여 클러스터 전체에서 관리됩니다.

Windows Server 노드의 경우 Windows 업데이트가 자동으로 실행되고 최신 업데이트를 적용하지 않습니다. Windows 업데이트 릴리스 주기와 사용자 고유의 유효성 검사 프로세스를 정기적으로 수행 하는 일정에 따라 AKS 클러스터의 Windows Server 노드 풀에서 업그레이드를 수행 해야 합니다. 이 업그레이드 프로세스는 최신 Windows Server 이미지 및 패치를 실행하는 노드를 만든 다음, 이전 노드를 제거합니다. 이 프로세스에 대한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

노드는 공용 IP 주소가 할당되지 않은 상태에서 프라이빗 가상 네트워크 서브넷에 배포됩니다. 문제 해결 및 관리를 목적으로 SSH는 기본적으로 사용하도록 설정됩니다. SSH 액세스는 내부 IP 주소를 사용하는 경우에만 가능합니다.

스토리지를 제공하기 위해 노드는 Azure Managed Disks를 사용합니다. 대부분의 VM 노드 크기의 경우 해당되는 항목은 고성능 SSD로 지원되는 프리미엄 디스크입니다. 관리 디스크에 저장된 데이터는 미사용 시 Azure 플랫폼에서 자동으로 저장 데이터 암호화됩니다. 중복성을 높이기 위해 이러한 디스크는 Azure 데이터 센터 내에서 안전하게 복제됩니다.

AKS 또는 다른 곳의 Kubernetes 환경은 현재 악의적인 다중 테넌트 사용에 대해 완전히 안전하지 않습니다. *Pod 보안 정책*과 같은 추가 보안 기능 또는 노드에 대해 보다 세분화된 RBAC(역할 기반 액세스 제어)를 사용하면 악용이 더 어려워집니다. 그러나 악의적인 다중 테넌트 워크로드를 실행할 때 진정한 보안을 위해서는 하이퍼바이저가 신뢰할 수 있는 유일한 보안 수준입니다. Kubernetes의 보안 도메인은 개별 노드가 아닌 전체 클러스터가 됩니다. 이러한 유형의 악의적인 다중 테넌트 워크로드의 경우 물리적으로 격리된 클러스터를 사용해야 합니다. 워크 로드를 격리 하는 방법에 대 한 자세한 내용은 [AKS의 클러스터 격리에 대 한 모범 사례][cluster-isolation]를 참조 하세요.

### <a name="compute-isolation"></a>계산 격리

 특정 워크 로드는 규정 준수 또는 규제 요구 사항으로 인해 다른 고객 워크 로드와 높은 수준의 격리를 요구할 수 있습니다. 이러한 워크 로드의 경우 Azure는 AKS 클러스터에서 에이전트 노드로 사용할 수 있는 [격리 된 가상 컴퓨터](../virtual-machines/isolation.md)를 제공 합니다. 이러한 격리 된 가상 머신은 특정 하드웨어 유형 및 단일 고객 전용으로 격리 됩니다. 

 AKS 클러스터에서 이러한 격리 된 가상 컴퓨터를 사용 하려면 AKS 클러스터를 만들거나 노드 풀을 추가할 때 [여기](../virtual-machines/isolation.md) 에 나열 된 격리 된 가상 컴퓨터 크기 중 하나를 **노드 크기로** 선택 합니다.


## <a name="cluster-upgrades"></a>클러스터 업그레이드

보안 및 규정 준수 또는 최신 기능을 사용하기 위해, Azure는 AKS 클러스터 및 구성 요소의 업그레이드를 오케스트레이션하는 도구를 제공합니다. 업그레이드 오케스트레이션에는 Kubernetes 마스터 및 에이전트 구성 요소가 모두 포함됩니다. AKS 클러스터에 대해 [사용 가능한 Kubernetes 버전의 목록을](supported-kubernetes-versions.md) 볼 수 있습니다. 업그레이드 프로세스를 시작하려면 사용 가능한 버전 중 하나를 지정합니다. 그러면 Azure에서 각각의 AKS 노드가 안전하게 차단되고 드레이닝되어 업그레이드가 수행됩니다.

### <a name="cordon-and-drain"></a>차단 및 드레이닝

업그레이드 프로세스가 진행 되는 동안 AKS 노드는 클러스터에서 개별적으로 통제 새 pod 예약 되지 않습니다. 그런 다음, 노드가 다음과 같이 드레이닝되고 업그레이드됩니다.

- 새 노드가 노드 풀에 배포 됩니다. 이 노드는 최신 OS 이미지 및 패치를 실행 합니다.
- 기존 노드 중 하나는 업그레이드에 대해 식별 됩니다. 이 노드의 pod는 노드 풀의 다른 노드에서 정상적으로 종료 되 고 예약 됩니다.
- 이 기존 노드는 AKS 클러스터에서 삭제 됩니다.
- 클러스터의 다음 노드는 업그레이드 프로세스의 일부로 모든 노드가 성공적으로 바뀔 때까지 동일한 프로세스를 사용 하 여 통제 되 고 방전 됩니다.

자세한 내용은 [AKS 클러스터 업그레이드][aks-upgrade-cluster]를 참조하세요.

## <a name="network-security"></a>네트워크 보안

온-프레미스 네트워크와의 연결 및 보안을 위해 AKS 클러스터를 기존 Azure 가상 네트워크 서브넷에 배포할 수 있습니다. 이러한 가상 네트워크에서는 온-프레미스 네트워크로 Express Route가 다시 연결되거나 Azure 사이트 간 VPN이 있을 수 있습니다. Kubernetes 수신 컨트롤러는 개인 및 내부 IP 주소로 정의될 수 있기 때문에 서비스는 이러한 내부 네트워크 연결을 통해서만 액세스할 수 있습니다.

### <a name="azure-network-security-groups"></a>Azure 네트워크 보안 그룹

가상 네트워크에서 트래픽 흐름을 필터링하기 위해 Azure에 네트워크 보안 그룹 규칙이 사용됩니다. 이러한 규칙은 리소스에 대한 액세스가 허용되거나 거부되는 원본 및 대상 IP 범위, 포트 및 프로토콜을 정의합니다. Kubernetes API 서버에 대 한 TLS 트래픽을 허용 하는 기본 규칙이 생성 됩니다. 부하 분산 장치, 포트 매핑 또는 수신 경로가 있는 서비스를 만들면 AKS에서 트래픽이 적절하게 흐르도록 네트워크 보안 그룹이 자동으로 수정됩니다.

AKS 클러스터에 대 한 고유한 서브넷을 제공 하 고 트래픽 흐름을 수정 하려는 경우 AKS에서 관리 하는 서브넷 수준 네트워크 보안 그룹을 수정 하지 마십시오. 추가 서브넷 수준 네트워크 보안 그룹을 만들어 트래픽 흐름을 수정할 수 있습니다 (예: 부하 분산 장치 액세스, 제어 평면과의 통신, [송신][aks-limit-egress-traffic]등).

### <a name="kubernetes-network-policy"></a>Kubernetes 네트워크 정책

클러스터의 pod 간에 네트워크 트래픽을 제한 하기 위해 AKS는 [Kubernetes 네트워크 정책][network-policy]에 대 한 지원을 제공 합니다. 네트워크 정책을 사용 하 여 네임 스페이스 및 레이블 선택기에 따라 클러스터 내의 특정 네트워크 경로를 허용 하거나 거부 하도록 선택할 수 있습니다.

## <a name="kubernetes-secrets"></a>Kubernetes 비밀

Kubernetes *비밀*은 액세스 자격 증명이나 키와 같은 Pod에 중요한 데이터를 삽입하는 데 사용됩니다. 먼저 Kubernetes API를 사용하여 비밀을 만듭니다. Pod 또는 배포를 정의할 때 특정 비밀을 요청할 수 있습니다. 비밀은 예약된 Pod가 있고 비밀이 필요한 노드에만 제공되며, 디스크에 기록되지 않고 *tmpfs*에 저장됩니다. 비밀이 필요한 노드의 마지막 Pod가 삭제되면 노드의 tmpfs에서 비밀이 삭제됩니다. 비밀은 지정된 네임스페이스 내에 저장되며 동일한 네임스페이스 내의 Pod에서만 액세스할 수 있습니다.

비밀을 사용하면 Pod 또는 서비스 YAML 매니페스트에 정의되는 중요한 정보가 줄어듭니다. 대신 Kubernetes API 서버에 저장된 비밀을 YAML 매니페스트의 일부로 요청합니다. 이 방법은 비밀에 대한 특정 Pod 액세스만 제공합니다. 참고: 원시 비밀 매니페스트 파일에는 base64 형식의 비밀 데이터가 포함 되어 있습니다. 자세한 내용은 [공식 설명서][secret-risks] 를 참조 하세요. 따라서이 파일은 중요 한 정보로 처리 되어야 하며 소스 제어에 커밋되지 않습니다.

Kubernetes 암호는 배포 키-값 저장소 인 etcd에 저장 됩니다. Etcd 스토어는 AKS에서 완벽 하 게 관리 되 고 [데이터는 Azure 플랫폼 내에서 미사용으로 암호화][encryption-atrest]됩니다. 

## <a name="next-steps"></a>다음 단계

AKS 클러스터의 보안을 유지하려면 [AKS 클러스터 업그레이드][aks-upgrade-cluster]를 참조하세요.

관련 모범 사례는 [AKS의 클러스터 보안 및 업그레이드 모범 사례][operator-best-practices-cluster-security] 및 [AKS의 pod 보안에 대 한][developer-best-practices-pod-security]모범 사례를 참조 하세요.

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS ID][aks-concepts-identity]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 스토리지][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
