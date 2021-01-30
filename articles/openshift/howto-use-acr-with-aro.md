---
title: Azure Red Hat OpenShift를 사용 하 여 Azure Container Registry 사용
description: Azure Red Hat OpenShift 클러스터에서 Azure Container Registry 컨테이너를 가져오고 실행 하는 방법에 대해 알아봅니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 91b43aa939579f5df8115048cf52b9bcdd7ade3d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063987"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO)를 사용 하 여 Azure Container Registry 사용

ACR (Azure Container Registry)는 지리적 복제와 같은 엔터프라이즈 기능을 사용 하 여 개인 Docker 컨테이너 이미지를 저장 하는 데 사용할 수 있는 관리 되는 컨테이너 레지스트리 서비스입니다. ARO 클러스터에서 ACR에 액세스 하기 위해 클러스터는 Docker 로그인 자격 증명을 Kubernetes 암호에 저장 하 여 ACR으로 인증할 수 있습니다.  마찬가지로, ARO 클러스터는 pod 사양에서 imagePullSecret을 사용 하 여 이미지를 끌어올 때 레지스트리에 대해 인증할 수 있습니다. 이 문서에서는 개인 Docker 컨테이너 이미지를 저장 하 고 끌어오는 Azure Red Hat OpenShift 클러스터를 사용 하 여 Azure Container Registry를 설정 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>전제 조건

이 가이드에서는 기존 Azure Container Registry 있다고 가정 합니다. 그렇지 않으면 Azure Portal 또는 [Azure CLI 지침](../container-registry/container-registry-get-started-azure-cli.md) 을 사용 하 여 컨테이너 레지스트리를 만듭니다.

또한이 문서에서는 기존 Azure Red Hat OpenShift 클러스터가 있고 CLI가 설치 되어 있다고 가정 `oc` 합니다. 그렇지 않은 경우에는 [ARO 클러스터 만들기 자습서](tutorial-create-cluster.md)의 지침을 따르세요.

## <a name="get-a-pull-secret"></a>끌어오기 비밀 가져오기

ARO 클러스터에서 레지스트리에 액세스 하려면 ACR의 끌어오기 암호가 필요 합니다.

끌어오기 비밀 자격 증명을 가져오기 위해 Azure Portal 또는 Azure CLI를 사용할 수 있습니다.

Azure Portal 사용 하는 경우 ACR 인스턴스로 이동 하 여 **액세스 키** 를 선택 합니다.  `docker-username`은 컨테이너 레지스트리의 이름이 며에는 password 또는 password2를 사용 `docker-password` 합니다.

![액세스 키](./media/acr-access-keys.png)

대신 Azure CLI를 사용 하 여 이러한 자격 증명을 가져올 수 있습니다.
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Kubernetes 암호 만들기

이제 이러한 자격 증명을 사용 하 여 Kubernetes 암호를 만듭니다. ACR 자격 증명을 사용 하 여 다음 명령을 실행 합니다.

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>이 암호는 현재 OpenShift 프로젝트 (Kubernetes 네임 스페이스)에 저장 되며 해당 프로젝트에서 만든 pod만 참조 가능한 됩니다.  클러스터 전체 끌어오기 암호를 만드는 방법에 대 한 자세한 내용은이 [문서](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) 를 참조 하세요.

## <a name="create-a-pod-using-a-private-registry-image"></a>개인 레지스트리 이미지를 사용 하 여 pod 만들기

이제 ARO 클러스터를 ACR에 연결 했으므로 ACR에서 이미지를 가져와서 pod를 만듭니다.

PodSpec로 시작 하 고 imagePullSecret로 만든 암호를 지정 합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Pod가 실행 중인지 테스트 하려면 다음 명령을 실행 하 고 상태가 **실행 중** 이 될 때까지 기다립니다.

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>다음 단계

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [빠른 시작: Azure CLI를 사용하여 프라이빗 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-azure-cli.md)
