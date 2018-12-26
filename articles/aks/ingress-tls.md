---
title: AKS(Azure Kubernetes Service) 클러스터로 HTTPS 수신 만들기
description: 자동 SSL 인증서 생성을 위해 Let's Encrypt를 사용하는 NGINX 수신 컨트롤러를 AKS(Azure Kubernetes Service) 클러스터에 설치하고 구성하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 7a7acb7da2e5999fc844fe6c6c2e191783df9190
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093964"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 HTTPS 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. [cert-manager][cert-manager] 프로젝트는 [Let's Encrypt][lets-encrypt] 인증서를 자동으로 생성하고 구성하는 데 사용됩니다. 마지막으로, 두 응용 프로그램이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 응용 프로그램에 액세스할 수 있습니다.

또한 다음을 수행할 수 있습니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 응용 프로그램 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 개인 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [고정 공용 IP를 사용하여 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt를 사용하는 수신 컨트롤러 만들기][aks-ingress-static-tls]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Helm을 사용하여 NGINX 수신 컨트롤러, cert-manager 및 샘플 웹앱을 설치합니다. AKS 클러스터 내에서, Tiller의 서비스 계정을 사용하여 Helm이 초기화되어 있어야 합니다. Helm의 최신 릴리스를 사용 중이어야 합니다. 업그레이드 지침은 [Helm 설치 문서][helm-install]를 참조하세요. Helm을 구성하고 사용하는 방법에 대한 자세한 내용은 [Helm을 사용하여 AKS(Azure Kubernetes Service)에 응용 프로그램 설치][use-helm]를 참조하세요.

또한 이 문서에서는 Azure CLI 버전 2.0.41 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

수신 컨트롤러를 만들려면 `Helm`을 사용하여 *nginx-ingress*를 설치합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

> [!TIP]
> 다음 예제에서는 `kube-system` 네임스페이스에 수신 컨트롤러를 설치합니다. 원하는 경우 환경용으로 다른 네임스페이스를 지정할 수 있습니다. AKS 클러스터가 RBAC를 사용할 수 없는 경우 명령에 `--set rbac.create=false`를 추가합니다.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

설치하는 동안 Azure 공용 IP 주소가 수신 컨트롤러에 대해 생성됩니다. 이 공용 IP 주소는 수신 컨트롤러의 수명 동안만 고정됩니다. 수신 컨트롤러를 삭제하면 공용 IP 주소 할당이 손실됩니다. 추가 수신 컨트롤러를 만들면 새 공용 IP 주소가 할당됩니다. 공용 IP 주소를 계속 사용하려는 경우에는 대신 [고정 공용 IP 주소로 수신 컨트롤러를 만들][aks-ingress-static-tls] 수 있습니다.

공용 IP 주소를 얻으려면 `kubectl get service` 명령을 사용합니다. 서비스에 IP 주소가 할당될 때까지 몇 분 정도 걸립니다.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

수신 규칙은 아직 생성되지 않았습니다. 공용 IP 주소로 이동하면, NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다.

## <a name="configure-a-dns-name"></a>DNS 이름 구성

HTTPS 인증서가 올바르게 작동하려면 수신 컨트롤러 IP 주소에 대한 FQDN을 구성합니다. 수신 컨트롤러의 IP 주소와 FQDN에 사용할 고유 이름으로 다음 스크립트를 업데이트합니다.

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

이제 FQDN을 통해 수신 컨트롤러에 액세스할 수 있습니다.

## <a name="install-cert-manager"></a>cert-manager 설치

NGINX 수신 컨트롤러는 TLS 종료를 지원합니다. HTTPS에 대한 인증서를 검색하고 구성하는 몇 가지 방법이 있습니다. 이 문서에서는 자동 [Lets Encrypt][lets-encrypt] 인증서 생성 및 관리 기능을 제공하는 [cert-manager][cert-manager]를 사용하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 Let's Encrypt에 대해 `staging` 환경을 사용합니다. 프로덕션 배포에서는 Helm 차트를 설치할 때 및 리소스 정의에서 `letsencrypt-prod` 및 `https://acme-v02.api.letsencrypt.org/directory`를 사용합니다.

RBAC 사용이 가능한 클러스터에 cert-manager 컨트롤러를 설치하려면 다음과 같은 `helm install` 명령을 사용합니다.

```console
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer
```

RBAC 사용이 가능한 클러스터가 아닌 경우, 다음 명령을 사용합니다.

```console
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    --set rbac.create=false \
    --set serviceAccount.create=false
```

cert-manager 구성에 대한 자세한 내용은 [cert-manager 프로젝트][cert-manager]를 참조합니다.

## <a name="create-a-ca-cluster-issuer"></a>CA 클러스터 발급자 만들기

인증서가 발급되기 전에 cert-manager에게는 [발급자][cert-manager-issuer] 또는 [ClusterIssuer][cert-manager-cluster-issuer] 리소스가 필요합니다. 이러한 Kubernetes 리소스는 기능이 동일하지만, `Issuer`는 단일 네임스페이스에서 작동하고 `ClusterIssuer`는 모든 네임스페이스에서 작동합니다. 자세한 내용은 [cert-manager 발급자][cert-manager-issuer] 설명서를 참조하세요.

다음 예제 매니페스트를 사용하여 클러스터 발급자(예: `cluster-issuer.yaml`)를 만듭니다. 조직의 유효한 주소로 메일 주소를 업데이트합니다.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

발급자를 만들려면 `kubectl apply -f cluster-issuer.yaml` 명령을 사용합니다.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>인증서 개체 만들기

다음으로, 인증서 리소스를 만들어야 합니다. 인증서 리소스는 원하는 X.509 인증서를 정의합니다. 자세한 내용은 [cert-manager 인증서][cert-manager-certificates]를 참조하세요.

다음 예제 매니페스트를 사용하여 인증서 리소스(예: `certificates.yaml`)를 만듭니다. *dnsNames* 및 *domains*를 이전 단계에서 만든 DNS 이름으로 업데이트합니다. 내부 전용 수신 컨트롤러를 사용하는 경우에 서비스의 내부 DNS 이름을 지정합니다.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

인증서 리소스를 만들려면 `kubectl apply -f certificates.yaml` 명령을 사용합니다.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>데모 응용 프로그램 실행

수신 컨트롤러와 인증서 관리 솔루션이 구성되었습니다. 이제 AKS 클러스터에서 두 개의 데모 응용 프로그램을 실행하겠습니다. 이 예제에서는 Helm을 사용하여 간단한 ‘Hello world’ 응용 프로그램의 두 인스턴스를 배포합니다.

샘플 Helm 차트를 설치하려면, 먼저 다음과 같이 Azure 샘플 리포지토리를 Helm 환경에 추가합니다.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

다음 명령을 사용하여 Helm 차트에서 첫 번째 데모 응용 프로그램을 만듭니다.

```console
helm install azure-samples/aks-helloworld
```

이제 데모 응용 프로그램의 두 번째 인스턴스를 설치합니다. 두 번째 인스턴스에서, 두 응용 프로그램을 시각적으로 구분할 수 있도록 새 제목을 지정합니다. 고유한 서비스 이름도 지정합니다.

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>수신 경로 만들기

이제 두 응용 프로그램이 모두 Kubernetes 클러스터에서 실행되고 있지만, `ClusterIP` 유형의 서비스로 구성되었습니다. 따라서 인터넷에서 응용 프로그램에 액세스할 수 없습니다. 응용 프로그램을 공개적으로 사용할 수 있도록 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 애플리케이션 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서 주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우트됩니다. 주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다. *hosts* 및 *host*를 이전 단계에서 만든 DNS 이름으로 업데이트합니다.

`hello-world-ingress.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

`kubectl apply -f hello-world-ingress.yaml` 명령을 사용하여 수신 리소스를 만듭니다.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>수신 구성 테스트

웹 브라우저에서 Kubernetes 수신 컨트롤러의 FQDN(예: *https://demo-aks-ingress.eastus.cloudapp.azure.com*)을 엽니다.

이 예제에서는 `letsencrypt-staging`을 사용하므로 브라우저에서 발급된 SSL 인증서를 신뢰하지 않습니다. 경고 프롬프트를 수락하여 응용 프로그램에서 계속 진행합니다. 인증서 정보에 이 *Fake LE Intermediate X1* 인증서가 Let's Encrypt에서 발급되었다고 표시됩니다. 이 가짜 인증서는 `cert-manager`에서 요청을 올바르게 처리했으며 공급자로부터 인증서를 받았음을 나타냅니다.

![Let's Encrypt 스테이징 인증서](media/ingress/staging-certificate.png)

`staging` 대신 `prod`를 사용하도록 Let's Encrypt를 변경하면, 다음 예제와 같이 Let's Encrypt에서 발급된 신뢰할 수 있는 인증서가 사용됩니다.

![Let's Encrypt 인증서](media/ingress/certificate.png)

데모 응용 프로그램이 웹 브라우저에 표시됩니다.

![애플리케이션 예제 1](media/ingress/app-one.png)

이제 FQDN에 */hello-world-two* 경로를 추가합니다(예: *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*). 사용자 지정 제목이 있는 두 번째 데모 응용 프로그램이 표시됩니다.

![애플리케이션 예제 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소, 인증서 및 샘플 앱을 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 정리하려면 먼저 인증서 리소스를 제거합니다.

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

이제 `helm list` 명령을 사용하여 Helm 릴리스를 나열합니다. 다음 예제 출력과 같이 이름이 *nginx-ingress*, *cert-manager* 및 *aks-helloworld*인 차트를 찾습니다.

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Tue Oct 16 17:24:05 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
loitering-waterbuffalo  1           Tue Oct 16 17:26:16 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
flabby-deer             1           Tue Oct 16 17:27:06 2018    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Tue Oct 16 17:27:02 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

`helm delete` 명령으로 해당 릴리스를 삭제합니다. 다음 예제는 NGINX 수신 배포, 인증서 관리자 및 두 개의 샘플 AKS Hello World 앱을 삭제합니다.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

다음으로, AKS Hello World 앱에 대한 Helm 리포지토리를 제거합니다.

```console
helm repo remove azure-samples
```

마지막으로 트래픽을 샘플 앱으로 유도한 수신 경로를 제거합니다.

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]
- [cert-manager][cert-manager]

또한 다음을 수행할 수 있습니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 응용 프로그램 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 개인 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [고정 공용 IP를 사용하여 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt를 사용하는 수신 컨트롤러 만들기][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
