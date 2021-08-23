---
title: Application Gateway를 사용하여 HTTP 또는 HTTPS를 통해 AKS 서비스 공개
description: 이 문서에서는 Application Gateway를 사용하여 HTTP 또는 HTTPS를 통해 AKS 서비스를 공개하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2764624d4f29432c10e0e7aa3ab49bb5a678d5e3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537699"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Application Gateway를 사용하여 HTTP 또는 HTTPS를 통해 AKS 서비스 공개 

이 자습서에는 [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/)를 사용하여 HTTP 또는 HTTPS를 통해 예제 Kubernetes 서비스를 공개하는 데 [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/)(Kubernetes 수신 리소스)를 사용하는 방법이 설명되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- `ingress-azure` Helm 차트가 설치되어 있어야 합니다.
  - [**그린필드 배포**](ingress-controller-install-new.md): 처음부터 시작하는 경우 Application Gateway를 사용하여 AKS 클러스터를 배포하고 AKS 클러스터에 애플리케이션 게이트웨이 수신 컨트롤러를 설치하는 단계가 설명된 설치 지침을 참조하세요.
  - [**브라운필드 배포**](ingress-controller-install-existing.md): 기존 AKS 클러스터와 Application Gateway가 있는 경우 지침을 참조하여 AKS 클러스터에 애플리케이션 게이트웨이 수신 컨트롤러를 설치합니다.
- 이 애플리케이션에서 HTTPS를 사용하려면 x509 인증서 및 해당 프라이빗 키가 필요합니다.

## <a name="deploy-guestbook-application"></a>`guestbook` 애플리케이션 배포

guestbook 애플리케이션은 웹 UI 프런트 엔드, 백 엔드 및 Redis 데이터베이스로 구성된 정식 Kubernetes 애플리케이션입니다. 기본적으로 `guestbook`은 `80` 포트에서 이름이 `frontend`인 서비스를 통해 해당 애플리케이션을 공개합니다. Kubernetes 수신 리소스가 없으면 AKS 클러스터 외부에서 서비스에 액세스할 수 없습니다. 여기에서는 이 애플리케이션을 사용하며 수신 리소스를 설정하여 HTTP 및 HTTPS를 통해 애플리케이션에 액세스합니다.

아래 지침에 따라 guestbook 애플리케이션을 배포합니다.

1. [여기](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)에서 `guestbook-all-in-one.yaml`을 다운로드합니다.
1. 다음을 실행하여 AKS 클러스터에 `guestbook-all-in-one.yaml`을 배포합니다.

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

이제 `guestbook` 애플리케이션이 배포되었습니다.

## <a name="expose-services-over-http"></a>HTTP를 통해 서비스 공개

guestbook 애플리케이션을 공개하려면 다음 수신 리소스를 사용합니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

이 수신은 `guestbook-all-in-one` 배포의 `frontend` 서비스를 Application Gateway의 기본 백 엔드로 공개합니다.

위의 수신 리소스를 `ing-guestbook.yaml`로 저장합니다.

1. 다음을 실행하여 `ing-guestbook.yaml`을 배포합니다.

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. 수신 컨트롤러 로그에서 배포 상태를 확인합니다.

이제 `guestbook` 애플리케이션을 사용할 수 있습니다. Application Gateway 공용 주소를 방문하여 사용 가능한지 확인할 수 있습니다.

## <a name="expose-services-over-https"></a>HTTPS를 통해 서비스 공개

### <a name="without-specified-hostname"></a>호스트 이름이 지정되지 않은 경우

호스트 이름을 지정하지 않으면 애플리케이션 게이트웨이를 가리키는 모든 호스트 이름에서 guestbook 서비스를 사용할 수 있습니다.

1. 수신을 배포하기 전에 인증서 및 프라이빗 키를 호스트하는 kubernetes 비밀을 만들어야 합니다. 다음을 실행하여 kubernetes 비밀을 만들 수 있습니다.

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 다음 수신을 정의합니다. 수신에서 `secretName` 섹션의 비밀 이름을 지정합니다.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > 위의 수신 리소스에 있는 `<guestbook-secret-name>`을 비밀 이름으로 바꿉니다. 위의 수신 리소스를 이름이 `ing-guestbook-tls.yaml`인 파일에 저장합니다.

1. 다음을 실행하여 ing-guestbook-tls.yaml을 배포합니다.

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. 수신 컨트롤러 로그에서 배포 상태를 확인합니다.

이제 HTTP 및 HTTPS 둘 다에서 `guestbook` 애플리케이션을 사용할 수 있습니다.

### <a name="with-specified-hostname"></a>호스트 이름이 지정된 경우

TLS 구성 및 서비스를 멀티플렉싱하기 위해 수신에 호스트 이름을 지정할 수도 있습니다.
호스트 이름을 지정하면 지정된 호스트에서만 guestbook 서비스를 사용할 수 있습니다.

1. 다음 수신을 정의합니다.
    수신에서 `secretName` 섹션의 비밀 이름을 지정하고 이에 맞게 `hosts` 섹션의 호스트 이름을 바꿉니다.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. 다음을 실행하여 `ing-guestbook-tls-sni.yaml`을 배포합니다.

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. 수신 컨트롤러 로그에서 배포 상태를 확인합니다.

이제 지정된 호스트(이 예제의 경우 `<guestbook.contoso.com>`)의 HTTP 및 HTTPS 둘 다에서 `guestbook` 애플리케이션을 사용할 수 있습니다.

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

다음 수신을 사용하면 이 수신에 경로를 더 추가하고 해당 경로를 다른 서비스로 리디렉션할 수 있습니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: </other/*>
        backend:
          serviceName: <other-service>
          servicePort: 80
       - backend:
          serviceName: frontend
          servicePort: 80
```
