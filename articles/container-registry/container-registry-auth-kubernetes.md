---
title: Kubernetes 끌어오기 비밀을 사용하여 Azure Container Registry로 인증
description: 서비스 주체를 사용하여 끌어오기 비밀을 만들어 Azure Container Registry의 이미지에 대한 액세스 권한을 Kubernetes 클러스터에 제공하는 방법 알아보기
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 06/02/2021
ms.openlocfilehash: 149035de0fc84c75cdcaa73c91d6cd5379c53498
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111439623"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster-using-a-pull-secret"></a>끌어오기 비밀을 사용하여 Azure Container Registry에서 Kubernetes 클러스터로 이미지 끌어오기

[minikube](https://minikube.sigs.k8s.io/) 및 [kind](https://kind.sigs.k8s.io/)와 같은 ‘로컬’ Kubernetes 클러스터를 포함하여 모든 Kubernetes 클러스터에서 컨테이너 이미지의 원본으로 Azure Container Registry를 사용할 수 있습니다. 이 문서에서는 Azure Container Registry에 대한 자격 증명을 사용하여 Kubernetes 끌어오기 비밀을 만드는 방법을 보여 줍니다. 그런 다음, 비밀을 사용하여 Pod 배포의 Azure Container Registry에서 이미지를 끌어올 수 있습니다.

이 예에서는 Azure Active Directory [서비스 주체 자격 증명](container-registry-auth-service-principal.md)을 사용하여 끌어오기 비밀을 만듭니다. [리포지토리 범위 액세스 토큰](container-registry-repository-scoped-permissions.md)과 같은 다른 Azure Container Registry 자격 증명을 사용하여 끌어오기 비밀을 구성할 수도 있습니다.

> [!NOTE]
> 끌어오기 비밀은 일반적으로 사용되지만 추가 관리 오버헤드가 발생합니다. [Azure Kubernetes Service](../aks/intro-kubernetes.md)를 사용하는 경우 클러스터의 관리 ID 또는 서비스 주체를 사용하는 것과 같은 [다른 옵션](authenticate-kubernetes-options.md)을 사용하여 각 Pod에서 추가 `imagePullSecrets` 설정 없이 이미지를 안전하게 끌어오는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 프라이빗 Azure Container Registry를 이미 만들었다고 가정합니다. 또한 `kubectl` 명령줄 도구를 통해 Kubernetes 클러스터를 실행하고 액세스할 수 있어야 합니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

서비스 주체 암호를 저장하거나 기억하지 못하는 경우 [az ad sp credential reset][az-ad-sp-credential-reset] 명령을 사용하여 다시 설정할 수 있습니다.

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

이 명령은 서비스 주체에 대해 유효한 새 암호를 반환합니다.

## <a name="create-an-image-pull-secret"></a>이미지 끌어오기 비밀 만들기

Kubernetes는 *이미지 끌어오기 비밀* 을 사용하여 레지스트리 인증에 필요한 정보를 저장합니다. Azure Container Registry에 대한 끌어오기 비밀을 만들려면 서비스 주체 ID, 암호 및 레지스트리 URL을 제공합니다. 

다음 `kubectl` 명령을 사용하여 이미지 끌어오기 비밀을 만듭니다.

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
여기서

| 값 | Description |
| :--- | :--- |
| `secret-name` | 이미지 끌어오기 비밀의 이름(예: *acr-secret*) |
| `namespace` | 비밀을 넣을 Kubernetes 네임스페이스 <br/> 기본 네임스페이스가 아닌 네임스페이스에 비밀을 배치하려는 경우에만 필요 |
| `container-registry-name` | Azure Container Registry의 이름(예: *myregistry*)<br/><br/>`--docker-server`는 레지스트리 로그인 서버의 정규화된 이름임  |
| `service-principal-ID` | Kubernetes에서 레지스트리에 액세스하는 데 사용할 서비스 주체의 ID |
| `service-principal-password` | 서비스 주체 암호 |

## <a name="use-the-image-pull-secret"></a>이미지 끌어오기 비밀 사용

이미지 끌어오기 비밀을 만든 후에는 이 비밀을 사용하여 Kubernetes Pod 및 배포를 만들 수 있습니다. 배포 파일의 `imagePullSecrets` 아래에 비밀의 이름을 제공하세요. 예를 들면 다음과 같습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

위의 예제에서 `my-awesome-app:v1`은 Azure Container Registry에서 끌어올 이미지의 이름이고 `acr-secret`은 레지스트리에 액세스하기 위해 만든 끌어오기 비밀의 이름입니다. Pod를 배포할 때 Kubernetes는 아직 클러스터에 없는 경우 레지스트리에서 이미지를 자동으로 끌어옵니다.


## <a name="next-steps"></a>다음 단계

* 서비스 사용자 및 Azure Container Registry 사용에 대한 자세한 내용은 [서비스 사용자로 Azure Container Registry 인증](container-registry-auth-service-principal.md)을 참조하세요.
* [Kubernetes 설명서](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)에서 이미지 끌어오기 비밀에 대한 자세한 정보


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset