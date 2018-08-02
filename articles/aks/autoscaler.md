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
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186716"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>AKS(Azure Kubernetes Service)의 클러스터 Autoscaler - 미리 보기

AKS(Azure Kubernetes Service)는 Azure에서 관리되는 Kubernetes 클러스터를 배포하기 위한 유연한 솔루션을 제공합니다. 리소스 요구가 증가하면서 클러스터 Autoscaler는 설정한 제약 조건에 따른 해당 요구에 맞춰 클러스터가 성장할 수 있게 합니다. 클러스터 Autoscaler(CA)는 보류 중인 Pod에 따라 에이전트 노드 크기를 조정하여 이 작업을 수행합니다. 보류 중인 Pod 또는 빈 노드를 확인하려면 주기적으로 클러스터를 검사하고 가능하면 크기를 늘립니다. 기본적으로 CA는 10초마다 보류 중인 Pod를 검사하고 10분을 초과하는 시간 동안 필요 없으면 노드를 제거합니다. 수평 Pod Autoscaler(HPA)에서 사용하는 경우 HPA는 Pod 복제본 및 리소스를 요구에 따라 업데이트합니다. 이 Pod 크기 조정에 따라 불필요한 노드가 있거나 충분한 노드가 없는 경우 CA가 응답해 새 노드 집합에서 Pod를 예약합니다.

> [!IMPORTANT]
> AKS(Azure Kubernetes Service) 클러스터 Autoscaler 통합은 현재 **미리 보기** 상태입니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="prerequisites"></a>필수 조건

이 문서에서는 RBAC 지원 AKS 클러스터가 설치되어 있다고 가정합니다. AKS 클러스터가 필요한 경우 [AKS(Azure Kubernetes Service) 빠른 시작][aks-quick-start]을 참조하세요.

 클러스터 Autoscaler를 사용하려면 클러스터는 Kubernetes v1.10.X 이상을 사용해야 하며 RBAC가 지원되어야 합니다. 클러스터를 업그레이드하려면 [AKS 클러스터 업그레이드][aks-upgrade]에 대한 문서를 참조합니다.

## <a name="gather-information"></a>정보 수집

다음 목록에서는 Autoscaler 정의에서 제공해야 하는 모든 정보를 보여 줍니다.

- *구독 ID*: 이 클러스터에 사용된 구독에 해당하는 ID
- *리소스 그룹 이름*: 클러스터가 속한 리소스 그룹의 이름 
- *클러스터 이름*: 클러스터의 이름
- *클라이언트 ID*: 단계를 생성하는 권한으로 부여한 앱 ID
- *클라이언트 비밀*: 단계를 생성하는 권한으로 부여한 앱 비밀
- *테넌트 ID*: 테넌트(계정 소유자)의 ID
- *노드 리소스 그룹*: 클러스터의 에이전트 노드가 포함된 리소스 그룹의 이름
- *노드 풀 이름*: 크기를 조정하려는 노드 풀의 이름
- *최소 노드 수*: 클러스터에 있는 최소 노드 수
- *최대 노드 수*: 클러스터에 있는 최대 노드 수
- *VM 유형*: Kubernetes 클러스터를 생성하는 데 사용된 서비스

다음을 사용하여 구독 ID를 가져옵니다. 

``` azurecli
az account show --query id
```

다음 명령을 실행하여 Azure 자격 증명 집합을 생성합니다.

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

앱 ID, 암호 및 테넌트 ID는 다음 단계의 clientID, clientSecret 및 tenantID입니다.

다음 명령을 실행하여 노드 풀의 이름을 가져옵니다. 

```console
$ kubectl get nodes --show-labels
```

출력:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

그런 다음, 레이블 **agentpool**의 값을 추출합니다. 클러스터의 노드 풀에 대한 기본 이름은 "nodepool1"입니다.

노드 리소스 그룹의 이름을 가져오려면 레이블 **kubernetes.azure.com<span></span>/cluster**의 값을 추출합니다. 노드 리소스 그룹 이름의 형식은 일반적으로 MC_[resource-group]\_[cluster-name]_[location]입니다.

vmType 매개 변수는 사용 중인 서비스, 여기서는 AKS를 가리킵니다.

이제 다음 정보가 있어야 합니다.

- 구독 ID
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- TenantID
- NodeResourceGroup
- VMType

다음으로, base64를 사용하여 이러한 값 모두를 인코딩합니다. 예를 들어 base64로 VMType 값을 인코딩하려면

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>비밀 만들기
이 데이터를 사용하여 이전 단계에서 찾은 다음 형식의 값을 사용하는 배포에 대한 비밀을 만듭니다.

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
apiVersion: extensions/v1beta1
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
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
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
kubectl create -f cluster-autoscaler-containerservice.yaml
```

클러스터 Autoscaler가 실행되고 있는지 확인하려면 다음 명령을 사용하고 Pod 목록을 확인합니다. Pod에 "cluster-autoscaler" 실행이라는 접두사가 붙은 경우 클러스터 Autoscaler가 배포된 것입니다.

```console
kubectl -n kube-system get pods
```

클러스터 Autoscaler의 상태를 보려면 실행합니다

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="next-steps"></a>다음 단계

수평 Pod Autoscaler로 클러스터 Autoscaler를 사용하려면 [Kubernetes 응용 프로그램 및 인프라 크기 조정][aks-tutorial-scale]을 체크 아웃합니다.

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
