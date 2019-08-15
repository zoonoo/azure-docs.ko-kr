---
title: Azure Kubernetes 서비스에서 송신 트래픽 제한 (AKS)
description: Azure Kubernetes 서비스 (AKS)에서 송신 트래픽을 제어 하는 데 필요한 포트 및 주소에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: cf9dc304efea8874d16953f74bf88a4317760819
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031830"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>미리 보기-클러스터 노드에 대 한 송신 트래픽 제한 및 Azure Kubernetes 서비스에서 필요한 포트 및 서비스에 대 한 액세스 제어 (AKS)

기본적으로 AKS 클러스터는 무제한 아웃 바운드 (송신) 인터넷 액세스를 포함 합니다. 이러한 수준의 네트워크 액세스를 통해 실행 하는 노드 및 서비스를 사용 하 여 필요에 따라 외부 리소스에 액세스할 수 있습니다. 송신 트래픽을 제한 하려는 경우 정상 클러스터 유지 관리 작업을 유지 하기 위해 제한 된 수의 포트와 주소를 액세스할 수 있어야 합니다. 그러면 클러스터는 외부 공용 리포지토리가 아닌 MCR (Microsoft Container Registry) 또는 Azure Container Registry (ACR)의 기본 시스템 컨테이너 이미지만 사용 하도록 구성 됩니다. 이러한 필수 포트와 주소를 허용 하도록 기본 방화벽 및 보안 규칙을 구성 해야 합니다.

이 문서에서는 AKS 클러스터에서 송신 트래픽을 제한 하는 경우 필요한 네트워크 포트 및 Fqdn (정규화 된 도메인 이름) 및 선택 사항에 대해 자세히 설명 합니다.  이 기능은 현재 미리 보기로 제공됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전 주의 사항

Azure CLI 버전 2.0.66 이상이 설치 및 구성 되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

송신 트래픽을 제한할 수 있는 AKS 클러스터를 만들려면 먼저 구독에서 기능 플래그를 사용 하도록 설정 합니다. 이 기능 등록은 MCR 또는 ACR의 기본 시스템 컨테이너 이미지를 사용 하도록 만드는 AKS 클러스터를 구성 합니다. *AKSLockingDownEgressPreview* 기능 플래그를 등록 하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 합니다.

> [!CAUTION]
> 구독에 기능을 등록 하면 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용 하도록 설정한 후에는 구독에서 만든 모든 AKS 클러스터에 대 한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 별도의 구독을 사용 하 여 미리 보기 기능을 테스트 하 고 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>송신 트래픽 개요

관리 및 운영 목적의 AKS 클러스터의 노드는 특정 포트 및 Fqdn (정규화 된 도메인 이름)에 액세스 해야 합니다. 이러한 작업은 API 서버와 통신 하거나 핵심 Kubernetes 클러스터 구성 요소 및 노드 보안 업데이트를 다운로드 하 여 설치 하는 것일 수 있습니다. 기본적으로 AKS 클러스터의 노드에 대해서는 송신 (아웃 바운드) 인터넷 트래픽이 제한 되지 않습니다. 클러스터는 외부 리포지토리에서 기본 시스템 컨테이너 이미지를 끌어올 수 있습니다.

AKS 클러스터의 보안을 강화 하기 위해 송신 트래픽을 제한할 수 있습니다. 클러스터는 MCR 또는 ACR에서 기본 시스템 컨테이너 이미지를 가져오도록 구성 됩니다. 이러한 방식으로 송신 트래픽을 잠그는 경우 AKS 노드가 필요한 외부 서비스와 올바르게 통신할 수 있도록 특정 포트 및 Fqdn을 정의 해야 합니다. 이러한 권한이 있는 포트 및 Fqdn이 없으면 AKS 노드가 API 서버와 통신할 수 없거나 핵심 구성 요소를 설치할 수 없습니다.

[Azure 방화벽][azure-firewall] 또는 타사 방화벽 어플라이언스를 사용 하 여 송신 트래픽을 보호 하 고 이러한 필수 포트 및 주소를 정의할 수 있습니다. AKS는 이러한 규칙을 자동으로 만들지 않습니다. 네트워크 방화벽에서 적절 한 규칙을 만들 때 다음 포트와 주소는 참조용입니다.

AKS에는 두 가지 포트 및 주소 집합이 있습니다.

* [AKS 클러스터에 필요한 포트 및 주소](#required-ports-and-addresses-for-aks-clusters) 는 권한 있는 송신 트래픽에 대 한 최소 요구 사항을 자세히 설명 합니다.
* [AKS 클러스터에 대 한 선택적 권장 주소와 포트](#optional-recommended-addresses-and-ports-for-aks-clusters) 는 모든 시나리오에 필요 하지 않지만 Azure Monitor와 같은 다른 서비스와의 통합은 제대로 작동 하지 않습니다. 이 선택적 포트 및 Fqdn 목록을 검토 하 고 AKS 클러스터에서 사용 되는 서비스 및 구성 요소에 대 한 권한을 부여 합니다.

> [!NOTE]
> 송신 트래픽 제한은 기능 플래그 등록을 사용 하도록 설정한 후 생성 된 새 AKS 클러스터 에서만 작동 합니다. 기존 클러스터의 경우 송신 트래픽을 제한 하기 전에 `az aks upgrade` 명령을 사용 하 여 [클러스터 업그레이드 작업을 수행][aks-upgrade] 합니다.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 클러스터에 필요한 포트 및 주소

AKS 클러스터에는 다음과 같은 아웃 바운드 포트/네트워크 규칙이 필요 합니다.

* TCP 포트 *443*
* 터널 프런트 pod가 API 서버의 터널 끝과 통신 하는 TCP 포트 *9000* 및 tcp 포트 *22*
    * 보다 구체적인 정보를 얻으려면 * hcp를 참조 하십시오 *.\< azmk8s.io\>* 및 * *. 실행.\< azmk8s.io 주소는 다음 표에 나와 있습니다.\>*

다음 FQDN/응용 프로그램 규칙이 필요 합니다.

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. Location을 AKS 클러스터가 배포 된 지역으로 바꿉니다. *\<\>* |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. Location을 AKS 클러스터가 배포 된 지역으로 바꿉니다. *\<\>* |
| aksrepos.azurecr.io        | HTTPS:443 | 이 주소는 ACR (Azure Container Registry)의 이미지에 액세스 하는 데 필요 합니다. |
| \*.blob.core.windows.net    | HTTPS:443 | 이 주소는 ACR에 저장 된 이미지의 백 엔드 저장소입니다. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR (Microsoft Container Registry)의 이미지에 액세스 하는 데 필요 합니다. |
| *.cdn.mscr.io              | HTTPS:443 | 이 주소는 Azure CDN (content delivery network)에 의해 지원 되는 MCR 저장소에 필요 합니다. |
| management.azure.com       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요 합니다. |
| login.microsoftonline.com  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요 합니다. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | 이 주소는 Linux 노드에 Snap 패키지를 설치 하는 데 필요 합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요 합니다. |
| *.docker.io                | HTTPS:443 | 이 주소는 터널 전면에 필요한 컨테이너 이미지를 끌어오는 데 필요 합니다. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS 클러스터에 대 한 선택적 권장 주소 및 포트

* DNS에 대 한 UDP 포트 *53*

AKS 클러스터가 올바르게 작동 하려면 다음 FQDN/응용 프로그램 규칙을 권장 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | 이 주소를 통해 Linux 클러스터 노드가 필요한 보안 패치와 업데이트를 다운로드할 수 있습니다. |
| packages.microsoft.com                  | HTTPS:443 | 이 주소는 캐시 된 *apt-get* 작업에 사용 되는 Microsoft 패키지 리포지토리입니다. |
| dc.services.visualstudio.com            | HTTPS:443 | Azure Monitor를 사용 하 여 올바른 메트릭 및 모니터링에 권장 됩니다. |
| *.opinsights.azure.com                  | HTTPS:443 | Azure Monitor를 사용 하 여 올바른 메트릭 및 모니터링에 권장 됩니다. |
| *.monitoring.azure.com                  | HTTPS:443 | Azure Monitor를 사용 하 여 올바른 메트릭 및 모니터링에 권장 됩니다. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 이 주소는 Azure Policy의 올바른 작업 (현재 AKS의 미리 보기 상태)에 사용 됩니다. |
| apt.dockerproject.org                   | HTTPS:443 | 이 주소는 GPU 기반 노드에 대 한 올바른 드라이버 설치 및 작업에 사용 됩니다. |
| nvidia.github.io                        | HTTPS:443 | 이 주소는 GPU 기반 노드에 대 한 올바른 드라이버 설치 및 작업에 사용 됩니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터에 대 한 송신 트래픽을 제한 하는 경우 허용할 포트와 주소를 배웠습니다. Pod 자체에서 통신할 수 있는 방법 및 클러스터 내에서의 제한 사항을 정의할 수도 있습니다. 자세한 내용은 [AKS에서 네트워크 정책을 사용 하 여 pod 간의 트래픽 보호][network-policy]를 참조 하세요.

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
