---
title: 컨테이너용 Azure Monitor 에이전트를 관리하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor에서 사용되는 컨테이너화된 Log Analytics 에이전트를 통해 가장 일반적인 유지 관리 작업을 관리하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275322"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>컨테이너용 Azure Monitor 에이전트를 관리하는 방법

컨테이너용 Azure Monitor는 Log Analytics 에이전트가 Linux용으로 컨테이너화된 버전을 사용합니다. 초기 배치 후에 수명 주기 동안 수행해야 하는 루틴 또는 선택적 작업이 있습니다. 이 문서에서는 에이전트를 수동으로 업그레이드하고 특정 컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하는 방법을 자세히 설명합니다. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>컨테이너용 Azure Monitor 에이전트를 업그레이드하는 방법

컨테이너용 Azure Monitor는 Log Analytics 에이전트가 Linux용으로 컨테이너화된 버전을 사용합니다. 새 버전의 에이전트가 릴리스되면 에이전트가 AKS (Azure Kubernetes Service)에서 호스트 되는 관리 되는 Kubernetes 클러스터 및 Azure Red Hat OpenShift에서 자동으로 업그레이드 됩니다. [하이브리드 Kubernetes 클러스터](container-insights-hybrid-setup.md) 의 경우 에이전트가 관리 되지 않으므로 에이전트를 수동으로 업그레이드 해야 합니다.

AKS에서 호스트 되는 클러스터에 대해 에이전트 업그레이드에 실패 하는 경우이 문서에서는 에이전트를 수동으로 업그레이드 하는 프로세스에 대해서도 설명 합니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>모니터링 되는 Kubernetes 클러스터의 업그레이드 에이전트

Azure Red Hat OpenShift를 제외 하 고 클러스터에서 에이전트를 업그레이드 하는 프로세스는 두 개의 바로 앞 단계로 구성 됩니다. 첫 번째 단계는 Azure CLI를 사용하여 컨테이너용 Azure Monitor를 사용한 모니터링을 비활성화하는 것입니다. [모니터링 비활성화](container-insights-optout.md?#azure-cli) 문서에서 설명하는 단계를 수행하세요. Azure CLI를 사용하면 작업 영역에 저장된 솔루션과 그 데이터에 영향을 주지 않으면서 클러스터에 포함된 노드에서 에이전트를 제거할 수 있습니다. 

>[!NOTE]
>사용자가 이 유지 관리 활동을 수행하는 동안 클러스터에 포함된 노드는 수집된 데이터를 전달하지 않고, 사용자가 에이전트를 제거한 시점과 새로운 버전을 설치한 시점 사이의 데이터가 성능 보기에 표시되지 않습니다. 
>

새 버전의 에이전트를 설치 하려면 [Azure CLI을 사용 하 여 모니터링 사용](container-insights-enable-new-cluster.md#enable-using-azure-cli)에서 설명한 단계를 수행 하 여이 프로세스를 완료 합니다.  

모니터링을 다시 사용 하도록 설정한 후 클러스터에 대 한 업데이트 된 상태 메트릭을 보려면 약 15 분 정도 걸릴 수 있습니다. 에이전트가 성공적으로 업그레이드되었는지 확인하려면 `kubectl logs omsagent-484hw --namespace=kube-system` 명령을 실행합니다.

상태는 다음 예제와 유사하며, 여기서 *omi* 및 *omsagent*의 값은 [에이전트 릴리스 기록](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)에 지정된 최신 버전과 일치해야 합니다.  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>하이브리드 Kubernetes 클러스터에서 에이전트 업그레이드

온-프레미스에서 호스트 되는 Kubernetes 클러스터에서 에이전트를 업그레이드 하는 프로세스는 다음 명령을 실행 하 여 Azure의 AKS 엔진과 Azure Stack 완료할 수 있습니다.

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Log Analytics 작업 영역이 Azure 중국에 있는 경우 다음 명령을 실행 합니다.

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Log Analytics 작업 영역이 Azure 미국 정부에 있는 경우 다음 명령을 실행 합니다.

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하는 방법

컨테이너용 Azure Monitor는 pod에서 실행 중인 컨테이너에서 환경 변수를 수집하고 **컨테이너** 보기에서 선택한 컨테이너의 속성 창에 표시합니다. Kubernetes 클러스터를 배포 하는 동안 또는 *AZMON_COLLECT_ENV*환경 변수를 설정 하 여 특정 컨테이너에 대해 컬렉션을 사용 하지 않도록 설정 하 여이 동작을 제어할 수 있습니다. 이 기능은 에이전트 버전 ciprod11292018 이상에서 사용할 수 있습니다.  

새 컨테이너 또는 기존 컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하려면 Kubernetes 배포 yaml 구성 파일에서 **AZMON_COLLECT_ENV** 변수를 **False** 값으로 설정합니다. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

다음 명령을 실행 하 여 Azure Red Hat OpenShift 이외의 Kubernetes 클러스터에 변경 내용을 적용 합니다.: `kubectl apply -f  <path to yaml file>`. ConfigMap을 편집 하 고 Azure Red Hat OpenShift 클러스터에이 변경 내용을 적용 하려면 명령을 실행 합니다.

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

그러면 기본 텍스트 편집기가 열립니다. 변수를 설정한 후 편집기에서 파일을 저장 합니다.

구성 변경 내용이 적용되었는지 확인하려면 컨테이너용 Azure Monitor의 **컨테이너** 보기에서 컨테이너를 선택하고, 속성 창에서 **환경 변수**를 확장합니다.  이 섹션에는 이전에 만든 변수인 **AZMON_COLLECT_ENV=FALSE**만 표시되어야 합니다. 다른 모든 컨테이너의 경우 환경 변수 섹션에 검색된 모든 환경 변수가 나열되어야 합니다.

환경 변수 검색을 다시 사용하도록 설정하려면 이전과 동일한 프로세스를 적용하고 값을 **False**에서 **True**로 변경한 다음, `kubectl` 명령을 다시 실행하여 컨테이너를 업데이트합니다.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>다음 단계

에이전트를 업그레이드하는 동안 문제가 발생하면 [문제 해결 가이드](container-insights-troubleshoot.md)를 검토하세요.
