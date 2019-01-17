---
title: Azure의 Kubernetes - 클러스터 Autoscaler
description: Azure Kubernetes Service(AKS)로 클러스터 Autoscaler를 사용하여 요구에 맞도록 클러스터를 자동으로 크기 조정하는 방법을 알아봅니다.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 178dd53046dc9e9b332272914b3aafbb80bc5f4c
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214983"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>AKS(Azure Kubernetes Service)의 클러스터 Autoscaler - 미리 보기

AKS(Azure Kubernetes Service)는 Azure에서 관리되는 Kubernetes 클러스터를 배포하기 위한 유연한 솔루션을 제공합니다. 리소스 요구가 증가하면서 클러스터 Autoscaler는 설정한 제약 조건에 따른 해당 요구에 맞춰 클러스터가 성장할 수 있게 합니다. 클러스터 Autoscaler(CA)는 보류 중인 Pod에 따라 에이전트 노드 크기를 조정하여 이 작업을 수행합니다. 보류 중인 Pod 또는 빈 노드를 확인하려면 주기적으로 클러스터를 검사하고 가능하면 크기를 늘립니다. 기본적으로 CA는 10초마다 보류 중인 Pod를 검사하고 10분을 초과하는 시간 동안 필요 없으면 노드를 제거합니다. [수평 Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)(HPA)에서 사용하는 경우 HPA는 Pod 복제본 및 리소스를 요구에 따라 업데이트합니다. 이 Pod 크기 조정에 따라 불필요한 노드가 있거나 충분한 노드가 없는 경우 CA가 응답해 새 노드 집합에서 Pod를 예약합니다.

이 문서에서는 에이전트 노드에서 클러스터 Autoscaler를 배포하는 방법을 설명합니다. 그러나 클러스터 Autoscaler는 kube 시스템 네임스페이스에 배포되므로 Autoscaler는 해당 Pod를 실행하는 노드의 크기를 축소하지 않습니다.

> [!IMPORTANT]
> AKS(Azure Kubernetes Service) 클러스터 Autoscaler 통합은 현재 **미리 보기** 상태입니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="prerequisites-and-considerations"></a>필수 구성 요소 및 고려 사항

이 문서에서는 RBAC 지원 AKS 클러스터가 설치되어 있다고 가정합니다. AKS 클러스터가 필요한 경우 [AKS(Azure Kubernetes Service) 빠른 시작][aks-quick-start]을 참조하세요.

 클러스터 Autoscaler를 사용하려면 클러스터는 Kubernetes v1.10.X 이상을 사용해야 하며 RBAC가 지원되어야 합니다. 클러스터를 업그레이드하려면 [AKS 클러스터 업그레이드][aks-upgrade]에 대한 문서를 참조합니다.

Pod에 대한 리소스 요청을 정의합니다. 클러스터 AutoScaler는 수평적 Pod AutoScaler처럼 실제 사용 중인 리소스를 확인하지 않고 Pod가 어떤 리소스 요청을 수행하는지 확인합니다. 배포 정의의 `spec: containers` 섹션 내에 CPU 및 메모리 요구 사항을 정의합니다. 다음 예제 코드 조각은 노드에 0.5 vCPU 및 64Mb 메모리를 요청합니다.

  ```yaml
  resources:
    requests:
      cpu: 500m
      memory: 64Mb
  ```

클러스터 AutoScaler가 사용되는 경우 노드의 수를 수동으로 조정하지 않도록 합니다. 클러스터 AutoScaler가 필요한 계산 리소스의 정확한 양을 확인할 수 없어서 수동으로 직접 정의한 노드의 수와 충돌할 수 있습니다.

## <a name="gather-information"></a>정보 수집

클러스터에서 Autoscaler를 실행하도록 클러스터에 대한 권한을 생성하려면 이 bash 스크립트를 실행합니다.

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

스크립트의 단계를 따른 후 스크립트는 다음과 같이 비밀의 형태로 세부 사항을 출력합니다.

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

다음으로 다음 명령을 실행하여 노드 풀의 이름을 가져옵니다. 

```console
$ kubectl get nodes --show-labels
```

출력:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

그런 다음, 레이블 **agentpool**의 값을 추출합니다. 클러스터의 노드 풀에 대한 기본 이름은 "nodepool1"입니다.

이제 비밀 및 노드 풀을 사용하여 배포 차트를 만들 수 있습니다.

## <a name="create-a-deployment-chart"></a>배포 차트 만들기

`aks-cluster-autoscaler.yaml`이라는 파일을 만들고 다음 YAML 코드에 복사합니다.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets", "replicasets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: gcr.io/google-containers/cluster-autoscaler:v1.2.2
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

이전 단계에서 만든 비밀을 복사하여 붙여넣고 파일의 시작 부분에 삽입합니다.

다음으로, 노드 범위를 설정하려면 MIN:MAX:NODE_POOL_NAME 형식으로 `command` 아래에 `--nodes`에 대한 인수를 채웁니다. 예를 들어 `--nodes=3:10:nodepool1`은 노드의 최소 수를 3으로, 최대 수를 10으로 그리고 노드 풀 이름은 nodepool1으로 설정합니다.

그런 다음, 사용하려는 클러스터 Autoscaler의 버전으로 **컨테이너** 아래의 이미지 필드를 채웁니다. AKS는 v1.2.2 이상을 요구합니다. 다음 예제에서는 v1.2.2를 사용합니다.

## <a name="deployment"></a>배포

실행으로 cluster-autoscaler 배포

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

클러스터 Autoscaler가 실행되고 있는지 확인하려면 다음 명령을 사용하고 Pod 목록을 확인합니다. "cluster-autoscaler"의 접두사로 실행되는 Pod가 있어야 합니다. 이를 확인하는 경우 클러스터 Autoscaler가 배포된 것입니다.

```console
kubectl -n kube-system get pods
```

클러스터 Autoscaler의 상태를 보려면 실행합니다

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>클러스터 Autoscaler 상태 해석

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

클러스터 Autoscaler 상태를 통해 두 개의 서로 다른 수준에서 클러스터 Autoscaler의 상태를 볼 수 있습니다. 클러스터 전체 및 각 노드 그룹 내에서 AKS는 현재 하나의 노드 풀만을 지원하므로 이러한 메트릭은 동일합니다.

* 상태는 노드의 전반적인 상태를 나타냅니다. 클러스터 Autoscaler가 클러스터에서 노드를 만들거나 제거하는 데 어려움이 있는 경우 이 상태는 "Unhealthy"로 변경됩니다. 다른 노드의 상태에 대한 분석도 있습니다.
    * "Ready"는 노드가 Pod를 예약할 준비가 완료된 것을 의미합니다.
    * "Unready"는 노드가 시작된 후 중단됨을 의미합니다.
    * "NotStarted"는 노드가 아직 완전히 시작되지 않음을 의미합니다.
    * "LongNotStarted"는 노드가 적절한 제한 내에서 시작하지 못함을 의미합니다.
    * "Registered"는 노드가 그룹에 등록됨을 의미합니다.
    * "Unregistered"는 노드가 클러스터 공급자 측에 있지만 Kubernetes 등록에 실패했음을 의미합니다.
  
* ScaleUp을 통해 클러스터가 확장이 클러스터에서 발생해야 함을 결정하는 경우를 확인할 수 있습니다.
    * 전환은 클러스터에서 노드 수가 변경되거나 노드 상태가 변경되는 경우입니다.
    * 준비된 노드 수는 사용 가능하고 클러스터에서 준비된 노드 수입니다. 
    * cloudProviderTarget은 클러스터 Autoscaler에서 클러스터가 해당 워크로드를 처리해야 함을 결정한 노드 수입니다.

* ScaleDown을 통해 규모 축소에 대한 후보가 있는 경우를 확인할 수 있습니다. 
    * 규모 축소에 대한 후보는 클러스터 Autoscaler에서 해당 워크로드를 처리하는 클러스터의 기능에 영향을 주지 않고 제거할 수 있는 것을 결정한 노드입니다. 
    * 제공된 시간은 후보 축소 및 해당 마지막 전환 시간에 대해 클러스터에서 확인된 마지막 시간을 표시합니다.

마지막으로 이벤트에서 클러스터 Autoscaler에서 수행한 강화 또는 규모 축소 이벤트, 실패 또는 성공 및 해당 시간을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

수평 Pod Autoscaler로 클러스터 Autoscaler를 사용하려면 [Kubernetes 애플리케이션 및 인프라 크기 조정][aks-tutorial-scale]을 체크 아웃합니다.

AKS 자습서를 통한 AKS 배포 및 관리에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
