---
title: 컨테이너용 Azure Monitor 에이전트를 관리하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor에서 사용되는 컨테이너화된 Log Analytics 에이전트를 통해 가장 일반적인 유지 관리 작업을 관리하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 6bfcb17d0a81c6e23c4bfa3dd4f4dd8cc3079379
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331473"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>컨테이너용 Azure Monitor 에이전트를 관리하는 방법
컨테이너용 Azure Monitor는 Log Analytics 에이전트가 Linux용으로 컨테이너화된 버전을 사용합니다. 초기 배치 후에 수명 주기 동안 수행해야 하는 루틴 또는 선택적 작업이 있습니다. 이 문서에서는 에이전트를 수동으로 업그레이드하고 특정 컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하는 방법을 자세히 설명합니다. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>컨테이너용 Azure Monitor 에이전트를 업그레이드하는 방법
컨테이너용 Azure Monitor는 Log Analytics 에이전트가 Linux용으로 컨테이너화된 버전을 사용합니다. 새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다.  

에이전트 업그레이드에 실패할 경우 이 문서에서 에이전트를 수동으로 업그레이드하는 프로세스에 대해 설명합니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>모니터링되는 Kubernetes 클러스터에서 에이전트 업그레이드
에이전트를 업그레이드하는 과정은 간단한 2가지 단계로 이루어집니다. 첫 번째 단계는 Azure CLI를 사용하여 컨테이너용 Azure Monitor를 사용한 모니터링을 비활성화하는 것입니다.  [모니터링 비활성화](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) 문서에서 설명하는 단계를 수행하세요. Azure CLI를 사용하면 작업 영역에 저장된 솔루션과 그 데이터에 영향을 주지 않으면서 클러스터에 포함된 노드에서 에이전트를 제거할 수 있습니다. 

>[!NOTE]
>사용자가 이 유지 관리 활동을 수행하는 동안 클러스터에 포함된 노드는 수집된 데이터를 전달하지 않고, 사용자가 에이전트를 제거한 시점과 새로운 버전을 설치한 시점 사이의 데이터가 성능 보기에 표시되지 않습니다. 
>

새로운 버전의 에이전트를 설치하려면 Azure CLI를 사용하여 [모니터링 온보딩](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) 문서에서 안내하는 단계를 수행하세요.  

모니터링을 다시 사용하도록 설정한 후에 해당 클러스터의 업데이트된 상태 메트릭이 나타나기까지는 약 15분 정도 소요됩니다. 에이전트가 성공적으로 업그레이드되었는지 확인하려면 `kubectl logs omsagent-484hw --namespace=kube-system` 명령을 실행합니다.

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하는 방법
컨테이너용 Azure Monitor는 pod에서 실행 중인 컨테이너에서 환경 변수를 수집하고 **컨테이너** 보기에서 선택한 컨테이너의 속성 창에 표시합니다. AKS 클러스터를 배포하는 동안 또는 이후에 환경 변수 *AZMON_COLLECT_ENV*를 설정하여 특정 컨테이너에 대한 수집을 사용하지 않도록 설정하면 이 동작을 제어할 수 있습니다. 이 기능은 에이전트 버전 ciprod11292018 이상에서 사용할 수 있습니다.  

새 컨테이너 또는 기존 컨테이너에서 환경 변수 수집을 사용하지 않도록 설정하려면 Kubernetes 배포 yaml 구성 파일에서 **AZMON_COLLECT_ENV** 변수를 **False** 값으로 설정합니다.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

`kubectl apply -f  <path to yaml file>` 명령을 실행하여 AKS 컨테이너에 변경 내용을 적용합니다.

구성 변경 내용이 적용되었는지 확인하려면 컨테이너용 Azure Monitor의 **컨테이너** 보기에서 컨테이너를 선택하고, 속성 창에서 **환경 변수**를 확장합니다.  이 섹션에는 이전에 만든 변수인 **AZMON_COLLECT_ENV=FALSE**만 표시되어야 합니다. 다른 모든 컨테이너의 경우 환경 변수 섹션에 검색된 모든 환경 변수가 나열되어야 합니다.   

환경 변수 검색을 다시 사용하도록 설정하려면 이전과 동일한 프로세스를 적용하고 값을 **False**에서 **True**로 변경한 다음, `kubectl` 명령을 다시 실행하여 컨테이너를 업데이트합니다.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>다음 단계
에이전트를 업그레이드하는 동안 문제가 발생하면 [문제 해결 가이드](container-insights-troubleshoot.md)를 검토하세요.