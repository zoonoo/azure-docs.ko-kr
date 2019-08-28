---
title: AKS (Azure Kubernetes Service)의 Windows Server 노드 풀에 대 한 제한 사항
description: AKS (Azure Kubernetes Service)에서 Windows Server 노드 풀 및 응용 프로그램 작업을 실행할 때 알려진 제한 사항에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 501aeb60eba1d94b4c5882a7c6cbfa8d0359e44d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033901"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 Windows Server 노드 풀 및 응용 프로그램 작업에 대 한 현재 제한 사항

AKS (Azure Kubernetes Service)에서 노드에서 게스트 OS로 Windows Server를 실행 하는 노드 풀을 만들 수 있습니다. 이러한 노드는 .NET Framework에서 빌드된 것과 같은 네이티브 Windows 컨테이너 응용 프로그램을 실행할 수 있습니다. Linux 및 Windows OS에서 컨테이너 지원을 제공 하는 방법에는 중요 한 차이점이 있으므로 일부 일반적인 Kubernetes 및 pod 관련 기능은 현재 Windows 노드 풀에서 사용할 수 없습니다.

이 문서에서는 AKS의 Windows Server 노드에 대 한 몇 가지 제한 사항 및 OS 개념을 간략하게 설명 합니다. Windows Server의 노드 풀은 현재 미리 보기 상태입니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Kubernetes의 Windows Server에 대 한 제한 사항

Windows Server 컨테이너는 Windows 기반 컨테이너 호스트에서 실행 해야 합니다. AKS에서 Windows Server 컨테이너를 실행 하려면 게스트 OS로 [Windows server를 실행 하는 노드 풀을 만들][windows-node-cli] 수 있습니다. 창 서버 노드 풀 지원에는 Kubernetes 프로젝트의 업스트림 Windows Server에 포함 되는 몇 가지 제한 사항이 포함 되어 있습니다. 이러한 제한 사항은 AKS에 국한 되지 않습니다. Kubernetes의 Windows Server에 대 한이 업스트림 지원에 대 한 자세한 내용은 [Kubernetes 제한 사항에서 Windows server 컨테이너](https://docs.microsoft.com/azure/aks/windows-node-limitations)를 참조 하세요.

Kubernetes의 Windows Server 컨테이너에 대 한 다음 업스트림 제한은 AKS와 관련이 있습니다.

- Windows server 컨테이너는 기본 Windows Server 노드 OS와 일치 하는 Windows Server 2019만 사용할 수 있습니다.
    - 기본 OS로 Windows Server 2016를 사용 하 여 작성 된 컨테이너 이미지는 지원 되지 않습니다.
- 권한 있는 컨테이너는 사용할 수 없습니다.
- RunAsUser, SELinux, AppArmor 또는 POSIX 기능과 같은 Linux 특정 기능을 Windows Server 컨테이너에서 사용할 수 없습니다.
    - UUI/GUID와 같은 Linux 전용 파일 시스템 제한 사항 및 사용자에 대 한 사용 권한 또한 Windows Server 컨테이너에서 사용할 수 없습니다.
- Azure 디스크 및 Azure Files는 Windows Server 컨테이너에서 NTFS 볼륨으로 액세스 되는 지원 되는 볼륨 유형입니다.
    - NFS 기반 저장소/볼륨은 지원 되지 않습니다.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Windows Server 노드 풀에 대 한 AKS 제한 사항

AKS의 Windows Server 노드 풀 지원에는 다음과 같은 추가 제한 사항이 적용 됩니다.

- AKS 클러스터는 항상 Linux 노드 풀을 첫 번째 노드 풀로 포함 합니다. AKS 클러스터 자체를 삭제 하지 않는 한이 첫 번째 Linux 기반 노드 풀을 삭제할 수 없습니다.
- AKS 클러스터는 Azure CNI (고급) 네트워킹 모델을 사용 해야 합니다.
    - Kubenet (기본) 네트워킹은 지원 되지 않습니다. Kubenet를 사용 하는 AKS 클러스터를 만들 수 없습니다. 네트워크 모델의 차이점에 대 한 자세한 내용은 [AKS의 응용 프로그램에 대 한 네트워크 개념][azure-network-models]을 참조 하세요.
    - Azure CNI 네트워크 모델에는 IP 주소 관리에 대 한 추가 계획 및 고려 사항이 필요 합니다. Azure CNI를 계획 하 고 구현 하는 방법에 대 한 자세한 내용은 [AKS에서 AZURE cni 네트워킹 구성][configure-azure-cni]을 참조 하세요.
- 최신 패치 수정 및 업데이트를 유지 하려면 AKS의 windows Server 노드를 최신 Windows Server 2019 릴리스로 *업그레이드* 해야 합니다. AKS의 기본 노드 이미지에서 Windows 업데이트를 사용할 수 없습니다. Windows 업데이트 릴리스 주기 및 사용자 고유의 유효성 검사 프로세스에서는 정기적인 일정에 따라 AKS 클러스터의 Windows Server 노드 풀에서 업그레이드를 수행해야 합니다. Windows Server 노드 풀을 업그레이드 하는 방법에 대 한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조 하세요.
    - 이러한 Windows Server 노드 업그레이드는 이전 노드가 제거 되기 전에 새 노드가 배포 될 때 가상 네트워크 서브넷의 추가 IP 주소를 일시적으로 사용 합니다.
    - 새 노드가 배포 된 후 이전 노드가 제거 되 면 vCPU 할당량도 구독에서 임시로 사용 됩니다.
    - AKS의 Linux 노드와 마찬가지로를 사용 하 `kured` 여 다시 부팅을 자동으로 업데이트 하 고 관리할 수 없습니다.
- AKS 클러스터에는 최대 8 개의 노드 풀이 있을 수 있습니다.
    - 이러한 8 개 노드 풀에서 최대 400 노드를 사용할 수 있습니다.
- Windows Server 노드 풀 이름은 6 자로 제한 됩니다.
- 네트워크 정책 및 cluster autoscaler와 같은 AKS의 미리 보기 기능은 Windows Server 노드에 대해 보증 되지 않습니다.
- 수신 컨트롤러는 NodeSelector를 사용 하 여 Linux 노드에서만 예약 해야 합니다.
- Azure Dev Spaces 현재 Linux 기반 노드 풀 에서만 사용할 수 있습니다.
- GMSA (그룹 관리 서비스 계정) 지원은 Windows Server 노드가 Active Directory 도메인에 가입 되지 않은 경우 AKS에서 현재 사용할 수 없습니다.
    - 이 기능을 사용 해야 하는 경우 오픈 소스 업스트림 [aks][aks-engine] 프로젝트는 현재 gMSA 지원 기능을 제공 합니다.

## <a name="os-concepts-that-are-different"></a>다른 OS 개념

Kubernetes는 주로 Linux에 초점을 맞추고 있습니다. 업스트림 [Kubernetes.io][kubernetes] 웹 사이트에서 사용 되는 많은 예제는 Linux 노드에서 사용 하기 위한 것입니다. Windows Server 컨테이너를 사용 하는 배포를 만들 때 OS 수준에서 다음과 같은 사항을 고려해 야 합니다.

- **Id** -Linux는 정수 형식으로 표현 된 USERID (UID) 및 GROUPID (GID)를 사용 합니다. 사용자 및 그룹 이름은 정식이 아닙니다. */etc/groups* 또는 */etc/passwd* 에서 UID + GID로의 별칭입니다.
    - Windows Server에서는 Windows 보안 액세스 관리자 (SAM) 데이터베이스에 저장 된 더 큰 이진 SID (보안 식별자)를 사용 합니다. 이 데이터베이스는 호스트와 컨테이너 간에 공유 되거나 컨테이너 간에 공유 되지 않습니다.
- **파일 사용 권한** -Windows Server는 사용 권한 비트 마스크 및 UID + GID가 아닌 sid를 기반으로 하는 액세스 제어 목록을 사용 합니다.
- **파일 경로** -Windows Server의 규칙은/대신 \를 사용 하는 것입니다.
    - 볼륨을 탑재 하는 pod 사양에서 Windows Server 컨테이너에 대 한 경로를 올바르게 지정 합니다. 예를 들어 Linux 컨테이너에서 */mnt/volume* 의 탑재 지점 대신 드라이브 문자 및 위치를 지정 합니다. 예를 들어 *K:* 드라이브로 탑재 하려면/sk/shhhs와 같이 지정 합니다.

## <a name="next-steps"></a>다음 단계

AKS에서 Windows Server 컨테이너를 시작 하려면 [AKS에서 Windows server를 실행 하는 노드 풀을 만듭니다][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
