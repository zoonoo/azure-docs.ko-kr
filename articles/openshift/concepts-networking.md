---
title: 개념 - OpenShift 4의 Azure Red Hat에 대한 네트워킹 다이어그램
description: Azure Red Hat OpenShift 네트워킹에 대한 네트워킹 다이어그램 및 개요
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419974"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>OpenShift 4의 Azure Red Hat 네트워킹

이 가이드에서는 OpenShift 4 클러스터의 Azure Red Hat 네트워킹 개요와 함께 다이어그램 및 중요한 엔드포인트 목록을 다룹니다.

핵심 OpenShift 네트워킹 개념에 대한 자세한 내용은 [Azure Red Hat OpenShift 4 네트워킹 설명서](https://docs.openshift.com/aro/4/networking/understanding-networking.html)를 참조하세요.

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift의 네트워킹 개념

![Azure Red Hat OpenShift 4 네트워킹 다이어그램](./media/concepts-networking/aro4-networking-diagram.png)

OpenShift 4에서 Azure Red Hat을 배포하는 경우 전체 클러스터가 가상 네트워크에 포함됩니다. 이 가상 네트워크 내에서 마스터 노드와 작업자 노드는 각각 자체 서브넷에 상주합니다. 각 서브넷은 공용 및 내부 부하 분산 장치를 사용합니다.

## <a name="explanation-of-endpoints"></a>엔드포인트 설명

다음 목록에서는 Azure Red Hat OpenShift 클러스터의 중요한 엔드포인트를 다룹니다.

* **aro-pls**
    * Microsoft 및 Red Hat site 안정성 엔지니어가 클러스터를 관리할 때 사용하는 Azure Private Link 엔드포인트입니다.
* **aro-internal-lb**
    * 이 엔드포인트는 api 서버로 트래픽을 분산합니다. 이 부하 분산 장치의 경우 마스터 노드가 백 엔드 풀에 있습니다.
* **aro-public-lb**
    * api가 공용인 경우 이 엔드포인트는 트래픽을 api 서버로 라우팅하고 분산합니다. 이 엔드포인트는 마스터가 Azure Resource Manager에 액세스하여 클러스터 상태를 다시 보고할 수 있도록 나가는 공용 IP를 할당합니다.
* **aro-internal**
    * 이 엔드포인트는 내부 서비스 트래픽의 균형을 조정합니다. 이 부하 분산 장치의 경우 작업자 노드가 백 엔드 풀에 있습니다.
    * 이 부하 분산 장치는 기본적으로 생성되지 않습니다. 이 부하 분산 장치는 올바른 주석을 사용하여 LoadBalancer 형식의 서비스를 만든 후에 생성됩니다. 예: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **네트워크 정책(수신)**
    * OpenShift SDN의 일부로 지원됩니다.
    * 기본적으로 사용하도록 설정되며, 고객이 적용합니다.
    * V1 NetworkPolicy 규격이지만, "송신 및 IPBlock"은 아직 지원되지 않습니다.
    * **aro**
    * 이 엔드포인트는 api 서버로 트래픽을 분산합니다. 이 부하 분산 장치의 경우 마스터 노드가 백 엔드 풀에 있습니다.
  * **aro-internal-lb**
    * 이 엔드포인트는 모든 공용 트래픽에 사용됩니다. 애플리케이션 및 경로를 만들 때에는 수신 트래픽의 경로입니다.
    * 이 부하 분산 장치는 Azure Load Balancer 아웃바운드 규칙을 통해 작업자 노드에서 실행 중인 Pod의 송신 인터넷 연결을 다룹니다.
        * 현재는 아웃바운드 규칙을 구성할 수 없습니다. 아웃바운드 규칙은 각 노드에 TCP 포트 1,024개를 할당합니다.
        * LB 규칙에 DisableOutboundSnat가 구성되지 않았으므로, Pod가 이 ALB에 구성된 공용 IP를 송신 IP로 가져올 수 있습니다.
        * 앞의 두 지점의 결과로, 임시 SNAT 포트를 추가하는 유일한 방법은 ARO에 공용 LoadBalancer 유형 서비스를 추가하는 것입니다.
* **네트워크 정책(송신)**
    * 송신 정책은 OpenShift의 송신 방화벽 기능을 사용하여 지원됩니다.
    * 네임스페이스/프로젝트당 하나만 지원됩니다.
    * 송신 정책은 "기본" 네임스페이스에서 지원되지 않습니다.
    * 송신 정책 규칙은 순서대로(첫 번째 규칙부터 마지막 규칙까지) 평가됩니다.
    * **aro-outbound-pip**
        * 이 엔드포인트는 작업자 노드의 PIP(공용 IP)로 사용됩니다.
        * 이 엔드포인트를 통해 서비스는 Azure Red Hat OpenShift 클러스터에서 들어오는 특정 IP를 허용 목록에 추가할 수 있습니다.
* **aro-node-nsg**
    * 서비스를 노출할 때 api는 트래픽이 통과하여 노드에 도달할 수 있도록 이 네트워크 보안 그룹에 규칙을 만듭니다.
    * 기본적으로 이 네트워크 보안 그룹은 모든 아웃바운드 트래픽을 허용합니다. 현재는 아웃바운드 트래픽을 Azure Red Hat OpenShift 컨트롤 플레인으로만 제한할 수 있습니다.
* **aro-controlplane-nsg**
    * 이 엔드포인트는 마스터 노드의 6443 포트로 들어가는 트래픽만 허용합니다.
* **Azure Container Registry**
    * Microsoft에서 내부적으로 제공하고 사용하는 컨테이너 레지스트리입니다.
        * 이 레지스트리는 호스트 플랫폼 이미지 및 클러스터 구성 요소를 제공합니다. 예를 들어 컨테이너를 모니터링하거나 기록합니다.
        * Azure Red Hat OpenShift 고객은 사용할 수 없습니다.  
        * 읽기 전용입니다.
        * 이 레지스트리에 대한 연결은 서비스 엔드포인트(Azure 서비스 간의 내부 연결)을 통해 수행됩니다.
        * 이 내부 레지스트리는 기본적으로 클러스터 외부에서 사용할 수 없습니다.
* **Private Link**
    * 클러스터를 관리하기 위해 네트워크를 통해 관리 평면에서 클러스터로 연결하는 것을 허용합니다.
    * 클러스터 관리를 도와주는 Microsoft 및 Red Hat 사이트 안정성 엔지니어입니다.

## <a name="networking-basics-in-openshift"></a>OpenShift의 네트워킹 기본 사항

OpenShift SDN(소프트웨어 정의 네트워킹)은 CNI(Container Network Interface) 사양 기반의 OpenFlow 구현인 OVS(Open vSwitch)를 사용하여 오버레이 네트워크를 구성하는 데 사용됩니다. SDN은 여러 플러그 인을 지원하며, 네트워크 정책은 OpenShift 4의 Azure Red Hat에서 사용되는 플러그 인입니다. 모든 네트워크 통신이 SDN에 의해 관리되므로, 가상 네트워크에서 Pod 간 통신을 수행하기 위한 추가 경로가 필요 없습니다.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Azure Red Hat OpenShift 네트워킹 사양

다음은 Azure Red Hat OpenShift와 관련된 기능입니다.
* 사용자 고유의 가상 네트워크를 사용할 수 있습니다.
* Pod 및 서비스 네트워크 CIDR을 구성할 수 있습니다.
* 노드와 마스터는 다른 서브넷에 있습니다.
* 노드 및 마스터 가상 네트워크 서브넷은 최소 /27이어야 합니다.
* Pod CIDR의 최소 크기는 /18입니다(Pod 네트워크는 라우팅할 수 없는 IP이며, OpenShift SDN 내에서만 사용됨).
* 각 노드에는 Pod에 대한 /23 서브넷(512개 IP)이 할당됩니다. 이 값은 변경할 수 없습니다.
* Pod를 여러 네트워크에 연결할 수 없습니다.
* 송신 고정 IP를 구성할 수 없습니다. (이것은 OpenShift 기능입니다. 자세한 내용은 [송신 IP 구성](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)을 참조하세요).

## <a name="network-settings"></a>네트워크 설정

다음 네트워크 설정은 Azure Red Hat OpenShift 4에서 사용할 수 있습니다.

* **API 표시 유형** - [az aro create 명령](tutorial-create-cluster.md#create-the-cluster)을 실행할 때 API 표시 여부를 설정합니다.
    * "퍼블릭" - 외부 네트워크에서 API 서버에 액세스할 수 있습니다.
    * "프라이빗" - API 서버에 마스터 서브넷의 개인 IP가 할당되므로, 연결된 네트워크(피어링된 가상 네트워크, 클러스터의 다른 서브넷)를 사용해야만 액세스할 수 있습니다. 프라이빗 DNS 영역은 고객을 대신하여 생성됩니다.
* **수신 표시 유형** - [az aro create 명령](tutorial-create-cluster.md#create-the-cluster)을 실행할 때 API 표시 여부를 설정합니다.
    * "퍼블릭" 경로는 기본적으로 퍼블릭 Azure 표준 Load Balancer입니다(변경 가능).
    * "프라이빗" 경로는 기본적으로 내부 부하 분산 장치입니다(변경 가능).

## <a name="network-security-groups"></a>네트워크 보안 그룹
네트워크 보안 그룹은 노드의 리소스 그룹(잠겨 있음)에 생성됩니다. 네트워크 보안 그룹은 노드의 NIC가 아닌 서브넷에 직접 할당됩니다. 네트워크 보안 그룹은 변경할 수 없습니다. 즉, 네트워크 보안 그룹을 변경할 수 있는 권한이 없습니다. 

그러나 공개적으로 표시되는 API 서버를 사용하는 경우에는 네트워크 보안 그룹을 만들어 NIC에 할당할 수 없습니다.

## <a name="domain-forwarding"></a>도메인 전달
Azure Red Hat OpenShift는 CoreDNS를 사용하므로 도메인 전달을 구성할 수 있습니다(자세한 내용은 [DNS 전달 사용](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) 설명서 참조).

현재는 자체 DNS를 가상 네트워크에 가져올 수 없습니다.


아웃바운드 트래픽 및 Azure Red Hat OpenShift의 송신 지원에 대한 자세한 내용은 [지원 정책](support-policies-v4.md) 설명서를 참조하세요.
