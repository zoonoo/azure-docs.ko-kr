---
title: Azure Kubernetes Service (AKS)에서 Windows Server 노드가 풀에 대 한 제한 사항
description: 실행 하면 Windows Server 노드 풀과 응용 프로그램 워크 로드가 Azure Kubernetes Service (AKS)에서 알려진된 제한 사항에 알아봅니다
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 12fb9dc67e8afae3dcb9ade97dd61ab438e0fac5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475409"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>노드 풀을 Windows Server 및 Azure Kubernetes Service (AKS)에서 응용 프로그램 워크 로드에 대 한 현재 제한 사항

Azure Kubernetes Service (AKS)를 게스트 OS로 노드에서 Windows Server를 실행 하는 노드 풀을 만들 수 있습니다. 이러한 노드는 네이티브 Windows 컨테이너 응용 프로그램을.NET Framework에서 구축 된 것과 같은 실행할 수 있습니다. Linux 및 Windows OS 컨테이너 지원 기능을 제공 하는 방법의 주요 차이점으로 몇 가지 일반적인 Kubernetes 및 pod 관련 기능이 현재 사용할 수 없는 Windows 노드가 풀에 대 한 합니다.

이 문서에서는 몇 가지 제한 사항 및 AKS에서 Windows Server 노드에 대 한 OS 개념을 설명 합니다. Windows Server에 대 한 노드 풀은 현재 미리 보기 중입니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Kubernetes의 Windows Server에 대 한 제한 사항

Windows Server 컨테이너는 Windows 기반 컨테이너 호스트에서 실행 해야 합니다. AKS에서 Windows Server 컨테이너를 실행 하려면 [Windows Server를 실행 하는 노드 풀을 만들] [ windows-node-cli] 게스트 OS로 합니다. 창 서버 노드 풀 지원에는 업스트림 Kubernetes 프로젝트에서 Windows 서버에 포함 된 몇 가지 제한 사항이 포함 됩니다. 이러한 제한 사항을 AKS 관련이 없습니다. Kubernetes에서 Windows Server에 대 한 업스트림이 지원에 대 한 자세한 내용은 참조 하세요. [Windows Server 컨테이너에서 Kubernetes 제한 사항][upstream-limitations]합니다.

AKS와 관련 된 Windows Server 컨테이너에서 Kubernetes에 대 한 다음 업스트림 제한은 다음과 같습니다.

- Windows Server 컨테이너 OS 기본 Windows 서버 노드를 일치 하는 Windows Server 2019만 사용할 수 있습니다.
    - 기본 OS가 지원 되지 않습니다 Windows Server 2016을 사용 하 여 빌드된 컨테이너 이미지입니다.
- 권한 있는 컨테이너를 사용할 수 없습니다.
- Linux 전용 RunAsUser, SELinux, AppArmor, 또는 POSIX 기능 등 Windows Server 컨테이너에서 사용할 수 없습니다.
    - 사용자 권한에 UUI/GUID와 같은 Linux 관련 된 파일 시스템 제한은 또한 Windows Server 컨테이너에서 사용할 수 없습니다.
- Azure 디스크 및 Azure Files 유형은 지원 되는 볼륨을 Windows Server 컨테이너에서 NTFS 볼륨으로 액세스 합니다.
    - NFS 기반 저장소 볼륨은 지원 되지 않습니다.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Windows Server 노드가 풀에 대 한 AKS 제한 사항

AKS에서 Windows Server 노드 풀 지원에 다음 추가 제한 사항이 적용 됩니다.

- AKS 클러스터는 항상 첫 번째 노드 풀으로 Linux 노드 풀을 포함합니다. AKS 클러스터 자체는 삭제 하지 않는 한이 첫 번째 linux 노드 풀을 삭제할 수 없습니다.
- 현재 AKS, 하나의 백 엔드 풀에 기본 Linux 노드 풀만 허용 하는 기본 부하 분산만 지원 합니다. 결과적으로, Windows pod에서 아웃 바운드 트래픽을 항상 [Azure 관리 되는 공용 IP 주소로 변환][azure-outbound-traffic]합니다. 이 IP 주소를 구성할 수 없는 경우 되므로 Windows pod에서 들어오는 허용 목록에 추가 트래픽을를 현재 사용할 수 없습니다. 
- AKS 클러스터는 Azure CNI (고급) 네트워킹 모델을 사용 해야 합니다.
    - (기본) Kubenet 네트워킹 지원 되지 않습니다. Kubenet를 사용 하 여 AKS 클러스터를 만들 수 없습니다. 네트워크 모델의 차이점에 대 한 자세한 내용은 참조 하세요. [AKS에서 응용 프로그램에 대 한 개념을 네트워크][azure-network-models]합니다.
    - Azure CNI 네트워크 모델에는 추가 계획 및 IP 주소 관리에 대 한 고려 사항에 필요합니다. 계획 하 고 Azure CNI를 구현 하는 방법에 대 한 자세한 내용은 참조 하세요. [AKS의 네트워킹 구성 Azure CNI][configure-azure-cni]합니다.
- AKS에서 Windows Server 노드 여야 *업그레이드* 최신 패치를 유지 하기 위해 최신 Windows Server 2019 릴리스로 수정 및 업데이트 합니다. AKS 노드 기본 이미지에는 Windows 업데이트를 사용할 수 없습니다. Windows 업데이트 릴리스 주기 및 사용자 고유의 유효성 검사 프로세스 정기적인 일정에 따라 AKS 클러스터에서 Windows Server 노드 풀에서 업그레이드를 수행 해야 합니다. Windows Server 노드 풀을 업그레이드 하는 방법은 참조 하세요 [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.
    - 이러한 Windows Server 노드 업그레이드는 이전 노드를 제거 하기 전에 배포에 새 노드를 일시적으로 가상 네트워크 서브넷의 추가 IP 주소를 사용 합니다.
    - vCPU 할당량 일시적으로 소비 되는 구독에 새 노드를 배포 된 이전 노드를 제거 합니다.
    - 자동으로 업데이트 하 고 사용 하 여 다시 부팅을 관리할 수 없습니다 `kured` AKS의 Linux 노드를 사용 하 여으로 합니다.
- AKS 클러스터에는 최대 8 개의 노드 풀 있을 수 있습니다.
    - 이러한 8 노드 풀에서 노드를 400 개 까지를 할 수 있습니다.
- Windows Server 노드 풀 이름을 6 자 제한이 있습니다.
- Windows Server 노드에 대 한 네트워크 정책 및 클러스터 autoscaler 보증 되지 같은 AKS에서 미리 보기 기능입니다.
- 수신 컨트롤러는 NodeSelector를 사용 하 여 Linux 노드에서 예약만 해야 합니다.
- Azure 개발 공간 에서만 사용 가능 현재 Linux 기반 노드가 풀에 대 한 합니다.
- 그룹 관리 서비스 계정 (gMSA) Windows 서버 노드에서 Active Directory 도메인에 가입 되지 경우 지원은 없습니다 AKS에서 현재 사용할 수 있습니다.
    - 오픈 소스, 업스트림 [aks 엔진] [ aks-engine] 프로젝트는이 기능을 사용 하는 경우에 현재 gMSA 지원을 제공 합니다.

## <a name="os-concepts-that-are-different"></a>다른 OS 개념

Kubernetes는 지금까지 Linux 중심입니다. 업스트림에 사용 되는 많은 예가 [Kubernetes.io] [ kubernetes] 웹 사이트 Linux 노드에서 사용 됩니다. 만들면 OS 수준 적용 시 다음 고려 사항은 Windows Server 컨테이너를 사용 하는 배포:

- **Identity** -Linux 사용자 Id (UID) 및 그룹 Id (GID) 정수 형식으로 표시를 사용 합니다. 사용자 및 그룹 이름은 정식 하지 않습니다. 영어가에 별칭이 *등은 / 그룹* 또는 *//etc/passwd* UID + GID 돌아갑니다.
    - Windows Server는 Windows 보안 액세스 관리자 (SAM) 데이터베이스에 저장 되는 큰 이진 보안 식별자 (SID)를 사용 합니다. 이 데이터베이스에는 호스트와 컨테이너 또는 컨테이너 간에 공유 되지 않습니다.
- **파일 권한** -Windows Server 사용 권한 및 UID + GID의 비트 마스크 아니라 Sid를 기준으로 하는 액세스 제어 목록
- **파일 경로** -사용 하려면 Windows Server에서 규칙은 \ 대신 /입니다.
    - 볼륨을 탑재 하는 pod 사양에서 올바르게 Windows Server 컨테이너에 대 한 경로 지정 합니다. 예를 들어, 탑재 하는 대신의 가리킨 */mnt 볼륨* Linux 컨테이너에서 드라이브 문자를 지정한 위치와 같은 */K/볼륨* 으로 탑재 하려면 합니다 *k:* 드라이브입니다.

## <a name="next-steps"></a>다음 단계

AKS에서 Windows Server 컨테이너를 시작 하기 [AKS에 Windows Server를 실행 하는 노드 풀을 만들][windows-node-cli]합니다.

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
