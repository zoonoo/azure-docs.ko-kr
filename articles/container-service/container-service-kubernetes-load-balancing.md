---
title: "Azure에서 Kubernetes 컨테이너 부하 분산 | Microsoft Docs"
description: "외부에서 연결하고 Azure Container Service의 Kubernetes 클러스터에서 여러 컨테이너 간에 부하를 분산합니다."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "컨테이너, 마이크로 서비스, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: e89ec01cb47a87a45378f73d138224095bcbebed
ms.openlocfilehash: 201d98c4f4ff29393ad308824ed0575f1ff602ee
ms.lasthandoff: 02/27/2017


---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>Azure Container Service의 Kubernetes 클러스터에서 컨테이너 부하 분산 
이 문서에서는 Azure Container Service의 Kubernetes 클러스터에서 부하를 분산하는 과정을 소개합니다. 부하 분산 서비스는 서비스에 대해 외부에서 액세스할 수 있는 IP 주소를 제공하고 에이전트 VM에서 실행되는 Pod 간에 네트워크 트래픽을 분산합니다.

Kubernetes 서비스가 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)를 사용하도록 설정하여 외부 네트워크(TCP 또는 UDP) 트래픽을 관리할 수 있습니다. 추가 구성을 통해 HTTP 또는 HTTPS 트래픽의 부하 분산 및 라우팅이나 좀 더 고급 시나리오도 가능합니다.

## <a name="prerequisites"></a>필수 조건
* Azure Container Service에서 [Kubernetes 클러스터 배포](container-service-kubernetes-walkthrough.md)
* 클러스터에 [클라이언트 연결](container-service-connect.md)

## <a name="azure-load-balancer"></a>Azure Load Balancer

기본적으로 Azure Container Service에 배포된 Kubernetes 클러스터에는 에이전트 VM에 대한 인터넷 연결 Azure Load Balancer가 포함됩니다. (마스터 VM에 대해서는 별도 부하 분산 장치 리소스가 구성됩니다.) Azure Load Balancer는 계층 4(TCP, UDP) 부하 분산 장치입니다.

Kubernetes 서비스를 만드는 경우 서비스에 대한 액세스를 허용하도록 Azure Load Balancer를 자동으로 구성할 수 있습니다. 부하 분산 장치를 구성하려면 서비스 `type`을 `LoadBalancer`로 설정합니다. 부하 분산 장치는 들어오는 서비스 트래픽의 공용 IP 주소 및 포트 번호를 에이전트 VM에 있는 Pod의 개인 IP 주소 및 포트 번호로 매핑(응답 트래픽의 경우 반대 방향으로 매핑)하는 규칙을 만듭니다. 

 다음은 Kubernetes 서비스 `type`을 `LoadBalancer`로 설정하는 방법을 보여 주는 두 가지 예제입니다. (예제를 시도한 후 더 이상 필요하지 않은 배포는 삭제합니다.)

### <a name="example-use-the-kubectl-expose-command"></a>예제: `kubectl expose` 명령 사용 
[Kubernetes 연습](container-service-kubernetes-walkthrough.md)에는 `kubectl expose` 명령 및 해당 `--type=LoadBalancer` 플래그를 사용하여 서비스를 노출하는 방법의 예제가 포함되어 있습니다. 단계는 다음과 같습니다.

1. 새 컨테이너 배포를 시작합니다. 예를 들어 다음 명령은 `mynginx`라는 새 배포를 시작합니다. 배포에는 Nginx 웹 서버에 대한 Docker 이미지를 기준으로 하는 세 개의 컨테이너로 구성됩니다.

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. 컨테이너가 실행 중인지 확인합니다. 예를 들어 `kubectl get pods`를 사용하여 컨테이너를 쿼리하면 다음과 유사한 출력이 표시됩니다.

    ![Nginx 컨테이너 가져오기](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. 배포에 대한 외부 트래픽을 허용하도록 부하 분산 장치를 구성하려면 `--type=LoadBalancer`를 사용하여 `kubectl expose`를 실행합니다. 다음 명령은 포트 80에서 Nginx 서버를 노출합니다.

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. `kubectl get svc`를 입력하여 클러스터의 서비스 상태를 표시합니다. 부하 분산 장치가 규칙을 구성하는 동안 서비스의 `EXTERNAL-IP`가 `<pending>`으로 표시됩니다. 몇 분 후에 외부 IP 주소가 구성됩니다. 

    ![Azure Load Balancer 구성](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. 서비스에 외부 IP 주소로 액세스할 수 있는지 확인합니다. 예를 들어 표시되는 IP 주소로 웹 브라우저를 엽니다. 브라우저는 컨테이너 중 하나에서 실행되는 Nginx 웹 서버를 표시합니다. 또는 `curl`이나 `wget` 명령을 실행합니다. 예:

    ```
    curl 13.82.93.130
    ```

    다음과 유사한 결과가 표시됩니다.

    ![curl을 사용하여 Nginx에 액세스](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. Azure Load Balancer의 구성을 보려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

7. 컨테이너 서비스 클러스터의 리소스 그룹을 찾은 후 에이전트 VM에 대한 부하 분산 장치를 선택합니다. 해당 이름은 컨테이너 서비스와 동일해야 합니다. (해당 이름에 **master-lb**가 포함된 마스터 노드에 대한 부하 분산 장치는 선택하지 마세요.) 

    ![리소스 그룹의 부하 분산 장치](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. 부하 분산 장치 구성의 세부 정보를 보려면 **부하 분산 장치 규칙** 및 구성된 규칙의 이름을 클릭합니다.

    ![부하 분산 장치 규칙](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>예제: 서비스 구성 파일에 `type: LoadBalancer` 지정

YAML 또는 JSON [서비스 구성 파일](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file)에서 Kubernetes 컨테이너 앱을 배포하는 경우 서비스 사양에 다음 줄을 추가하여 외부 부하 분산 장치를 지정합니다.

```YAML
 "type": "LoadBalancer"
``` 



다음 단계에서는 Kubernetes [방명록 예제](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook)를 사용합니다. 이 예제는 Redis 및 PHP Docker 이미지를 기준으로 하는 다중 계층 웹앱입니다. 서비스 구성 파일에서 프런트 엔드 PHP 서버가 Azure Load Balancer를 사용하도록 지정할 수 있습니다.

1. [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one)에서 `guestbook-all-in-one.yaml` 파일을 다운로드합니다. 
2. `frontend` 서비스에 대한 `spec`을 찾아봅니다.
3. `type: LoadBalancer` 줄의 주석 처리를 제거합니다.

    ![서비스 구성의 부하 분산 장치](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. 파일을 저장하고 다음 명령을 실행하여 앱을 배포합니다.

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. `kubectl get svc`를 입력하여 클러스터의 서비스 상태를 표시합니다. 부하 분산 장치가 규칙을 구성하는 동안 `frontend` 서비스의 `EXTERNAL-IP`가 `<pending>`으로 표시됩니다. 몇 분 후에 외부 IP 주소가 구성됩니다. 

    ![Azure Load Balancer 구성](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. 서비스에 외부 IP 주소로 액세스할 수 있는지 확인합니다. 예를 들어 서비스의 외부 IP 주소로 웹 브라우저를 열 수 있습니다.

    ![외부에서 방명록 액세스](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    방명록 항목을 추가할 수 있습니다.

7. Azure Load Balancer의 구성을 보려면 [Azure Portal](https://portal.azure.com)에서 클러스터에 대한 부하 분산 장치 리소스를 찾습니다. 이전 예제의 단계를 참조하세요.

### <a name="considerations"></a>고려 사항

* 부하 분산 장치 규칙은 비동기적으로 생성되고 프로비전된 분산 장치에 대한 정보는 서비스의 `status.loadBalancer` 필드에 게시됩니다.
* 모든 서비스는 부하 분산 장치의 자체 가상 IP 주소에 자동으로 할당됩니다.
* DNS 이름으로 부하 분산 장치에 연결하려는 경우 도메인 서비스 공급자와 함께 규칙의 IP 주소에 대한 DNS 이름을 만듭니다.

## <a name="http-or-https-traffic"></a>HTTP 또는 HTTPS 트래픽

컨테이너 웹앱으로 HTTP 또는 HTTPS 트래픽 부하를 분산하고 TLS(전송 계층 보안)에 대한 인증서를 관리하려면 Kubernetes [수신](https://kubernetes.io/docs/user-guide/ingress/) 리소스를 사용합니다. 수신은 인바운드 연결이 클러스터 서비스에 연결하도록 하는 규칙 컬렉션입니다. 수신 리소스가 작동하려면 Kubernetes 클러스터에서 [수신 컨트롤러](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers)가 실행되고 있어야 합니다.

Azure Container Service는 Kubernetes 수신 컨트롤러를 자동으로 구현하지 않습니다. 여러 컨트롤러 구현을 사용할 수 있습니다. 현재 수신 규칙을 구성하고 HTTP 및 HTTPS 트래픽 부하를 분산하기 위해 [Nginx 수신 컨트롤러](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx)가 권장됩니다. 

자세한 내용은 [Nginx 수신 컨트롤러 설명서](https://github.com/kubernetes/ingress/tree/master/controllers/nginx/README.md)를 참조하세요.

> [!IMPORTANT]
> Azure Container Service에서 Nginx 수신 컨트롤러를 사용하려면 `type: LoadBalancer`를 사용하여 컨트롤러 배포를 서비스로 노출해야 합니다. 이렇게 하면 Azure Load Balancer가 해당 컨트롤러로 트래픽을 라우팅하도록 구성됩니다. 자세한 내용은 이전 섹션을 참조하세요.


## <a name="next-steps"></a>다음 단계

* [Kubernetes LoadBalancer 설명서](https://kubernetes.io/docs/user-guide/load-balancer/) 참조
* [Kubernetes 송/수신 컨트롤러](https://kubernetes.io/docs/user-guide/ingress/)에 대해 자세히 알아보기
* [Kubernetes 예제](https://github.com/kubernetes/kubernetes/tree/master/examples) 참조


