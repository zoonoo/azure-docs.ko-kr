---
title: Azure Red Hat OpenShift에서 Azure Container Registry 사용
description: Azure Red Hat OpenShift 클러스터에서 Azure Container Registry로부터 컨테이너를 끌어오고 실행하는 방법을 알아봅니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634416"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>ARO(Azure Red Hat OpenShift)에서 Azure Container Registry 사용

ACR(Azure Container Registry)은 지역 복제와 같은 엔터프라이즈 기능을 사용하여 프라이빗 Docker 컨테이너 이미지를 저장하는 데 사용할 수 있는 관리형 컨테이너 레지스트리 서비스입니다. ARO 클러스터에서 ACR에 액세스하기 위해 클러스터는 Kubernetes 비밀에 Docker 로그인 자격 증명을 저장하여 ACR에서 인증할 수 있습니다.  마찬가지로, ARO 클러스터는 이미지를 끌어올 때 Pod 사양에서 imagePullSecret을 사용하여 레지스트리에서 인증할 수 있습니다. 이 문서에서는 프라이빗 Docker 컨테이너 이미지를 저장하고 끌어오기 위해 Azure Red Hat OpenShift 클러스터를 사용하여 Azure Container Registry를 설정하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 가이드에서는 기존 Azure Container Registry가 있다고 가정합니다. 그렇지 않은 경우 Azure Portal 또는 [Azure CLI 지침](../container-registry/container-registry-get-started-azure-cli.md)을 사용하여 컨테이너 레지스트리를 만듭니다.

또한 이 문서에서는 기존 Azure Red Hat OpenShift 클러스터가 있고 `oc` CLI가 설치되어 있다고 가정합니다. 그렇지 않은 경우 [ARO 클러스터 만들기 자습서](tutorial-create-cluster.md)의 지침을 따르세요.

## <a name="get-a-pull-secret"></a>끌어오기 비밀 가져오기

ARO 클러스터에서 레지스트리에 액세스하려면 ACR의 끌어오기 비밀이 필요합니다.

끌어오기 비밀 자격 증명을 가져오려면 Azure Portal 또는 Azure CLI를 사용할 수 있습니다.

Azure Portal을 사용하는 경우 ACR 인스턴스로 이동하여 **액세스 키** 를 선택합니다.  `docker-username`은 컨테이너 레지스트리의 이름이며 `docker-password`에는 password 또는 password2를 사용 합니다.

![액세스 키](./media/acr-access-keys.png)

대신, Azure CLI를 사용하여 이러한 자격 증명을 가져올 수 있습니다.
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Kubernetes 비밀 만들기

이제 이러한 자격 증명을 사용하여 Kubernetes 비밀을 만듭니다. ACR 자격 증명을 사용하여 다음 명령을 실행합니다.

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>이 비밀은 현재 OpenShift 프로젝트(Kubernetes 네임스페이스)에 저장되며 해당 프로젝트에서 만든 Pod만 참조할 수 있습니다.  클러스터 전체 끌어오기 비밀을 만드는 방법에 대한 자세한 내용은 이 [문서](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html)를 참조하세요.

## <a name="create-a-pod-using-a-private-registry-image"></a>프라이빗 레지스트리 이미지를 사용하여 Pod 만들기

이제 ARO 클러스터를 ACR에 연결했으므로 ACR에서 이미지를 가져와서 Pod를 만들겠습니다.

podSpec으로 시작하고 imagePullSecret으로 만든 비밀을 지정합니다.

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

Pod가 실행 중인지 테스트하려면 다음 명령을 실행하고 상태가 **실행 중** 이 될 때까지 기다립니다.

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>다음 단계

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [빠른 시작: Azure CLI를 사용하여 프라이빗 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-azure-cli.md)
