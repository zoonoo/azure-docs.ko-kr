---
title: "AKS(Azure Container Service) 클러스터 노드에 대한 SSH 연결 만들기"
description: "AKS(Azure Container Service) 클러스터 노드를 사용하는 SSH 연결을 만듭니다."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>AKS(Azure Container Service) 클러스터 노드에 대한 SSH 연결 만들기

경우에 따라 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 AKS(Azure Container Service) 노드에 액세스해야 할 수도 있습니다. AKS 노드는 인터넷에 노출되지 않습니다. 이 문서에서 설명하는 단계를 사용하여 AKS 노드를 사용하는 SSH 연결을 만듭니다.

## <a name="configure-ssh-access"></a>SSH 액세스 구성

 SSH를 특정 노드에 연결하려면 `hostNetwork` 액세스가 있는 Pod를 만듭니다. 또한 Pod 액세스를 위한 서비스도 만듭니다. 이 구성은 권한이 부여되며 사용한 후에 제거해야 합니다.

`aks-ssh.yaml`이라는 파일을 만들고 이 매니페스트에 복사합니다. 노드 이름을 대상 AKS 노드의 이름으로 업데이트합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

매니페스트를 실행하여 Pod와 서비스를 만듭니다.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

노출된 서비스의 외부 IP 주소를 가져옵니다. IP 주소 구성을 완료하는 데 1분 정도 걸릴 수 있습니다. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

ssh 연결을 만듭니다. 

AKS 클러스터에 대한 기본 사용자 이름은 `azureuser`입니다. 클러스터를 만들 때 이 계정을 변경한 경우 적절한 관리 사용자 이름으로 바꿉니다. 

키가 `~/ssh/id_rsa`에 없으면 `ssh -i` 인수를 사용하여 올바른 위치를 제공합니다.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>SSH 액세스 제거

작업이 완료되면 SSH 액세스 Pod와 서비스를 삭제합니다.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```