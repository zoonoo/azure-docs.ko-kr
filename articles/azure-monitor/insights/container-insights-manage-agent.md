---
title: 컨테이너용 Azure Monitor 에이전트를 관리하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor에서 사용되는 컨테이너화된 Log Analytics 에이전트를 통해 가장 일반적인 유지 관리 작업을 관리하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: b656b0cc89e40dd732def4ebf56dceae69a033b0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618440"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>컨테이너용 Azure Monitor 에이전트를 관리하는 방법

컨테이너용 Azure Monitor는 Log Analytics 에이전트가 Linux용으로 컨테이너화된 버전을 사용합니다. 초기 배치 후에 수명 주기 동안 수행해야 하는 루틴 또는 선택적 작업이 있습니다. 이 문서에서는 에이전트를 수동으로 업그레이드하고 특정 컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하는 방법을 자세히 설명합니다. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>컨테이너용 Azure Monitor 에이전트를 업그레이드하는 방법

컨테이너용 Azure Monitor는 Log Analytics 에이전트가 Linux용으로 컨테이너화된 버전을 사용합니다. 새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service) 및 Azure Red Hat OpenShift 버전 3.x에 호스트된 관리되는 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다. [하이브리드 Kubernetes 클러스터](container-insights-hybrid-setup.md) 및 Azure Red Hat OpenShift 버전 4.x의 경우 에이전트가 관리되지 않으며 에이전트를 수동으로 업그레이드해야 합니다.

AKS 또는 Azure Red Hat OpenShift 버전 3.x에 호스트되는 클러스터에서 에이전트 업그레이드가 실패하는 경우 이 문서에서는 에이전트를 수동으로 업그레이드하는 프로세스도 설명합니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.

### <a name="upgrade-agent-on-aks-cluster"></a>AKS 클러스터의 에이전트 업그레이드

AKS 클러스터에서 에이전트를 업그레이드하는 프로세스는 간단한 2개 단계로 이루어집니다. 첫 번째 단계는 Azure CLI를 사용하여 컨테이너용 Azure Monitor를 사용한 모니터링을 비활성화하는 것입니다. [모니터링 비활성화](container-insights-optout.md?#azure-cli) 문서에서 설명하는 단계를 수행하세요. Azure CLI를 사용하면 작업 영역에 저장된 솔루션과 그 데이터에 영향을 주지 않으면서 클러스터에 포함된 노드에서 에이전트를 제거할 수 있습니다. 

>[!NOTE]
>사용자가 이 유지 관리 활동을 수행하는 동안 클러스터에 포함된 노드는 수집된 데이터를 전달하지 않고, 사용자가 에이전트를 제거한 시점과 새로운 버전을 설치한 시점 사이의 데이터가 성능 보기에 표시되지 않습니다. 
>

새로운 버전의 에이전트를 설치하려면 [Azure CLI를 사용하여 모니터링 사용](container-insights-enable-new-cluster.md#enable-using-azure-cli)에 설명된 단계에 따라 이 프로세스를 완료합니다.  

모니터링을 다시 사용하도록 설정한 후에 해당 클러스터의 업데이트된 상태 메트릭이 나타나기까지는 15분 정도 소요됩니다. 에이전트가 성공적으로 업그레이드 되었는지 확인 하려면 다음 중 하나를 수행 합니다.

* 명령을 실행 `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` 합니다. 반환 된 상태에서 출력의 *컨테이너* 섹션에서 Omsagent의 **이미지** 아래에 있는 값을 확인 합니다.
* **노드** 탭에서 클러스터 노드를 선택 하 고 오른쪽의 **속성** 창에서 **에이전트 이미지 태그**아래의 값을 확인 합니다.

표시 된 에이전트 버전은 [릴리스 기록](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) 페이지에 나열 된 최신 버전과 일치 해야 합니다.

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>하이브리드 Kubernetes 클러스터에서 에이전트 업그레이드

다음 단계를 수행하여 실행 중인 Kubernetes 클러스터에서 에이전트를 업그레이드합니다.

* AKS 엔진을 사용하여 Azure에서 호스트되는 자체 관리되는 Kubernetes 클러스터.
* AKS 엔진을 사용하여 Azure Stack 또는 온-프레미스에서 호스트되는 자체 관리되는 Kubernetes 클러스터.
* Red Hat OpenShift 버전 4.x.

Log Analytics 작업 영역이 상업용 Azure에 있는 경우 다음 명령을 실행합니다.

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Log Analytics 작업 영역이 Azure 중국 21Vianet에 있는 경우 다음 명령을 실행 합니다.

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Log Analytics 작업 영역이 Azure US Government에 있는 경우 다음 명령을 실행합니다.

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Azure Red Hat OpenShift v4에서 에이전트 업그레이드

다음 단계를 수행하여 Azure Red Hat OpenShift 버전 4.x에서 실행 중인 Kubernetes 클러스터에서 에이전트를 업그레이드합니다. 

>[!NOTE]
>Azure Red Hat OpenShift 버전 4.x는 Azure 상업용 클라우드에서만 실행할 수 있습니다.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

이 명령을 사용 하 여 서비스 주체를 사용 하는 방법에 대 한 자세한 내용은 [Azure Arc 사용 Kubernetes 클러스터 모니터링 사용](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) 에서 **서비스 주체 사용** 을 참조 하세요.

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Azure Arc의 업그레이드 에이전트 사용 Kubernetes

다음 명령을 수행 하 여 Azure Arc enabled Kubernetes 클러스터에서 에이전트를 업그레이드 합니다.

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $azureArcClusterResourceId
```

이 명령을 사용 하 여 서비스 주체를 사용 하는 방법에 대 한 자세한 내용은 [Azure Arc 사용 Kubernetes 클러스터 모니터링 사용](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) 에서 **서비스 주체 사용** 을 참조 하세요.


## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하는 방법

컨테이너용 Azure Monitor는 pod에서 실행 중인 컨테이너에서 환경 변수를 수집하고 **컨테이너** 보기에서 선택한 컨테이너의 속성 창에 표시합니다. Kubernetes 클러스터를 배포하는 동안 또는 이후에 환경 변수 *AZMON_COLLECT_ENV*를 설정하여 특정 컨테이너에 대한 수집을 사용하지 않도록 설정하면 이 동작을 제어할 수 있습니다. 이 기능은 에이전트 버전 ciprod11292018 이상에서 사용할 수 있습니다.  

새 컨테이너 또는 기존 컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하려면 Kubernetes 배포 yaml 구성 파일에서 **AZMON_COLLECT_ENV** 변수를 **False** 값으로 설정합니다. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

다음 명령을 실행하여 Azure Red Hat OpenShift 이외의 Kubernetes 클러스터에 변경 내용을 적용합니다. `kubectl apply -f  <path to yaml file>`. ConfigMap을 편집하고 Azure Red Hat OpenShift 클러스터에 이 변경 내용을 적용하려면 다음 명령을 실행합니다.

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

이렇게 하면 기본 텍스트 편집기가 열립니다. 변수를 설정한 후 편집기에서 파일을 저장합니다.

구성 변경 내용이 적용되었는지 확인하려면 컨테이너용 Azure Monitor의 **컨테이너** 보기에서 컨테이너를 선택하고, 속성 창에서 **환경 변수**를 확장합니다.  이 섹션에는 이전에 만든 변수인 **AZMON_COLLECT_ENV=FALSE**만 표시되어야 합니다. 다른 모든 컨테이너의 경우 환경 변수 섹션에 검색된 모든 환경 변수가 나열되어야 합니다.

환경 변수 검색을 다시 사용하도록 설정하려면 이전과 동일한 프로세스를 적용하고 값을 **False**에서 **True**로 변경한 다음, `kubectl` 명령을 다시 실행하여 컨테이너를 업데이트합니다.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>다음 단계

에이전트를 업그레이드하는 동안 문제가 발생하면 [문제 해결 가이드](container-insights-troubleshoot.md)를 검토하세요.
