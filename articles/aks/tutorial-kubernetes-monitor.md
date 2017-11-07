---
title: "Azure의 Kubernertes 자습서 - Kubernetes 모니터링 | Microsoft Docs"
description: "AKS 자습서 - Microsoft OMS(Operations Management Suite)를 사용하여 Kubernetes 모니터링"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 199779d47b6b13ac1d426e64364b4b24fe5db3d5
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-azure-container-service-aks"></a>AKS(Azure Container Service) 모니터링

Kubernetes 클러스터 및 컨테이너를 모니터링하는 것은 중요하며, 특히 여러 앱을 사용하여 대규모의 프로덕션 클러스터를 관리하는 경우 그렇습니다. 

Microsoft 또는 다른 공급자가 제공하는 여러 Kubernetes 모니터링 솔루션을 활용할 수 있습니다. 이 자습서에서는 Microsoft의 클라우드 기반 IT 관리 솔루션인 [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md)의 컨테이너 솔루션을 사용하여 Kubernetes 클러스터를 모니터링합니다. (OMS 컨테이너 솔루션은 미리 보기 상태입니다.)

총 8부 중 7부인 이 자습서에서는 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * OMS 작업 영역 설정 가져오기
> * Kubernetes 노드에서 OMS 에이전트 설정
> * OMS 포털 또는 Azure Portal에서 모니터링 정보에 액세스

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이러한 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다. 

이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

## <a name="get-workspace-settings"></a>작업 영역 설정 가져오기

[OMS 포털](https://mms.microsoft.com)에 액세스할 수 있는 경우 **설정** > **연결된 원본** > **Linux 서버**로 이동합니다. 거기서 *작업 영역 ID* 및 기본 또는 보조 *작업 영역 키*를 찾을 수 있습니다. 클러스터에서 OMS 에이전트를 설정하는 데 필요한 이러한 값을 기록해 둡니다.

## <a name="set-up-oms-agents"></a>OMS 에이전트 설정

다음은 Linux 클러스터 노드에서 OMS 에이전트를 설정하기 위한 YAML 파일입니다. 이 파일은 Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)를 만들며, 이는 각 클러스터 노드에서 하나의 같은 Pod를 실행합니다. DaemonSet 리소스는 모니터링 에이전트 배포에 적합합니다. 

다음 텍스트를 `oms-daemonset.yaml`이라는 파일에 저장하고, *myWorkspaceID* 및 *myWorkspaceKey*의 자리 표시자 값을 해당 OMS 작업 영역 ID 및 키로 바꿉니다. (프로덕션에서 이러한 값을 비밀로 인코딩할 수 있습니다.)

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

다음 명령을 사용하여 DaemonSet를 만듭니다.

```azurecli
kubectl create -f oms-daemonset.yaml
```

DaemonSet가 만들어졌는지 확인하려면 다음을 실행합니다.

```azurecli
kubectl get daemonset
```

다음과 유사하게 출력됩니다.

```azurecli
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

에이전트가 실행된 후 OMS에서 데이터를 수집하고 처리하는 데 몇 분 정도 걸립니다.

## <a name="access-monitoring-data"></a>모니터링 데이터 액세스

OMS 포털 또는 Azure Portal의 [컨테이너 솔루션](../log-analytics/log-analytics-containers.md)을 통해 OMS 컨테이너 모니터링 데이터를 보고 분석합니다. 

[OMS 포털](https://mms.microsoft.com)을 사용하여 컨테이너 솔루션을 설치하려면 **솔루션 갤러리**로 이동합니다. 그런 다음 **컨테이너 솔루션**을 추가합니다. 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview)에서 컨테이너 솔루션을 추가합니다.

OMS 포털의 OMS 대시보드에서 **컨테이너** 요약 타일을 찾습니다. 타일을 클릭하면 컨테이너 이벤트, 오류, 상태, 이미지 인벤토리, CPU 및 메모리 사용량과 같은 세부 정보를 볼 수 있습니다. 더 세부적인 정보를 보려면 아무 타일에서 행을 클릭하거나 [로그 검색](../log-analytics/log-analytics-log-searches.md)을 수행합니다.

![OMS 포털의 컨테이너 대시보드](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

마찬가지로 Azure Portal에서 **Log Analytics**로 이동하고 작업 영역 이름을 선택합니다. **컨테이너** 요약 타일을 보려면 **솔루션** > **컨테이너**를 클릭합니다. 세부 정보를 보려면 타일을 클릭합니다.

모니터링 데이터 쿼리 및 분석에 대한 자세한 지침은 [Azure Log Analytics 설명서](../log-analytics/index.yml)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 OMS를 사용하여 Kubernetes 클러스터를 모니터링했습니다. 설명한 작업은 다음과 같습니다.

> [!div class="checklist"]
> * OMS 작업 영역 설정 가져오기
> * Kubernetes 노드에서 OMS 에이전트 설정
> * OMS 포털 또는 Azure Portal에서 모니터링 정보에 액세스


다음 자습서로 이동하여 Kubernetes를 새 버전으로 업그레이드 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes 업그레이드](./tutorial-kubernetes-upgrade-cluster.md)
