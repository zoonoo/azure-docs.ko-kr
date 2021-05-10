---
title: 핵심 구 추출 Kubernetes 구성 및 배포 단계
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 Kubernetes 구성 및 배포 단계
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 27c78566877f27e80ae5ae27c5250f228c7ae676
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96017878"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>AKS 클러스터에 핵심 구 추출 컨테이너 배포

1. Azure CLI를 열고 Azure에 로그인합니다.

    ```azurecli
    az login
    ```

1. AKS 클러스터에 로그인합니다. `your-cluster-name` 및 `your-resource-group`를 해당 값으로 바꿉니다.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    이 명령을 실행하면 다음과 유사한 메시지가 보고됩니다.

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure 계정에서 여러 구독을 사용할 수 있고 `az aks get-credentials` 명령이 오류와 함께 반환되는 경우 일반적인 문제는 잘못된 구독을 사용하고 있는 것입니다. 리소스를 만든 것과 동일한 구독을 사용하도록 Azure CLI 세션의 컨텍스트를 설정하고 다시 시도하세요.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 선택한 텍스트 편집기를 엽니다. 이 예제에서는 Visual Studio Code를 사용합니다.

    ```console
    code .
    ```

1. 텍스트 편집기 내에서 *keyphrase.yaml* 이라는 새 파일을 만들고 다음 YAML을 붙여넣습니다. `billing/value` 및 `apikey/value`를 사용자 고유의 정보로 바꿉니다.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. 파일을 저장하고 텍스트 편집기를 닫습니다.
1. *keyphrase.yaml* 파일을 대상으로 하여 Kubernetes `apply` 명령을 실행합니다.

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    명령이 배포 구성을 성공적으로 적용하면 다음 출력과 비슷한 메시지가 표시됩니다.

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Pod가 배포되었는지 확인합니다.

    ```console
    kubectl get pods
    ```

    Pod의 실행 상태에 대한 출력:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 서비스를 사용할 수 있는지 확인하고 IP 주소를 가져옵니다.

    ```console
    kubectl get services
    ```

    Pod에서 *keyphrase* 서비스의 실행 상태에 대한 출력:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
