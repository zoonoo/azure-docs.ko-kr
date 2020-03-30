---
title: 응용 프로그램 게이트웨이 인그리스 컨트롤러에 대한 다중 네임스페이스 지원 사용
description: 이 문서에서는 응용 프로그램 게이트웨이 침투 컨트롤러를 사용하여 Kubernetes 클러스터에서 여러 네임스페이스 지원을 활성화하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279924"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>애플리케이션 게이트웨이 인그레스 컨트롤러를 사용하여 AKS 클러스터에서 여러 네임스페이스 지원 지원 지원

## <a name="motivation"></a>동기
Kubernetes [네임스페이스를](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 통해 Kubernetes 클러스터를 분할하여 대규모 팀의 하위 그룹에 할당할 수 있습니다. 그런 다음 이러한 하위 팀은 리소스, 보안, 구성 등을 보다 세밀하게 제어하여 인프라를 배포하고 관리할 수 있습니다. Kubernetes를 사용하면 하나 이상의 침투 리소스를 각 네임스페이스 내에서 독립적으로 정의할 수 있습니다.

버전 0.7 [Azure 응용 프로그램 게이트웨이 Kubernetes 침투](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) 컨트롤러(AGIC)는 여러 네임스페이스에서 이벤트를 검색하고 관찰할 수 있습니다. AKS 관리자가 앱 [게이트웨이를](https://azure.microsoft.com/services/application-gateway/) 사용으로 사용하기로 결정하면 모든 네임스페이스는 동일한 응용 프로그램 게이트웨이 인스턴스를 사용합니다. Ingress 컨트롤러를 한 번 설치하면 액세스 가능한 네임스페이스를 모니터링하고 연결된 응용 프로그램 게이트웨이를 구성합니다.

Helm 구성에서 하나 이상의 다른 네임스페이스로 명시적으로 변경되지 않는 한 AGIC의 버전 0.7은 `default` 네임스페이스를 계속 독점적으로 관찰합니다(아래 섹션 참조).

## <a name="enable-multiple-namespace-support"></a>여러 네임스페이스 지원 사용
여러 네임스페이스 지원을 사용하려면 다음을 수행하십시오.
1. 다음 방법 중 하나로 [helm-config.yaml](#sample-helm-config-file) 파일을 수정합니다.
   - `watchNamespace` [helm-config.yaml에서](#sample-helm-config-file) 키를 완전히 삭제 - AGIC는 모든 네임 스페이스를 준수합니다
   - 빈 `watchNamespace` 문자열로 설정 - AGIC는 모든 네임스페이스를 관찰합니다.
   - 쉼표 ()`watchNamespace: default,secondNamespace`- AGIC는 이러한 네임스페이스를 단독으로 관찰합니다.
2. 다음과 같은 경우 헬름 템플릿 변경 사항을 적용합니다.`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

여러 네임스페이스를 관찰할 수 있는 기능으로 배포되면 AGIC는 다음을 수행합니다.
  - 모든 액세스 가능한 네임스페이스의 리소스 를 목록으로 나열합니다.
  - 에 추가된 리소스를 받는 필터`kubernetes.io/ingress.class: azure/application-gateway`
  - 결합된 [응용 프로그램 게이트웨이 구성 구성](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) 작성
  - [ARM을](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 통해 연결된 응용 프로그램 게이트웨이에 구성을 적용합니다.

## <a name="conflicting-configurations"></a>충돌하는 구성
여러 네임스페이스 [의 받는 리소스는](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) AGIC에게 단일 응용 프로그램 게이트웨이에 대해 충돌하는 구성을 만들도록 지시할 수 있습니다. (예를 들어 동일한 도메인을 주장하는 두 개의 인크레sses.)

계층 의 맨 위에 - **청취자** (IP 주소, 포트 및 호스트) 및 **라우팅 규칙** (바인딩 수신기, 백 엔드 풀 및 HTTP 설정)을 여러 네임 스페이스 / 수신에 의해 생성되고 공유 할 수 있습니다.

반면에 경로, 백 엔드 풀, HTTP 설정 및 TLS 인증서는 하나의 네임스페이스에서만 만들 수 있으며 중복은 제거됩니다.

예를 들어, 다음과 같은 중복 인서리소스 `staging` 정의네임스페이스및 `production` 다음의 경우를 `www.contoso.com`고려하십시오.
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

각 Kubernetes 네임스페이스로 `www.contoso.com` 라우팅하기 위해 트래픽을 요구하는 두 개의 리소스가 들어있음에도 불구하고 하나의 백 엔드만 트래픽을 서비스할 수 있습니다. AGIC는 리소스 중 하나에 대해 "선착순"을 기준으로 구성을 만듭니다. 두 개의 받는 리소스가 동시에 만들어지면 알파벳의 앞부분에서 리소스가 우선합니다. 위의 예제에서 우리는 단지 `production` 받는 사람에 대 한 설정을 만들 수 있을 것입니다. 응용 프로그램 게이트웨이는 다음 리소스로 구성됩니다.

  - 수신기:`fl-www.contoso.com-80`
  - 라우팅 규칙:`rr-www.contoso.com-80`
  - 백엔드 풀:`pool-production-contoso-web-service-80-bp-80`
  - HTTP 설정:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - 상태 프로브:`pb-production-contoso-web-service-80-websocket-ingress`

*수신기* 및 *라우팅 규칙을*제외하고 생성된 응용 프로그램 게이트웨이 리소스에는 생성된 네임스페이스()의`production`이름이 포함됩니다.

두 유입 리소스가 서로 다른 시점에서 AKS 클러스터에 도입되는 경우 AGIC는 응용 프로그램 게이트웨이를 다시 구성하고 트래픽을 에서 로 `namespace-B` `namespace-A`다시 라우팅하는 시나리오에서 끝날 가능성이 높습니다.

예를 들어 먼저 `staging` 추가한 경우 AGIC는 스테이징 백 엔드 풀로 트래픽을 라우팅하도록 응용 프로그램 게이트웨이를 구성합니다. 이후 단계에서 `production` 는 시작 을 도입 하면 AGIC 응용 프로그램 게이트웨이를 다시 프로그래밍 하 `production` 고 백 엔드 풀에 트래픽을 라우팅 시작 합니다.

## <a name="restrict-access-to-namespaces"></a>네임스페이스에 대한 액세스 제한
기본적으로 AGIC는 네임스페이스 내의 추가된 받는 것을 기반으로 응용 프로그램 게이트웨이를 구성합니다. 이 동작을 제한하려면 다음 옵션이 있습니다.
  - 네임스페이스를 명시적으로 정의하여 AGIC는 `watchNamespace` [helm-config.yaml의](#sample-helm-config-file) YAML 키를 통해 관찰해야 합니다.
  - [역할/역할 바인딩을](https://docs.microsoft.com/azure/aks/azure-ad-rbac) 사용하여 AGIC를 특정 네임스페이스로 제한합니다.

## <a name="sample-helm-config-file"></a>샘플 헬름 구성 파일
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

