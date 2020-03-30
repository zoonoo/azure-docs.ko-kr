---
title: Azure Kubernetes 서비스(AKS)의 Windows 서버 노드 풀에 대한 제한 사항
description: Azure Kubernetes 서비스(AKS)에서 Windows 서버 노드 풀 및 응용 프로그램 워크로드를 실행할 때 알려진 제한 사항에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: f4e9f63d0da1797b92c123034e6775f5b07bd4b3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366398"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)의 Windows 서버 노드 풀 및 응용 프로그램 워크로드에 대한 현재 제한 사항

AKS(Azure Kubernetes Service)에서 Windows 서버를 노드의 게스트 OS로 실행하는 노드 풀을 만들 수 있습니다. 이러한 노드는 .NET Framework에 빌드된 응용 프로그램과 같은 네이티브 Windows 컨테이너 응용 프로그램을 실행할 수 있습니다. Linux 및 Windows OS가 컨테이너 지원을 제공하는 방식에는 큰 차이가 있기 때문에 Windows 노드 풀에서는 현재 몇 가지 일반적인 Kubernetes 및 포드 관련 기능을 사용할 수 없습니다.

이 문서에서는 AKS의 Windows 서버 노드에 대한 몇 가지 제한 사항 및 OS 개념을 간략하게 설명합니다. Windows 서버용 노드 풀은 현재 미리 보기 상태입니다.

> [!IMPORTANT]
> AKS 미리보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 대로" 및 "사용 가능한 경우"로 제공되며 서비스 수준 계약 및 제한 보증에서 제외됩니다. AKS 미리 보기는 최선의 노력을 기울여 고객 지원에 의해 부분적으로 적용됩니다. 따라서 이러한 기능은 프로덕션 용으로 사용되지 않습니다. 자세한 내용은 다음 지원 문서를 참조하십시오.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>어떤 Windows 운영 체제가 지원되나요?

AKS는 Windows Server 2019를 호스트 OS 버전으로 사용하며 프로세스 격리만 지원합니다. 다른 Windows Server 버전을 사용하여 빌드된 컨테이너 이미지는 지원되지 않습니다. [윈도우 컨테이너 버전 호환성][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes는 윈도우와 리눅스에서 다른가요?

Windows 서버 풀 지원에는 Kubernetes 프로젝트의 업스트림 Windows 서버의 일부인 몇 가지 제한 사항이 포함되어 있습니다. 이러한 제한은 AKS에만 국한되지 않습니다. Kubernetes의 Windows 서버에 대한 이 업스트림 지원에 대한 자세한 내용은 [Kubernetes 프로젝트의 Kubernetes][intro-windows] 문서에서 Windows 지원 소개의 [지원되는 기능 및 제한][upstream-limitations] 섹션을 참조하십시오.

Kubernetes는 역사적으로 리눅스에 초점을 맞추고 있습니다. 업스트림 [Kubernetes.io][kubernetes] 웹 사이트에 사용되는 많은 예는 Linux 노드에서 사용하기 위한 것입니다. Windows Server 컨테이너를 사용하는 배포를 만들 때 OS 수준에서 다음 고려 사항이 적용됩니다.

- **ID** - Linux는 정수 사용자 식별자(UID)로 사용자를 식별합니다. 또한 사용자는 로그온할 수 있는 상숫자 사용자 이름이 있으며, 리눅스는 사용자의 UID로 변환됩니다. 마찬가지로 Linux는 정수 그룹 식별자(GID)로 사용자 그룹을 식별하고 그룹 이름을 해당 GID로 변환합니다.
    - Windows 서버는 SAM(Windows 보안 액세스 관리자) 데이터베이스에 저장된 더 큰 이진 보안 식별자(SID)를 사용합니다. 이 데이터베이스는 호스트와 컨테이너 간에 또는 컨테이너 간에 공유되지 않습니다.
- **파일 권한** - Windows Server는 사용 권한 및 UID+GID의 비트 마스크가 아닌 ID를 기반으로 하는 액세스 제어 목록을 사용합니다.
- **파일 경로** - Windows Server의 규칙은 \대신 \를 사용하는 것입니다.
    - 볼륨을 탑재하는 포드 사양에서 Windows Server 컨테이너에 대한 경로를 올바르게 지정합니다. 예를 들어 Linux 컨테이너에서 */mnt/volume의* 탑재 지점 대신 드라이브 문자와 */K/Volume과* 같은 위치를 지정하여 *K:* 드라이브로 마운트합니다.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows에서 어떤 종류의 디스크가 지원됩니까?

Azure 디스크 및 Azure 파일은 지원되는 볼륨 유형으로 Windows 서버 컨테이너에서 NTFS 볼륨으로 액세스됩니다.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>AKS에서 Windows 클러스터만 실행할 수 있습니까?

AKS 클러스터의 마스터 노드(제어 평면)는 AKS 서비스에서 호스팅되며 마스터 구성 요소를 호스팅하는 노드의 운영 체제에 노출되지 않습니다. 모든 AKS 클러스터는 Linux 기반의 기본 첫 번째 노드 풀로 만들어집니다. 이 노드 풀에는 클러스터가 작동하는 데 필요한 시스템 서비스가 포함되어 있습니다. 클러스터의 안정성과 클러스터 작업을 수행할 수 있도록 첫 번째 노드 풀에서 두 개 이상의 노드를 실행하는 것이 좋습니다. AKS 클러스터 자체가 삭제되지 않는 한 첫 번째 Linux 기반 노드 풀은 삭제할 수 없습니다.

## <a name="what-network-plug-ins-are-supported"></a>어떤 네트워크 플러그인이 지원됩니까?

Windows 노드 풀이 있는 AKS 클러스터는 Azure CNI(고급) 네트워킹 모델을 사용해야 합니다. Kubenet(기본) 네트워킹은 지원되지 않습니다. 네트워크 모델의 차이점에 대한 자세한 내용은 [AKS의 응용 프로그램에 대한 네트워크 개념을][azure-network-models]참조하십시오. - Azure CNI 네트워크 모델에는 IP 주소 관리를 위한 추가 계획 및 고려 사항이 필요합니다. Azure CNI를 계획하고 구현하는 방법에 대한 자세한 내용은 [AKS에서 Azure CNI 네트워킹 구성을][configure-azure-cni]참조하십시오.

## <a name="can-i-change-the-max--of-pods-per-node"></a>최대를 변경할 수 있습니다. 노드당 포드의 번호?

예. 사용 가능한 의미 및 옵션은 [최대 창 수를][maximum-number-of-pods]참조하십시오.

## <a name="how-do-patch-my-windows-nodes"></a>내 Windows 노드를 패치하는 방법은 무엇입니까?

최신 패치 수정 및 업데이트를 받으려면 AKS의 Windows 서버 노드를 *업그레이드해야* 합니다. AKS의 노드에서는 Windows 업데이트가 활성화되지 않습니다. AKS는 패치를 사용할 수 있게 되는 즉시 새 노드 풀 이미지를 릴리스하며, 패치 및 핫픽스를 최신 상태로 유지하기 위해 노드 풀을 업그레이드하는 것은 고객의 책임입니다. 이는 사용 중인 Kubernetes 버전에서도 마찬가지입니다. AKS 릴리스 노트는 새 버전을 사용할 수 있는 시기를 나타냅니다. Windows 서버 풀 업그레이드에 대한 자세한 내용은 [AKS의 노드 풀 업그레이드를][nodepool-upgrade]참조하십시오.

> [!NOTE]
> 업데이트된 Windows Server 이미지는 노드 풀을 업그레이드하기 전에 클러스터 업그레이드(제어 평면 업그레이드)가 수행된 경우에만 사용됩니다.
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Windows 노드 풀에 대한 서비스 주체를 회전하려면 어떻게 해야 합니까?

미리 보기 중에 Windows 노드 풀은 서비스 주 서버 회전을 미리 보기 제한으로 지원하지 않습니다. 서비스 주체를 업데이트하려면 새 Windows 노드 풀을 만들고 이전 풀에서 새 풀로 포드를 마이그레이션합니다. 이 작업이 완료되면 이전 노드 풀을 삭제합니다.

## <a name="how-many-node-pools-can-i-create"></a>얼마나 많은 노드 풀을 만들 수 있습니까?

AKS 클러스터에는 최대 10개의 노드 풀이 있을 수 있습니다. 이러한 노드 풀에서 최대 1,000개의 노드를 가질 수 있습니다. [노드 풀 제한][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Windows 노드 풀의 이름을 지정할 수 있는 것은 무엇입니까?

이름을 최대 6(6개)까지 유지해야 합니다. 이것은 AKS의 현재 제한사항입니다.

## <a name="are-all-features-supported-with-windows-nodes"></a>모든 기능이 Windows 노드에서 지원되나요?

네트워크 정책과 쿠베넷은 현재 Windows 노드에서 지원되지 않습니다. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Windows 노드에서 인서스 컨트롤러를 실행할 수 있습니까?

예. Windows 서버 컨테이너를 지원하는 inress-controller는 AKS의 Windows 노드에서 실행할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Windows 노드에서 Azure 개발자 공간을 사용할 수 있습니까?

Azure 개발자 공간은 현재 Linux 기반 노드 풀에서만 사용할 수 있습니다.

## <a name="can-my-windows-server-containers-use-gmsa"></a>내 Windows 서버 컨테이너에서 gMSA를 사용할 수 있습니까?

그룹 관리 서비스 계정(gMSA) 지원은 현재 AKS에서 사용할 수 없습니다.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Windows 노드 및 컨테이너가 있는 컨테이너에 Azure 모니터를 사용할 수 있습니까?

예, 그러나 Azure Monitor는 Windows 컨테이너에서 로그(stdout)를 수집하지 않습니다. Windows 컨테이너에서 stdout 로그의 라이브 스트림에 계속 연결할 수 있습니다.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>지원되지 않는 기능이 필요한 경우 어떻게 해야 합니까?

우리는 당신이 AKS에서 Windows에 필요한 모든 기능을 가지고 열심히 노력하고 있지만, 당신은 간격이 발생하는 경우, 오픈 소스, 업스트림 [aks 엔진][aks-engine] 프로젝트는 Windows 지원을 포함하여 Azure에서 Kubernetes를 실행하는 쉽고 완벽하게 사용자 정의 할 수있는 방법을 제공합니다. AKS 로드맵을 통해 사용할 수 있는 기능에 [대한 로드맵을][aks-roadmap]확인하십시오.

## <a name="next-steps"></a>다음 단계

AKS에서 Windows Server 컨테이너를 시작하려면 [AKS에서 Windows Server를 실행하는 노드 풀을 만듭니다.][windows-node-cli]

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
