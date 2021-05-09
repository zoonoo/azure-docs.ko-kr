---
title: AKS(Azure Kubernetes Service)에서 UDR(사용자 정의 경로) 사용자 지정
description: AKS(Azure Kubernetes Service)에서 사용자 지정 송신 경로를 정의하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: e9433978c8ee855ec66901c7692e4d2b59261fd3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773048"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>사용자 지정 경로를 사용하여 클러스터 송신 사용자 지정

AKS 클러스터에서 송신은 특정 시나리오에 맞게 사용자 지정할 수 있습니다. 기본적으로 AKS는 송신을 위해 설정 및 사용할 표준 SKU Load Balancer를 프로비저닝합니다. 그러나 공용 IP가 허용되지 않거나 송신에 대한 추가 홉이 필요한 경우에는 기본 설정이 모든 시나리오의 요구 사항을 충족하지 못할 수 있습니다.

이 문서에서는 공용 IP를 허용하지 않고 클러스터가 NVA(네트워크 가상 어플라이언스) 뒤에 있어야 하는 경우와 같은 사용자 지정 네트워크 시나리오를 지원하기 위해 클러스터의 송신 경로를 사용자 지정하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항
* Azure CLI 버전 2.0.81 이상
* API 버전 `2020-01-01` 이상


## <a name="limitations"></a>제한 사항
* OutboundType은 클러스터를 만들 때만 정의할 수 있으며 나중에 업데이트할 수 없습니다.
* `outboundType`을 설정하려면 `VirtualMachineScaleSets`가 `vm-set-type` 및 `Standard`의 `load-balancer-sku`를 사용하는 AKS 클러스터가 필요합니다.
* `outboundType`을 `UDR` 값으로 설정하려면 클러스터에 대한 유효한 아웃바운드 연결을 사용하는 사용자 정의 경로가 필요합니다.
* `outboundType`을 `UDR` 값으로 설정하면 부하 분산 장치로 라우팅되는 수신 원본 IP가 클러스터의 나가는 송신 대상 주소와 **일치하지 않을** 수 있습니다.

## <a name="overview-of-outbound-types-in-aks"></a>AKS의 아웃바운드 형식 개요

`loadBalancer` 또는 `userDefinedRouting` 형식의 고유한 `outboundType`을 사용하여 AKS 클러스터를 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> 아웃바운드 형식은 클러스터의 송신 트래픽에만 영향을 줍니다. 자세한 내용은 [setting up ingress controllers](ingress-basic.md)(수신 컨트롤러 설정)를 참조하세요.

> [!NOTE]
> UDR 및 kubenet 네트워킹에서 고유한 [경로 테이블][byo-route-table]을 사용할 수 있습니다. 클러스터 ID(서비스 주체 또는 관리 ID)에 사용자 지정 경로 테이블에 대한 Contributor 권한이 있는지 확인하세요.

### <a name="outbound-type-of-loadbalancer"></a>loadBalancer의 아웃바운드 형식

`loadBalancer`가 설정된 경우 AKS는 다음 구성을 자동으로 완료합니다. 부하 분산 장치는 AKS 할당 공용 IP를 통해 송신하는 데 사용됩니다. 아웃바운드 형식의 `loadBalancer`는 AKS 리소스 공급자가 만든 부하 분산 장치에서 송신하는 것으로 간주되는 `loadBalancer` 형식의 Kubernetes 서비스를 지원합니다.

다음 구성은 AKS가 수행합니다.
   * 공용 IP 주소가 클러스터 송신을 위해 프로비저닝됩니다.
   * 공용 IP 주소가 부하 분산 장치 리소스에 할당됩니다.
   * 부하 분산 장치의 백 엔드 풀이 클러스터의 에이전트 노드에 대해 설정됩니다.

다음은 기본적으로 AKS 클러스터에 배포된 네트워크 토폴로지이며 `loadBalancer`의 `outboundType`을 사용합니다.

![다이어그램에서는 수신 IP와 송신 IP를 보여 줍니다. 여기서 수신 IP는 트래픽을 부하 분산 장치로 보내고, 부하 분산 장치는 트래픽을 내부 클러스터와 주고받으며 기타 트래픽을 송신 IP에 보내고, 송신 IP는 트래픽을 인터넷, MCR, Azure 필수 서비스, AKS 컨트롤 플레인에 보냅니다.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>userDefinedRouting의 아웃바운드 형식

> [!NOTE]
> 아웃바운드 형식 사용은 고급 네트워킹 시나리오이며 적절한 네트워크 구성이 필요합니다.

`userDefinedRouting`이 설정된 경우 AKS는 자동으로 송신 경로를 구성하지 않습니다. 송신 설정을 직접 수행해야 합니다.

SLB(표준 부하 분산 장치) 아키텍처를 사용하지 않을 경우 명시적 송신을 설정해야 하므로 이전에 구성된 서브넷을 사용하여 기존 가상 네트워크에 AKS 클러스터를 배포해야 합니다. 따라서 이 아키텍처를 사용하려면 방화벽, 게이트웨이, 프록시 등의 어플라이언스로 송신 트래픽을 명시적으로 보내거나 표준 부하 분산 장치나 어플라이언스에 할당된 공용 IP에서 NAT(Network Address Translation)를 수행할 수 있도록 해야 합니다.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>userDefinedRouting을 사용하여 부하 분산 장치 만들기

아웃바운드 형식이 UDR인 AKS 클러스터는 ‘loadBalancer’ 형식의 첫 번째 Kubernetes 서비스가 배포될 때만 SLB(표준 부하 분산 장치)를 수신합니다. 부하 분산 장치는 ‘인바운드’ 요청의 공용 IP 주소와 ‘인바운드’ 요청의 백 엔드 풀을 사용하여 구성됩니다.  인바운드 규칙은 Azure 클라우드 공급자가 구성하지만 아웃바운드 형식이 UDR이라고 해서 **아무 아웃바운드 공용 IP 주소나 아웃바운드 규칙도** 구성되지 않습니다. UDR이 계속 송신 트래픽의 유일한 원본입니다.

Azure 부하 분산 장치는 [규칙이 적용될 때까지 요금이 부과되지 않습니다](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>아웃바운드 형식의 UDR 및 Azure Firewall을 사용하여 클러스터 배포

사용자 정의 경로를 사용하는 아웃바운드 형식의 클러스터 애플리케이션을 설명하기 위해 자체 서브넷의 Azure Firewall을 사용하여 가상 네트워크에서 클러스터를 구성할 수 있습니다. [restrict egress traffic with Azure firewall example](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)(Azure Firewall을 사용하여 송신 트래픽 제한 예제)에서 이 예제를 참조하세요.

> [!IMPORTANT]
> 아웃바운드 형식이 UDR이면 경로 테이블에 0.0.0.0/0의 경로와 NVA(네트워크 가상 어플라이언스)의 다음 홉 대상이 있어야 합니다.
> 경로 테이블에서 이미 기본값 0.0.0.0/0이 인터넷에 지정되어 있으면 SNAT에 대한 공용 IP 없이 이 경로를 추가해도 송신이 제공되지 않습니다. AKS는 사용자가 인터넷을 가리키는 0.0.0.0/0 경로를 만들지 않고 대신 NVA, 게이트웨이 등을 가리키는 경로를 만드는지 확인합니다. 아웃바운드 형식 UDR을 사용할 경우 *loadbalancer* 형식의 서비스가 구성되어 있지 않은 한 **인바운드 요청** 의 부하 분산 장치 공용 IP 주소가 만들어지지 않습니다. 아웃바운드 형식 UDR이 설정된 경우 AKS에서는 **아웃바운드 요청** 의 공용 IP 주소를 만들지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure 네트워킹 UDR 개요](../virtual-network/virtual-networks-udr-overview.md)를 참조하세요.

[경로 테이블을 만들거나 변경하거나 삭제하는 방법](../virtual-network/manage-route-table.md)을 참조하세요.

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
