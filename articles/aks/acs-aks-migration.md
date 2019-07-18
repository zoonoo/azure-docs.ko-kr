---
title: Azure Container Service (ACS)에서 AKS (Azure Kubernetes Service)로 마이그레이션
description: Azure Container Service (ACS)에서 AKS (Azure Kubernetes Service)로 마이그레이션.
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65977707"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Azure Container Service (ACS)에서 AKS (Azure Kubernetes Service)로 마이그레이션

이 문서에서는 계획 하 고 Kubernetes 사용 하 여 Azure 컨테이너 서비스 (ACS)와 Azure Kubernetes Service (AKS) 간의 성공적인 마이그레이션을 실행 합니다. 중요 한 결정을 내릴 수, 있도록이 가이드는 ACS와 AKS 간의 차이점에 자세히 설명 하 고 마이그레이션 프로세스의 개요를 제공 합니다.

## <a name="differences-between-acs-and-aks"></a>ACS와 AKS 간의 차이점

ACS와 AKS 마이그레이션에 영향을 주는 몇 가지 주요 영역에서 다릅니다. 모든 마이그레이션 전에 검토 하 고 다음과 같은 차이점을 해결 하기 위해 계획 해야 합니다.

* AKS 노드 사용 [관리 디스크](../virtual-machines/windows/managed-disks-overview.md)합니다.
    * AKS 노드에 연결 하기 전에 관리 되지 않는 디스크를 변환 해야 합니다.
    * 사용자 지정 `StorageClass` 개체를 Azure 디스크에서 변경 해야 `unmanaged` 에 `managed`입니다.
    * 모든 `PersistentVolumes` 사용할지 `kind: Managed`합니다.
* AKS 지원 [여러 노드 풀](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (현재 미리 보기로 제공).
* Windows Server 기반 노드를 현재 [AKS에 미리 보기](https://azure.microsoft.com/blog/kubernetes-on-azure/)합니다.
* AKS의 제한적인 지원 [지역](https://docs.microsoft.com/azure/aks/quotas-skus-regions)합니다.
* AKS는 호스팅된 Kubernetes 제어 평면을 사용하는 관리되는 서비스입니다. ACS 마스터 구성을 이전에 수정한 경우 응용 프로그램을 수정 해야 할 수 있습니다.

## <a name="differences-between-kubernetes-versions"></a>Kubernetes 버전 간의 차이점

(예를 들어 1.7.x 1.9.x 하)에서 Kubernetes의 최신 버전으로 마이그레이션하는, 경우 Kubernetes API에 몇 가지 변경 내용을 이해 하려면 다음 리소스 검토:

* [ThirdPartyResource CustomResourceDefinition로 마이그레이션](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [버전 1.8 및 1.9의에서 워크 로드 API 변경 내용](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>마이그레이션 고려 사항

### <a name="agent-pools"></a>에이전트 풀

AKS에서 Kubernetes 제어 플레인을 관리 하는 있지만 크기와 새 클러스터에 포함할 노드의 수를 정의할 여전히 있습니다. ACS에서 AKS로의 일대일 매핑이 필요하다고 가정하는 경우 기존 ACS 노드 정보를 캡처합니다. 새 AKS 클러스터를 만들 때이 데이터를 사용 합니다.

예제:

| 이름 | 카운트 | VM 크기 | 운영 체제 |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

마이그레이션 중에 추가 가상 머신이 구독에 배포되므로 할당량과 한도가 이러한 리소스에 충분한지 확인해야 합니다. 

자세한 내용은 [Azure 구독 및 서비스 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits)합니다. Azure portal에서 현재 할당량을 확인 하려면로 이동 합니다 [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)구독을 선택한 다음 선택 **사용량 + 할당량**합니다.

### <a name="networking"></a>네트워킹

복잡한 애플리케이션의 경우 일반적으로 한 번에 마이그레이션되는 것이 아니라 시간이 지남에 따라 마이그레이션됩니다. 이전 및 새 환경 네트워크를 통해 통신 해야 할 수 있는 것을 의미 합니다. 이전에 사용 하는 응용 프로그램 `ClusterIP` 서비스를 통신 형식으로 표시 되기 위해 필요할 수 `LoadBalancer` 를 적절 하 게 유지 되어야 합니다.

마이그레이션을 완료 하려면 클라이언트 AKS에서 실행 되는 새 서비스를 가리키도록 합니다. AKS 클러스터 앞에 있는 부하 분산 장치를 가리키도록 DNS를 업데이트 하 여 트래픽을 리디렉션하는 것이 좋습니다.

### <a name="stateless-applications"></a>상태 비저장 응용 프로그램

상태 비저장 애플리케이션 마이그레이션은 가장 간단한 경우입니다. YAML 정의 새 클러스터에 적용 하 고, 모든 항목이 예상 대로 작동 하는지 확인 하 고, 새 클러스터를 활성화 하는 트래픽을 리디렉션해야 합니다.

### <a name="stateful-applications"></a>상태 저장 응용 프로그램

데이터 손실 또는 예기치 않은 가동 중지를 방지 하려면 상태 저장 응용 프로그램 마이그레이션을 계획 신중 하 게 합니다.

#### <a name="highly-available-applications"></a>항상 사용 가능한 응용 프로그램

고가용성 구성의 일부 상태 저장 응용 프로그램을 배포할 수 있습니다. 이러한 응용 프로그램은 복제본에서 데이터를 복사할 수 있습니다. 현재이 유형의 배포를 사용 하는 경우를 새 AKS 클러스터에서 새 멤버를 만들려면 한 후 다운스트림 호출자에 거의 영향을 마이그레이션할 수 있습니다. 일반적으로이 시나리오에 대 한 마이그레이션 단계는 다음과 같습니다.

1. AKS에서 새 보조 복제본을 만듭니다.
2. 데이터가 복제 될 때까지 기다립니다.
3. 새 주 복제본을 보조 복제본을 확인 하려면 실패 합니다.
4. AKS 클러스터 트래픽을 가리킵니다.

#### <a name="migrating-persistent-volumes"></a>영구적 볼륨 마이그레이션

AKS를 기존 영구적 볼륨 마이그레이션하려는 경우 일반적으로 이러한 단계를 수행 하겠습니다.

1. 정지 응용 프로그램에 씁니다. (이 단계는 선택 사항이 며 가동 중지 시간이 필요 합니다.)
2. 디스크의 스냅숏을 수행 합니다.
3. 스냅숏에서 새 관리 디스크를 만듭니다.
4. AKS에서 영구적 볼륨을 만듭니다.
5. Pod 사양을 업데이트할 [기존 볼륨을 사용 하 여](https://docs.microsoft.com/azure/aks/azure-disk-volume) PersistentVolumeClaims (정적 프로 비전) 하는 대신 합니다.
6. AKS 응용 프로그램을 배포 합니다.
7. 유효성을 검사 합니다.
8. AKS 클러스터 트래픽을 가리킵니다.

> [!IMPORTANT]
> 정지 쓰기를 하지 않으려는 경우에 새 배포로 데이터를 복제 해야 합니다. 그렇지 않은 경우 디스크 스냅숏을 수행한 후 작성 된 데이터 누락 됩니다.

몇 가지 오픈 소스 도구는 관리 디스크를 만들고 Kubernetes 클러스터 간에 볼륨을 마이그레이션할 수 있습니다.

* [Azure CLI 디스크 복사본 확장](https://github.com/noelbundick/azure-cli-disk-copy-extension) 복사 하 고 리소스 그룹 및 Azure 지역에 걸쳐 디스크를 변환 합니다.
* [Azure CLI Kube 확장](https://github.com/yaron2/azure-kube-cli) ACS Kubernetes 볼륨을 열거 하 고 AKS 클러스터를 마이그레이션합니다.

#### <a name="azure-files"></a>Azure 파일

디스크와 달리 Azure Files는 여러 호스트에 동시에 탑재할 수 있습니다. AKS 클러스터에서 Azure 및 Kubernetes를 방지 하지 않습니다 하 ACS 클러스터를 계속 사용 하는 pod를 만들지 못하게 합니다. 데이터 손실 및 예기치 않은 동작을 방지 하려면 클러스터는 동시에 동일한 파일에 작성 되지를 확인 합니다.

응용 프로그램은 동일한 파일 공유를 가리키는 여러 복제본을 호스팅할 수, 상태 비저장 마이그레이션 단계를 따르고 YAML 정의 새 클러스터를 배포 합니다. 그렇지 않은 경우 한 가지 가능한 마이그레이션 방법에 다음 단계가 포함됩니다.

1. AKS에 복제본 개수가 0 인 응용 프로그램을 배포 합니다.
2. 0으로 ACS에서 응용 프로그램을 확장 합니다. (이 단계에는 가동 중지 시간이 필요 합니다.)
3. AKS에서 1까지 응용 프로그램을 확장 합니다.
4. 유효성을 검사 합니다.
5. AKS 클러스터 트래픽을 가리킵니다.

빈 공유 및 원본 데이터의 복사본 만들기를 시작 하려는 경우 사용할 수 있습니다 합니다 [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) 를 데이터를 마이그레이션하는 명령입니다.

### <a name="deployment-strategy"></a>배포 전략

기존 CI/CD 파이프라인에 사용 하 여 AKS에 성공한 구성을 배포 하는 것이 좋습니다. 기존 배포 작업을 복제 하 고 있는지 `kubeconfig` 새 AKS 클러스터를 가리킵니다.

가능 하지 않은 경우 ACS에서 리소스 정의 내보내기 및 AKS에 적용할 합니다. `kubectl`을 사용하여 개체를 내보낼 수 있습니다.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

여러 오픈 소스 도구 배포 요구 사항에 따라 유용할 수 있습니다.

* [Velero](https://github.com/heptio/ark) (Kubernetes 1.7이이 도구에 필요 합니다.)
* [Azure CLI Kube 확장](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>마이그레이션 단계

1. [AKS 클러스터 만들기](https://docs.microsoft.com/azure/aks/create-cluster) Azure portal, Azure CLI 또는 Azure Resource Manager 템플릿을 통해.

   > [!NOTE]
   > AKS에 대 한 샘플 Azure Resource Manager 템플릿의 찾을 합니다 [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) github 리포지토리.

2. YAML 정의 필요한 대로 변경 합니다. 예를 들어 바꿉니다 `apps/v1beta1` 사용 하 여 `apps/v1` 에 대 한 `Deployments`합니다.

3. [볼륨을 마이그레이션할](#migrating-persistent-volumes) (선택 사항) ACS 클러스터에서 AKS 클러스터에 있습니다.

4. AKS에 응용 프로그램을 배포할 CI/CD 시스템을 사용 합니다. 또는 kubectl을 사용 하 여 YAML 정의 적용 합니다.

5. 유효성을 검사 합니다. 응용 프로그램이 예상 대로 작동 하는지는 마이그레이션된 데이터는 복사 된 있는지 확인 합니다.

6. 트래픽을 리디렉션하십시오. 클라이언트에서 AKS 배포를 가리키도록 DNS를 업데이트합니다.

7. 마이그레이션 후 작업을 완료 합니다. 볼륨을 마이그레이션하고 정지 기록 하지 않도록 선택한 경우 새 클러스터로 데이터를 복사 합니다.
