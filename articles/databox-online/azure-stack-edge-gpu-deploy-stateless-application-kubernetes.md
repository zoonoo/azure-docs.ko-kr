---
title: kubectl을 사용하여 Azure Stack Edge Pro GPU 디바이스에 Kubernetes 상태 비저장 애플리케이션 배포 | Microsoft Docs
description: Microsoft Azure Stack Edge Pro 디바이스에서 kubectl을 사용하여 Kubernetes 상태 비저장 애플리케이션 배포를 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: fd55ce702ae3c9485fc2dcc37fc90915a8990ce7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638151"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 kubectl을 통해 Kubernetes 상태 비저장 애플리케이션 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 기존 Kubernetes 클러스터에서 kubectl 명령을 사용하여 상태 비저장 애플리케이션을 배포하는 방법을 설명합니다. 또한 이 문서에서는 상태 비저장 애플리케이션에서 Pod를 만들고 설정하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

Kubernetes 클러스터를 만들고 `kubectl` 명령줄 도구를 사용하려면 다음을 확인해야 합니다.

- 1노드 Azure Stack Edge Pro 디바이스에 대한 로그인 자격 증명이 있어야 합니다.

- Azure Stack Edge Pro 디바이스에 액세스하려면 Windows 클라이언트 시스템에 Windows PowerShell 5.0 이상이 설치되어 있어야 합니다. 지원되는 운영 체제를 사용하는 다른 클라이언트도 있을 수 있습니다. 이 문서에서는 Windows 클라이언트를 사용하는 절차에 대해 설명합니다. 최신 버전의 Windows PowerShell을 다운로드하려면 [Windows PowerShell 설치](/powershell/scripting/install/installing-windows-powershell)로 이동합니다.

- Azure Stack Edge Pro 디바이스에는 컴퓨팅이 설정됩니다. 컴퓨팅을 사용하도록 설정하려면 디바이스의 로컬 UI에서 **컴퓨팅** 페이지로 이동합니다. 그런 다음, 컴퓨팅을 사용하도록 설정하려는 네트워크 인터페이스를 선택합니다. **사용** 을 선택합니다. 컴퓨팅을 사용하도록 설정하면 해당 네트워크 인터페이스에서 디바이스에 가상 스위치가 생성됩니다. 자세한 내용은 [Azure Stack Edge Pro에서 컴퓨팅 네트워크 사용](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)을 참조하세요.

- Azure Stack Edge Pro 디바이스에는 v1.9 이상 버전을 실행하는 Kubernetes 클러스터 서버가 있습니다. 자세한 내용은 [Microsoft Azure Stack Edge Pro 디바이스에서 Kubernetes 클러스터 만들기 및 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조하세요.

- `kubectl`이 설치되어 있습니다.

## <a name="deploy-a-stateless-application"></a>상태 비저장 애플리케이션 배포

시작하기 전에 다음을 수행해야 합니다.

1. Kubernetes 클러스터를 만듭니다.
2. 네임스페이스를 설정합니다.
3. 네임스페이스와 사용자를 연결합니다.
4. 사용자 구성을 `C:\Users\<username>\.kube`에 저장합니다.
5. `kubectl`을 설치합니다.

이제 Azure Stack Edge Pro 디바이스에서 상태 비저장 애플리케이션 배포 실행 및 관리를 시작할 수 있습니다. `kubectl` 사용을 시작하기 전에 올바른 버전의 `kubectl`이 있는지 확인해야 합니다.

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>올바른 버전의 kubectl이 있고 구성을 설정했는지 확인합니다.

`kubectl`의 버전을 확인하려면 다음을 수행합니다.

1. `kubectl`의 버전이 1.9보다 크거나 같은지 확인합니다.

   ```powershell
   kubectl version
   ```
    
   출력의 예는 다음과 같습니다.
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   이 경우 kubectl의 클라이언트 버전이 v1.15.2 이고 호환되면 계속합니다.

2. Kubernetes 클러스터에서 실행 중인 Pod의 목록을 가져옵니다. Pod는 Kubernetes 클러스터에서 실행되는 애플리케이션 컨테이너 또는 프로세스입니다.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   명령 사용법의 예는 다음과 같습니다.
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   클러스터에서 실행 중인 애플리케이션이 없기 때문에 리소스(Pod)가 없다는 내용이 출력에 표시되어야 합니다.

   이 명령은 "C:\Users\\&lt;username&gt;\\.kube\"의 디렉터리 구조를 구성 파일로 채웁니다. kubectl 명령줄 도구는 이러한 파일을 사용하여 Kubernetes 클러스터에서 상태 비저장 애플리케이션을 만들고 관리합니다.  

3. "C:\Users\\&lt;username&gt;\\.kube\"의 디렉터리 구조를 수동으로 확인하여 *kubectl* 이 다음 하위 폴더로 채워졌는지 확인합니다.

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> 모든 kubectl 명령의 목록을 보려면 `kubectl --help`를 입력합니다.

### <a name="create-a-stateless-application-using-a-deployment"></a>배포를 사용하여 상태 비저장 애플리케이션 만들기

Kubectl 명령줄 버전이 올바르고 필요한 구성 파일이 있는지 확인했으므로 상태 비저장 애플리케이션 배포를 만들 수 있습니다.

Pod는 Kubernetes 애플리케이션의 기본 실행 단위이며, 사용자가 Kubernetes 개체 모델에서 만들거나 배포하는 가장 작고 가장 간단한 단위입니다. Pod는 또한 스토리지 리소스, 고유한 네트워크 IP 및 컨테이너가 실행되는 방법을 제어하는 옵션을 캡슐화합니다.

사용자가 만드는 상태 비저장 애플리케이션의 형식은 nginx 웹 서버 배포입니다.

상태 비저장 애플리케이션 배포를 만들고 관리하는 데 사용하는 모든 kubectl 명령은 구성과 연결된 네임스페이스를 지정해야 합니다. [Microsoft Azure Stack Edge Pro 디바이스에서 Kubernetes 클러스터 만들기 및 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md) 자습서에서 `New-HcsKubernetesNamespace`를 사용하여 Azure Stack Edge Pro 디바이스의 클러스터에 연결된 상태에서 네임스페이스를 만들었습니다.

kubectl 명령에서 네임스페이스를 지정하려면 `kubectl <command> -n <namespace-string>`을 사용합니다.

다음 단계에 따라 nginx 배포를 만듭니다.

1. Kubernetes 배포 개체를 만들어 상태 비저장 애플리케이션을 적용합니다.

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   이 예제에서 애플리케이션 YAML 파일의 경로는 외부 원본입니다.

   다음은 명령과 해당 출력을 사용하는 샘플입니다.

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   또는 로컬 컴퓨터에 다음 마크다운을 저장하고 *-f* 매개 변수에서 경로와 파일 이름을 대체할 수 있습니다. 예를 들면 "C:\Kubernetes\deployment.yaml"입니다. 애플리케이션 배포에 대한 구성은 다음과 같습니다.

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   이 명령은 애플리케이션을 실행하는 데 필요한 두 가지 Pod를 포함하는 기본 nginx 배포를 만듭니다.

2. 생성된 Kubernetes nginx 배포에 대한 설명을 가져옵니다.

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   명령의 샘플 사용법과 출력은 다음과 같습니다.
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   *복제본* 설정의 경우 다음이 표시됩니다.
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   *복제본* 설정은 배포 사양에 두 개의 Pod가 필요하고, 해당 Pod가 생성되고 업데이트되었으며 사용할 준비가 되었음을 나타냅니다.

   > [!NOTE]
   > 복제본 세트는 디바이스 노드 오류 또는 디바이스 업그레이드 중단 등의 이유로 삭제되거나 종료된 Pod를 대체합니다. 따라서 애플리케이션에 단일 Pod만 필요한 경우에도 복제본 세트를 사용하는 것이 좋습니다.

3. 배포의 Pod를 나열하려면 다음을 수행합니다.

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   명령의 샘플 사용법과 출력은 다음과 같습니다.
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   이 출력은 kubectl을 사용하여 참조할 수 있는 고유한 이름을 가진 두 개의 Pod가 있는지 확인합니다.

4. 배포의 개별 Pod에 대한 정보를 보려면 다음을 수행합니다.

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  명령의 샘플 사용법과 출력은 다음과 같습니다.

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>복제본 수를 늘려서 애플리케이션 배포 크기를 조정합니다.

각 Pod는 지정된 애플리케이션의 단일 인스턴스를 실행하기 위한 것입니다. 여러 인스턴스를 실행하기 위해 애플리케이션을 수평으로 스케일링하려면 Pod 수를 각 인스턴스별로 하나씩 늘릴 수 있습니다. Kubernetes에서는 이를 복제라고 합니다.
새 YAML 파일을 적용하여 애플리케이션 배포의 Pod 수를 늘릴 수 있습니다. YAML 파일은 복제본 설정을 4로 변경하여 배포의 Pod 수를 4개의 Pod로 늘립니다. Pod 수를 2개에서 4개로 늘리려면 다음을 수행합니다.

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

또는 로컬 컴퓨터에 다음 마크다운을 저장하고 `kubectl apply`에 대한 *-f* 매개 변수에서 경로와 파일 이름을 대체할 수 있습니다. 예를 들면 "C:\Kubernetes\deployment-scale.yaml"입니다. 애플리케이션 배포 규모에 대한 구성은 다음과 같습니다.

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

배포에 4개의 Pod가 있는지 확인하려면 다음을 수행합니다.

```powershell
kubectl get pods -l app=nginx
```

배포 크기를 2개에서 4개 Pod로 조정하는 예제 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

출력에서 볼 수 있듯이, 이제 배포에서 애플리케이션을 실행할 수 있는 Pod가 4개 있습니다.

### <a name="delete-a-deployment"></a>배포 삭제

모든 Pod를 포함한 배포를 삭제하려면 배포 이름 *nginx-deployment* 및 네임스페이스 이름을 지정하여 `kubectl delete deployment`를 실행해야 합니다. 배포를 삭제하려면 다음을 수행합니다.

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

명령 사용법의 예와 출력은 다음과 같습니다.

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>다음 단계

[Kubernetes 개요](azure-stack-edge-gpu-kubernetes-overview.md)