---
title: Kubernetes 클러스터에서 인증
description: 서비스 주체를 사용하여 끌어오기 비밀을 만들어 Azure 컨테이너 레지스트리의 이미지에 대한 액세스 권한을 Kubernetes 클러스터에 제공하는 방법을 알아봅니다.
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154895"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Azure 컨테이너 레지스트리에서 Kubernetes 클러스터로 이미지 끌어오기

Azure 컨테이너 레지스트리를 [minikube](https://minikube.sigs.k8s.io/) 및 [kind와](https://kind.sigs.k8s.io/)같은 "로컬" Kubernetes 클러스터를 포함하여 모든 Kubernetes 클러스터가 있는 컨테이너 이미지의 소스로 사용할 수 있습니다. 이 문서에서는 Azure Active Directory 서비스 주체를 기반으로 Kubernetes 끌어오기 비밀을 만드는 방법을 보여 주며 있습니다. 그런 다음 비밀을 사용하여 Kubernetes 배포에서 Azure 컨테이너 레지스트리에서 이미지를 가져옵니다.

> [!TIP]
> 관리되는 [Azure Kubernetes 서비스를](../aks/intro-kubernetes.md)사용하는 경우 이미지 끌어오기를 위한 대상 Azure 컨테이너 레지스트리와 [클러스터를 통합할](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) 수도 있습니다. 

이 문서에서는 개인 Azure 컨테이너 레지스트리를 이미 만든 것으로 가정합니다. 또한 `kubectl` 명령줄 도구를 통해 Kubernetes 클러스터를 실행하고 액세스할 수 있어야 합니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

서비스 주 암호를 저장하거나 기억하지 않는 경우 [az 광고 sp 자격 증명 재설정][az-ad-sp-credential-reset] 명령을 통해 재설정할 수 있습니다.

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

이 명령은 서비스 주체에 대한 유효한 새 암호를 반환합니다.

## <a name="create-an-image-pull-secret"></a>이미지 끌어오기 비밀 만들기

Kubernetes는 *레지스트리에* 인증하는 데 필요한 정보를 저장하기 위해 이미지 끌어오기 비밀을 사용합니다. Azure 컨테이너 레지스트리에 대한 끌어오기 암호를 만들려면 서비스 주체 ID, 암호 및 레지스트리 URL을 제공합니다. 

다음 `kubectl` 명령을 사용하여 이미지 끌어오기 비밀을 만듭니다.

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
각 항목이 나타내는 의미는 다음과 같습니다.

| 값 | 설명 |
| :--- | :--- |
| `secret-name` | 이미지 의 이름 풀 비밀, 예를 들어, *acr-secret* |
| `namespace` | Kubernetes 네임 스페이스에 비밀을 넣어 <br/> 기본 네임스페이스가 아닌 네임스페이스에 비밀을 배치하려는 경우에만 필요합니다. |
| `container-registry-name` | Azure 컨테이너 레지스트리이름 |
| `service-principal-ID` | Kubernetes가 레지스트리에 액세스하는 데 사용할 서비스 주체의 ID |
| `service-principal-password` | 서비스 주체 암호 |

## <a name="use-the-image-pull-secret"></a>이미지 풀 시크릿 사용

이미지 끌어오기 비밀을 만든 후에는 이를 사용하여 Kubernetes 포드 및 배포를 만들 수 있습니다. 배포 파일에 보안 `imagePullSecrets` 비밀의 이름을 제공합니다. 예를 들어:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

앞의 `your-awesome-app:v1` 예제에서는 Azure 컨테이너 레지스트리에서 가져올 이미지의 이름이며 `acr-secret` 레지스트리에 액세스하기 위해 만든 끌어오기 비밀의 이름입니다. 포드를 배포할 때 Kubernetes는 클러스터에 아직 없는 경우 레지스트리에서 이미지를 자동으로 가져옵니다.


## <a name="next-steps"></a>다음 단계

* 서비스 주체 및 Azure 컨테이너 레지스트리 작업에 대한 자세한 내용은 [서비스 주체가](container-registry-auth-service-principal.md) 있는 Azure 컨테이너 레지스트리 인증을 참조하십시오.
* [Kubernetes 문서에서](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) 이미지 끌어오기 비밀에 대해 자세히 알아보기


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset