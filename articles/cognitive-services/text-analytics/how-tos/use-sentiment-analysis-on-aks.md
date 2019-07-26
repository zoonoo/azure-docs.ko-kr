---
title: Azure Kubernetes Service 실행
titleSuffix: Text Analytics - Azure Cognitive Services
description: 감정 분석 이미지를 사용 하 여 Text Analytics 컨테이너를 Azure Kubernetes Service에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 290a01e7e478f718607c0550702474cd31979a63
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377432"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>감정 분석 컨테이너를 Azure Kubernetes Service에 배포

감정 분석 이미지를 사용 하 여 Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) 컨테이너를 AKS (Azure Kubernetes Service)에 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 Text Analytics 리소스를 만드는 방법, 연결 된 감정 분석 이미지를 만드는 방법 및 브라우저에서 두 작업의 오케스트레이션을 실행 하는 방법을 보여 줍니다. 컨테이너를 사용 하면 인프라 관리에서 응용 프로그램 개발에 집중 하는 대신 주의를 끌 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다. Azure Cloud Shell 사용 하지 마세요. 다음이 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 텍스트 편집기 (예: [Visual Studio Code](https://code.visualstudio.com/download)).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 설치 되었습니다.
* [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 가 설치 되어 있습니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
    * F0 또는 표준 가격 책정 계층을 사용 하는 **Azure Text Analytics** 리소스
    * S0 가격 책정 계층을 사용 하는 **Azure Cognitive Services** 리소스

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>AKS 클러스터에 Text Analytics 컨테이너 배포

1. Azure CLI를 열고 Azure에 로그인 합니다.

    ```azurecli
    az login
    ```

1. AKS 클러스터에 로그인 합니다. `your-cluster-name` 및`your-resource-group` 을 적절 한 값으로 바꿉니다.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    이 명령이 실행 되 면 다음과 유사한 메시지가 보고 됩니다.

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure 계정에서 여러 구독을 사용할 수 있고 `az aks get-credentials` 명령이 오류와 함께 반환 되는 경우 일반적인 문제는 잘못 된 구독을 사용 하는 것입니다. 을 사용 하 여 리소스를 만든 구독과 동일한 구독을 사용 하도록 Azure CLI 세션의 컨텍스트를 설정 하 고 다시 시도 하세요.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 선택한 텍스트 편집기를 엽니다. 이 예제에서는 Visual Studio Code를 사용 합니다.

    ```azurecli
    code .
    ```

1. 텍스트 편집기 내에서 _감정_라는 새 파일을 만들고 다음 yaml를이 파일에 붙여넣습니다. `billing/value` 및`apikey/value` 을 사용자의 정보로 바꾸어야 합니다.

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
1. 감정를 대상 `apply` 으로 하  여 Kubernetes 명령을 실행 합니다.

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    명령에서 배포 구성을 성공적으로 적용 한 후에는 다음 출력과 비슷한 메시지가 표시 됩니다.

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Pod가 배포 되었는지 확인 합니다.

    ```console
    kubectl get pods
    ```

    Pod의 실행 상태에 대 한 출력입니다.

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 서비스를 사용할 수 있는지 확인 하 고 IP 주소를 가져옵니다.

    ```console
    kubectl get services
    ```

    Pod에서 _감정_ 서비스의 실행 상태에 대 한 출력입니다.

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>다음 단계

* 더 많은 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md) 사용
* [Text Analytics 연결 된 서비스](../vs-text-connected-service.md) 사용
