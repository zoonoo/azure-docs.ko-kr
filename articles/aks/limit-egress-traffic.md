---
title: AKS(Azure Kubernetes Service)에서 송신 트래픽 제한
description: AKS(Azure Kubernetes Service)에서 송신 트래픽을 제어하는 데 필요한 포트 및 주소에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 194e799daf107220c28404001d223e521dceeb3f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870896"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 클러스터 노드의 송신 트래픽 제어

기본적으로 AKS 클러스터에는 무제한 아웃바운드(송신) 인터넷 액세스가 있습니다. 이러한 수준의 네트워크 액세스가 있으면 사용자가 실행하는 노드와 서비스는 필요할 때마다 외부 리소스에 액세스할 수 있습니다. 송신 트래픽을 제한하려면 정상적인 클러스터 유지 관리 작업을 유지할 수 있도록 제한된 수의 포트 및 주소에 액세스할 수 있어야 합니다. 클러스터는 기본적으로 MCR(Microsoft Container Registry) 또는 Azure Container Registry(ACR)의 기본 시스템 컨테이너 이미지만 사용하도록 구성되어 있습니다. 이러한 필수 포트와 주소를 허용하도록 기본 방화벽 및 보안 규칙을 구성하세요.

이 문서에서는 AKS 클러스터에서 송신 트래픽을 제한하는 경우 필요한 네트워크 포트와 FQDN(정규화된 도메인 이름) 및 선택 사항에 대해 자세히 설명합니다.

> [!IMPORTANT]
> 이 문서에서는 AKS 서브넷에서 나가는 트래픽을 잠그는 방법에 대해서만 설명합니다. AKS에는 수신 요구 사항이 없습니다.  NSG(네트워크 보안 그룹) 및 방화벽을 사용하여 내부 서브넷 트래픽을 차단하는 것은 지원되지 않습니다. 클러스터 내에서 트래픽을 제어하고 차단하려면 [네트워크 정책][network-policy]을 사용하세요.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.66 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="egress-traffic-overview"></a>송신 트래픽 개요

관리와 운영을 목적으로 AKS 클러스터의 노드는 특정 포트와 FQDN(정규화된 도메인 이름)에 액세스해야 합니다. 이러한 작업에는 API 서버와 통신 또는 코어 Kubernetes 클러스터 구성 요소와 노드 보안 업데이트를 다운로드하여 설치하기 등이 있습니다. 기본적으로 AKS 클러스터의 노드에 대해서는 송신(아웃바운드) 인터넷 트래픽이 제한되지 않습니다. 클러스터는 외부 리포지토리에서 기본 시스템 컨테이너 이미지를 끌어올 수 있습니다.

AKS 클러스터의 보안을 강화하기 위해 송신 트래픽을 제한할 수 있습니다. 클러스터는 MCR 또는 ACR에서 기본 시스템 컨테이너 이미지를 가져오도록 구성되어 있습니다. 이러한 방식으로 송신 트래픽을 잠그는 경우, AKS 노드가 필요한 외부 서비스와 올바르게 통신할 수 있도록 특정 포트와 FQDN을 정의합니다. 이런 권한이 있는 포트와 FQDN이 없으면 AKS 노드가 API 서버와 통신하거나 핵심 구성 요소를 설치할 수 없습니다.

[Azure Firewall][azure-firewall] 또는 타사 방화벽 어플라이언스를 사용하여 송신 트래픽을 보호하고 필요한 포트와 주소를 정의할 수 있습니다. AKS에서는 이러한 규칙이 자동으로 생성되지 않습니다. 다음 포트와 주소는 네트워크 방화벽에서 적절한 규칙을 만들 때 참조할 수 있습니다.

> [!IMPORTANT]
> Azure Firewall을 사용하여 송신 트래픽을 제한하고 UDR(사용자 정의 경로)을 만들어서 모든 송신 트래픽을 강제로 적용하는 경우에는 수신 트래픽을 올바르게 허용하도록 방화벽에서 적절한 DNAT 규칙을 만들어야 합니다. UDR과 함께 Azure Firewall을 사용하면 비대칭 라우팅으로 인해 수신 설정이 손상됩니다. (AKS 서브넷에 방화벽의 개인 IP 주소로 이동하는 기본 경로가 있는데, 공용 부하 분산 장치 - 수신 또는 LoadBalancer 유형의 Kubernetes 서비스를 사용하는 경우 문제가 발생합니다.) 이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태를 저장하고 방화벽이 설정된 세션을 인식하지 못하므로 반환 패킷을 삭제합니다. Azure Firewall을 수신 또는 서비스 부하 분산 장치와 통합하는 방법을 알아보려면 [Azure Firewall을 Azure 표준 Load Balancer와 통합](https://docs.microsoft.com/azure/firewall/integrate-lb)을 참조하세요.
> 송신 작업자 노드 IP와 API 서버의 IP 사이 네트워크 규칙을 사용하여 TCP 포트 9000, TCP 포트 22 및 UDP 포트 1194의 트래픽을 잠글 수 있습니다.

AKS에는 두 가지 포트와 주소 집합이 있습니다.

* [AKS 클러스터에 필요한 포트와 주소](#required-ports-and-addresses-for-aks-clusters)에는 권한 있는 송신 트래픽에 대한 최소 요구 사항이 자세히 설명되어 있습니다.
* [AKS 클러스터에 권장되는 선택적 주소와 포트](#optional-recommended-addresses-and-ports-for-aks-clusters)는 모든 시나리오에 필요하지 않지만 Azure Monitor와 같은 다른 서비스와의 통합이 제대로 작동하지 않습니다. 선택적 포트와 FQDN 목록을 검토하고 AKS 클러스터에 사용되는 서비스와 구성 요소에 권한을 부여하세요.

> [!NOTE]
> 송신 트래픽 제한은 새 AKS 클러스터에서만 가능합니다. 기존 클러스터의 경우 송신 트래픽을 제한하기 전에 `az aks upgrade` 명령을 사용하여 [클러스터 업그레이드 작업을 수행][aks-upgrade]하세요.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 클러스터에 필요한 포트 및 주소

다음 아웃바운드 포트/네트워크 규칙은 AKS 클러스터에 대한 필수 사항입니다.

* TCP 포트 *443*
* TCP [IPAddrOfYourAPIServer]:443은 API 서버와 통신해야 하는 앱이 있으면 필요합니다.  이 변경 내용은 클러스터를 만든 후에 설정할 수 있습니다.
* 터널 앞 Pod의 TCP 포트 *9000*, TCP 포트 *22*, UDP 포트 *1194*는 API 서버의 터널 끝과 통신하는 데 필요합니다.
    * 보다 구체적인 정보는 다음 표에서 * *.hcp.\<location\>.azmk8s.io*, * *.tun.\<location\>.azmk8s.io* 주소를 참조하세요.
* UDP 포트 *123*은 NTP(Network Time Protocol) 시간 동기화(Linux 노드)에 필요합니다.
* API 서버에 직접 액세스하는 포드가 있으면 DNS용 UDP 포트 *53*도 필요합니다.

다음 FQDN/애플리케이션 규칙이 필요합니다.

> [!IMPORTANT]
> * **.blob.core.windows.net 및 aksrepos.azurecr.io**는 더 이상 송신 잠금에 필요한 FQDN 규칙이 아닙니다.  기존 클러스터의 경우 `az aks upgrade` 명령을 사용하여 [클러스터 업그레이드 작업을 수행][aks-upgrade]하고 해당 규칙을 제거하세요.

> [!IMPORTANT]
> *.cdn.mscr.io가 Azure 퍼블릭 클라우드 지역의 경우 *.data.mcr.microsoft.com으로 대체되었습니다. 기존 방화벽 규칙을 업그레이드하여 변경 내용을 적용하세요.

- Azure 글로벌

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꾸세요. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꾸세요. |
| *.cdn.mscr.io       | HTTPS:443 | 이 주소는 Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 클러스터를 업그레이드 및 확장하는 동안 클러스터가 작동하는 데 필요한 자사 이미지/차트(예: moby)가 포함되어 있습니다. |
| *.data.mcr.microsoft.com             | HTTPS:443 | 이 주소는 Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| management.azure.com       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요합니다. |
| login.microsoftonline.com  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI가 포함됩니다. |
| acs-mirror.azureedge.net      | HTTPS:443 | 이 주소는 kubenet 및 Azure CNI와 같은 필수 이진 파일을 설치하는 데 필요한 리포지토리입니다. |

- Azure China 21Vianet

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꾸세요. |
| *.tun.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꾸세요. |
| *.azk8s.cn        | HTTPS:443 | 이 주소는 필요한 이진 파일과 이미지를 다운로드하는 데 필요합니다.|
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 클러스터를 업그레이드 및 확장하는 동안 클러스터가 작동하는 데 필요한 자사 이미지/차트(예: moby)가 포함되어 있습니다. |
| *.cdn.mscr.io       | HTTPS:443 | 이 주소는 Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| *.data.mcr.microsoft.com             | HTTPS:443 | 이 주소는 Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| management.chinacloudapi.cn       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요합니다. |
| login.chinacloudapi.cn  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI가 포함됩니다. |

- Azure Government

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꾸세요. |
| *.tun.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 <-> API 서버 통신에 필요합니다. *\<location\>* 을 AKS 클러스터가 배포된 지역으로 바꾸세요. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 클러스터를 업그레이드 및 확장하는 동안 클러스터가 작동하는 데 필요한 자사 이미지/차트(예: moby)가 포함되어 있습니다. |
|*.cdn.mscr.io              | HTTPS:443 | 이 주소는 Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| *.data.mcr.microsoft.com             | HTTPS:443 | 이 주소는 Azure CDN(Content Delivery Network)이 지원하는 MCR 스토리지에 필요합니다. |
| management.usgovcloudapi.net       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요합니다. |
| login.microsoftonline.us  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI가 포함됩니다. |
| acs-mirror.azureedge.net      | HTTPS:443 | 이 주소는 kubenet 및 Azure CNI와 같은 필수 이진 파일을 설치하는 데 필요한 리포지토리입니다. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS 클러스터에 권장되는 선택적 주소와 포트

다음 아웃바운드 포트/네트워크 규칙은 AKS 클러스터에 대한 선택 사항입니다.

AKS 클러스터가 올바르게 작동하려면 다음 FQDN/애플리케이션 규칙이 권장됩니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | 이 주소를 통해 Linux 클러스터 노드가 필요한 보안 패치와 업데이트를 다운로드할 수 있습니다. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>GPU 사용 AKS 클러스터에 대한 필수 주소 및 포트

GPU를 사용하는 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작동에 사용됩니다. |
| us.download.nvidia.com | HTTPS:443 | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작동에 사용됩니다. |
| apt.dockerproject.org | HTTPS:443 | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작동에 사용됩니다. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>컨테이너용 Azure Monitor 사용 시 필요한 주소와 포트

컨테이너에 대해 Azure Monitor를 사용하도록 설정된 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Azure Monitor를 사용한 올바른 메트릭 및 모니터링 원격 분석에 필요합니다. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Azure Monitor가 로그 분석 데이터를 수집하는 데 사용됩니다. |
| *.oms.opinsights.azure.com | HTTPS:443 | 이 주소는 로그 분석 서비스를 인증하는 데 사용되는 omsagent에서 사용합니다. |
|*.microsoftonline.com | HTTPS:443 | Azure Monitor를 인증하고 메트릭을 전송하는 데 사용됩니다. |
|*.monitoring.azure.com | HTTPS:443 | Azure Monitor에 메트릭 데이터를 전송하는 데 사용됩니다. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Azure Dev Spaces 사용 시 필요한 주소와 포트

Azure Dev Spaces를 사용하도록 설정된 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 이 주소는 Linux alpine 및 기타 Azure Dev Spaces 이미지를 끌어오는 데 사용됩니다. |
| gcr.io | HTTPS:443 | 이 주소는 Helm/Tiller 이미지를 끌어오는 데 사용됩니다. |
| storage.googleapis.com | HTTPS:443 | 이 주소는 Helm/Tiller 이미지를 끌어오는 데 사용됩니다. |
| azds-\<guid\>.\<location\>.azds.io | HTTPS:443 | 컨트롤러에 대한 Azure Dev Spaces 백 엔드 서비스와 통신하는 데 사용됩니다. 정확한 FQDN은 %USERPROFILE%\.azds\settings.json의 "dataplaneFqdn"에서 찾을 수 있습니다. |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Azure Policy(공개 미리 보기)를 사용하도록 설정된 AKS 클러스터에 필요한 주소와 포트

> [!CAUTION]
> 아래 기능 중 일부는 미리 보기 상태입니다.  이 문서의 제안 사항은 기능이 공개 미리 보기 및 향후 릴리스 단계로 이동하면서 변경 될 수 있습니다.

Azure Policy를 사용하도록 설정된 AKS 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 이 주소는 Azure Policy가 올바르게 작동하는 데 사용됩니다. (현재 AKS의 미리 보기 상태) |
| raw.githubusercontent.com | HTTPS:443 | 이 주소는 Azure Policy가 올바르게 작동하도록 GitHub에서 기본 제공 정책을 끌어오는 데 사용됩니다. (현재 AKS의 미리 보기 상태) |
| *.gk.\<location\>.azmk8s.io | HTTPS:443    | 감사 결과를 얻기 위해 마스터 서버에서 실행되는 게이트키퍼 감사 엔드포인트와 통신하는 Azure Policy 추가 기능입니다. |
| dc.services.visualstudio.com | HTTPS:443 | 원격 분석 데이터를 Application Insights 엔드포인트로 보내는 Azure Policy 추가 기능입니다. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Windows Server 기반 노드 사용 시 필요한 항목

Windows Server 기반 노드 풀을 사용하려면 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Windows 관련 이진 파일을 설치하려면 필요합니다. |
| mp.microsoft.com, www<span></span>.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Windows 관련 이진 파일을 설치하려면 필요합니다. |
| kms.core.windows.net | TCP:1688 | Windows 관련 이진 파일을 설치하려면 필요합니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터의 송신 트래픽을 제한하는 경우 허용할 포트와 주소를 알아보았습니다. Pod 자체가 서로 통신할 수 있는 방법 및 클러스터 내의 제한 사항을 정의할 수도 있습니다. 자세한 내용은 [AKS에서 네트워크 정책을 사용하여 Pod 간 트래픽 보호][network-policy]를 참조하세요.

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
