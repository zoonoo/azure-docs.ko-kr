---
title: ACS(Azure Container Service)에서 AKS(Azure Kubernetes Service)로 마이그레이션
description: ACS(Azure Container Service)에서 AKS(Azure Kubernetes Service)로 마이그레이션
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: e42b0e7bd1bce40b7c58d75cb07f5a3f8afa5836
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385044"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>ACS(Azure Container Service)에서 AKS(Azure Kubernetes Service)로 마이그레이션

이 문서는 Kubernetes가 있는 ACS(Azure Container Service)와 AKS(Azure Kubernetes Service) 간의 성공적인 마이그레이션을 계획하고 실행하는 데 도움을 주기 위한 것입니다. 이 가이드에서는 ACS와 AKS 간의 차이점에 대해 자세히 설명하고, 마이그레이션 프로세스에 대한 개요를 제공하며, 중요한 결정을 내리는 데 도움을 줍니다.

## <a name="differences-between-acs-and-aks"></a>ACS와 AKS 간의 차이점

ACS와 AKS는 마이그레이션에 영향을 주는 일부 주요 영역에서 서로 다릅니다. 마이그레이션하기 전에 검토하고 계획을 세워야 하는 차이점은 다음과 같습니다.

* AKS 노드는 [Managed Disks](../virtual-machines/windows/managed-disks-overview.md)를 사용합니다.
    * 관리되지 않는 디스크는 AKS 노드에 연결하기 전에 변환해야 합니다.
    * Azure 디스크에 대한 사용자 지정 `StorageClass` 개체는 `unmanaged`에서 `managed`로 변경해야 합니다.
    * 모든 `PersistentVolumes`에서 `kind: Managed`를 사용해야 합니다.
* AKS는 현재 하나의 에이전트 풀만 지원합니다.
* Windows Server 기반 노드는 현재 [비공개 미리 보기](https://azure.microsoft.com/blog/kubernetes-on-azure/)로 있습니다.
* AKS [지원 지역](https://docs.microsoft.com/azure/aks/container-service-quotas) 목록을 확인합니다.
* AKS는 호스팅된 Kubernetes 제어 평면을 사용하는 관리되는 서비스입니다. 이전에 ACS 마스터의 구성을 수정한 경우 애플리케이션을 수정해야 할 수도 있습니다

### <a name="differences-between-kubernetes-versions"></a>Kubernetes 버전 간의 차이점

새 버전의 Kubernetes로 마이그레이션하는 경우(예: 1.7.x에서 1.9.x) 주의가 필요한 k8s API가 약간 변경되었습니다.

* [ThirdPartyResource를 CustomResourceDefinition으로 마이그레이션](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [버전 1.8 및 1.9에서 워크로드 API 변경](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>마이그레이션 고려 사항

### <a name="agent-pools"></a>에이전트 풀

AKS에서 Kubernetes 제어 평면을 관리하는 동안에도 새 클러스터에 포함할 노드의 크기와 수를 정의합니다. ACS에서 AKS로의 일대일 매핑이 필요하다고 가정하는 경우 기존 ACS 노드 정보를 캡처합니다. 새 AKS 클러스터를 만들 때 이 데이터를 사용합니다.

예제:

| 이름 | 개수 | VM 크기 | 운영 체제 |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 |  Windows |

마이그레이션 중에 추가 가상 머신이 구독에 배포되므로 할당량과 한도가 이러한 리소스에 충분한지 확인해야 합니다. [Azure 구독 및 서비스 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits)을 검토하여 자세히 알아볼 수 있습니다. 현재 할당량을 확인하려면 Azure Portal에서 [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동하고, 구독을 선택한 다음, `Usage + quotas`를 선택합니다.

### <a name="networking"></a>네트워킹

복잡한 애플리케이션의 경우 일반적으로 한 번에 마이그레이션되는 것이 아니라 시간이 지남에 따라 마이그레이션됩니다. 즉 이전 환경과 새 환경이 네트워크를 통해 통신해야 할 수도 있습니다. 이전에 `ClusterIP` 서비스를 사용하여 통신할 수 있었던 애플리케이션은 `LoadBalancer` 유형으로 공개하고 적절하게 보호해야 합니다.

마이그레이션을 완료하려면 클라이언트가 AKS에서 실행되는 새 서비스를 가리키도록 합니다. 트래픽을 리디렉션하는 경우 AKS 클러스터 앞에 있는 Load Balancer를 가리키도록 DNS를 업데이트하는 것이 좋습니다.

### <a name="stateless-applications"></a>상태 비저장 애플리케이션

상태 비저장 애플리케이션 마이그레이션은 가장 간단한 경우입니다. YAML 정의를 새 클러스터에 적용하고, 모든 항목이 예상대로 작동하는지 확인한 다음, 트래픽을 리디렉션하여 새 클러스터를 활성화합니다.

### <a name="stateful-applications"></a>상태 저장 애플리케이션

상태 저장 애플리케이션을 마이그레이션하려면 데이터 손실이나 예기치 않은 가동 중지 시간을 방지하기 위해 신중하게 계획해야 합니다.

#### <a name="highly-available-applications"></a>고가용성 애플리케이션

일부 상태 저장 애플리케이션은 고가용성 구성으로 배포할 수 있으며, 복제본 간에 데이터를 복사할 수 있습니다. 이 시나리오가 현재 배포를 설명하는 경우 새 AKS 클러스터에 새 멤버를 만들고, 다운스트림 호출자에게 미치는 영향을 최소화하면서 마이그레이션할 수 있습니다. 이 시나리오의 마이그레이션 단계는 일반적으로 다음과 같습니다.

1. AKS에 새 보조 복제본을 만들기
2. 데이터가 복제될 때까지 대기
3. 보조 복제본을 새 주 복제본으로 만들도록 장애 조치
4. AKS 클러스터를 가리키도록 트래픽 지정

#### <a name="migrating-persistent-volumes"></a>영구적 볼륨 마이그레이션

기존 영구적 볼륨을 AKS로 마이그레이션하는 경우 고려해야 할 몇 가지 요인이 있습니다. 일반적으로 관련 단계는 다음과 같습니다.

1. (선택 사항) 애플리케이션에 쓰기 정지(가동 중지 시간 필요)
2. 디스크 스냅숏
3. 스냅숏에서 새 Managed Disks 만들기
4. AKS에서 영구적 볼륨 만들기
5. PersistentVolumeClaims(정적 프로비전) 대신 [기존 볼륨을 사용](https://docs.microsoft.com/azure/aks/azure-disk-volume)하도록 Pod 사양 업데이트
6. AKS에 애플리케이션 배포
7. 유효성 검사
8. AKS 클러스터를 가리키도록 트래픽 지정

> **중요**: 쓰기 작업을 정지하지 않으려면 디스크 스냅숏 이후에 작성된 데이터가 누락되므로 새 배포에 데이터를 복제해야 합니다.

Managed Disks를 만들고 Kubernetes 클러스터 간에 볼륨을 마이그레이션하는 데 도움이 되는 오픈 소스 도구가 있습니다.

* [noelbundick/azure-cli-disk-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) - 리소스 그룹 및 Azure 지역 간에 디스크를 복사하고 변환합니다.
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) - ACS Kubernetes 볼륨을 열거하고, 이를 AKS 클러스터로 마이그레이션합니다.

#### <a name="azure-files"></a>Azure 파일

디스크와 달리 Azure Files는 여러 호스트에 동시에 탑재할 수 있습니다. Azure와 Kubernetes는 모두 ACS 클러스터에서 계속 사용하는 AKS 클러스터에 Pod를 만들 수 없습니다. 데이터 손실과 예기치 않은 동작을 방지하려면 두 클러스터 모두에서 동일한 파일에 동시에 쓰지 않도록 해야 합니다.

애플리케이션에서 동일한 파일 공유를 가리키는 여러 복제본을 호스팅할 수 있는 경우, 상태 비저장 마이그레이션 단계를 수행하고 YAML 정의를 새 클러스터에 배포할 수 있습니다.

그렇지 않은 경우 한 가지 가능한 마이그레이션 방법에 다음 단계가 포함됩니다.

1. 복제본 수가 0인 AKS에 애플리케이션 배포
2. ACS에서 애플리케이션 크기를 0으로 조정(가동 중지 시간 필요)
3. AKS에서 애플리케이션 크기를 최대 1까지 조정
4. 유효성 검사
5. AKS 클러스터를 가리키도록 트래픽 지정

빈 공유로 시작한 다음, 원본 데이터의 복사본을 만들려면 [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) 명령을 사용하여 데이터를 마이그레이션할 수 있습니다.

### <a name="deployment-strategy"></a>배포 전략

권장되는 방법은 기존 CI/CD 파이프라인을 사용하여 AKS에 유효한 것으로 알려진 구성을 배포하는 것이 좋습니다. 기존 배포 작업을 복제하고 `kubeconfig`에서 새 AKS 클러스터를 가리키는지 확인합니다.

가능하지 않은 경우 ACS에서 리소스 정의를 내보낸 다음, 이를 AKS에 적용해야 합니다. `kubectl`을 사용하여 개체를 내보낼 수 있습니다.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

또한 필요에 따라 도움을 줄 수 있는 몇 가지 오픈 소스 도구가 있습니다.

* [heptio/ark](https://github.com/heptio/ark) - k8s 1.7 필요
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>마이그레이션 단계

### <a name="1-create-an-aks-cluster"></a>1. AKS 클러스터 만들기

문서의 지침에 따라 Azure Portal, Azure CLI 또는 Resource Manager 템플릿을 통해 [AKS 클러스터를 만들](https://docs.microsoft.com/azure/aks/create-cluster) 수 있습니다.

> GitHub의 [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) 리포지토리에서 AKS용 Azure Resource Manager 템플릿 샘플을 찾을 수 있습니다

### <a name="2-modify-applications"></a>2. 애플리케이션 수정

필요에 따라 YAML 정의를 수정합니다. 예: `Deployments`에 대해 `apps/v1beta1`을 `apps/v1`로 바꾸기

### <a name="3-optional-migrate-volumes"></a>3. (선택 사항) 볼륨 마이그레이션

ACS 클러스터에서 AKS 클러스터로 볼륨을 마이그레이션합니다. 자세한 내용은 [영구적 볼륨 마이그레이션](#Migrating-Persistent-Volumes) 섹션에 나와 있습니다.

### <a name="4-deploy-applications"></a>4. 애플리케이션 배포

CI/CD 시스템을 사용하여 AKS에 애플리케이션을 배포하거나, kubectl을 사용하여 YAML 정의를 적용합니다.

### <a name="5-validate"></a>5. 유효성 검사

애플리케이션이 예상대로 작동하고 마이그레이션된 데이터가 복사되었는지 확인합니다.

### <a name="6-redirect-traffic"></a>6. 트래픽 리디렉션

클라이언트에서 AKS 배포를 가리키도록 DNS를 업데이트합니다.

### <a name="7-post-migration-tasks"></a>7. 마이그레이션 후 작업

볼륨을 마이그레이션하고 쓰기를 정지하지 않도록 선택한 경우 해당 데이터를 새 클러스터에 복사해야 합니다.
