---
title: 컨테이너용 Azure Monitor(미리 보기) 에이전트를 업그레이드하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Monitor가 컨테이너용으로 사용하는 Log Analytics 에이전트를 업그레이드하는 방법을 설명합니다.
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
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 08206b9de4fca3b422c5b076d7e0c1c180f82fbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184089"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>컨테이너용 Azure Monitor(미리 보기) 에이전트를 업그레이드하는 방법
컨테이너용 Azure Monitor는 Log Analytics 에이전트가 Linux용으로 컨테이너화된 버전을 사용합니다. 새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다.  

에이전트 업그레이드에 실패할 경우 이 문서에서 에이전트를 수동으로 업그레이드하는 프로세스에 대해 설명합니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>모니터링되는 Kubernetes 클러스터에서 에이전트 업그레이드
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

## <a name="next-steps"></a>다음 단계
에이전트를 업그레이드하는 동안 문제가 발생하면 [문제 해결 가이드](container-insights-troubleshoot.md)를 검토하세요.