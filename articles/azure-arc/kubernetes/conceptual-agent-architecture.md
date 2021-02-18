---
title: Azure Arc 사용 Kubernetes 에이전트 아키텍처
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes 에이전트의 아키텍처 개요를 제공 합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 287ffdd40dc9ffdb91abb58b305d8b35b0bc3674
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652567"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc 사용 Kubernetes 에이전트 아키텍처

자체에서 [Kubernetes](https://kubernetes.io/) 는 하이브리드 및 다중 클라우드 환경에서 컨테이너 화 된 워크 로드를 일관 되 게 배포할 수 있습니다. 그러나 Azure Arc enabled Kubernetes는 다른 유형의 환경에서 정책, 거 버 넌 스 및 보안을 관리 하는 중앙 집중식으로 일관 된 제어 평면으로 작동 합니다. 이 문서에서는 다음을 제공합니다.

* 클러스터를 Azure Arc에 연결 하는 아키텍처에 대 한 개요입니다.
* 에이전트를 통해 연결 패턴을 찾습니다.
* 클러스터 환경과 Azure 간에 교환 되는 데이터에 대 한 설명입니다.

## <a name="deploy-agents-to-your-cluster"></a>클러스터에 에이전트 배포

대부분의 온-프레미스 데이터 센터는 네트워크 경계 방화벽에서 인바운드 통신을 차단 하는 엄격한 네트워크 규칙을 적용 합니다. Azure Arc enabled Kubernetes는 방화벽에서 인바운드 포트를 요구 하지 않고 아웃 바운드 통신을 위해 선택적 송신 끝점만 사용 하도록 설정 하 여 이러한 제한에 적용 됩니다. Azure Arc enabled Kubernetes 에이전트는이 아웃 바운드 통신을 시작 합니다. 

![아키텍처 개요](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Azure Arc에 클러스터 연결

1. 선택한 인프라 (VMware vSphere, Amazon Web Services, Google Cloud Platform 등)에서 Kubernetes 클러스터를 만듭니다. 

    > [!NOTE]
    > Azure Arc enabled Kubernetes는 현재 Azure Arc에 기존 Kubernetes 클러스터를 연결 하는 것만 지원 하므로 고객은 Kubernetes 클러스터 자체의 수명 주기를 만들고 관리 해야 합니다.  

1. Azure CLI를 사용 하 여 클러스터에 대 한 Azure Arc 등록을 시작 합니다.
    * Azure CLI는 투구를 사용 하 여 클러스터에 에이전트 투구 차트를 배포 합니다.
    * 클러스터 노드는 [Microsoft Container Registry](https://github.com/microsoft/containerregistry) 에 대 한 아웃 바운드 통신을 시작 하 고 네임 스페이스에 다음 에이전트를 만드는 데 필요한 이미지를 가져옵니다 `azure-arc` .

        | 에이전트 | 설명 |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc enabled Kubernetes는 현재 [시스템 할당 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)만 지원 합니다. `clusteridentityoperator` 첫 번째 아웃 바운드 통신을 시작 합니다. 이 첫 번째 통신에서는 다른 에이전트가 Azure와 통신 하기 위해 사용 하는 MSI (관리 서비스 ID) 인증서를 페치합니다. |
        | `deployment.apps/config-agent` | 클러스터에 적용 된 원본 제어 구성 리소스에 대해 연결 된 클러스터를 감시 합니다. 준수 상태를 업데이트 합니다. |
        | `deployment.apps/controller-manager` | Azure Arc 구성 요소 간의 상호 작용을 오케스트레이션 하는 연산자의 연산자입니다. |    
        | `deployment.apps/metrics-agent` | 최적의 성능을 확인 하기 위해 다른 Arc 에이전트의 메트릭을 수집 합니다. |
        | `deployment.apps/cluster-metadata-operator` | 클러스터 버전, 노드 수 및 Azure Arc 에이전트 버전을 포함한 클러스터 메타데이터를 수집합니다. |
        | `deployment.apps/resource-sync-agent` | 위에서 언급 한 클러스터 메타 데이터를 Azure에 동기화 합니다. |
        | `deployment.apps/flux-logs-agent` | 원본 제어 구성의 일부로 배포 된 flux 연산자에서 로그를 수집 합니다. |
    
1. 모든 Azure Arc enabled Kubernetes agent pod가 `Running` 상태 이면 클러스터가 Azure arc에 연결 되어 있는지 확인 합니다. 다음이 표시 됩니다.
    * [Azure Resource Manager](../../azure-resource-manager/management/overview.md)의 Azure Arc 사용 Kubernetes 리소스입니다. Azure는 실제 Kubernetes 클러스터가 아닌 고객 관리 Kubernetes 클러스터의 프로젝션으로이 리소스를 추적 합니다.
    * 클러스터 메타 데이터 (예: Kubernetes 버전, 에이전트 버전 및 노드 수)는 Azure Arc enabled Kubernetes 리소스에 메타 데이터로 표시 됩니다.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>클러스터 환경 및 Azure 간의 데이터 교환

| 데이터 형식 | 시나리오 | 통신 모드 |
| --------- | -------- | ------------------ |
| Kubernetes 클러스터 버전 | 클러스터 메타 데이터 | 에이전트가 Azure에 푸시 |
| 클러스터의 노드 수 | 클러스터 메타 데이터 | 에이전트가 Azure에 푸시 |
| 에이전트 버전 | 클러스터 메타 데이터 | 에이전트가 Azure에 푸시 |
| Kubernetes 배포 유형 | 클러스터 메타 데이터 | Azure에 Azure CLI 푸시 |
| 인프라 유형 (AWS/GCP/vSphere/...) | 클러스터 메타 데이터 | Azure에 Azure CLI 푸시 |
| 클러스터의 노드 vCPU 수 | 결제 | Azure에 Azure CLI 푸시 |
| 에이전트 하트 비트 | 리소스 상태 | 에이전트가 Azure에 푸시 |
| 에이전트의 리소스 소비 (메모리/CPU) | 진단 및 지원 가능성 | 에이전트가 Azure에 푸시 |
| 모든 에이전트 컨테이너의 로그 | 진단 및 지원 가능성 | 에이전트가 Azure에 푸시 |
| 에이전트 업그레이드 가용성 | 에이전트 업그레이드 | 에이전트가 Azure에서 끌어옵니다. |
| 필요한 구성 상태: Git 리포지토리 URL, flux 연산자 매개 변수, 개인 키, 알려진 호스트 콘텐츠, HTTPS 사용자 이름, 토큰 또는 암호 | 구성 | 에이전트가 Azure에서 끌어옵니다. |
| Flux 운영자 설치 상태 | 구성 | 에이전트가 Azure에 푸시 |
| 클러스터 내에서 게이트 키퍼를 적용 해야 하는 Azure Policy 할당 | Azure Policy | 에이전트가 Azure에서 끌어옵니다. |
| 클러스터 내 정책 사항을의 감사 및 준수 상태 | Azure Policy | 에이전트가 Azure에 푸시 |
| 고객 작업에 대 한 메트릭 및 로그 | Azure Monitor | 에이전트는 고객의 테 넌 트 및 구독에서 Log Analytics 작업 영역 리소스에 푸시합니다. |

## <a name="connectivity-status"></a>연결 상태

| 상태 | 설명 |
| ------ | ----------- |
| Connecting | Azure Arc enabled Kubernetes 리소스가 Azure Resource Manager에서 만들어지지만 서비스에서 에이전트 하트 비트를 아직 받지 못했습니다. |
| 연결됨 | Azure Arc enabled Kubernetes 서비스가 지난 15 분 내에 에이전트 하트 비트를 받았습니다. |
| 오프라인 | Azure Arc enabled Kubernetes 리소스가 이전에 연결 되었지만 서비스가 15 분 동안 에이전트 하트 비트를 받지 못했습니다. |
| 만료됨 | MSI 인증서를 발급 한 후 90 일이 지나면 만료 기간이 만료 됩니다. 이 인증서가 만료 되 면 리소스가 고려 되 `Expired` 고 구성, 모니터링 및 정책과 같은 모든 기능이이 클러스터에서 작동 하지 않습니다. 만료 된 Azure Arc 사용 Kubernetes 리소스를 해결 하는 방법에 대 한 자세한 내용은 [FAQ 문서](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)를 참조 하세요. |

## <a name="understand-connectivity-modes"></a>연결 모드 이해

| 연결 모드 | 설명 |
| ----------------- | ----------- |
| 모두 연결 됨 | 에이전트는 GitOps 구성을 전파 하 고, Azure Policy 및 게이트 키퍼 정책을 적용 하 고, Azure Monitor에서 워크 로드 메트릭과 로그를 수집 하는 지연을 최소화 하면서 Azure와 일관 되 게 통신할 수 있습니다. |
| 반 연결 됨 | 에서 끌어온 MSI 인증서는 `clusteridentityoperator` 인증서가 만료 될 때까지 최대 90 일 동안 유효 합니다. 만료 되 면 Azure Arc enabled Kubernetes 리소스가 작동을 중지 합니다. 클러스터에서 모든 Azure Arc 기능을 다시 활성화 하려면 Azure Arc enabled Kubernetes 리소스 및 에이전트를 삭제 하 고 다시 만듭니다. 90 일 동안 적어도 30 일 마다 클러스터를 연결 합니다. |
| 연결 끊김 | 현재 연결 되지 않은 환경의 Kubernetes 클러스터는 azure Arc enabled Kubernetes에서 Azure에 액세스할 수 없습니다. 이 기능에 관심이 있는 경우 [Azure Arc의 UserVoice 포럼](https://feedback.azure.com/forums/925690-azure-arc)에서 아이디어를 제출 하거나 사전 투표 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Arc에 클러스터 연결](./connect-cluster.md)
* [구성의 개념적 개요](./conceptual-configurations.md)