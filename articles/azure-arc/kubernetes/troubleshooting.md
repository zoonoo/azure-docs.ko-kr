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
ms.openlocfilehash: e1f4e84f16c6b584f1ffbd918a86c251f47efcca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654003"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc 사용 Kubernetes 문제 해결

이 문서에서는 연결, 권한 및 에이전트와 관련 된 문제에 대 한 문제 해결 가이드를 제공 합니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결

### <a name="azure-cli"></a>Azure CLI

`az connectedk8s`또는 CLI 명령을 사용 하기 전에 `az k8s-configuration` Azure CLI가 올바른 Azure 구독에 대해 작동 하도록 설정 되었는지 확인 합니다.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc 에이전트

Azure Arc가 지원되는 Kubernetes의 모든 에이전트는 `azure-arc` 네임스페이스에 pod로 배포됩니다. 모든 pod은 실행 중 이며 상태 검사를 통과 해야 합니다.

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

투구 릴리스가 없거나 누락 된 경우 클러스터를 다시 [Azure Arc에 연결](./connect-cluster.md) 해 보세요.

에서 투구 릴리스가 있는 경우 `STATUS: deployed` 다음을 사용 하 여 에이전트의 상태를 확인 합니다 `kubectl` .

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

모든 pod는 `STATUS` `Running` `3/3` 또는 `2/2` 열 아래에로 표시 되어야 합니다 `READY` . 로그를 인출 하 고 또는을 반환 하는 pod을 설명 합니다 `Error` `CrashLoopBackOff` . Pod 상태가 중단 되 면 `Pending` 클러스터 노드에 리소스가 부족할 수 있습니다. [클러스터](https://kubernetes.io/docs/tasks/administer-cluster/) 를 강화 하면 이러한 pod 상태로 전환 될 수 있습니다 `Running` .

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Azure Arc에 Kubernetes 클러스터 연결

클러스터를 Azure에 연결 하려면 Azure 구독에 대 한 액세스 및 대상 클러스터에 대 한 액세스 권한이 모두 필요 `cluster-admin` 합니다. 클러스터에 연결할 수 없거나 사용 권한이 부족 한 경우 클러스터를 Azure Arc에 연결 하지 못합니다.

### <a name="insufficient-cluster-permissions"></a>클러스터 권한 부족

제공 된 kubeconfig 파일에 Azure Arc 에이전트를 설치할 수 있는 충분 한 권한이 없는 경우 Azure CLI 명령은 오류를 반환 합니다.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

클러스터를 Azure Arc에 연결 하는 사용자는 `cluster-admin` 클러스터에서 역할을 할당 받아야 합니다.

### <a name="installation-timeouts"></a>설치 시간 초과

Kubernetes 클러스터를 Azure Arc enabled Kubernetes에 연결 하려면 클러스터에 Azure Arc 에이전트를 설치 해야 합니다. 클러스터가 저속 인터넷 연결을 통해 실행 되는 경우 에이전트에 대 한 컨테이너 이미지 끌어오기는 Azure CLI 시간 제한 보다 오래 걸릴 수 있습니다.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>투구 문제

투구 `v3.3.0-rc.1` 버전에는 [](https://github.com/helm/helm/pull/8527) `connectedk8s` 다음 오류를 발생 시킬 수 있는 모든 후크가 실행 되는 모든 후크가 실행 되는 문제가 있습니다.

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

이 문제를 복구 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Arc enabled Kubernetes 리소스를 삭제 합니다.
2. 컴퓨터에서 다음 명령을 실행 합니다.
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. 릴리스 후보 버전이 아닌 컴퓨터에 안정적인 버전의 투구 3 [을 설치](https://helm.sh/docs/intro/install/) 합니다.
4. `az connectedk8s connect`적절 한 값으로 명령을 실행 하 여 클러스터를 Azure Arc에 연결 합니다.

## <a name="configuration-management"></a>구성 관리

### <a name="general"></a>일반
구성 리소스와 관련 된 문제를 해결 하려면 매개 변수가 지정 된 az commands를 실행 `--debug` 합니다.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>구성 만들기

Azure Arc enabled Kubernetes resource ()에 대 한 쓰기 권한이 `Microsoft.Kubernetes/connectedClusters/Write` 필요 하 고 해당 클러스터에 대 한 구성을 만드는 데에도 충분 합니다.

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

컨테이너에 대 한 Azure Monitor를 사용 하려면 DaemonSet가 특권 모드에서 실행 되어야 합니다. 모니터링할 정식 Charmed Kubernetes 클러스터를 성공적으로 설정 하려면 다음 명령을 실행 합니다.

```console
juju config kubernetes-worker allow-privileged=true
```