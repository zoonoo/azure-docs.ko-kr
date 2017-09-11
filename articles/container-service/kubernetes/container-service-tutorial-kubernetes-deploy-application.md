---
title: "Azure Container Service 자습서 - 응용 프로그램 배포 | Microsoft Docs"
description: "Azure Container Service 자습서 - 응용 프로그램 배포"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: ko-kr
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>Kubernetes에서 응용 프로그램 실행

7개 중 4단계인 이 자습서에서는 응용 프로그램 예제를 Kubernetes 클러스터에 배포합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Kubernetes 매니페스트 파일 다운로드
> * Kubernetes에서 응용 프로그램 실행
> * 응용 프로그램 테스트

후속 자습서에서는 이 응용 프로그램을 스케일 아웃하고 업데이트하며, Kubernetes 클러스터를 모니터링하도록 Operations Management Suite를 구성합니다.

이 자습서에서는 Kubernetes 개념에 대한 기본적인 이해가 있다고 가정하며 Kubernetes에 대한 자세한 정보는 [Kubernetes 설명서](https://kubernetes.io/docs/home/)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이러한 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다. 이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

최소한 이 자습서에는 Kubernetes 클러스터가 필요합니다.

## <a name="get-manifest-file"></a>매니페스트 파일 가져오기

이 자습서에서는 Kubernetes 매니페스트를 사용하여 [Kubernetes 개체](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)를 배포합니다. Kubernetes 매니페스트는 Kubernetes 개체 배포 및 구성 지침이 포함된 YAML 또는 JSON 형식이 지정된 파일입니다.

이 자습서의 응용 프로그램 매니페스트 파일은 이전 자습서에서 복제된 Azure Vote 응용 프로그램 리포지토리에서 제공됩니다. 아직 이 리포지토리를 복제하지 않은 경우 다음 명령을 사용하여 리포지토리를 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

매니페스트 파일은 복제된 리포지토리의 다음 디렉터리에 있습니다.

```bash
/azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yml
```

## <a name="update-manifest-file"></a>매니페스트 파일 업데이트

Azure Container Registry를 사용하여 컨테이너 이미지를 저장하는 경우 매니페스트를 ACR loginServer 이름으로 업데이트해야 합니다.

[az acr list](/cli/azure/acr#list) 명령을 사용하여 ACR 로그인 서버 이름을 가져옵니다.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

샘플 매니페스트를 *microsoft*라는 리포지토리 이름으로 미리 만들었습니다. 텍스트 편집기로 파일을 열고 *microsoft* 값을 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>응용 프로그램 배포

[kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) 명령을 사용하여 응용 프로그램을 실행합니다. 이 명령은 매니페스트 파일을 구문 분석하고 정의된 Kubernetes 개체를 만듭니다.

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yml
```

출력:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>응용 프로그램 테스트

인터넷에 응용 프로그램을 노출하는 [Kubernetes 서비스](https://kubernetes.io/docs/concepts/services-networking/service/)가 생성됩니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다. 

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) 명령을 사용합니다.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

처음에는 *azure-vote-front* 서비스에 대한 **EXTERNAL-IP**가 *보류 중*으로 표시됩니다. EXTERNAL-IP 주소가 *보류 중*에서 *IP 주소*로 변경되면 `CTRL-C`를 사용하여 kubectl 조사식 프로세스를 중지합니다.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

응용 프로그램을 보려면 외부 IP 주소로 이동합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 투표 응용 프로그램을 Azure Container Service Kubernetes 클러스터에 배포했습니다. 완료된 작업은 다음과 같습니다.  

> [!div class="checklist"]
> * Kubernetes 매니페스트 파일 다운로드
> * Kubernetes에서 응용 프로그램 실행
> * 응용 프로그램 테스트

다음 자습서로 이동하여 Kubernetes 응용 프로그램과 기본 Kubernetes 인프라를 모두 크기 조정하는 방법에 대해 알아봅니다. 

> [!div class="nextstepaction"]
> [Kubernetes 응용 프로그램 및 인프라 크기 조정](./container-service-tutorial-kubernetes-scale.md)
