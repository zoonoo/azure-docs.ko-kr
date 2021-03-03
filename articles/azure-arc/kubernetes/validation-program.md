---
title: Azure Arc Kubernetes 유효성 검사 프로그램 사용
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Kubernetes 배포에 대 한 Arc 유효성 검사 프로그램을 설명 합니다.
keywords: Kubernetes, Arc, Azure, K8s, 유효성 검사
ms.openlocfilehash: 0d7756f407a3a176c8913d99bdb45a5026300569
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689218"
---
# <a name="azure-arc-validation-program"></a>Azure Arc 유효성 검사 프로그램

Azure Arc 지원 Kubernetes는 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터에서 작동합니다. 또한 Azure Arc 팀은 Azure Arc enabled Kubernetes Kubernetes 배포판의 유효성을 검사 하는 핵심 업계 Kubernetes 제공 공급자와 함께 작업 했습니다. 이러한 공급자가 릴리스한 Kubernetes 배포판의 이후 주 버전과 부 버전은 Azure Arc enabled Kubernetes와의 호환성에 대 한 유효성을 검사 합니다.

## <a name="validated-distributions"></a>유효성을 검사 한 배포

다음 Microsoft에서 제공 하는 Kubernetes 배포판 및 인프라 공급자는 Azure Arc enabled Kubernetes에 대 한 규칙 테스트를 성공적으로 통과 했습니다.

| 배포 및 인프라 공급자 | 버전 |
| ---------------------------------------- | ------- |
| Azure의 클러스터 API 공급자            | 릴리스 버전: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Kubernetes 버전: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| Azure Stack HCI의 AKS                   | 릴리스 버전: [12 월 2020 업데이트](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Kubernetes 버전: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

다음 공급자와 해당 Kubernetes 배포판은 Azure Arc enabled Kubernetes에 대 한 규칙 테스트를 성공적으로 통과 했습니다.

| 공급자 이름 | 배포 이름 | 버전 |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4.6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4.7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes 그리드](https://tanzu.vmware.com/kubernetes-grid) | Kubernetes 버전: v 1.17.5 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes 엔진](https://rancher.com/products/rke/) | RKE CLI 버전: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Kubernetes 버전: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | 버전 2.2.1 |

또한 Azure Arc 팀은 다음과 같은 공용 클라우드 공급자에서 규칙 테스트 및 검증 된 Azure Arc 사용 Kubernetes 시나리오를 실행 했습니다.

| 공용 클라우드 공급자 이름 | 배포 이름 | 버전 |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | 탄력적 Kubernetes 서비스 (EKS) | v 1.18.9  |
| Google Cloud Platform      | GKE(Google Kubernetes Engine) | v 1.17.15 |

## <a name="scenarios-validated"></a>시나리오 유효성 검사

다음 시나리오에서는 Azure Arc enabled Kubernetes 유효성 검사의 일부로 준수 테스트를 실행 합니다.

1. Kubernetes 클러스터를 Azure Arc에 연결 합니다. 
    * 클러스터에 Azure Arc enabled Kubernetes agent 투구 차트를 배포 합니다.
    * 클러스터에 관리 되는 시스템 Id (MSI) 인증서를 설정 합니다.
    * 에이전트는 클러스터 메타 데이터를 Azure로 보냅니다.

2. 구성: 
    * Azure Arc enabled Kubernetes 리소스 위에 구성을 만듭니다.
    * GitOps 워크플로를 설정 하는 데 필요한 [Flux](https://docs.fluxcd.io/)는 클러스터에 배포 됩니다.
    * Flux는 데모 Git 리포지토리에서 매니페스트 및 투구 차트를 끌어와 클러스터에 배포 합니다.

## <a name="next-steps"></a>다음 단계

* [클러스터를 Azure Arc에 연결](./connect-cluster.md)
* [Arc 사용 Kubernetes 클러스터에 대 한 구성 만들기](./use-gitops-connected-cluster.md)
* [Azure Policy를 사용 하 여 대규모로 구성 적용](./use-azure-policy.md)
