---
title: Azure Kubernetes Service (AKS)에서 송신 트래픽을 제한합니다
description: Azure Kubernetes Service (AKS)에서 송신 트래픽을 제어 하는 데 필요한 어떤 포트와 주소 되는지 알아보세요.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: iainfou
ms.openlocfilehash: 43ba7593336372bbbd7a3a4bb9821665a42bbf29
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752186"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>미리 보기-클러스터 노드 및 필요한 포트 및 Azure Kubernetes Service (AKS)에서 서비스에 대 한 액세스 제어에 대 한 송신 트래픽 제한

AKS 클러스터는 기본적으로 아웃 바운드 (송신) 인터넷을 무제한 권한이 있습니다. 이 수준의 네트워크 액세스에는 노드 및 필요에 따라 외부 리소스에 액세스 하기 위해 실행 하는 서비스 수 있습니다. 송신 트래픽을 제한 하려는 경우에 제한 된 수의 포트 및 주소 정상 클러스터 유지 관리 작업을 유지 하기 위해 액세스할 수 있어야 합니다. 그런 다음 클러스터를만 Microsoft 컨테이너 레지스트리 (MCR) 또는 하지 외부 공용 리포지토리에 Azure 컨테이너 레지스트리 (ACR)에서 기본 시스템 컨테이너 이미지를 사용 하도록 구성 됩니다. 이러한 필수 포트 및 주소를 허용 하도록 기본 설정 된 방화벽 및 보안 규칙을 구성 해야 합니다.

이 문서는 네트워크 포트 및 정규화 된 도메인 이름 (Fqdn)는 필수 및 선택적 AKS 클러스터에서 송신 트래픽을 제한 하는 경우 자세히 설명 합니다.  이 기능은 현재 미리 보기로 제공됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전에

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.66 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

송신 트래픽을 제한할 수 있는 AKS 클러스터를 만들려면 먼저 구독에 기능 플래그를 사용 하도록 설정 합니다. 이 기능은 등록 MCR 또는 ACR에서 기본 시스템 컨테이너 이미지를 사용할 수 있도록 만드는 모든 AKS 클러스터를 구성 합니다. 등록 하는 *AKSLockingDownEgressPreview* 기능 플래그를 사용 합니다 [az 기능 등록] [ az-feature-register] 다음 예와에서 같이 명령:

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. 사용 하 여 등록 상태를 확인할 수 있습니다 합니다 [az 기능 목록] [ az-feature-list] 명령:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 새로 고침의 등록 된 *Microsoft.ContainerService* 를 사용 하 여 리소스 공급자를 [az provider register] [ az-provider-register] 명령:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>송신 트래픽 개요

관리 및 운영 목적을 위해 AKS 클러스터의 노드는 특정 포트 및 정규화 된 도메인 이름 (Fqdn)에 액세스 해야 합니다. 이러한 작업은 API 서버와 통신할 수 또는 다운로드 한 후 핵심 Kubernetes 클러스터 구성 요소 및 노드 보안 업데이트를 설치 하 수 있습니다. 기본적으로 송신 (아웃 바운드) 인터넷 트래픽을 AKS 클러스터의 노드에 대 한 제한 되지 않습니다. 클러스터 외부 리포지토리에서 기본 시스템 컨테이너 이미지를 끌어올 수 있습니다.

AKS 클러스터의 보안을 강화 하려면 송신 트래픽을 제한 하는 것이 좋습니다. 클러스터는 기본 시스템 MCR 또는 ACR에서 컨테이너 이미지를 가져오도록 구성 됩니다. 이 방식으로 송신 트래픽에 잠근 경우 특정 포트 및 AKS 노드를 올바르게 필요한 외부 서비스와 통신할 수 있도록 Fqdn을 정의 해야 합니다. AKS 노드 없이 이러한 권한 있는 포트 및 Fqdn API 서버와 통신할 하거나 핵심 구성 요소를 설치할 수 없습니다.

사용할 수 있습니다 [Azure 방화벽] [ azure-firewall] 또는 타사 방화벽 어플라이언스를 송신 트래픽이 보호 및 정의 하는 데 필요한 이러한 포트 및 주소입니다. AKS 자동으로 만들어지지는지 않습니다 이러한 규칙이 있습니다. 네트워크 방화벽에서 적절 한 규칙을 만든 다음 포트와 주소 참조 됩니다.

AKS, 포트 및 주소의 두 가지 있습니다.

* 합니다 [AKS 클러스터에 필요한 포트 및 주소](#required-ports-and-addresses-for-aks-clusters) 권한 있는 송신 트래픽에 대 한 최소 요구 사항에 자세히 설명 합니다.
* 합니다 [주소 및 포트 AKS 클러스터에 대 한 선택적 권장](#optional-recommended-addresses-and-ports-for-aks-clusters) Azure Monitor는 올바르게 작동 하지 않습니다 같은 모든 시나리오에 있지만 다른 서비스와의 통합에 대 한 필요 하지 않습니다. 선택적 포트 및 Fqdn이이 목록을 검토 하 고 서비스 및 AKS 클러스터에 사용 되는 구성 요소 중 하나에 권한을 부여 합니다.

> [!NOTE]
> 기능 플래그 등록을 사용 하도록 설정한 후에 만들어진 새 AKS 클러스터 에서만 작동 송신 트래픽을 제한 합니다. 기존 클러스터에 대 한 [클러스터 업그레이드 작업을 수행] [ aks-upgrade] 사용 하는 `az aks upgrade` 송신 트래픽을 제한 하기 전에 명령입니다.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>필요한 포트 및 AKS 클러스터에 대 한 주소

다음 아웃 바운드 포트 네트워크 규칙은 AKS 클러스터에 필요 합니다.

* TCP 포트 *443*
* TCP 포트 *9000* 이 고 TCP 포트 *22* API 서버의 터널 종료 통신할 터널 프런트 pod에 대 한 합니다.
    * 보다 구체적인, 참조는 * *.hcp.\< 위치\>. azmk8s.io* 및 * *. tun.\< 위치\>. azmk8s.io* 다음 표에 주소입니다.

다음 FQDN 응용 프로그램 규칙이 필요 합니다.

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. 바꿉니다 *\<위치\>* AKS 클러스터를 배포할 지역입니다. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. 바꿉니다 *\<위치\>* AKS 클러스터를 배포할 지역입니다. |
| aksrepos.azurecr.io        | HTTPS:443 | 이 주소가 액세스 이미지에서 컨테이너 레지스트리 ACR (Azure)에 필요 합니다. |
| \*.blob.core.windows.net    | HTTPS:443 | 이 주소는 백 엔드 저장소 ACR에 저장 된 이미지입니다. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 Microsoft 컨테이너 레지스트리 (MCR)에 대 한 액세스 이미지에 필요 합니다. |
| *.cdn.mscr.io              | HTTPS:443 | 이 주소는 Azure content delivery network (CDN)에서 지 원하는 MCR 저장소 필요 합니다. |
| management.azure.com       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요 합니다. |
| login.microsoftonline.com  | HTTPS:443 | 이 주소는 Azure Active Directory 인증을 위해 필요 합니다. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Linux 노드에 스냅인 패키지를 설치 하려면이 주소가 필요 합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화 필요 합니다. |
| *.docker.io                | HTTPS:443 | 이 주소는 터널 앞에 대 한 필요한 컨테이너 이미지를 끌어오고 해야 합니다. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>주소 및 포트 AKS 클러스터에 대 한 권장 되는 선택 사항

* UDP 포트 *53* DNS에 대 한

다음 FQDN / 응용 프로그램 규칙은 제대로 작동 하려면 AKS 클러스터에 대 한 권장 됩니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | 이 주소에는 Linux 클러스터 노드에 필수 보안 패치 및 업데이트를 다운로드할 수 있습니다. |
| packages.microsoft.com                  | HTTPS:443 | 이 주소는 사용 되는 Microsoft 패키지 리포지토리 캐시에 대 한 *apt get* 작업 합니다. |
| dc.services.visualstudio.com            | HTTPS:443 | 올바른 메트릭 및 Azure Monitor를 사용 하 여 모니터링 합니다. |
| *.opinsights.azure.com                  | HTTPS:443 | 올바른 메트릭 및 Azure Monitor를 사용 하 여 모니터링 합니다. |
| *.monitoring.azure.com                  | HTTPS:443 | 올바른 메트릭 및 Azure Monitor를 사용 하 여 모니터링 합니다. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 이 주소는 (현재 AKS에서 미리 보기)는에서 Azure Policy의 올바른 작동을 위해 사용 됩니다. |
| apt.dockerproject.org                   | HTTPS:443 | 이 주소는 올바른 드라이버 설치 및 GPU 기반 노드에서 작업에 사용 됩니다. |
| nvidia.github.io                        | HTTPS:443 | 이 주소는 올바른 드라이버 설치 및 GPU 기반 노드에서 작업에 사용 됩니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 어떤 포트와 주소를 클러스터에 대 한 송신 트래픽을 제한 하는 경우를 허용 하도록 배웠습니다. 제한 사항 및 자체에서 pod 수 통신 하는 방법을 정의할 수도 있습니다 클러스터 내에서 갖습니다. 자세한 내용은 [AKS에 네트워크 정책을 사용 하 여 pod 간에 트래픽을 보호][network-policy]합니다.

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
