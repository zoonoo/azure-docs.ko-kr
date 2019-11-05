---
title: Azure Container Service (ACS)에서 Azure Kubernetes 서비스 (AKS)로 마이그레이션
description: Azure Container Service (ACS)에서 Azure Kubernetes 서비스 (AKS)로 마이그레이션합니다.
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 84e0af89e2b3247bc922ab84286a79a0934323a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472998"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Azure Container Service (ACS)에서 Azure Kubernetes 서비스 (AKS)로 마이그레이션

이 문서는 Kubernetes 및 AKS (Azure Kubernetes Service)를 사용 하 여 Azure Container Service (ACS) 간의 성공적인 마이그레이션을 계획 하 고 실행 하는 데 도움이 됩니다. 핵심 사항을 결정 하는 데 도움이 되도록이 가이드에서는 ACS와 AKS의 차이점에 대해 자세히 설명 하 고 마이그레이션 프로세스에 대 한 개요를 제공 합니다.

## <a name="differences-between-acs-and-aks"></a>ACS와 AKS 간의 차이점

ACS 및 AKS는 마이그레이션에 영향을 주는 몇 가지 주요 영역에서 다릅니다. 마이그레이션을 수행 하기 전에 다음과 같은 차이점을 검토 하 고 계획 해야 합니다.

* AKS 노드 [는 관리 디스크](../virtual-machines/windows/managed-disks-overview.md)를 사용 합니다.
    * 관리 되지 않는 디스크는 먼저 변환 해야 AKS 노드에 연결할 수 있습니다.
    * Azure 디스크에 대 한 사용자 지정 `StorageClass` 개체는 `unmanaged`에서 `managed`로 변경 해야 합니다.
    * 모든 `PersistentVolumes` `kind: Managed`를 사용 해야 합니다.
* AKS는 [여러 노드 풀](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)을 지원 합니다.
* Windows Server 기반 노드는 현재 [AKS에서 미리 보기로](https://azure.microsoft.com/blog/kubernetes-on-azure/)제공 됩니다.
* AKS는 제한 된 [지역](https://docs.microsoft.com/azure/aks/quotas-skus-regions)집합을 지원 합니다.
* AKS는 호스팅된 Kubernetes 제어 평면을 사용하는 관리되는 서비스입니다. 이전에 ACS 마스터의 구성을 수정한 경우 응용 프로그램을 수정 해야 할 수 있습니다.

## <a name="differences-between-kubernetes-versions"></a>Kubernetes 버전 간의 차이점

최신 버전의 Kubernetes로 마이그레이션하는 경우 다음 리소스를 검토 하 여 Kubernetes 버전 관리 전략을 이해 하세요.

* [Kubernetes 버전 및 버전 기울이기 지원 정책](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>마이그레이션 고려 사항

### <a name="agent-pools"></a>에이전트 풀

AKS는 Kubernetes 제어 평면을 관리 하지만 새 클러스터에 포함할 노드의 크기와 수를 계속 정의 합니다. ACS에서 AKS로의 일대일 매핑이 필요하다고 가정하는 경우 기존 ACS 노드 정보를 캡처합니다. 새 AKS 클러스터를 만들 때이 데이터를 사용 합니다.

예:

| Name | 카운트 | VM 크기 | 운영 체제 |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

마이그레이션 중에 추가 가상 머신이 구독에 배포되므로 할당량과 한도가 이러한 리소스에 충분한지 확인해야 합니다. 

자세한 내용은 [Azure 구독 및 서비스 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits)을 참조 하세요. 현재 할당량을 확인 하려면 Azure Portal [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동 하 여 구독을 선택한 다음 **사용량 + 할당량**을 선택 합니다.

### <a name="networking"></a>네트워킹

복잡한 애플리케이션의 경우 일반적으로 한 번에 마이그레이션되는 것이 아니라 시간이 지남에 따라 마이그레이션됩니다. 즉, 이전 환경과 새 환경이 네트워크를 통해 통신 해야 할 수 있습니다. 이전에 `ClusterIP` 서비스를 사용 하 여 통신 하는 응용 프로그램은 `LoadBalancer` 형식으로 노출 하 고 적절 하 게 보호 해야 할 수 있습니다.

마이그레이션을 완료 하기 위해 클라이언트가 AKS에서 실행 되는 새 서비스를 가리키도록 할 수 있습니다. AKS 클러스터 앞에 있는 Load Balancer를 가리키도록 DNS를 업데이트 하 여 트래픽을 리디렉션하는 것이 좋습니다.

### <a name="stateless-applications"></a>상태 비저장 응용 프로그램

상태 비저장 애플리케이션 마이그레이션은 가장 간단한 경우입니다. YAML 정의를 새 클러스터에 적용 하 고 모든 항목이 예상 대로 작동 하는지 확인 하 고 트래픽을 리디렉션하여 새 클러스터를 활성화 합니다.

### <a name="stateful-applications"></a>상태 저장 응용 프로그램

데이터 손실 또는 예기치 않은 가동 중지 시간을 방지 하기 위해 상태 저장 응용 프로그램의 마이그레이션을 신중 하 게 계획 합니다.

#### <a name="highly-available-applications"></a>항상 사용 가능한 응용 프로그램

고가용성 구성에서 일부 상태 저장 응용 프로그램을 배포할 수 있습니다. 이러한 응용 프로그램은 복제본 간에 데이터를 복사할 수 있습니다. 현재이 종류의 배포를 사용 하는 경우 새 AKS 클러스터에 새 멤버를 만든 다음 다운스트림 호출자에 게 미치는 영향을 최소화 하 여 마이그레이션할 수 있습니다. 일반적으로이 시나리오의 마이그레이션 단계는 다음과 같습니다.

1. AKS에서 새 보조 복제본을 만듭니다.
2. 데이터가 복제 될 때까지 기다립니다.
3. 장애 조치 (failover)-보조 복제본을 새 주 복제본으로 만듭니다.
4. AKS 클러스터에 대 한 트래픽을 가리킵니다.

#### <a name="migrating-persistent-volumes"></a>영구적 볼륨 마이그레이션

기존 영구 볼륨을 AKS로 마이그레이션하는 경우 일반적으로 다음 단계를 수행 합니다.

1. 응용 프로그램에 쓰기를 정지 합니다. (이 단계는 선택 사항이 며 가동 중지 시간이 필요 합니다.)
2. 디스크의 스냅숏을 생성 합니다.
3. 스냅숏에서 새 관리 디스크를 만듭니다.
4. AKS에서 영구적 볼륨을 만듭니다.
5. PersistentVolumeClaims (정적 프로 비전)가 아닌 [기존 볼륨을 사용](https://docs.microsoft.com/azure/aks/azure-disk-volume) 하도록 pod 사양을 업데이트 합니다.
6. 응용 프로그램을 AKS에 배포 합니다.
7. 유효화.
8. AKS 클러스터에 대 한 트래픽을 가리킵니다.

> [!IMPORTANT]
> 쓰기를 정지 하지 않도록 선택 하는 경우 새 배포에 데이터를 복제 해야 합니다. 그렇지 않으면 디스크 스냅숏을 만든 후 작성 된 데이터를 놓치지 않습니다.

일부 오픈 소스 도구는 관리 디스크를 만들고 Kubernetes 클러스터 간에 볼륨을 마이그레이션하는 데 도움이 될 수 있습니다.

* [Azure CLI 디스크 복사 확장](https://github.com/noelbundick/azure-cli-disk-copy-extension) 은 리소스 그룹 및 Azure 지역에서 디스크를 복사 하 고 변환 합니다.
* [Azure KUBE CLI 확장](https://github.com/yaron2/azure-kube-cli) 은 ACS Kubernetes 볼륨을 열거 하 고이를 AKS 클러스터로 마이그레이션합니다.

#### <a name="azure-files"></a>Azure 파일

디스크와 달리 Azure Files는 여러 호스트에 동시에 탑재할 수 있습니다. AKS 클러스터에서 Azure 및 Kubernetes는 ACS 클러스터가 여전히 사용 하는 pod를 만들 수 있도록 방지 하지 않습니다. 데이터 손실 및 예기치 않은 동작을 방지 하려면 클러스터가 같은 파일에 동시에 쓰지 않도록 합니다.

응용 프로그램이 동일한 파일 공유를 가리키는 여러 복제본을 호스팅할 수 있는 경우 상태 비저장 마이그레이션 단계를 수행 하 고 새 클러스터에 YAML 정의를 배포 합니다. 그렇지 않은 경우 한 가지 가능한 마이그레이션 방법에 다음 단계가 포함됩니다.

1. 복제본 수가 0 인 AKS에 응용 프로그램을 배포 합니다.
2. ACS의 응용 프로그램 크기를 0으로 조정 합니다. 이 단계에는 가동 중지 시간이 필요 합니다.
3. AKS에서 응용 프로그램을 최대 1 개까지 확장 합니다.
4. 유효화.
5. AKS 클러스터에 대 한 트래픽을 가리킵니다.

빈 공유로 시작 하 고 원본 데이터의 복사본을 만들려면 [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) 명령을 사용 하 여 데이터를 마이그레이션할 수 있습니다.

### <a name="deployment-strategy"></a>배포 전략

기존 CI/CD 파이프라인을 사용 하 여 AKS에 알려진 양호한 구성을 배포 하는 것이 좋습니다. 기존 배포 작업을 복제 하 고 `kubeconfig` 새 AKS 클러스터를 가리키는지 확인 합니다.

가능 하지 않은 경우 ACS에서 리소스 정의를 내보낸 다음 AKS에 적용 합니다. `kubectl`을 사용하여 개체를 내보낼 수 있습니다.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

배포 요구 사항에 따라 몇 가지 오픈 소스 도구가 도움이 될 수 있습니다.

* [Velero](https://github.com/heptio/ark) (이 도구에는 Kubernetes 1.7이 필요 합니다.)
* [Azure Kube CLI 확장](https://github.com/yaron2/azure-kube-cli)
* [Resster](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>마이그레이션 단계

1. Azure Portal, Azure CLI 또는 Azure Resource Manager 템플릿을 통해 [AKS 클러스터를 만듭니다](https://docs.microsoft.com/azure/aks/create-cluster) .

   > [!NOTE]
   > GitHub의 [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) 리포지토리에서 AKS 샘플 Azure Resource Manager 템플릿을 찾습니다.

2. YAML 정의를 필요한 대로 변경 합니다. 예를 들어 `apps/v1beta1`를 `Deployments``apps/v1`으로 바꿉니다.

3. ACS 클러스터에서 AKS 클러스터로 볼륨 (옵션)을 [마이그레이션합니다](#migrating-persistent-volumes) .

4. CI/CD 시스템을 사용 하 여 응용 프로그램을 AKS에 배포 합니다. 또는 kubectl를 사용 하 여 YAML 정의를 적용 합니다.

5. 유효화. 응용 프로그램이 예상 대로 작동 하 고 마이그레이션된 데이터가 복사 되었는지 확인 합니다.

6. 트래픽 리디렉션. 클라이언트에서 AKS 배포를 가리키도록 DNS를 업데이트합니다.

7. 마이그레이션 후 작업을 완료 합니다. 볼륨을 마이그레이션하고 쓰기를 정지 하지 않기로 선택한 경우 해당 데이터를 새 클러스터에 복사 합니다.
