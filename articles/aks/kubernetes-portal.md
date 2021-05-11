---
title: Azure Portal에서 Kubernetes 리소스에 액세스
description: Kubernetes 리소스와 상호 작용하여 Azure Portal에서 AKS(Azure Kubernetes Service) 클러스터를 관리하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: ce5dc74dc3625b2b1fed447c4e6480308267d32a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578675"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Azure Portal에서 Kubernetes 리소스에 액세스

Azure Portal에는 AKS(Azure Kubernetes Service) 클러스터의 Kubernetes 리소스에 쉽게 액세스할 수 있는 Kubernetes 리소스 보기가 포함되어 있습니다. Azure Portal에서 Kubernetes 리소스를 보면 Azure Portal과 `kubectl` 명령줄 도구 사이의 컨텍스트 전환이 감소하여 Kubernetes 리소스를 보고 편집하는 환경이 간소화됩니다. 리소스 보기에는 현재 여러 리소스 종류(예: 배포, Pod 및 복제본 세트)가 포함되어 있습니다.

Azure Portal의 Kubernetes 리소스 보기가 더 이상 사용되지 않는 [AKS 대시보드 추가 기능][kubernetes-dashboard]을 대체합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Portal에서 Kubernetes 리소스를 보려면 AKS 클러스터가 필요합니다. 모든 클러스터가 지원되지만 Azure Active Directory (Azure AD) 통합을 사용하는 경우 클러스터에서 [AKS 관리형 Azure AD 통합][aks-managed-aad]을 사용해야 합니다. 클러스터에서 레거시 Azure AD를 사용하는 경우 포털에서 또는 [Azure CLI][cli-aad-upgrade]를 사용하여 클러스터를 업그레이드할 수 있습니다. [Azure Portal을 사용][portal-cluster]하여 새 AKS 클러스터를 만들 수도 있습니다.

## <a name="view-kubernetes-resources"></a>Kubernetes 리소스 보기

Kubernetes 리소스를 보려면 Azure Portal에서 AKS 클러스터로 이동합니다. 왼쪽의 탐색 창은 리소스에 액세스하는 데 사용됩니다. 리소스에는 다음이 포함됩니다.

- **네임스페이스** 에는 클러스터의 네임스페이스가 표시됩니다. 네임스페이스 목록의 맨 위에 있는 필터는 네임스페이스 리소스를 신속하게 필터링 및 표시하는 방법을 제공합니다.
- **워크로드** 에는 배포, Pod, 복제본 세트, 상태 저장 세트, 디먼 세트, 클러스터에 배포된 cron 작업 관련 정보가 표시됩니다. 아래 스크린샷에서는 예제 AKS 클러스터의 기본 시스템 Pod을 보여줍니다.
- **서비스 및 수신** 은 클러스터의 서비스와 수신 리소스를 모두 표시합니다.
- **스토리지** 는 Azure 스토리지 클래스 및 영구 볼륨 정보를 표시합니다.
- **구성** 은 클러스터의 구성 맵 및 비밀을 표시합니다.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Azure Portal에 표시된 Kubernetes Pod 정보." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>애플리케이션 배포

이 예제에서는 샘플 AKS 클러스터를 사용하여 [AKS 빠른 시작][portal-quickstart]에서 Azure Vote 애플리케이션을 배포합니다.

1. 리소스 보기(네임스페이스, 워크로드, 서비스 및 수신, 스토리지 또는 구성)에서 **추가** 를 선택합니다.
1. [AKS 빠른 시작][portal-quickstart]에서 Azure Vote 애플리케이션에 대한 YAML을 붙여넣습니다.
1. YAML 편집기의 맨 아래에 있는 **추가** 를 선택하여 애플리케이션을 배포합니다. 

YAML 파일이 추가되면 리소스 보기에는 생성된 Kubernetes 서비스가 모두 표시됩니다. Azure Vote 애플리케이션에 액세스하는 내부 서비스(azure-vote-back) 및 외부 서비스(azure-vote-front)입니다. 외부 서비스는 브라우저에서 애플리케이션을 쉽게 볼 수 있도록 연결된 외부 IP 주소를 포함합니다.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure Portal에 표시된 Azure Vote 애플리케이션 정보." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>배포 인사이트 모니터

[컨테이너용 Azure Monitor][enable-monitor] 사용이 설정된 AKS 클러스터는 배포 및 기타 인사이트를 신속하게 볼 수 있습니다. Kubernetes 리소스 보기에서 사용자는 CPU 및 메모리 사용량을 포함하여 개별 배포의 라이브 상태를 볼 수 있고, 특정 노드 및 컨테이너에 대한 자세한 정보를 확인하기 위해 Azure Monitor로 전환할 수 있습니다. 다음은 샘플 AKS 클러스터의 배포 인사이트에 대한 예입니다.

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Azure Portal에 표시되는 배포 인사이트." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>YAML 편집

Kubernetes 리소스 보기에는 YAML 편집기도 있습니다. 기본 제공 YAML 편집기를 통해 포털 내에서 서비스 및 배포를 업데이트하거나 만들고 변경 내용을 즉시 적용할 수 있습니다.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Azure Portal에 표시된 Kubernetes 서비스에 대한 YAML 편집기.":::

YAML을 편집한 후에는 **검토 + 저장** 을 선택하고 변경 내용을 확인한 후 다시 저장하면 변경 내용이 적용됩니다.

>[!WARNING]
> UI나 CLI를 통해 직접 프로덕션 변경을 수행하지 않는 것이 좋습니다. [CI(연속 통합) 및 CD(연속 배포) 모범 사례](kubernetes-action.md)를 활용하는 것이 좋습니다. Azure Portal Kubernetes 관리 기능 및 YAML 편집기는 개발 및 테스트 설정에서 새 배포를 학습하고 배포하도록 빌드되었습니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 일반적인 문제 및 문제 해결 단계를 설명합니다.

### <a name="unauthorized-access"></a>무단 액세스

Kubernetes 리소스에 액세스하려면 AKS 클러스터, Kubernetes API 및 Kubernetes 개체에 대한 액세스 권한이 있어야 합니다. 클러스터 관리자 또는 AKS 클러스터에 액세스할 수 있는 권한이 있는 사용자여야 합니다. 클러스터 보안에 대한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션][concepts-identity]을 참조하세요.

>[!NOTE]
> Azure Portal의 Kubernetes 리소스 보기는 [관리형 AAD 지원 클러스터](managed-aad.md) 또는 비 AAD 지원 클러스터에서만 지원합니다. 관리형 AAD 지원 클러스터를 사용 중인 경우 AAD 사용자 또는 ID에 [user `kubeconfig`](control-kubeconfig-access.md)를 가져올 권한 외에 Kubernetes API에 액세스할 역할/역할 바인딩이 있어야 합니다.

### <a name="enable-resource-view"></a>리소스 보기 사용

기존 클러스터의 경우 Kubernetes 리소스 보기를 사용 설정해야 할 수 있습니다. 리소스 보기를 사용하도록 설정하려면 포털에서 클러스터에 대한 프롬프트를 따릅니다.

> [!TIP]
> [**API 서버 권한 있는 IP 범위**](api-server-authorized-ip-ranges.md)에 대해 AKS 기능을 추가하여 API 서버 액세스를 방화벽의 공용 엔드포인트로만 제한할 수 있습니다. 이러한 클러스터에 대한 또 다른 옵션은 `--api-server-authorized-ip-ranges`를 업데이트하여 로컬 클라이언트 컴퓨터 또는 (탐색되는 포털에서의) IP 주소 범위에 대한 액세스를 포함하는 것입니다. 이 액세스를 허용하려면 컴퓨터의 공용 IPv4 주소가 필요합니다. 아래 명령을 사용하거나 인터넷 브라우저에서 "내 IP 주소"를 검색하여 이 주소를 찾을 수 있습니다.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 대한 Kubernetes 리소스에 액세스하는 방법을 살펴보았습니다. Kubernetes 리소스 뷰어를 사용하여 액세스되는 클러스터 리소스 및 YAML 파일을 자세히 알아보려면 [배포 및 YAML 매니페스트][deployments]를 참조하세요.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md