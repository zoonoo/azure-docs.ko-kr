---
title: Azure Kubernetes 서비스를 실행 합니다.
titleSuffix: Text Analytics - Azure Cognitive Services
description: Azure Kubernetes 서비스에 감정 분석 이미지를 사용 하 여 텍스트 분석 컨테이너를 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a419ed3b9c0d2c4db9c552642dc5c662786f6730
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561245"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Azure Kubernetes 서비스 (AKS)를 감정 분석 컨테이너 배포

Cognitive Services를 배포 하는 방법을 알아봅니다 [텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) 감정 분석 이미지를 Azure Kubernetes 서비스 (AKS)를 사용 하 여 컨테이너입니다. 이 절차 Text Analytics 리소스 만들기, 두 브라우저에서이 오케스트레이션이 실행 하는 기능과 연결 된 감정 분석 이미지 만들기를 보여 줍니다. 컨테이너를 사용 하 여 응용 프로그램 개발에 집중 하는 대신에 인프라를 관리에서 개발자의 주의 이동할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다. Azure Cloud Shell은 사용하지 않도록 합니다.

* Azure 구독을 사용합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 예를 들어 텍스트 편집기: [Visual Studio Code](https://code.visualstudio.com/download)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치합니다.
* 설치 합니다 [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)합니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
    * F0 또는 표준 가격 책정 계층만 있는 **Text Analytics** 리소스
    * S0 가격 책정 계층이 있는 **Cognitive Services** 리소스

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>텍스트 분석 컨테이너를 AKS 클러스터 배포

1. Azure CLI 및 로그인을 열고 Azure에

    ```azurecli
    az login
    ```

1. AKS 클러스터에 로그인 (대체 합니다 `your-cluster-name` 고 `your-resource-group` 적절 한 값을 사용 하 여)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    이 명령은 실행 한 후에 다음과 유사한 메시지를 보고 합니다.

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure 계정에 사용할 수 있는 여러 구독이 있는 경우 및 `az aks get-credentials` 명령 오류가 반환, 일반적인 문제는 잘못 된 구독을 사용 하는 것입니다. 사용 하 여 리소스를 만든 동일한 구독을 사용 하 여 Azure CLI 세션의 컨텍스트를 설정 하 고 다시 시도 하면 됩니다.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 원하는 텍스트 편집기를 엽니다 (이 예제에서는 __Visual Studio Code__):

    ```azurecli
    code .
    ```

1. 텍스트 편집기에서 이라는 새 파일을 만듭니다 _sentiment.yaml_ 다음 YAML 붙여 넣습니다. 로 대체 해야 합니다 `billing/value` 및 `apikey/value` 고유의 합니다.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. 파일을 저장 하 고 텍스트 편집기를 닫습니다.
1. Kubernetes를 실행 `apply` 명령에 _sentiment.yaml_ 을 대상으로 합니다.

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    명령 다음에 적용 되었습니다. 배포 구성은 다음 출력과 유사한 메시지:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. POD 배포 되었는지 확인 합니다.

    ```console
    kubectl get pods
    ```

    이 POD의 실행 상태를 출력 합니다.

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 서비스를 사용할 수 있는지 확인 하 고 IP 주소를 가져옵니다.

    ```console
    kubectl get services
    ```

    실행 상태를 출력 합니다 _감정_ POD에서 서비스:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>다음 단계

* 더 많은 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md)를 사용합니다.
* 사용 된 [Text Analytics에 연결 된 서비스](../vs-text-connected-service.md)
