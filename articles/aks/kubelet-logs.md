---
title: AKS(Azure Kubernetes Service)에서 kubelet 로그 가져오기
description: AKS(Azure Kubernetes Service) 클러스터 노드에서 kubelet 로그 가져오기
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0467be7e91fdbf4685fc41a375ea86a503e26009
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>AKS(Azure Kubernetes Service) 클러스터 노드에서 kubelet 로그 가져오기

경우에 따라 문제 해결을 위해 AKS(Azure Kubernetes Service) 노드에서 kubelet 로그를 가져와야 합니다. 이 문서에서는 이러한 로그를 가져오는 한 가지 옵션을 자세히 설명합니다.

## <a name="create-an-ssh-connection"></a>SSH 연결 만들기

먼저 kubelet 로그를 가져와야 하는 노드와 SSH 연결을 만듭니다. 이 작업 방법은 [AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기][aks-ssh] 문서에 자세히 설명되어 있습니다.

## <a name="get-kubelet-logs"></a>kubelet 로그 가져오기

노드에 연결한 후에는 kubelet 로그를 가져오는 다음 명령을 실행합니다.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

샘플 출력:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md