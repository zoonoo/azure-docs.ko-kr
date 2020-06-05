---
title: 핵심 구 추출 Kubernetes 구성 및 배포 단계
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 Kubernetes 구성 및 배포 단계
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6ef7efe3d48fd20c5141803430260a80395faa82
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877840"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>AKS 클러스터에 핵심 구 추출 컨테이너 배포

1. Azure CLI를 열고 Azure에 로그인 합니다.

    ```azurecli
    az login
    ```

1. AKS 클러스터에 로그인 합니다. `your-cluster-name`및을 `your-resource-group` 적절 한 값으로 바꿉니다.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    이 명령이 실행 되 면 다음과 유사한 메시지가 보고 됩니다.

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure 계정에서 여러 구독을 사용할 수 있고 `az aks get-credentials` 명령이 오류와 함께 반환 되는 경우 일반적인 문제는 잘못 된 구독을 사용 하는 것입니다. 을 사용 하 여 리소스를 만든 구독과 동일한 구독을 사용 하도록 Azure CLI 세션의 컨텍스트를 설정 하 고 다시 시도 하세요.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 선택한 텍스트 편집기를 엽니다. 이 예제에서는 Visual Studio Code를 사용 합니다.

    ```console
    code .
    ```

1. 텍스트 편집기 내에서 *키 구*라는 새 파일을 만들고 다음 yaml를이 파일에 붙여넣습니다. `billing/value`및을 `apikey/value` 사용자의 정보로 바꾸어야 합니다.

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

1. 파일을 저장 하 고 텍스트 편집기를 닫습니다.
1. `apply` *키 구* 파일을 대상으로 하 여 Kubernetes 명령을 실행 합니다.

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    명령에서 배포 구성을 성공적으로 적용 한 후에는 다음 출력과 비슷한 메시지가 표시 됩니다.

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Pod가 배포 되었는지 확인 합니다.

    ```console
    kubectl get pods
    ```

    Pod의 실행 상태에 대 한 출력입니다.

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 서비스를 사용할 수 있는지 확인 하 고 IP 주소를 가져옵니다.

    ```console
    kubectl get services
    ```

    Pod에서 *키 구* 서비스의 실행 상태에 대 한 출력입니다.

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
