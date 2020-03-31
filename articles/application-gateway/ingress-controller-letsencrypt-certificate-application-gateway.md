---
title: 응용 프로그램 게이트웨이에서 LetsEncrypt.org 인증서 사용
description: 이 문서에서는 LetsEncrypt.org 인증서를 가져오고 AKS 클러스터용 응용 프로그램 게이트웨이에서 사용하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957978"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>AKS 클러스터에 대한 응용 프로그램 게이트웨이에서 LetsEncrypt.org 있는 인증서 사용

이 섹션에서는 [LETSENCRYPT.ORG](https://letsencrypt.org/) 활용하고 도메인에 대한 TLS/SSL 인증서를 자동으로 획득하도록 AKS를 구성합니다. 인증서는 AKS 클러스터에 대한 SSL/TLS 종료를 수행하는 응용 프로그램 게이트웨이에 설치됩니다. 여기에 설명된 설정은 인증서 생성 및 관리를 자동화하는 [인증서 관리자](https://github.com/jetstack/cert-manager) Kubernetes 추가 기능을 사용합니다.

아래 단계에 따라 기존 AKS 클러스터에 [인증서 관리자를](https://docs.cert-manager.io) 설치합니다.

1. 투구 차트

    다음 스크립트를 실행하여 `cert-manager` 투구 차트를 설치합니다. 그러면

    - AKS에 `cert-manager` 새 네임스페이스 만들기
    - 인증서, 챌린지, 클러스터 발급자, 발급자, 주문
    - 인증서 관리자 차트 [설치(docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. 클러스터 발급자 리소스

    리소스를 `ClusterIssuer` 만듭니다. 서명된 `cert-manager` 인증서를 `Lets Encrypt` 얻을 수 있는 인증 기관을 대표해야 합니다.

    cert-manager는 namespaced가 아닌 `ClusterIssuer` 리소스를 사용하여 여러 네임스페이스에서 사용할 수 있는 인증서를 발급합니다. `Let’s Encrypt`ACME 프로토콜을 사용하여 지정된 도메인 이름을 제어하고 인증서를 발급합니다. `ClusterIssuer` 속성 구성에 대한 자세한 내용은 [여기를 참조하십시오.](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer`테스트에 `cert-manager` 사용되는 `Lets Encrypt` 스테이징 환경(브라우저/클라이언트 트러스트 저장소에 없는 루트 인증서)을 사용하여 인증서를 발급하도록 지시합니다.

    아래 YAML의 기본 챌린지 `http01`유형은 . 다른 과제는 [letsencrypt.org - 챌린지 유형에 대해 문서화되어 있습니다.](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > 아래 `<YOUR.EMAIL@ADDRESS>` YAML에서 업데이트

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. 앱 배포

    인크립팅 인증서를 사용하여 `guestbook` 응용 프로그램 게이트웨이를 사용하여 응용 프로그램을 노출하는 받는 리소스를 만듭니다.

    응용 프로그램 게이트웨이에 DNS 이름이 있는 공용 프런트 엔드 `azure.com` IP 구성(기본 `Azure DNS Zone` 도메인 사용 또는 서비스 프로비저닝 및 사용자 지정 도메인 할당)이 있는지 확인합니다.
    인증서 관리자에게 `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`태그가 지정된 인그레스 리소스를 처리하도록 알려주는 주석에 유의하십시오.

    > [!IMPORTANT] 
    > 아래 `<PLACEHOLDERS.COM>` YAML에서 사용자 고유의 도메인(또는 응용 프로그램 게이트웨이(예: 'kh-aks-ingress.westeurope.cloudapp.azure.com')으로 업데이트합니다.

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    몇 초 후 자동으로 발급되는 `guestbook` **스테이징** `Lets Encrypt` 인증서를 사용하여 응용 프로그램 게이트웨이 HTTPS URL을 통해 서비스에 액세스할 수 있습니다.
    브라우저에서 잘못된 인증서 권한을 경고할 수 있습니다. 준비 인증서는 에서 `CN=Fake LE Intermediate X1`발급됩니다. 이는 시스템이 예상대로 작동했으며 프로덕션 인증서를 사용할 준비가 되었다는 것을 나타냅니다.

4. 생산 증명서

    스테이징 인증서가 성공적으로 설정되면 프로덕션 ACME 서버로 전환할 수 있습니다.
    1. Reress 리소스의 스테이징 어칭을 다음과 같은 것으로 바꿉꿉을 바꿉습니다.`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. 이전 단계에서 `ClusterIssuer` 만든 기존 스테이징을 삭제하고 위의 ClusterIssueR YAML에서 ACME 서버를 새 스테이징으로 대체하여 새 스테이징을 만듭니다.`https://acme-v02.api.letsencrypt.org/directory`

5. 인증서 만료 및 갱신

    인증서가 `Lets Encrypt` 만료되기 `cert-manager` 전에 Kubernetes 비밀 저장소에서 인증서가 자동으로 업데이트됩니다. 이 때 응용 프로그램 게이트웨이 인그리스 컨트롤러는 응용 프로그램 게이트웨이를 구성하는 데 사용하는 받는 리소스에 참조되는 업데이트된 비밀을 적용합니다.
