---
title: Azure Kubernetes 서비스(AKS)에서 송신 트래픽 제한
description: Azure Kubernetes 서비스(AKS)에서 송신 트래픽을 제어하는 데 필요한 포트 및 주소 에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080186"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에서 클러스터 노드에 대한 트래픽 송신 제어

기본적으로 AKS 클러스터에는 무제한 아웃바운드(egress) 인터넷 액세스가 있습니다. 이 수준의 네트워크 액세스를 사용하면 실행하는 노드와 서비스가 필요에 따라 외부 리소스에 액세스할 수 있습니다. 송신 트래픽을 제한하려면 정상 클러스터 유지 관리 작업을 유지하려면 제한된 수의 포트와 주소에 액세스할 수 있어야 합니다. 클러스터는 기본적으로 McR(Microsoft 컨테이너 레지스트리) 또는 Azure 컨테이너 레지스트리(ACR)의 기본 시스템 컨테이너 이미지만 사용하도록 구성됩니다. 이러한 필수 포트 및 주소를 허용하도록 기본 방화벽 및 보안 규칙을 구성합니다.

이 문서에서는 AKS 클러스터에서 송신 트래픽을 제한하는 경우 필요한 네트워크 포트와 정규화된 도메인 이름(FQDN)에 대해 자세히 설명하며 선택 사항입니다.

> [!IMPORTANT]
> 이 문서에서는 AKS 서브넷을 떠나는 트래픽을 잠그는 방법만 설명합니다. AKS에는 인서스 요구 사항이 없습니다.  NSG(네트워크 보안 그룹) 및 방화벽을 사용하여 내부 서브넷 트래픽을 차단하는 것은 지원되지 않습니다. 클러스터 내의 트래픽을 제어하고 차단하려면 [네트워크 정책을][network-policy]사용합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.66 이상 설치 및 구성이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하십시오.

## <a name="egress-traffic-overview"></a>송신 트래픽 개요

관리 및 운영 목적을 위해 AKS 클러스터의 노드는 특정 포트와 검증된 도메인 이름(FQDN)에 액세스해야 합니다. 이러한 작업은 API 서버와 통신하거나 코어 Kubernetes 클러스터 구성 요소 및 노드 보안 업데이트를 다운로드한 다음 설치하는 것입니다. 기본적으로 송신(아웃바운드) 인터넷 트래픽은 AKS 클러스터의 노드에 대해 제한되지 않습니다. 클러스터는 외부 리포지토리에서 기본 시스템 컨테이너 이미지를 가져올 수 있습니다.

AKS 클러스터의 보안을 강화하려면 송신 트래픽을 제한할 수 있습니다. 클러스터는 MCR 또는 ACR에서 기본 시스템 컨테이너 이미지를 가져오도록 구성됩니다. 이러한 방식으로 송신 트래픽을 잠그는 경우 AKS 노드가 필요한 외부 서비스와 올바르게 통신할 수 있도록 특정 포트와 FQDN을 정의합니다. 이러한 인증된 포트와 FQDN이 없으면 AKS 노드가 API 서버와 통신하거나 핵심 구성 요소를 설치할 수 없습니다.

[Azure 방화벽][azure-firewall] 또는 제3자 방화벽 어플라이언스를 사용하여 송신 트래픽을 보호하고 이러한 필수 포트 및 주소를 정의할 수 있습니다. AKS는 자동으로 이러한 규칙을 만들지 않습니다. 다음 포트와 주소는 네트워크 방화벽에서 적절한 규칙을 만들 때 참조용입니다.

> [!IMPORTANT]
> Azure 방화벽을 사용하여 송신 트래픽을 제한하고 UDR(사용자 정의 경로)을 만들어 모든 송신 트래픽을 강제로 만들 때 방화벽에 적절한 DNAT 규칙을 만들어 트래픽이 올바르게 허용되도록 해야 합니다. UDR을 사용하여 Azure 방화벽을 사용하면 비대칭 라우팅으로 인해 받는 설정이 중단됩니다. (AKS 서브넷에 방화벽의 개인 IP 주소로 이동하는 기본 경로가 있지만 공용 로드 밸런서(형식의 침투 또는 Kubernetes 서비스 인로드 밸런서)를 사용하는 경우 문제가 발생합니다. 이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태 설정이 기때문에 방화벽이 설정된 세션을 인식하지 못해 반환 패킷을 삭제합니다. Azure 방화벽을 받는 사용자 또는 서비스 로드 밸러커와 통합하는 방법을 알아보려면 [Azure 방화벽과 Azure 표준 로드 밸해시 통합을](https://docs.microsoft.com/azure/firewall/integrate-lb)참조하세요.
> 송신 작업자 노드 IP(들)와 API 서버의 IP 간의 네트워크 규칙을 사용하여 TCP 포트 9000, TCP 포트 22 및 UDP 포트 1194에 대한 트래픽을 잠글 수 있습니다.

AKS에는 두 가지 포트 및 주소 집합이 있습니다.

* [AKS 클러스터에 필요한 포트와 주소는](#required-ports-and-addresses-for-aks-clusters) 승인된 송신 트래픽에 대한 최소 요구 사항을 자세히 설명합니다.
* [AKS 클러스터에 대한 선택적 권장 주소 및 포트는](#optional-recommended-addresses-and-ports-for-aks-clusters) 모든 시나리오에 필요하지 않지만 Azure Monitor와 같은 다른 서비스와의 통합은 제대로 작동하지 않습니다. 이 선택적 포트 및 FQDNs 목록을 검토하고 AKS 클러스터에 사용되는 모든 서비스 및 구성 요소에 대해 권한을 부여합니다.

> [!NOTE]
> 송신 트래픽을 제한하는 것은 새 AKS 클러스터에서만 작동합니다. 기존 클러스터의 경우 송신 트래픽을 `az aks upgrade` 제한하기 전에 명령을 사용하여 [클러스터 업그레이드 작업을 수행합니다.][aks-upgrade]

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 클러스터에 필요한 포트 및 주소

AKS 클러스터의 경우 다음과 같은 아웃바운드 포트/네트워크 규칙이 필요합니다.

* TCP 포트 *443*
* TCP [IPAddrOfYourAPIServer]:443 API 서버에 문의 해야 하는 응용 프로그램이 있는 경우 필요 합니다.  이 변경 사항은 클러스터를 만든 후 설정할 수 있습니다.
* TCP 포트 *9000,* TCP 포트 *22* 및 UDP 포트 *1194는* API 서버에서 터널 단부와 통신하는 터널 프런트 포드를 위한 것이다.
    * 자세한 내용은 **.hcp를 참조하십시오.\< 위치\>.azmk8s.io* **.tun.\< 위치\>.azmk8s.io* 다음 표의 주소입니다.
* NTP(네트워크 시간 프로토콜) 시간 동기화(Linux 노드)에 대한 UDP *포트(123).*
* API 서버에 직접 액세스하는 포드가 있는 경우에도 DNS용 UDP 포트 *53이* 필요합니다.

다음 FQDN / 응용 프로그램 규칙이 필요합니다.

> [!IMPORTANT]
> ***.blob.core.windows.net 및 aksrepos.azurecr.io** 더 이상 송신 잠금에 대한 FQDN 규칙이 필요하지 않습니다.  기존 클러스터의 경우 `az aks upgrade` 명령을 사용하여 클러스터 업그레이드 작업을 [수행하여][aks-upgrade] 이러한 규칙을 제거합니다.

> [!IMPORTANT]
> *.cdn.mscr.io Azure 퍼블릭 클라우드 리전의 경우 *.data.mcr.microsoft.com대체되었습니다. 변경 사항이 적용될 수 있도록 기존 방화벽 규칙을 업그레이드하십시오.

- Azure 글로벌

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp. \<위치\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 < > API 서버 통신이 필요합니다. * \<위치를\> * AKS 클러스터가 배포된 지역으로 바꿉습니다. |
| *.tun. \<위치\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 < > API 서버 통신이 필요합니다. * \<위치를\> * AKS 클러스터가 배포된 지역으로 바꿉습니다. |
| *.cdn.mscr.io       | HTTPS:443 | 이 주소는 CDN(Azure 콘텐츠 배달 네트워크)에서 지원하는 MCR 저장소에 필요합니다. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 클러스터의 업그레이드 및 확장 중에 클러스터의 작동에 필요한 자말 이미지/차트(예: moby 등)가 포함되어 있습니다. |
| *data.mcr.microsoft.com             | HTTPS:443 | 이 주소는 CDN(Azure 콘텐츠 전송 네트워크)에서 지원하는 MCR 저장소에 필요합니다. |
| management.azure.com       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요합니다. |
| login.microsoftonline.com  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 모비, PowerShell 및 Azure CLI가 포함됩니다. |
| acs-mirror.azureedge.net      | HTTPS:443 | 이 주소는 kubenet 및 Azure CNI와 같은 필수 바이너리를 설치하는 데 필요한 리포지토리용입니다. |

- Azure China 21Vianet

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp. \<위치\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 < > API 서버 통신이 필요합니다. * \<위치를\> * AKS 클러스터가 배포된 지역으로 바꿉습니다. |
| *.tun. \<위치\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 < > API 서버 통신이 필요합니다. * \<위치를\> * AKS 클러스터가 배포된 지역으로 바꿉습니다. |
| *.azk8s.cn        | HTTPS:443 | 이 주소는 필요한 바이너리 및 이미지를 다운로드하는 데 필요합니다.|
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 클러스터의 업그레이드 및 확장 중에 클러스터의 작동에 필요한 자말 이미지/차트(예: moby 등)가 포함되어 있습니다. |
| *.cdn.mscr.io       | HTTPS:443 | 이 주소는 CDN(Azure 콘텐츠 배달 네트워크)에서 지원하는 MCR 저장소에 필요합니다. |
| *data.mcr.microsoft.com             | HTTPS:443 | 이 주소는 CDN(Azure 콘텐츠 전송 네트워크)에서 지원하는 MCR 저장소에 필요합니다. |
| management.chinacloudapi.cn       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요합니다. |
| login.chinacloudapi.cn  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 모비, PowerShell 및 Azure CLI가 포함됩니다. |

- Azure Government

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp. \<위치\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 < > API 서버 통신이 필요합니다. * \<위치를\> * AKS 클러스터가 배포된 지역으로 바꿉습니다. |
| *.tun. \<위치\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | 이 주소는 노드 < > API 서버 통신이 필요합니다. * \<위치를\> * AKS 클러스터가 배포된 지역으로 바꿉습니다. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR(Microsoft 컨테이너 레지스트리)의 이미지에 액세스하는 데 필요합니다. 이 레지스트리에는 클러스터의 업그레이드 및 확장 중에 클러스터의 작동에 필요한 자말 이미지/차트(예: moby 등)가 포함되어 있습니다. |
|*.cdn.mscr.io              | HTTPS:443 | 이 주소는 CDN(Azure 콘텐츠 배달 네트워크)에서 지원하는 MCR 저장소에 필요합니다. |
| *data.mcr.microsoft.com             | HTTPS:443 | 이 주소는 CDN(Azure 콘텐츠 전송 네트워크)에서 지원하는 MCR 저장소에 필요합니다. |
| management.usgovcloudapi.net       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요합니다. |
| login.microsoftonline.us  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시된 *apt-get* 작업에 사용되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 모비, PowerShell 및 Azure CLI가 포함됩니다. |
| acs-mirror.azureedge.net      | HTTPS:443 | 이 주소는 kubenet 및 Azure CNI와 같은 필수 바이너리를 설치하는 데 필요한 리포지토리용입니다. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS 클러스터에 대한 권장 주소 및 포트 옵션

AKS 클러스터의 경우 다음 아웃바운드 포트/네트워크 규칙은 선택 사항입니다.

AKS 클러스터가 올바르게 작동하려면 다음 FQDN/응용 프로그램 규칙을 사용하는 것이 좋습니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | 이 주소를 사용하면 Linux 클러스터 노드에서 필요한 보안 패치 및 업데이트를 다운로드할 수 있습니다. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>GPU지원 AKS 클러스터에 필요한 주소 및 포트

GPU가 활성화된 AKS 클러스터에는 다음 FQDN/응용 프로그램 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작업에 사용됩니다. |
| us.download.nvidia.com | HTTPS:443 | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작업에 사용됩니다. |
| apt.dockerproject.org | HTTPS:443 | 이 주소는 GPU 기반 노드에서 올바른 드라이버 설치 및 작업에 사용됩니다. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>컨테이너에 대해 Azure 모니터를 사용하는 필수 주소 및 포트

컨테이너에 대한 Azure 모니터가 활성화된 AKS 클러스터에는 다음 FQDN/응용 프로그램 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | 이는 Azure Monitor를 사용하여 올바른 메트릭 및 모니터링 원격 분석을 위한 것입니다. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Azure 모니터에서 로그 분석 데이터를 수집하는 데 사용됩니다. |
| *.oms.opinsights.azure.com | HTTPS:443 | 이 주소는 로그 분석 서비스를 인증하는 데 사용되는 omsagent에서 사용됩니다. |
|*.microsoftonline.com | HTTPS:443 | Azure Monitor에 메트릭을 인증하고 보내는 데 사용됩니다. |
|*.monitoring.azure.com | HTTPS:443 | 이는 메트릭 데이터를 Azure 모니터로 보내는 데 사용됩니다. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Azure 개발자 공간이 활성화된 필수 주소 및 포트

Azure 개발자 공간을 사용하도록 설정한 AKS 클러스터에는 다음 FQDN/응용 프로그램 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 이 주소는 linux 알파인 및 기타 Azure 개발자 공간 이미지를 가져오는 데 사용됩니다. |
| gcr.io | HTTP:443 | 이 주소는 투구/틸러 이미지를 당기는 데 사용됩니다. |
| storage.googleapis.com | HTTP:443 | 이 주소는 투구/틸러 이미지를 당기는 데 사용됩니다. |
| azds-\<\>가이드 . \<위치\>.azds.io | HTTPS:443 | 컨트롤러에 대한 Azure 개발자 공간 백 엔드 서비스와 통신합니다. 정확한 FQDN은 %USERPROFILE %\.azds\settings.json의 "데이터 플레인Fqdn"에서 찾을 수 있습니다. |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Azure 정책(공개 미리 보기)이 활성화된 AKS 클러스터에 필요한 주소 및 포트

> [!CAUTION]
> 아래 기능 중 일부는 미리 보기상태입니다.  이 문서의 제안은 기능이 공개 미리 보기 및 향후 릴리스 단계로 이동함에 따라 변경될 수 있습니다.

Azure 정책을 사용하도록 설정한 AKS 클러스터에는 다음 FQDN/응용 프로그램 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 이 주소는 Azure 정책의 올바른 작업에 사용됩니다. (현재 AKS에서 미리 보기) |
| raw.githubusercontent.com | HTTPS:443 | 이 주소는 Azure 정책의 올바른 작동을 보장하기 위해 GitHub에서 기본 제공 정책을 가져오는 데 사용됩니다. (현재 AKS에서 미리 보기) |
| *.gk. \<위치\>.azmk8s.io | HTTPS:443    | 마스터 서버에서 실행 중인 게이트키퍼 감사 끝점에 대해 이야기하는 Azure 정책 추가 기능을 사용하여 감사 결과를 가져옵니다. |
| dc.services.visualstudio.com | HTTPS:443 | 원격 분석 데이터를 응용 프로그램 인사이트 끝점으로 보내는 Azure 정책 추가 기능입니다. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Windows 서버 기반 노드(공개 미리 보기)에 필요한 경우 사용 가능

> [!CAUTION]
> 아래 기능 중 일부는 미리 보기상태입니다.  이 문서의 제안은 기능이 공개 미리 보기 및 향후 릴리스 단계로 이동함에 따라 변경될 수 있습니다.

Windows 서버 기반 AKS 클러스터에는 다음 FQDN/응용 프로그램 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | 창 관련 바이너리를 설치하려면 |
| mp.microsoft.com,<span></span>www.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | 창 관련 바이너리를 설치하려면 |
| kms.core.windows.net | TCP:1688 | 창 관련 바이너리를 설치하려면 |


## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터에 대한 송신 트래픽을 제한하는 경우 허용할 포트와 주소를 배웠습니다. 또한 포드 자체가 통신할 수 있는 방법과 클러스터 내에서 포드가 어떤 제한을 가지고 있는지 정의할 수도 있습니다. 자세한 내용은 [AKS의 네트워크 정책을 사용하여 포드 간의 보안 트래픽을][network-policy]참조하십시오.

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
