---
title: Azure 방화벽을 사용 하 여 AKS (Azure Kubernetes Service) 배포 보호
description: Azure 방화벽을 사용 하 여 AKS (Azure Kubernetes Service) 배포를 보호 하는 방법을 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/29/2020
ms.author: victorh
ms.openlocfilehash: 602671f1052de2d9446f32946271cea2f9995044
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87412952"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Azure 방화벽을 사용 하 여 AKS (Azure Kubernetes Service) 배포 보호

AKS (azure Kubernetes Service)는 Azure에서 관리 되는 Kubernetes 클러스터를 제공 합니다. Azure에 대 한 대부분의 책임을 오프 로드 하 여 Kubernetes을 관리 하는 복잡성 및 운영 오버 헤드를 줄입니다. AKS는 상태 모니터링 및 유지 관리와 같은 중요 한 작업을 처리 하 고, 촉진 된 거 버 넌 스를 통해 엔터프라이즈급의 보안 클러스터를 제공 합니다.

Kubernetes는 사용 가능한 계산 리소스 및 각 컨테이너의 리소스 요구 사항에 따라 가상 컴퓨터의 클러스터를 오케스트레이션 하 고 해당 가상 컴퓨터에서 실행할 컨테이너를 예약 합니다. 컨테이너는 pod로 그룹화 되며, Kubernetes에 대 한 기본 작업 단위와 원하는 상태로 확장 됩니다.

관리와 운영을 목적으로 AKS 클러스터의 노드는 특정 포트와 FQDN(정규화된 도메인 이름)에 액세스해야 합니다. 이러한 작업에는 API 서버와 통신 또는 코어 Kubernetes 클러스터 구성 요소와 노드 보안 업데이트를 다운로드하여 설치하기 등이 있습니다. Azure 방화벽을 통해 사용자 환경을 잠그고 아웃 바운드 트래픽을 필터링 할 수 있습니다.

이 문서의 지침에 따라 azure 방화벽을 사용 하 여 Azure Kubernetes 클러스터에 대 한 추가 보호를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 응용 프로그램을 실행 하는 배포 된 Azure Kubernetes 클러스터.

   자세한 내용은 [자습서: Azure Kubernetes 서비스 (AKS) 클러스터 배포](../aks/tutorial-kubernetes-deploy-cluster.md) 및 [자습서: azure Kubernetes service에서 응용 프로그램 실행 (AKS)](../aks/tutorial-kubernetes-deploy-application.md)을 참조 하세요.


## <a name="securing-aks"></a>AKS 보안

Azure 방화벽은 구성을 간소화 하는 AKS FQDN 태그를 제공 합니다. 아웃 바운드 AKS 플랫폼 트래픽을 허용 하려면 다음 단계를 사용 합니다.

- Azure 방화벽을 사용 하 여 아웃 바운드 트래픽을 제한 하 고 UDR (사용자 정의 경로)을 만들어 모든 아웃 바운드 트래픽을 전달 하는 경우 방화벽에서 적절 한 DNAT 규칙을 만들어 인바운드 트래픽을 올바르게 허용 해야 합니다. 

   UDR과 함께 Azure 방화벽을 사용 하면 비대칭 라우팅으로 인해 인바운드 설정이 중단 됩니다. AKS 서브넷에 방화벽의 개인 IP 주소로 이동 하는 기본 경로가 있지만 공용 부하 분산 장치를 사용 하는 경우이 문제가 발생 합니다. 예를 들어 *LoadBalancer*형식의 인바운드 또는 Kubernetes 서비스입니다.

   이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태를 저장하고 방화벽이 설정된 세션을 인식하지 못하므로 반환 패킷을 삭제합니다. Azure Firewall을 수신 또는 서비스 부하 분산 장치와 통합하는 방법을 알아보려면 [Azure Firewall을 Azure 표준 Load Balancer와 통합](integrate-lb.md)을 참조하세요.
- 응용 프로그램 규칙 컬렉션을 만들고 *AzureKubernetesService* FQDN 태그를 사용 하도록 설정 하는 규칙을 추가 합니다. 원본 IP 주소 범위는 호스트 풀 가상 네트워크이 고, 프로토콜은 https이 고, 대상은 AzureKubernetesService입니다.
- 다음 아웃바운드 포트/네트워크 규칙은 AKS 클러스터에 대한 필수 사항입니다.

   - TCP 포트 443
   - API 서버와 통신 해야 하는 앱이 있는 경우 TCP [*IPAddrOfYourAPIServer*]: 443가 필요 합니다. 이 변경 내용은 클러스터를 만든 후에 설정할 수 있습니다.
   - 터널 전면 pod가 API 서버의 터널 끝과 통신할 수 있도록 TCP 포트 9000 및 UDP 포트 1194

      보다 구체적인 정보를 보려면 **. <location> azmk8s.io* 및 주소를 다음 표에 나와 있습니다.
   - UDP 포트 123은 NTP(Network Time Protocol) 시간 동기화(Linux 노드)에 필요합니다.
   - API 서버에 직접 액세스하는 포드가 있으면 DNS용 UDP 포트 53도 필요합니다.

   자세한 내용은 [Azure Kubernetes 서비스에서 클러스터 노드에 대 한 송신 트래픽 제어 (AKS)](../aks/limit-egress-traffic.md)를 참조 하세요.
- AzureMonitor 및 Storage 서비스 태그를 구성 합니다. Azure Monitor는 log analytics 데이터를 수신 합니다.

   작업 영역 URL은 `<worksapceguid>.ods.opinsights.azure.com` , 및를 개별적으로 허용할 수도 `<worksapceguid>.oms.opinsights.azure.com` 있습니다. 다음 방법 중 하나로 해결할 수 있습니다.

    - 호스트 풀 서브넷에서, 및로의 https 액세스를 허용 `*. ods.opinsights.azure.com` `*.oms. opinsights.azure.com` 합니다. 이러한 와일드 카드 Fqdn은 필요한 액세스를 사용 하지만 덜 제한적입니다.
    - 다음 log analytics 쿼리를 사용 하 여 정확한 필수 Fqdn을 나열 하 고 방화벽 응용 프로그램 규칙에서 명시적으로 허용 합니다.
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>다음 단계

- Azure Kubernetes Service에 대 한 자세한 내용은 [AKS (Azure Kubernetes service)에 대 한 Kubernetes 핵심 개념](../aks/concepts-clusters-workloads.md)을 참조 하세요.
