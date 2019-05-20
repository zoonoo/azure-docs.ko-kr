---
title: 컨테이너용 Azure Monitor 문제 해결 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor의 문제를 해결하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 2e3e39ef24d82393d981c0ce276b3338419e0b2d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521764"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>컨테이너용 Azure Monitor 문제 해결

컨테이너용 Azure Monitor로 Azure Kubernetes Service(AKS) 클러스터 모니터링을 구성할 때 데이터 컬렉션 또는 상태 보고를 방지하는 문제가 발생할 수 있습니다. 이 문서에서는 몇 가지 일반적인 문제 및 문제 해결 단계를 자세히 설명합니다.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>온 보 딩 또는 업데이트 작업 동안 권한 부여 오류가 발생 했습니다.
컨테이너에 대 한 Azure Monitor를 사용 하도록 설정 또는 메트릭을 수집을 지원 하도록 클러스터를 업데이트 하는 동안 오류가 나타날 수 있습니다 다음 모양의 *< 사용자의 Id > 클라이언트 ' id '< 사용자의 objectId >' 개체를 사용 하 여 되지 않은 범위 'Microsoft.Authorization/roleAssignments/write' 작업을 수행 하려면 권한 부여*

온 보 딩 또는 업데이트 프로세스 중 부여 합니다 **모니터링 메트릭을 게시자** 클러스터 리소스에 역할 할당을 시도 합니다. 메트릭 컬렉션을 지원 하기 위해 컨테이너 또는 업데이트에 대 한 Azure Monitor를 사용 하도록 설정 하는 프로세스를 시작 하는 사용자에 액세스할 수 있어야 합니다 **Microsoft.Authorization/roleAssignments/write** AKS 클러스터에 대 한 권한 리소스 범위입니다. 멤버만 합니다 **소유자** 하 고 **사용자 액세스 관리자** 기본 제공 역할에는이 사용 권한에 대 한 액세스 권한이 부여 됩니다. 보안 정책에 필요한 세부 수준 사용 권한을 할당 하는 경우 확인 하는 것이 좋습니다 [사용자 지정 역할](../../role-based-access-control/custom-roles.md) 필요한 사용자에 게 할당 합니다. 

수동으로 Azure portal에서 다음 단계를 수행 하 여이 역할을 부여할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 입력 **Kubernetes**합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 선택 **Azure Kubernetes**합니다.
3. Kubernetes 클러스터의 목록에서 목록에서 하나를 선택 합니다.
2. 왼쪽 메뉴에서 클릭 **액세스 제어 (IAM)** 합니다.
3. 선택 **+ 추가** 역할 할당을 추가 하 고 선택 하는 **모니터링 메트릭을 게시자** 역할 아래에서 **선택** 상자에 입력 **AKS** 를 클러스터만 기준으로 결과 서비스 주체는 구독에 정의 된 필터입니다. 클러스터에 관련 된 목록에서 선택 합니다.
4. **저장**을 선택하여 역할 할당을 완료합니다. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>컨테이너용 Azure Monitor가 사용되지만 정보를 보고하지 않습니다.
컨테이너에 대 한 Azure Monitor가 성공적으로 사용 되 고 구성 하지만 상태 정보를 확인할 수 없습니다 또는 결과 없음 로그 쿼리에서 반환 하는 경우에 다음이 단계를 수행 하 여 문제를 진단 합니다. 

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

5. 에이전트가 성공적으로 배포 되었는지 확인 하려면 명령을 실행 합니다. `kubectl logs omsagent-484hw --namespace=kube-system`

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

| 오류 메시지  | 액션(Action) |  
| ---- | --- |  
| 오류 메시지: `No data for selected filters`  | 새로 만든 클러스터에 대한 모니터링 데이터 흐름을 설정하는 데는 약간의 시간이 걸릴 수 있습니다. 클러스터에 대해 표시할 데이터에 대 한 10 ~ 15 분 이상를 허용 합니다. |   
| 오류 메시지: `Error retrieving data` | Azure Kubenetes 서비스 클러스터에서 상태 및 성능 모니터링을 설정하는 동안 클러스터와 Azure Log Analytics 작업 영역 간에 연결이 설정됩니다. Log Analytics 작업 영역은 클러스터에 대한 모든 모니터링 데이터를 저장하는 데 사용됩니다. Log Analytics 작업 영역이 삭제되거나 손실되었으면 이 오류가 발생할 수 있습니다. [액세스 관리](../platform/manage-access.md#view-workspace-details)를 검토하여 작업 영역을 사용할 수 있는지 확인합니다. 작업 영역이 없는 경우에 다시 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터의 모니터링을 사용 하도록 설정 해야 합니다. 를 다시 사용 해야 합니다 [사용 안 함](container-insights-optout.md) 클러스터에 대 한 모니터링 및 [사용](container-insights-enable-new-cluster.md) 다시 컨테이너에 대 한 Azure Monitor 합니다. |  
| 오류 메시지: `Error retrieving data`(az aks cli를 통해 컨테이너용 Azure Monitor를 추가한 후) | 사용 하 여 모니터링을 사용 하면 `az aks cli`, 컨테이너에 대 한 Azure Monitor에서는 제대로 배포 되지 않습니다. 솔루션 배포 되는지 여부를 확인 합니다. 이렇게 하려면 Log Analytics 작업 영역으로 이동하여 왼쪽 창에서 **솔루션**을 선택하여 해당 솔루션을 사용할 수 있는지 확인합니다. 이 문제를 해결하려면 [컨테이너용 Azure Monitor를 배포하는 방법](container-insights-onboard.md)의 지침에 따라 솔루션을 다시 배포해야 합니다. |  

문제 진단을 지원하기 위해 [여기](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)서 사용할 수 있는 문제 해결 스크립트를 제공했습니다.  

## <a name="next-steps"></a>다음 단계
모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너용 Azure Monitor 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.