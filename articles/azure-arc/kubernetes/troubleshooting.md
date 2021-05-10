---
title: 일반적인 Azure Arc 사용 Kubernetes 문제 해결
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Arc 사용 Kubernetes 클러스터를 통한 일반적인 문제를 해결합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 992ea75c48b2630032e1314610986fbc610eec7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025784"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc 사용 Kubernetes 문제 해결

이 문서에서는 연결, 권한 및 에이전트 문제에 대한 문제 해결 가이드를 제공합니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결

### <a name="azure-cli"></a>Azure CLI

`az connectedk8s` 또는 `az k8s-configuration` CLI 명령을 사용하기 전에 Azure CLI가 올바른 Azure 구독에 대해 작동하도록 설정되어 있는지 확인합니다.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc 에이전트

Azure Arc가 지원되는 Kubernetes의 모든 에이전트는 `azure-arc` 네임스페이스에 pod로 배포됩니다. 모든 Pod가 실행되고 상태 검사를 통과해야 합니다.

먼저, Azure Arc Helm 릴리스를 확인합니다.

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Helm 릴리스가 없거나 누락된 경우 [클러스터를 Azure Arc에 다시 연결](./quickstart-connect-cluster.md)해 보세요.

`STATUS: deployed`에 Helm 릴리스가 있는 경우 `kubectl`을 사용하여 에이전트의 상태를 확인합니다.

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

모든 Pod는 `READY` 열에 `3/3` 또는 `2/2`가 있으며 `STATUS`를 `Running`으로 표시해야 합니다. 로그를 가져오고 `Error` 또는 `CrashLoopBackOff`를 반환하는 Pod를 설명합니다. Pod가 `Pending` 상태에서 중단되면 클러스터 노드에 리소스가 부족할 수 있습니다. [클러스터를 스케일링하면](https://kubernetes.io/docs/tasks/administer-cluster/) 이러한 pod가 `Running` 상태로 전환될 수 있습니다.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Kubernetes 클러스터를 Azure Arc에 연결

클러스터를 Azure에 연결하려면 Azure 구독에 대한 액세스와 대상 클러스터에 대한 `cluster-admin` 액세스가 필요합니다. 클러스터에 연결할 수 없거나 사용 권한이 부족한 경우 클러스터를 Azure Arc에 연결하지 못합니다.

### <a name="insufficient-cluster-permissions"></a>클러스터 권한 부족

제공된 kubeconfig 파일에 Azure Arc 에이전트를 설치할 수 있는 충분한 권한이 없는 경우 Azure CLI 명령은 오류를 반환합니다.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

클러스터를 Azure Arc에 연결하는 사용자는 클러스터에서 `cluster-admin` 역할이 할당되어야 합니다.

### <a name="installation-timeouts"></a>설치 시간 초과

Kubernetes 클러스터를 Azure Arc 지원 Kubernetes에 연결하려면 클러스터에 Azure Arc 에이전트를 설치해야 합니다. 클러스터가 저속 인터넷 연결을 통해 실행되는 경우 에이전트에 대한 컨테이너 이미지 풀링은 Azure CLI 시간 제한보다 오래 걸릴 수 있습니다.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm 문제

Helm `v3.3.0-rc.1` 버전에는 Helm 설치/업그레이드(`connectedk8s` CLI 확장에 사용)로 인해 모든 후크가 실행되고 다음 오류를 야기하는 [이슈](https://github.com/helm/helm/pull/8527)가 있습니다.

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

이 문제에서 복구하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Arc 지원 Kubernetes 리소스를 삭제합니다.
2. 머신에서 다음 명령을 실행합니다.
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. 릴리스 후보 버전 대신, 머신에 Helm 3의 [안정적인 버전을 설치](https://helm.sh/docs/intro/install/)합니다.
4. 적절한 값으로 `az connectedk8s connect` 명령을 실행하여 클러스터를 Azure Arc에 연결합니다.

## <a name="configuration-management"></a>구성 관리

### <a name="general"></a>일반
구성 리소스의 문제를 해결하려면 `--debug` 매개 변수를 지정하여 az 명령을 실행합니다.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>구성 만들기

Azure Arc 지원 Kubernetes 리소스에 대한 쓰기 권한(`Microsoft.Kubernetes/connectedClusters/Write`)은 해당 클러스터에 대한 구성을 만드는 데도 필요하며 충분합니다.

### <a name="configuration-remains-pending"></a>구성을 `Pending`으로 유지

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>모니터링

컨테이너용 Azure Monitor를 사용하려면 DaemonSet가 권한 있는 모드에서 실행되어야 합니다. 모니터링을 위해 Canonical Charmed Kubernetes 클러스터를 설정하려면 다음 명령을 실행합니다.

```console
juju config kubernetes-worker allow-privileged=true
```