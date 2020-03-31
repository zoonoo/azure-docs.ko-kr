---
title: 일반적인 Azure Kubernetes Service 문제 해결
description: AKS(Azure Kubernetes Service)를 사용 할 때 발생하는 일반적인 문제를 해결하는 방법을 알아봅니다.
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368335"
---
# <a name="aks-troubleshooting"></a>AKS 문제 해결

AKS(Azure Kubernetes Service) 클러스터를 만들거나 관리할 때 경우에 따라 문제가 발생할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 및 문제 해결 단계를 자세히 설명합니다.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>일반적으로 Kubernetes 문제 디버깅에 대한 정보는 어디에서 찾나요?

[Kubernetes 클러스터 문제 해결에 대한 공식 가이드](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)를 검색합니다.
pod, 노드, 클러스터 등의 문제 해결과 관련해서 Microsoft 엔지니어가 게시한 [문제 해결 가이드](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)도 있습니다.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>만들기 또는 업그레이드 동안 "할당량 초과" 오류가 발생합니다. 어떻게 해야 하나요? 

[코어를 요청](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)해야 합니다.

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>AKS의 노드당 최대 Pod 설정이란?

노드당 최대 Pod 설정은 Azure Portal에서 AKS 클러스터를 배포하는 경우 기본적으로 30입니다.
노드당 최대 Pod 설정은 Azure CLI에서 AKS 클러스터를 배포하는 경우 기본적으로 110입니다. Azure CLI의 최신 버전을 사용하도록 합니다. 이 기본 설정은 `az aks create` 명령에서 `–-max-pods` 플래그를 사용하여 변경할 수 있습니다.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>고급 네트워킹을 사용하여 AKS 클러스터를 배포하는 동안 insufficientSubnetSize 오류가 발생합니다. 어떻게 해야 하나요?

Azure CNI(고급 네트워킹)가 사용되는 경우 AKS는 구성된 노드당 "최대 포드"를 기반으로 IP 주소를 할당합니다. 노드당 구성된 최대 포드에 따라 서브넷 크기는 노드 수와 노드당 최대 포드 설정의 곱수보다 커야 합니다. 다음 방정식은 다음과 같은 개요를 설명합니다.

서브넷 크기> 클러스터의 노드 수(향후 확장 요구 사항을 고려) * 노드 집합당 최대 포드입니다.

자세한 내용은 [클러스터에 대한 IP 주소 지정 계획](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)을 참조하세요.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>내 Pod가 CrashLoopBackOff 모드에서 중단됩니다. 어떻게 해야 하나요?

Pod가 해당 모드에서 중단되는 이유는 다양할 수 있습니다. 다음을 확인할 수 있습니다.

* `kubectl describe pod <pod-name>`를 사용하여 pod 자체
* `kubectl logs <pod-name>`을 사용하여 로그

Pod 문제를 해결하는 방법에 대한 자세한 내용은 [애플리케이션 디버그](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)를 참조하세요.

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>기존 클러스터에서 RBAC를 사용하도록 설정하려고 합니다. 어떻게 하면 되나요?

현재는 기존 클러스터에서 RBAC(역할 기반 액세스 제어)를 사용하도록 설정할 수 없습니다. 새 클러스터를 명시적으로 만들어야 합니다. CLI를 사용하는 경우 RBAC가 기본적으로 사용됩니다. AKS 포털을 사용하는 경우 만들기 워크플로에서 RBAC를 사용하도록 설정하는 토글 단추를 사용할 수 있습니다.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>기본 설정의 Azure CLI 또는 Azure Portal에서 RBAC가 설정된 클러스터를 만들었으나 kubernetes 대시보드에서 경고가 많이 표시됩니다. 이전에는 대시보드가 경고없이 잘 작동되었습니다. 어떻게 해야 하나요?

대시보드에 경고가 표시되는 이유는 클러스터에 RBAC가 설정되어 있으나 해당 클러스터에 대한 액세스는 기본적으로 사용하지 않도록 설정되어 있기 때문입니다. 일반적으로 클러스터의 모든 사용자에게 기본적으로 대시보드를 노출하면 보안 위협이 초래될 수 있으므로 이렇게 하는 것이 적절합니다. 그래도 대시보드를 사용하도록 설정하려면 [이 블로그 게시물](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)의 단계를 따르세요.

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>대시보드에 연결할 수 없습니다. 어떻게 해야 하나요?

클러스터 외부의 서비스에 액세스하는 가장 쉬운 방법은 `kubectl proxy`를 실행하는 것입니다. 그러면 localhost 포트 8001로 전송된 요청이 Kubernetes API 서버로 프록시됩니다. 여기에서 API 서버는 서비스로 프록시할 수 있습니다. `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`

Kubernetes 대시보드가 표시되지 않으면 `kube-proxy` `kube-system` 네임스페이스에서 포드가 실행되고 있는지 확인합니다. 실행 중 상태가 아니면 pod를 삭제합니다. 그러면 다시 시작됩니다.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>kubectl logs를 사용하여 로그를 가져올 수 없고 API 서버에 연결할 수 없습니다. "서버에서 오류: 오류 다이얼 백 엔드: 전화 tcp...". 어떻게 해야 하나요?

기본 네트워크 보안 그룹이 수정되지 않았는지, API 서버에 연결하기 위해 포트 22와 9000이 모두 열려 있는지 확인합니다. 명령을 사용하여 *kube 시스템* 네임스페이스에서 포드가 `kubectl get pods --namespace kube-system` 실행되고 있는지 확인합니다. `tunnelfront` 실행되지 않으면 pod를 강제로 삭제합니다. 그러면 다시 시작됩니다.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>업그레이드하거나 확장하려고 하고 "메시지: 속성 'imageReference' 변경은 허용되지 않습니다" 오류가 발생합니다. 이 문제를 어떻게 해결하나요?

AKS 클러스터 내의 에이전트 노드에서 태그를 수정했기 때문에 이 오류가 발생하는 것일 수 있습니다. MC_* 리소스 그룹에서 태그 및 리소스의 기타 속성을 수정 및 삭제하면 예기치 않은 결과가 발생할 수 있습니다. AKS 클러스터의 MC_* 그룹 아래에서 리소스를 수정하면 SLO(서비스 수준 목표)가 중단됩니다.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>클러스터가 실패한 상태이며 업그레이드 또는 크기 조정이 수정될 때까지 작동하지 않는 오류가 있습니다.

*이 문제 해결 지원은https://aka.ms/aks-cluster-failed*

이 오류는 여러 가지 이유로 클러스터가 실패한 상태로 들어갈 때 발생합니다. 이전에 실패한 작업을 다시 시도하기 전에 클러스터 실패 상태를 해결하려면 아래 단계를 따르십시오.

1. 클러스터가 `failed` 상태가 바을 `upgrade` 때까지 `scale` 작업이 성공하지 못합니다. 일반적인 루트 문제 및 해결 은 다음과 같습니다.
    * **CRP(계산) 할당량이 부족한**크기 조정. 해결하려면 먼저 클러스터를 할당량 내에서 안정적인 목표 상태로 다시 조정합니다. 그런 다음 다음 다음 [단계를 수행하여](../azure-portal/supportability/resource-manager-core-quotas-request.md) 초기 할당량 제한을 초과하여 다시 확장하기 전에 계산 할당량 증가를 요청합니다.
    * 고급 네트워킹 및 불충분 한 **서브넷(네트워킹) 리소스로**클러스터 확장. 해결하려면 먼저 클러스터를 할당량 내에서 안정적인 목표 상태로 다시 조정합니다. 그런 다음 [다음 다음 단계를 수행하여](../azure-resource-manager/templates/error-resource-quota.md#solution) 초기 할당량 제한을 초과하여 다시 확장하기 전에 리소스 할당량 증가를 요청합니다.
2. 업그레이드 실패의 근본 원인이 해결되면 클러스터가 성공한 상태여야 합니다. 성공한 상태가 확인되면 원래 작업을 다시 시도합니다.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>클러스터가 현재 업그레이드 중이거나 업그레이드에 실패한 상태상태를 업그레이드하거나 확장하려고 할 때 오류가 표시됩니다.

*이 문제 해결 지원은https://aka.ms/aks-pending-upgrade*

단일 노드 풀또는 여러 노드 풀이 있는 클러스터가 있는 [클러스터의](use-multiple-node-pools.md) 업그레이드 및 확장 작업은 상호 배타적입니다. 클러스터 또는 노드 풀을 동시에 업그레이드하고 확장할 수 없습니다. 대신 각 작업 유형은 동일한 리소스에 대한 다음 요청 전에 대상 리소스에서 완료되어야 합니다. 따라서 활성 업그레이드 또는 스케일 작업이 발생하거나 시도중이어서 실패할 때 작업이 제한됩니다. 

클러스터에서 자세한 상태를 `az aks show -g myResourceGroup -n myAKSCluster -o table` 검색하기 위해 실행 된 문제를 진단하는 데 도움이됩니다. 결과에 따라 다음을 수행합니다.

* 클러스터를 적극적으로 업그레이드하는 경우 작업이 종료될 때까지 기다립니다. 성공한 경우 이전에 실패한 작업을 다시 시도합니다.
* 클러스터가 업그레이드에 실패한 경우 이전 섹션에 설명된 단계를 따릅니다.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>클러스터를 다른 구독으로 이동하거나 클러스터를 사용하여 새 테넌트에 대한 구독을 이동할 수 있습니까?

AKS 클러스터를 다른 구독으로 이동했거나 클러스터 소유 구독을 새 테넌트로 이동한 경우 역할 할당 및 서비스 주체 권한이 손실되어 클러스터가 기능을 잃게 됩니다. AKS는 이 제약 조건으로 인해 **구독 또는 테넌자 간에 클러스터 이동을 지원하지 않습니다.**

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>가상 시스템 스케일 집합이 필요한 기능을 사용하려고 하는 오류가 있습니다.

*이 문제 해결 지원은 aka.ms/aks-vmss-enablement*

다음 예제와 같이 AKS 클러스터가 가상 시스템 규모 집합에 없음을 나타내는 오류가 발생할 수 있습니다.

**에이전트풀 '에이전트풀'이 자동 크기 조정을 사용하도록 설정했지만 가상 컴퓨터 크기 집합에 없습니다.**

클러스터 자동 크기 조정기 또는 여러 노드 풀과 같은 기능을 사용하려면 가상 시스템 크기 집합을 사용하는 AKS 클러스터를 만들어야 합니다. 가상 시스템 규모 집합에 종속된 기능을 사용하려고 시도하고 일반 비가상 시스템 규모 집합 AKS 클러스터를 대상으로 하는 경우 오류가 반환됩니다.

AKS 클러스터를 올바르게 만들려면 해당 문서에서 단계를 *시작하기 전에* 수행합니다.

* [클러스터 자동 크기 조정기 사용](cluster-autoscaler.md)
* [여러 노드 풀 생성 및 사용](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS 리소스 및 매개 변수에 대해 어떤 명명 제한이 적용됩니까?

*이 문제 해결 지원은 aka.ms/aks-naming-rules*

이름 지정 제한은 Azure 플랫폼과 AKS모두에서 구현됩니다. 리소스 이름 또는 매개 변수가 이러한 제한 중 하나를 중단하면 다른 입력을 제공하도록 요청하는 오류가 반환됩니다. 다음과 같은 일반적인 명명 지침이 적용됩니다.

* 클러스터 이름은 1-63자여야 합니다. 허용되는 문자는 문자, 숫자, 대시 및 밑줄뿐입니다. 첫 번째 와 마지막 문자는 문자 또는 숫자여야 합니다.
* AKS *MC_* 리소스 그룹 이름은 리소스 그룹 이름과 리소스 이름을 결합합니다. 자동 생성된 구문은 `MC_resourceGroupName_resourceName_AzureRegion` 80char를 초과하지 않아야 합니다. 필요한 경우 리소스 그룹 이름 또는 AKS 클러스터 이름의 길이를 줄입니다.
* *dnsPrefix는* 영숫자 값으로 시작하고 끝나야 하며 1-54자 사이여야 합니다. 유효한 문자에는 영숫자 값과 하이픈(-)이 포함됩니다. *dnsPrefix는* 마침표(.)와 같은 특수 문자를 포함할 수 없습니다.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>클러스터를 생성, 업데이트, 확장, 삭제 또는 업그레이드하려고 할 때 다른 작업이 진행 중일 때 해당 작업이 허용되지 않는 오류가 표시됩니다.

*이 문제 해결 지원은 aka.ms/aks-pending-operation*

클러스터 작업은 이전 작업이 아직 진행 중인 경우 제한됩니다. 클러스터의 자세한 상태를 검색하려면 `az aks show -g myResourceGroup -n myAKSCluster -o table` 명령을 사용합니다. 필요에 따라 자체 리소스 그룹 및 AKS 클러스터 이름을 사용합니다.

클러스터 상태의 출력에 따라 다음을 수행합니다.

* 클러스터가 *성공* 또는 *실패*이외의 프로비저닝 상태에 있는 경우*작업(업그레이드/업데이트/만들기/크기 조정/삭제/마이그레이션)이*종료될 때까지 기다립니다. 이전 작업이 완료되면 최신 클러스터 작업을 다시 시도합니다.

* 클러스터에 업그레이드가 실패한 경우 [클러스터가 실패한 상태이며 업그레이드 또는 크기 조정이 수정될 때까지 작동하지 않는 오류를 수신하는](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)단계에 따라 수행합니다.

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>기존 클러스터를 통과하지 않고 새 클러스터를 만들려고 할 때 서비스 주체를 찾을 수 없다는 오류가 표시됩니다.

AKS 클러스터를 만들 때 는 서비스 주체가 대신 리소스를 만들어야 합니다. AKS는 클러스터 생성 시 새 새 디렉터리를 만들 수 있는 기능을 제공하지만 클러스터를 만드는 데 성공하려면 Azure Active Directory가 적절한 시간에 새 서비스 주체를 완전히 전파해야 합니다. 이 전파가 너무 오래 걸리면 클러스터는 사용 가능한 서비스 주체를 찾을 수 없으므로 유효성 검사에 실패합니다. 

이에 대한 다음 해결 방법을 사용합니다.
1. 이미 여러 지역에 전파되어 클러스터 생성 시 AKS에 전달하기 위해 존재하는 기존 서비스 주체를 사용합니다.
2. 자동화 스크립트를 사용하는 경우 서비스 주 체 생성과 AKS 클러스터 생성 사이에 시간 지연을 추가합니다.
3. Azure 포털을 사용하는 경우 몇 분 후에 유효성 검사 페이지를 만들고 다시 시도하는 동안 클러스터 설정으로 돌아갑니다.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>내 송신 트래픽을 제한한 후 오류가 표시됩니다.

AKS 클러스터에서 송신 트래픽을 제한할 때 AKS에 대한 [필수 및 선택적 권장](limit-egress-traffic.md) 아웃바운드 포트/네트워크 규칙 및 FQDN/응용 프로그램 규칙이 있습니다. 설정이 이러한 규칙과 충돌하는 경우 특정 `kubectl` 명령을 실행하지 못할 수 있습니다. AKS 클러스터를 만들 때 오류가 표시될 수도 있습니다.

설정이 필수 또는 선택적 권장 아웃바운드 포트/네트워크 규칙 및 FQDN/응용 프로그램 규칙과 충돌하지 않는지 확인합니다.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 저장소 및 AKS 문제 해결

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Azure 디스크에 권장되는 안정적인 버전의 Kubernetes는 무엇입니까? 

| 쿠베르네테스 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.9 이상 |
| 1.13 | 1.13.6 이상 |
| 1.14 | 1.14.2 이상 |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>주권 클라우드에서 Azure 디스크를 지원하는 Kubernetes 버전은 무엇입니까?

| 쿠베르네테스 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.0 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 | 1.14.0 이상 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach Azure 디스크에 대 한 실패: 구문 분석 "/dev/디스크/azure/scsi1/lun1": 잘못 된 구문

Kubernetes 버전 1.10에서 MountVolume.WaitForAttach Azure 디스크 다시 마운트에 실패할 수 있습니다.

Linux에서 잘못된 DevicePath 형식 오류가 표시될 수 있습니다. 예를 들어:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

Windows에서 LUN(장치 경로) 번호 오류가 잘못 표시될 수 있습니다. 예를 들어:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

이 문제는 Kubernetes의 다음 버전에서 해결되었습니다.

| 쿠베르네테스 버전 | 고정 버전 |
| -- | :--: |
| 1.10 | 1.10.2 이상 |
| 1.11 | 1.11.0 이상 |
| 1.12 이상 | 해당 없음 |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Azure 디스크에 대한 옵션에서 uid 및 gid를 설정할 때 실패

Azure Disk는 기본적으로 ext4, xfs 파일 시스템을 사용하며, 마운트옵션은 uid=x, gid=x와 같은 마운트 시 설정할 수 없습니다. 예를 들어 마운트를 설정하려고 하면옵션 uid=999,gid=999와 같은 오류가 표시됩니다.

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

다음 중 하나를 수행하여 문제를 완화할 수 있습니다.

* runAsUser및 fsGroup에서 gid에서 uid를 설정하여 [포드에 대한 보안 컨텍스트를 구성합니다.](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) 예를 들어 다음 설정은 포드 실행을 루트로 설정하고 모든 파일에 액세스할 수 있도록 합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > gid및 uid는 기본적으로 루트 또는 0으로 마운트되기 때문에. gid 또는 uid가 루트가 아닌 것으로 설정된 경우(예: 1000) Kubernetes는 해당 디스크 아래의 모든 디렉터리 및 파일을 변경하는 데 사용합니다. `chown` 이 작업은 시간이 오래 걸릴 수 있으며 디스크 장착 속도가 매우 느려질 수 있습니다.

* initContainers에서 gid `chown` 및 uid를 설정합니다. 예를 들어:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>포드에서 사용 중 Azure 디스크 영구 볼륨 클레임을 삭제할 때 오류

포드에서 사용 중인 Azure 디스크 영구 볼륨 클레임을 삭제하려고 하면 오류가 표시될 수 있습니다. 예를 들어:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

Kubernetes 버전 1.10 이상에서는 이 오류를 방지하기 위해 기본적으로 영구 볼륨 클레임 보호 기능이 활성화되어 있습니다. 이 문제에 대 한 수정 프로그램이 없는 Kubernetes 버전을 사용 하는 경우 영구 볼륨 클레임을 삭제 하기 전에 영구 볼륨 클레임을 사용 하 여 포드를 삭제 하 여이 문제를 완화 할 수 있습니다.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>노드에 디스크를 연결할 때 오류 "디스크에 대한 Lun을 찾을 수 없습니다"

노드에 디스크를 연결할 때 다음과 같은 오류가 표시될 수 있습니다.

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

이 문제는 Kubernetes의 다음 버전에서 해결되었습니다.

| 쿠베르네테스 버전 | 고정 버전 |
| -- | :--: |
| 1.10 | 1.10.10 이상 |
| 1.11 | 1.11.5 이상 |
| 1.12 | 1.12.3 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 이상 | 해당 없음 |

이 문제에 대한 수정 프로그램이 없는 Kubernetes 버전을 사용하는 경우 몇 분 간 기다렸다가 다시 시도하여 문제를 완화할 수 있습니다.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>여러 연결/분리 작업 중 Azure Disk 연결/분리 오류, 마운트 문제 또는 I/O 오류

Kubernetes 버전 1.9.2에서 시작하여 여러 연결/분리 작업을 병렬로 실행할 때 더러움 VM 캐시로 인해 다음과 같은 디스크 문제가 발생할 수 있습니다.

* 디스크 연결/분리 오류
* 디스크 I/O 오류
* VM에서 예기치 않은 디스크 분리
* VM이 존재하지 않는 디스크를 연결하여 실패한 상태로 실행됨

이 문제는 Kubernetes의 다음 버전에서 해결되었습니다.

| 쿠베르네테스 버전 | 고정 버전 |
| -- | :--: |
| 1.10 | 1.10.12 이상 |
| 1.11 | 1.11.6 이상 |
| 1.12 | 1.12.4 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 이상 | 해당 없음 |

이 문제에 대한 수정 프로그램이 없는 Kubernetes 버전을 사용하는 경우 아래를 시도하여 문제를 완화할 수 있습니다.

* 디스크가 장기간 분리되기를 기다리는 경우 디스크를 수동으로 분리해 보십시오.

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>무기한 분리대기 하는 Azure 디스크

경우에 따라 Azure Disk 분리 작업이 첫 번째 시도에서 실패하면 분리 작업을 다시 시도하지 않으며 원래 노드 VM에 연결된 상태로 유지됩니다. 이 오류는 디스크를 한 노드에서 다른 노드로 이동할 때 발생할 수 있습니다. 예를 들어:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

이 문제는 Kubernetes의 다음 버전에서 해결되었습니다.

| 쿠베르네테스 버전 | 고정 버전 |
| -- | :--: |
| 1.11 | 1.11.9 이상 |
| 1.12 | 1.12.7 이상 |
| 1.13 | 1.13.4 이상 |
| 1.14 이상 | 해당 없음 |

이 문제에 대한 수정 프로그램이 없는 Kubernetes 버전을 사용하는 경우 디스크를 수동으로 분리하여 문제를 완화할 수 있습니다.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure Disk 분리 오류로 인해 잠재적인 경합 상태 문제 및 잘못된 데이터 디스크 목록

Azure 디스크가 분리되지 않으면 최대 6번 다시 시도하여 지수 백오프를 사용하여 디스크를 분리합니다. 또한 약 3분 동안 데이터 디스크 목록에 노드 수준 잠금을 유지합니다. 수동 연결 또는 분리 작업과 같은 기간 동안 디스크 목록이 수동으로 업데이트되면 노드 수준 잠금에 의해 보유된 디스크 목록이 더 이상 사용되지 않게 되고 노드 VM에 불안정이 발생합니다.

이 문제는 Kubernetes의 다음 버전에서 해결되었습니다.

| 쿠베르네테스 버전 | 고정 버전 |
| -- | :--: |
| 1.12 | 1.12.9 이상 |
| 1.13 | 1.13.6 이상 |
| 1.14 | 1.14.2 이상 |
| 1.15 이상 | 해당 없음 |

이 문제에 대한 수정 프로그램이 없는 Kubernetes 버전을 사용하고 있고 노드 VM에 더 이상 사용되지 않는 디스크 목록이 있는 경우 VM에서 존재하지 않는 모든 디스크를 단일 대량 작업으로 분리하여 문제를 완화할 수 있습니다. **기존 디스크를 개별적으로 분리하는 데 실패할 수 있습니다.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>많은 수의 Azure 디스크로 인해 느린 연결/분리

노드 VM에 연결된 Azure 디스크 수가 10보다 큰 경우 연결 및 분리 작업이 느려질 수 있습니다. 이 문제는 알려진 문제이며 현재 해결 방법이 없습니다.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure Disk 분리 오류로 인해 잠재적인 노드 VM이 실패한 상태

일부 에지의 경우 Azure Disk 분리가 부분적으로 실패하고 노드 VM을 실패한 상태로 둘 수 있습니다.

이 문제는 Kubernetes의 다음 버전에서 해결되었습니다.

| 쿠베르네테스 버전 | 고정 버전 |
| -- | :--: |
| 1.12 | 1.12.10 이상 |
| 1.13 | 1.13.8 이상 |
| 1.14 | 1.14.4 이상 |
| 1.15 이상 | 해당 없음 |

이 문제에 대한 수정 프로그램이 없는 Kubernetes 버전을 사용하고 있고 노드 VM이 실패한 상태인 경우 다음 중 하나를 사용하여 VM 상태를 수동으로 업데이트하여 문제를 완화할 수 있습니다.

* 가용성 집합 기반 클러스터의 경우:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* VMSS 기반 클러스터의 경우:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 파일 및 AKS 문제 해결

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Azure 파일에 권장되는 Kubernetes 의 권장 안정 버전은 무엇입니까?
 
| 쿠베르네테스 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.6 이상 |
| 1.13 | 1.13.4 이상 |
| 1.14 | 1.14.0 이상 |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>주권 클라우드에서 Azure 파일을 지원하는 Kubernetes 버전은 무엇입니까?

| 쿠베르네테스 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.0 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 | 1.14.0 이상 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Azure 파일을 사용할 때 기본 마운트옵션은 무엇입니까?

권장 설정:

| 쿠베르네테스 버전 | 파일 모드 및 디모드 값|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 이상 | 0777 |

Kubernetes 버전 1.8.5 이상과 동적으로 저장소 클래스를 사용하여 영구 볼륨을 만드는 클러스터를 사용하는 경우 저장소 클래스 개체에 마운트 옵션을 지정할 수 있습니다. 다음 예제에서는 *0777*을 설정합니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

몇 가지 추가 유용한 *마운트옵션* 설정:

* *mfsymlinks는* Azure 파일 마운트 (cifs) 지원 기호 링크를 만들 것입니다
* *nobrl은* 바이트 범위 잠금 요청을 서버로 전송하지 않습니다. 이 설정은 cifs 스타일 필수 바이트 범위 잠금을 중단하는 특정 응용 프로그램에 필요합니다. 대부분의 cif 서버는 아직 자문 바이트 범위 잠금을 요청하는 것을 지원하지 않습니다. *nobrl을*사용하지 않는 경우 cifs 스타일 필수 바이트 범위 잠금으로 중단되는 응용 프로그램은 다음과 유사한 오류 메시지를 일으킬 수 있습니다.
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Azure 파일을 사용할 때 오류 "사용 권한을 변경할 수 없습니다"

Azure 파일 플러그인에서 PostgreSQL을 실행할 때 다음과 유사한 오류가 표시될 수 있습니다.

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

이 오류는 cifs/SMB 프로토콜을 사용하는 Azure Files 플러그인으로 인해 발생합니다. cifs/SMB 프로토콜을 사용하는 경우 파일 및 디렉터리 권한을 탑재한 후 변경할 수 없습니다.

이 문제를 해결 하려면 Azure 디스크 플러그인함께 *subPath를* 사용 합니다. 

> [!NOTE] 
> ext3/4 디스크 유형의 경우 디스크 서식이 지정된 후 분실+발견 디렉터리가 있습니다.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure 파일은 많은 작은 파일을 처리할 때 Azure Disk에 비해 대기 시간이 많습니다.

많은 작은 파일을 처리하는 것과 같은 경우에 Azure Disk와 비교할 때 Azure 파일을 사용할 때 대기 시간이 오래 될 수 있습니다.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>저장소 계정에서 "액세스 허용 허용" 설정을 사용하도록 설정했을 때 오류가 발생했습니다.

AKS의 동적 프로비저닝에 사용되는 저장소 계정에서 *선택한 네트워크에서 액세스를 허용하도록* 설정하면 AKS가 파일 공유를 만들 때 오류가 발생합니다.

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

이 오류는 Kubernetes *영구 볼륨 컨트롤러가* *선택한 네트워크에서 액세스를 허용할*때 선택한 네트워크에 있지 않기 때문입니다.

Azure Files 를 사용하여 [정적 프로비저닝을](azure-files-volume.md)사용하여 문제를 완화할 수 있습니다.

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure 파일 Windows 창에서 다시 마운트 하지 못합니다.

Azure Files 마운트가 있는 Windows 포드가 삭제된 다음 동일한 노드에서 다시 만들도록 예약되면 마운트가 실패합니다. 이 오류는 Azure `New-SmbGlobalMapping` Files 마운트가 노드에 이미 탑재되어 있으므로 명령이 실패하기 때문입니다.

예를 들어 다음과 유사한 오류가 표시될 수 있습니다.

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

이 문제는 Kubernetes의 다음 버전에서 해결되었습니다.

| 쿠베르네테스 버전 | 고정 버전 |
| -- | :--: |
| 1.12 | 1.12.6 이상 |
| 1.13 | 1.13.4 이상 |
| 1.14 이상 | 해당 없음 |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>저장소 계정 키변경으로 인해 Azure Files 마운트가 실패합니다.

저장소 계정 키가 변경된 경우 Azure Files에서 오류가 발생할 수 있습니다.

base64 인코딩된 저장소 계정 키를 사용 하 여 Azure 파일 암호에서 *azurestorageaccountkey* 필드를 수동으로 수동으로 업데이트 하 여 문제를 완화할 수 있습니다.

base64에서 저장소 계정 키를 인코딩하려면 `base64`을 사용할 수 있습니다. 예를 들어:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Azure 비밀 파일을 업데이트하려면 `kubectl edit secret`을 사용합니다. 예를 들어:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

몇 분 후 에이전트 노드는 업데이트된 저장소 키를 사용 하 여 Azure 파일 마운트를 다시 시도 합니다.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>클러스터 자동 크기 조정기는 노드 그룹 크기를 수정하는 데 실패한 오류로 확장되지 않습니다.

클러스터 자동 크기 조정기의 크기 조정이 확장/축소되지 않고 [클러스터 자동 크기 조정기 로그에서][view-master-logs]아래와 같은 오류가 표시되는 경우.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

이 오류는 클러스터 자동 크기 조정기가 실제로 클러스터에 있는 값과 다른 값으로 끝나는 업스트림 클러스터 자동 크기 조정기 경합 조건 때문입니다. 이 상태에서 벗어나려면 [클러스터 자동 크기 조정기를][cluster-autoscaler]비활성화하고 다시 활성화하기만 하면 됩니다.

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>느린 디스크 첨부 파일, GetAzureDiskLun 10~15분 소요 되며 오류가 발생합니다.

**1.15.0보다 오래된** Kubernetes 버전에서는 **디스크에 대한 Lun을 찾을 수 없습니다 오류 WaitForAttach**와 같은 오류가 발생할 수 있습니다.  이 문제는 약 15분을 기다렸다가 다시 시도하는 것입니다.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
