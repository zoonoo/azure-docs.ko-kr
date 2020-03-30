---
title: 애플리케이션 게이트웨이 인그레스 컨트롤러 문제 해결
description: 이 문서에서는 응용 프로그램 게이트웨이 인그레스 컨트롤러에서 일반적인 질문 및/또는 문제를 해결하는 방법에 대한 설명서를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795506"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>인그레스 컨트롤러에서 일반적인 질문이나 문제 해결

[Azure 클라우드 셸은](https://shell.azure.com/) AKS 및 AGIC 설치와 관련된 문제를 해결하는 가장 편리한 방법입니다. [shell.azure.com](https://shell.azure.com/) 또는 링크를 클릭하여 셸을 시작합니다.

[![포함 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>간단한 Kubernetes 응용 프로그램과 함께 테스트

아래 단계는 다음과 같습니다.
  - 고급 네트워킹을 사용하도록 설정한 AKS 클러스터가 있습니다.
  - AGIC가 AKS 클러스터에 설치되었습니다.
  - AKS 클러스터와 공유되는 VNET에서 응용 프로그램 게이트웨이를 이미 사용했습니다.

응용 프로그램 게이트웨이 + AKS + AGIC 설치가 올바르게 설정되어 있는지 확인하려면 가능한 가장 간단한 앱을 배포하십시오.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

위의 스크립트에서 모든 줄을 한 번에 복사하여 [Azure Cloud Shell에](https://shell.azure.com/)붙여넣습니다. 마지막 명령부터 `cat` 시작하여 전체 `EOF`명령이 복사되었는지 확인하십시오.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

AKS 클러스터 위에 앱을 성공적으로 배포하면 새 Pod, Service 및 Ingress가 추가됩니다.

[클라우드 쉘과](https://shell.azure.com/)포드의 목록을 `kubectl get pods -o wide`가져옵니다 : .
'테스트 agic-app-pod'라는 포드가 생성될 것으로 예상됩니다. IP 주소가 있습니다. 이 주소는 AKS와 함께 사용되는 응용 프로그램 게이트웨이의 VNET 내에 있어야 합니다.

![포드](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

서비스 목록을 가져옵니다. `kubectl get services -o wide` 우리는 '테스트 agic-앱 서비스'라는 서비스를 볼 것으로 예상.

![포드](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

받는 사람의 목록을 가져옵니다. `kubectl get ingress` '테스트-agic-app-ingress'라는 이름의 Ingress 리소스가 생성될 것으로 예상됩니다. 리소스에는 호스트 이름 'test.agic.contoso.com'이 있습니다.

![포드](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

포드 중 하나는 AGIC입니다. `kubectl get pods`포드 목록이 표시되며, 그 중 하나는 '인그레스-azure'로 시작됩니다. 해당 포드의 `kubectl logs <name-of-ingress-controller-pod>` 모든 로그를 사용하여 성공적으로 배포되었는지 확인합니다. 성공적으로 배포하면 로그에 다음 줄이 추가되었을 것입니다.
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

또는 Cloud [Shell에서](https://shell.azure.com/) AGIC 포드의 정확한 이름이 있어야 `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` `<ingress-azure....>` 하는 위치에서 성공적인 응용 프로그램 게이트웨이 구성을 나타내는 줄만 검색할 수 있습니다.

응용 프로그램 게이트웨이에는 다음과 같은 구성이 적용됩니다.

- 청취자: ![청취자](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- 라우팅 규칙: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- 백엔드 풀:
  - 백 엔드 주소 풀에 하나의 IP 주소가 있으며 앞에서 관찰한 Pod의 `kubectl get pods -o wide` 
 ![IP 주소와 backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


마지막으로 [Cloud](https://shell.azure.com/) Shell `cURL` 내에서 명령을 사용하여 새로 배포된 앱에 대한 HTTP 연결을 설정할 수 있습니다.

1. 응용 `kubectl get ingress` 프로그램 게이트웨이의 공용 IP 주소를 얻는 데 사용
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` 사용

![포드](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

그 결과 `HTTP/1.1 200 OK` 응용 프로그램 게이트웨이 + AKS + AGIC 시스템이 예상대로 작동하고 있음을 나타냅니다.


## <a name="inspect-kubernetes-installation"></a>쿠베르네테스 설치 검사

### <a name="pods-services-ingress"></a>포드, 서비스, 인그레스
응용 프로그램 게이트웨이 침투 컨트롤러 (AGIC) 지속적으로 다음 Kubernetes 리소스모니터링: [배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) 또는 [포드,](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod) [서비스,](https://kubernetes.io/docs/concepts/services-networking/service/) [침투](https://kubernetes.io/docs/concepts/services-networking/ingress/)


AGIC가 예상대로 작동하려면 다음이 있어야 합니다.
  1. AKS에는 하나 이상의 건강한 포드가 있어야 **합니다.**
     의 Pod가 있는 `kubectl get pods -o wide --show-labels` 경우 다음과 같이 출력이 있을 수 있습니다. [Cloud Shell](https://shell.azure.com/) `apsnetapp`
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 하나 이상의 **서비스**, 일치하는 `selector` 레이블을 통해 위의 포드를 참조합니다.
     [클라우드 셸에서](https://shell.azure.com/) 이를 확인합니다.`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress,** 에 의해 `kubernetes.io/ingress.class: azure/application-gateway`추가된 , 위의 서비스를 참조하여 [클라우드 셸에서](https://shell.azure.com/) 이를 확인합니다.`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 위의 사용 어칭 보기: `kubectl get ingress aspnetapp -o yaml` (사용 의 `aspnetapp` 이름으로 대체)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     인그레스 리소스에 추가해야 `kubernetes.io/ingress.class: azure/application-gateway`합니다.
 

### <a name="verify-observed-namespace"></a>관찰된 네임스페이스 확인

* Kubernetes 클러스터의 기존 네임스페이스를 가져옵니다. 앱이 실행되는 네임스페이스는 무엇입니까? AGIC가 해당 네임스페이스를 보고 있습니까? 관찰된 [네임스페이스를](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) 올바르게 구성하는 방법에 대한 다중 네임스페이스 지원 설명서를 참조하십시오.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC 포드는 네임스페이스에 `default` 있어야 `NAMESPACE`합니다(열 참조). 정상 포드는 `Running` 열에 `STATUS` 있을 것입니다. 적어도 하나의 AGIC 포드가 있어야합니다.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* AGIC 포드가 정상적이지`STATUS` 않은 경우(위의 명령의 열은 정상이 아님): `Running`
  - 그 이유를 이해하기 위해 로그를 가져옵니다.`kubectl logs <pod-name>`
  - 포드의 이전 인스턴스에 대해:`kubectl logs <pod-name> --previous`
  - 더 많은 컨텍스트를 얻으려면 포드를 설명합니다.`kubectl describe pod <pod-name>`


* Kubernetes [서비스](https://kubernetes.io/docs/concepts/services-networking/service/) 및 [침투 리소스가](https://kubernetes.io/docs/concepts/services-networking/ingress/) 있습니까?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* 당신의 [인그레스는](https://kubernetes.io/docs/concepts/services-networking/ingress/) 다음과 함께 추가됩니다 : `kubernetes.io/ingress.class: azure/application-gateway`? AGIC는 이 추가가 있는 Kubernetes 침투 리소스에 대해서만 감시합니다.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC는 특정 중요한 오류에 대해 Kubernetes 이벤트를 내보전합니다. 다음을 볼 수 있습니다.
  - 터미널에서`kubectl get events --sort-by=.metadata.creationTimestamp`
  - [Kubernetes 웹 UI(대시보드)를](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 사용하여 브라우저에서


## <a name="logging-levels"></a>로깅 수준

AGIC에는 3개의 로깅 수준이 있습니다. 수준 1은 기본 수준이며 최소한의 로그 줄을 표시합니다.
반면 레벨 5는 ARM에 적용된 구성의 위생화된 내용을 포함하여 모든 로그를 표시합니다.

Kubernetes 커뮤니티는 [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) 도구에 대한 9 단계의 로깅을 설정했습니다. 이 리포지토리에서는 이 중 3가지를 유사한 의미체계로 활용하고 있습니다.


| 자세한 정도 | 설명 |
|-----------|-------------|
|  1        | 기본 로그 수준; 시작 세부 정보, 경고 및 오류를 표시합니다. |
|  3        | 이벤트 및 변경 에 대한 확장 정보; 생성된 객체 목록 |
|  5        | 마샬링된 개체를 로그합니다. ARM에 적용된 소독된 JSON 구성을 보여줍니다. |


자세한 수준은 `verbosityLevel` [helm-config.yaml](#sample-helm-config-file) 파일의 변수를 통해 조정할 수 있습니다. 자세한 수준을 증가하여 `5` JSON 구성을 [ARM에](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)디스패치하도록 합니다.
  - `verbosityLevel: 5` [helm-config.yaml에서](#sample-helm-config-file) 자체적으로 라인에 추가하고 다시 설치
  - 로그를 가져옵니다.`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>샘플 헬름 구성 파일
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

