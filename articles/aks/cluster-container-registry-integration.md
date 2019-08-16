---
title: Azure Kubernetes Service와 Azure Container Registry 통합
description: ACR (Kubernetes Service) Azure Container Registry를 사용 하 여 AKS (Azure Service)를 통합 하는 방법 알아보기
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: ec017901e36a01042485e9aeca2431c8a6838ab8
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536756"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>미리 보기-Azure Kubernetes Service에서 Azure Container Registry 인증

AKS(Azure Kubernetes Service)에서 ACR(Azure Container Registry)을 사용할 때는 인증 메커니즘을 설정해야 합니다. 이 문서에서는 이러한 두 가지 Azure 서비스 간 인증을 위해 권장되는 구성에 대해 설명합니다.

Azure CLI를 사용 하 여 몇 가지 간단한 명령에서 ACR 통합에 AKS를 설정할 수 있습니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책](support-policies.md)
> * [Azure 지원 FAQ](faq.md)

## <a name="before-you-begin"></a>시작하기 전 주의 사항

다음이 있어야 합니다.

* **Azure 구독** 에 대 한 **소유자** 또는 **azure 계정 관리자** 역할
* Azure CLI 버전 2.0.70 이상 및 aks-preview 0.4.8 확장도 필요 합니다.
* 클라이언트에 [docker가 설치 되어](https://docs.docker.com/install/) 있어야 하며 [docker 허브](https://hub.docker.com/) 에 액세스할 수 있어야 합니다.

## <a name="install-latest-aks-cli-preview-extension"></a>최신 AKS CLI 미리 보기 확장 설치

**Aks-preview 0.4.8** extension 이상이 필요 합니다.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>ACR 통합을 사용 하 여 새 AKS 클러스터 만들기

AKS 클러스터를 처음 만들 때 AKS 및 ACR 통합을 설정할 수 있습니다.  AKS 클러스터가 ACR과 상호 작용할 수 있도록 Azure Active Directory **서비스 주체가** 사용 됩니다. 다음 CLI 명령은 지정 하는 리소스 그룹에 ACR을 만들고 서비스 주체에 대 한 적절 한 **Acrpull** 역할을 구성 합니다. *Acr 이름이* 없으면의 `aks<resource-group>acr` 기본 acr 이름이 자동으로 생성 됩니다.  아래 매개 변수에 대 한 유효한 값을 제공 합니다.  대괄호 안의 매개 변수는 선택 사항입니다.
```azurecli
az login
az aks create -n myAKSCluster -g myResourceGroup --enable-acr [--acr <acr-name-or-resource-id>]
```
이 단계를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-acr-integration-for-existing-aks-clusters"></a>기존 AKS 클러스터에 대 한 ACR 통합 만들기

아래에 **acr 이름** 및 **acr-리소스 id** 에 대 한 유효한 값을 제공 하 여 acr을 기존 acr 클러스터와 통합 합니다.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acr-resource-id>
```

## <a name="log-in-to-your-acr"></a>ACR에 로그인

다음 명령을 사용 하 여 ACR에 로그인 합니다.  매개 변수 <acrname> 를 ACR 이름으로 바꿉니다.  예를 들어 기본값은 **aks > acr <** 입니다.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Docker 허브에서 이미지를 끌어오고 ACR에 푸시

Docker 허브에서 이미지를 끌어오거나, 이미지에 태그를 만들고, ACR로 푸시합니다.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>상태를 업데이트 하 고 pod를 확인 합니다.

배포를 확인 하려면 다음 단계를 수행 합니다.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Yaml 파일을 보고, 해당 값을 ACR 로그인 서버, 이미지 및 태그로 바꿔서 이미지 속성을 편집 합니다.

```
$ cat acr-nginx.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create