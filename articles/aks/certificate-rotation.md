---
title: Azure Kubernetes 서비스(AKS)에서 인증서 회전
description: AKS(Azure Kubernetes) 클러스터에서 인증서를 회전하는 방법을 알아봅니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: f299b13baf5811b92bdc2e40b027868617d7574c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368522"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에서 인증서 회전

Azure Kubernetes 서비스(AKS)는 많은 구성 요소에서 인증을 위해 인증서를 사용합니다. 보안 또는 정책상의 이유로 해당 인증서를 주기적으로 교체해야 할 수 있습니다. 예를 들어 90일마다 모든 인증서를 회전하는 정책이 있을 수 있습니다.

이 문서에서는 AKS 클러스터에서 인증서를 회전하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.77 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하십시오.

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 인증서, 인증서 기관 및 서비스 계정

AKS는 다음 인증서, 인증 기관 및 서비스 계정을 생성하고 사용합니다.

* AKS API 서버는 클러스터 CA라는 인증 기관(CA)을 만듭니다.
* API 서버에는 API 서버에서 쿠벨렛으로의 단방향 통신을 위한 인증서에 서명하는 클러스터 CA가 있습니다.
* 각 kubelet은 또한 KUbelet에서 API 서버로통신하기 위해 클러스터 CA에서 서명하는 인증서 서명 요청(CSR)을 만듭니다.
* etcd 키 값 저장소에는 API 서버 등에서 통신하기 위해 클러스터 CA에서 서명한 인증서가 있습니다.
* etcd 키 값 저장소는 AKS 클러스터의 etcd 복제본 간의 데이터 복제를 인증하고 인증하기 위해 인증서에 서명하는 CA를 만듭니다.
* API 집계는 클러스터 CA를 사용하여 Azure에 대한 개방형 서비스 브로커와 같은 다른 API와의 통신을 위한 인증서를 발급합니다. API 집계자는 해당 인증서를 발급하기 위한 자체 CA를 가질 수도 있지만 현재 클러스터 CA를 사용합니다.
* 각 노드는 클러스터 CA에서 서명하는 SA(서비스 계정) 토큰을 사용합니다.
* `kubectl` 클라이언트에는 AKS 클러스터와 통신하기 위한 인증서가 있습니다.

> [!NOTE]
> 2019년 3월 이전에 생성된 AKS 클러스터에는 2년 후에 만료되는 인증서가 있습니다. 2019년 3월 이후에 생성된 클러스터 또는 인증서가 회전된 클러스터에는 30년 후에 만료되는 클러스터 CA 인증서가 있습니다. 다른 모든 인증서는 2년 후에 만료됩니다. 클러스터가 생성된 시기를 확인하려면 노드 풀의 `kubectl get nodes` *나이를* 확인하는 데 사용합니다.
> 
> 또한 클러스터 인증서의 만료 날짜를 확인할 수 있습니다. 예를 들어 다음 명령에는 *myAKSCluster* 클러스터에 대한 인증서 세부 정보가 표시됩니다.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>클러스터 인증서 회전

> [!WARNING]
> 인증서를 사용하여 `az aks rotate-certs` 회전하면 AKS 클러스터에 최대 30분의 가동 중지 시간이 발생할 수 있습니다.

[az aks get-credentials를][az-aks-get-credentials] 사용하여 AKS 클러스터에 로그인합니다. 이 명령은 로컬 컴퓨터에서 클라이언트 `kubectl` 인증서를 다운로드하고 구성합니다.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

클러스터의 모든 인증서, CA 및 SA를 회전하는 데 사용합니다. `az aks rotate-certs`

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> 완료하는 데 최대 30분이 `az aks rotate-certs` 걸릴 수 있습니다. 완료하기 전에 명령이 `az aks show` 실패하면 클러스터의 상태가 *인증서 회전임을*확인하는 데 사용합니다. 클러스터가 실패한 상태인 경우 다시 `az aks rotate-certs` 실행하여 인증서를 다시 회전합니다.

명령을 실행하여 이전 인증서가 더 이상 `kubectl` 유효하지 않은지 확인합니다. `kubectl`에서 사용되는 인증서를 업데이트하지 않았기 때문에 오류가 표시됩니다.  예를 들어:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

을 실행하여 `az aks get-credentials` `kubectl` 사용하는 인증서를 업데이트합니다.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

이제 성공할 명령을 실행하여 인증서가 `kubectl` 업데이트되었는지 확인합니다. 예를 들어:

```console
kubectl get no
```

> [!NOTE]
> [Azure 개발자 공간과][dev-spaces]같이 AKS 위에 실행되는 서비스가 있는 경우 해당 [서비스와 관련된 인증서도 업데이트해야][dev-spaces-rotate] 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터의 인증서, C 및 SA를 자동으로 회전하는 방법을 보여 주어 있습니다. AKS 보안 모범 사례에 대한 자세한 내용은 [AZURE Kubernetes 서비스(AKS)에서 클러스터 보안 및 업그레이드에 대한 모범 사례를][aks-best-practices-security-upgrades] 확인할 수 있습니다.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
