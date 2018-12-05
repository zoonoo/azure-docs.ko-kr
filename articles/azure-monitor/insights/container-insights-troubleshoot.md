---
title: 컨테이너용 Azure Monitor 문제 해결 방법(미리 보기) | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor의 문제를 해결하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: magoedte
ms.openlocfilehash: da35f88550bb7bd1a4543f03936dcd52ddd926d9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308362"
---
# <a name="troubleshooting-azure-monitor-for-containers-preview"></a>컨테이너용 Azure Monitor 문제 해결(미리 보기)

컨테이너용 Azure Monitor로 Azure Kubernetes Service(AKS) 클러스터 모니터링을 구성할 때 데이터 컬렉션 또는 상태 보고를 방지하는 문제가 발생할 수 있습니다. 이 문서에서는 몇 가지 일반적인 문제 및 문제 해결 단계를 자세히 설명합니다.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>컨테이너용 Azure Monitor가 사용되지만 정보를 보고하지 않습니다.
컨테이너용 Azure Monitor가 성공적으로 사용되고 구성되어 있지만 상태 정보를 볼 수 없거나 Log Analytics 로그 쿼리에서 결과가 반환되지 않는 경우 다음 단계에 따라 문제를 진단합니다. 

1. 다음 명령을 실행하여 에이전트의 상태를 확인합니다. 

    `kubectl get ds omsagent --namespace=kube-system`

    출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 다음 명령을 사용하여 에이전트 버전 *06072018* 이상의 배포 상태를 확인합니다.

    `kubectl get deployment omsagent-rs -n=kube-system`

    출력은 다음 예제와 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Pod의 상태를 확인하여 `kubectl get pods --namespace=kube-system` 명령을 통해 실행 중인지 확인합니다.

    출력은 다음 예와 비슷하며 omsagent의 상태는 *Running*입니다.

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 에이전트 로그를 확인합니다. 컨테이너화된 에이전트가 배포되면 OMI 명령을 실행하여 빠른 검사가 실행되고 에이전트 및 공급자의 버전이 표시됩니다. 

5. 에이전트가 성공적으로 온보드되었는지 확인하려면 다음 명령을 실행합니다. `kubectl logs omsagent-484hw --namespace=kube-system`

    상태는 다음 예제와 유사합니다.

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>오류 메시지

아래 표에서는 Azure Monitor를 컨테이너에 사용할 때 발생할 수 있는 알려진 오류를 요약하고 있습니다.

| 오류 메시지  | 조치 |  
| ---- | --- |  
| 오류 메시지: `No data for selected filters`  | 새로 만든 클러스터에 대한 모니터링 데이터 흐름을 설정하는 데는 약간의 시간이 걸릴 수 있습니다. 클러스터에 대한 데이터가 표시될 때까지 10-15분 이상 기다려주세요. |   
| 오류 메시지: `Error retrieving data` | Azure Kubenetes 서비스 클러스터에서 상태 및 성능 모니터링을 설정하는 동안 클러스터와 Azure Log Analytics 작업 영역 간에 연결이 설정됩니다. Log Analytics 작업 영역은 클러스터에 대한 모든 모니터링 데이터를 저장하는 데 사용됩니다. Log Analytics 작업 영역이 삭제되거나 손실되었으면 이 오류가 발생할 수 있습니다. [액세스 관리](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information)를 검토하여 작업 영역을 사용할 수 있는지 확인합니다. 작업 영역이 없는 경우 컨테이너용 Azure Monitor를 사용하여 클러스터를 다시 온보딩해야 합니다. 다시 온보딩하려면 클러스터에 대한 모니터링을 [비활성화](container-insights-optout.md)하고 컨테이너용 Azure Monitor를 다시 [활성화](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster)해야 합니다. |  
| 오류 메시지: `Error retrieving data`(az aks cli를 통해 컨테이너용 Azure Monitor를 추가한 후) | `az aks cli`를 사용하여 온보딩하는 경우 드문 경우이지만 컨테이너용 Azure Monitor가 제대로 온보딩되지 않을 수 있습니다. 솔루션이 온보딩되었는지 확인합니다. 이렇게 하려면 Log Analytics 작업 영역으로 이동하여 왼쪽 창에서 **솔루션**을 선택하여 해당 솔루션을 사용할 수 있는지 확인합니다. 이 문제를 해결하려면 [컨테이너용 Azure Monitor를 배포하는 방법](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json)의 지침에 따라 솔루션을 다시 배포해야 합니다. |  

문제 진단을 지원하기 위해 [여기](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)서 사용할 수 있는 문제 해결 스크립트를 제공했습니다.  

## <a name="next-steps"></a>다음 단계
모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너용 Azure Monitor 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.