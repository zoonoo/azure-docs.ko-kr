---
title: Azure의 Kubernetes 자습서 - Kubernetes 모니터링
description: AKS 자습서 - Azure Log Analytics를 사용하여 Kubernetes 모니터링
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 522109e37a0e8a54848b524697e601b4ea8992d5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-monitor-azure-kubernetes-service-aks"></a>자습서: AKS(Azure Kubernetes Service) 모니터링

Kubernetes 클러스터 및 컨테이너를 모니터링하는 것은 중요하며, 특히 여러 응용 프로그램을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우 그렇습니다.

이 자습서에서는 [Log Analytics용 컨테이너 솔루션][log-analytics-containers]을 사용하여 AKS 클러스터의 모니터링을 구성합니다.

총 8부 중 7부인 이 자습서에서는 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * 컨테이너 모니터링 솔루션 구성
> * 모니터링 에이전트 구성
> * Azure Portal에서 모니터링 정보에 액세스

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이러한 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다.

이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 돌아갑니다.

## <a name="configure-the-monitoring-solution"></a>모니터링 솔루션 구성

Azure Portal에서 **리소스 만들기**를 선택하고 `Container Monitoring Solution`을 검색합니다. 찾은 후에는 **만들기**를 선택합니다.

![솔루션 추가](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

새 Log Analytics 작업 영역을 만들거나 기존 작업 영역을 선택합니다. Log Analytics 작업 영역 양식이 이 프로세스를 안내합니다.

작업 영역을 만들 때 쉽게 검색할 수 있게 **대시보드에 고정**을 선택합니다.

![Log Analytics 작업 영역](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

완료되면 **확인**을 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택하여 컨테이너 모니터링 솔루션을 만듭니다.

작업 영역을 만들면 Azure Portal에 포털에 표시됩니다.

## <a name="get-workspace-settings"></a>작업 영역 설정 가져오기

Log Analytics 작업 영역 ID 및 키는 Kubernetes 노드에서 솔루션 에이전트를 구성하는 데 필요합니다.

이러한 값을 검색하려면 컨테이너 솔루션 왼쪽 메뉴에서 **OMS 작업 영역**을 선택합니다. **고급 설정**을 선택하고 **작업 영역 ID** 및 **기본 키**를 적어둡니다.

## <a name="create-kubernetes-secret"></a>Kubernetes 비밀 만들기

[kubectl create secret][kubectl-create-secret] 명령을 사용하여 `omsagent-secret`이라는 Kubernetes 비밀에 Log Analytics 작업 영역 설정을 저장합니다. Log Analytics 작업 영역 ID를 사용하여 `WORKSPACE_ID` 및 작업 영역 키를 사용하여 `WORKSPACE_KEY`를 업데이트합니다.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="configure-monitoring-agents"></a>모니터링 에이전트 구성

다음 Kubernetes 매니페스트 파일을 사용하여 Kubernetes 클러스터에서 컨테이너 모니터링 에이전트를 구성할 수 있습니다. 그러면 각 클러스터 노드에서 단일 Pod를 실행하는 Kubernetes [DaemonSet][kubernetes-daemonset]이 만들어집니다.

`oms-daemonset.yaml`이라는 파일에 다음 텍스트를 저장합니다.

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
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
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
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
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

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

에이전트가 실행된 후 Log Analytics에서 데이터를 수집하고 처리하는 데 몇 분 정도 걸립니다.

## <a name="access-monitoring-data"></a>모니터링 데이터 액세스

Azure Portal에서 포털 대시보드에 고정된 Log Analytics 작업 영역을 선택합니다. **컨테이너 모니터링 솔루션** 타일을 클릭합니다. 여기서 AKS 클러스터 및 클러스터의 컨테이너에 대한 정보를 찾을 수 있습니다.

![대시보드](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

모니터링 데이터 쿼리 및 분석에 대한 자세한 지침은 [Azure Log Analytics 설명서][log-analytics-docs]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Log Analytics를 사용하여 Kubernetes 클러스터를 모니터링했습니다. 설명한 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 컨테이너 모니터링 솔루션 구성
> * 모니터링 에이전트 구성
> * Azure Portal에서 모니터링 정보에 액세스

다음 자습서로 이동하여 Kubernetes를 새 버전으로 업그레이드 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes 업그레이드][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubectl-create-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
