---
title: 응용 프로그램 게이트웨이를 사용하여 HTTP 또는 HTTPS를 통해 AKS 서비스 노출
description: 이 문서에서는 응용 프로그램 게이트웨이를 사용하여 HTTP 또는 HTTPS를 통해 AKS 서비스를 노출하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795584"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>응용 프로그램 게이트웨이를 사용하여 HTTP 또는 HTTPS를 통해 AKS 서비스 노출 

이 자습서에서는 [Kubernetes 침투 리소스의](https://kubernetes.io/docs/concepts/services-networking/ingress/) 사용을 보여 주며 HTTP 또는 HTTPS를 통해 [Azure 응용 프로그램 게이트웨이를](https://azure.microsoft.com/services/application-gateway/) 통해 Kubernetes 서비스를 노출하는 데 도움이 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 설치된 `ingress-azure` 투구 차트.
  - [**그린필드 배포**](ingress-controller-install-new.md): 처음부터 시작하는 경우 응용 프로그램 게이트웨이를 사용하여 AKS 클러스터를 배포하고 AKS 클러스터에 응용 프로그램 게이트웨이 인그리스 컨트롤러를 설치하는 단계를 간략하게 설명하는 이러한 설치 지침을 참조하십시오.
  - [**브라운필드 배포**](ingress-controller-install-existing.md): 기존 AKS 클러스터 및 응용 프로그램 게이트웨이가 있는 경우 다음 지침을 참조하여 AKS 클러스터에 응용 프로그램 게이트웨이 인그레이즈 컨트롤러를 설치합니다.
- 이 응용 프로그램에서 HTTPS를 사용하려면 x509 인증서와 개인 키가 필요합니다.

## <a name="deploy-guestbook-application"></a>응용 `guestbook` 프로그램 배포

방명록 응용 프로그램은 웹 UI 프런트 엔드, 백 엔드 및 Redis 데이터베이스를 구성하는 표준 Kubernetes 응용 프로그램입니다. 기본적으로 포트에 `guestbook` `frontend` `80`이름이 있는 서비스를 통해 응용 프로그램을 노출합니다. Kubernetes 침투 리소스가 없으면 AKS 클러스터 외부에서 서비스에 액세스할 수 없습니다. 우리는 HTTP와 HTTPS를 통해 응용 프로그램에 액세스하기 위해 응용 프로그램 및 설치 Ingress 리소스를 사용합니다.

방명록 응용 프로그램을 배포하려면 아래 지침에 따라 설명합니다.

1. 여기에서 `guestbook-all-in-one.yaml` [here](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml) 다운로드
1. 실행하여 AKS 클러스터에 배포 `guestbook-all-in-one.yaml`

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

이제 응용 `guestbook` 프로그램이 배포되었습니다.

## <a name="expose-services-over-http"></a>HTTP를 통해 서비스 노출

방명록 응용 프로그램을 노출하기 위해 다음과 같은 받는 리소스를 사용할 예정입니다.

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

이 문제는 `frontend` `guestbook-all-in-one` 배포의 서비스를 응용 프로그램 게이트웨이의 기본 백 엔드로 노출합니다.

위의 인드레스 리소스를 `ing-guestbook.yaml`로 저장합니다.

1. 실행하여 배포: `ing-guestbook.yaml`

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. 배포 상태에 대 한 받는 컨트롤러의 로그를 확인 합니다.

이제 `guestbook` 응용 프로그램을 사용할 수 있습니다. 응용 프로그램 게이트웨이의 공용 주소를 방문하여 확인할 수 있습니다.

## <a name="expose-services-over-https"></a>HTTPS를 통해 서비스 노출

### <a name="without-specified-hostname"></a>지정된 호스트 이름 없이

호스트 이름을 지정하지 않으면 응용 프로그램 게이트웨이를 가리키는 모든 호스트 이름에서 방명록 서비스를 사용할 수 있습니다.

1. 침투를 배포하기 전에 인증서 및 개인 키를 호스트하기 위해 kubernetes 비밀을 만들어야 합니다. 실행하여 kubernetes 비밀을 만들 수 있습니다.

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 다음 의 현재 를 정의합니다. inress에서 `secretName` 섹션에서 비밀의 이름을 지정합니다.

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
    > 위의 `<guestbook-secret-name>` Inress 리소스에서 비밀의 이름으로 바꿉니다. 위의 Inress 리소스를 파일 `ing-guestbook-tls.yaml`이름에 저장합니다.

1. 실행하여 ing-게스트북 tls.yaml 배포

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. 배포 상태에 대 한 받는 컨트롤러의 로그를 확인 합니다.

이제 `guestbook` 응용 프로그램은 HTTP와 HTTPS 모두에서 사용할 수 있습니다.

### <a name="with-specified-hostname"></a>지정된 호스트 이름

또한 TLS 구성 및 서비스를 다중화하기 위해 받는 데 호스트 이름을 지정할 수도 있습니다.
호스트 이름을 지정하면 방명록 서비스는 지정된 호스트에서만 사용할 수 있습니다.

1. 다음 의 현재 를 정의합니다.
    inresss에서 `secretName` 섹션에서 비밀의 이름을 지정하고 그에 따라 섹션의 `hosts` 호스트 이름을 바꿉니다.

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

1. 실행을 `ing-guestbook-tls-sni.yaml` 통해 배포

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. 배포 상태에 대 한 받는 컨트롤러의 로그를 확인 합니다.

이제 `guestbook` 응용 프로그램은 지정된 호스트에서만 HTTP 및 HTTPS에서 사용할 수 있습니다(이`<guestbook.contoso.com>` 예제에서는).

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

다음 받은 내용은 이 침입에 추가 경로를 추가하고 해당 경로를 다른 서비스로 리디렉션할 수 있도록 합니다.

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
