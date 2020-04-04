---
title: AKS(Azure Kubernetes Service) 클러스터로 HTTPS 수신 만들기
description: AZURE Kubernetes 서비스(AKS) 클러스터에서 자동 TLS 인증서 생성을 위해 암호화하자를 사용하는 NGINX 침투 컨트롤러를 설치하고 구성하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: ece117d60ae3d7fd70c0972bb463340c5d38c9e1
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637256"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 HTTPS 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. [cert-manager][cert-manager] 프로젝트는 [Let's Encrypt][lets-encrypt] 인증서를 자동으로 생성하고 구성하는 데 사용됩니다. 마지막으로, 두 애플리케이션이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 애플리케이션에 액세스할 수 있습니다.

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [고정 공용 IP를 사용하여 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt를 사용하는 수신 컨트롤러 만들기][aks-ingress-static-tls]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 이 문서에서는 AKS 클러스터와 동일한 리소스 그룹에 [DNS 영역이][dns-zone] 있는 [사용자 지정 도메인이][custom-domain] 있다고 가정합니다.

이 문서에서는 Helm을 사용하여 NGINX 수신 컨트롤러, cert-manager 및 샘플 웹앱을 설치합니다. Helm의 최신 릴리스를 사용 중이어야 합니다. 업그레이드 지침은 Helm [설치 문서를][helm-install]참조하십시오. Helm 구성 및 사용에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)에서 Helm을 사용하여 응용 프로그램 설치를][use-helm]참조하십시오.

또한 이 문서에서는 Azure CLI 버전 2.0.64 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

받는 컨트롤러를 만들려면 `helm` 명령을 사용하여 *nginx-inress를 설치합니다.* 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러도 Linux 노드에서 예약해야 합니다. 현재 AKS에서 미리 보기 중인 Windows Server 노드는 inress 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예제는 기본 에서 인그레스 *리소스라는*이름의 침투 리소스에 대해 Kubernetes 네임스페이스를 만듭니다. 필요에 따라 사용자 고유의 환경에 대한 네임스페이스를 지정합니다.

> [!TIP]
> 클러스터의 컨테이너에 대한 요청에 대한 [클라이언트 소스 IP 보존을][client-source-ip] 사용하려면 Helm 설치 명령에 추가합니다. `--set controller.service.externalTrafficPolicy=Local` 클라이언트 소스 IP는 *X-전달-For*아래의 요청 헤더에 저장됩니다. 클라이언트 소스 IP 보존을 사용하도록 설정된 usingres s 컨트롤러를 사용하는 경우 SSL 통과가 작동하지 않습니다.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

설치하는 동안 Azure 공용 IP 주소가 수신 컨트롤러에 대해 생성됩니다. 이 공용 IP 주소는 수신 컨트롤러의 수명 동안만 고정됩니다. 수신 컨트롤러를 삭제하면 공용 IP 주소 할당이 손실됩니다. 추가 수신 컨트롤러를 만들면 새 공용 IP 주소가 할당됩니다. 공용 IP 주소를 계속 사용하려는 경우에는 대신 [고정 공용 IP 주소로 수신 컨트롤러를 만들][aks-ingress-static-tls] 수 있습니다.

공용 IP 주소를 얻으려면 `kubectl get service` 명령을 사용합니다. 서비스에 IP 주소가 할당될 때까지 몇 분 정도 걸립니다.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

수신 규칙은 아직 생성되지 않았습니다. 공용 IP 주소로 이동하면, NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다.

## <a name="add-an-a-record-to-your-dns-zone"></a>DNS 영역에 A 레코드 추가

az 네트워크 dns 레코드를 사용하여 NGINX 서비스의 외부 IP 주소로 DNS 영역에 *A* [레코드를 추가합니다.][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> 선택적으로 사용자 지정 도메인 대신 인드레스 컨트롤러 IP 주소에 대해 FQDN을 구성할 수 있습니다. 이 샘플은 Bash 셸용입니다.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>cert-manager 설치

NGINX 수신 컨트롤러는 TLS 종료를 지원합니다. HTTPS에 대한 인증서를 검색하고 구성하는 몇 가지 방법이 있습니다. 이 문서에서는 자동 [Lets Encrypt][lets-encrypt] 인증서 생성 및 관리 기능을 제공하는 [cert-manager][cert-manager]를 사용하는 방법을 보여 줍니다.

인증서 관리자 컨트롤러를 설치하려면 다음을 수행하십시오.

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

cert-manager 구성에 대한 자세한 내용은 [cert-manager 프로젝트][cert-manager]를 참조합니다.

## <a name="create-a-ca-cluster-issuer"></a>CA 클러스터 발급자 만들기

인증서가 발급되기 전에 cert-manager에게는 [발급자][cert-manager-issuer] 또는 [ClusterIssuer][cert-manager-cluster-issuer] 리소스가 필요합니다. 이러한 Kubernetes 리소스는 기능이 동일하지만, `Issuer`는 단일 네임스페이스에서 작동하고 `ClusterIssuer`는 모든 네임스페이스에서 작동합니다. 자세한 내용은 [cert-manager 발급자][cert-manager-issuer] 설명서를 참조하세요.

다음 예제 매니페스트를 사용하여 클러스터 발급자(예: `cluster-issuer.yaml`)를 만듭니다. 조직의 유효한 주소로 메일 주소를 업데이트합니다.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
```

발급자를 만들려면 `kubectl apply` 명령을 사용합니다.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>데모 애플리케이션 실행

수신 컨트롤러와 인증서 관리 솔루션이 구성되었습니다. 이제 AKS 클러스터에서 두 개의 데모 애플리케이션을 실행하겠습니다. 이 예제에서 Helm은 간단한 *Hello 월드* 응용 프로그램의 두 인스턴스를 배포하는 데 사용됩니다.

샘플 Helm 차트를 설치하기 전에 Helm 환경에 Azure 샘플 리포지토리를 추가합니다.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

azure 샘플 / *aks-helloworld 헬름 차트를 사용하여 aks-helloworld라는* 데모 응용 프로그램을 만듭니다. *azure-samples/aks-helloworld*

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

*aks-helloworld-2라는*데모 응용 프로그램의 두 번째 인스턴스를 만듭니다. *--set을*사용하여 두 응용 프로그램이 시각적으로 구별되도록 새 제목과 고유한 서비스 이름을 지정합니다.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>수신 경로 만들기

이제 두 애플리케이션이 모두 Kubernetes 클러스터에서 실행됩니다. 그러나 형식의 서비스로 `ClusterIP` 구성되며 인터넷에서 액세스할 수 없습니다. 응용 프로그램을 공개적으로 사용할 수 있도록 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 애플리케이션 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서는 주소 에 대한 트래픽 *안녕하세요-세계-inress. MY_CUSTOM_DOMAIN* *aks-helloworld* 서비스로 라우팅됩니다. 주소안녕하세요 *세계 에 트래픽. MY_CUSTOM_DOMAIN/헬로월드-2는* *아크헬로월드-2서비스로* 라우팅된다. 안녕하세요 *세계 에 트래픽. MY_CUSTOM_DOMAIN/정적* 정적 에셋에 대 한 *aks-helloworld* 라는 서비스에 라우팅 됩니다.

아래 예제 `hello-world-ingress.yaml` YAML을 사용하여 명명된 파일을 만듭니다. *hosts* 및 *host*를 이전 단계에서 만든 DNS 이름으로 업데이트합니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

`kubectl apply` 명령을 사용하여 수신 리소스를 만듭니다.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>인증서 개체가 만들어졌는지 확인

다음으로, 인증서 리소스를 만들어야 합니다. 인증서 리소스는 원하는 X.509 인증서를 정의합니다. 자세한 내용은 [인증서 관리자 인증서를][cert-manager-certificates]참조하십시오. Cert 관리자는 v0.2.2 이후 인증서 관리자와 함께 자동으로 배포되는 ingress-shim을 사용하여 인증서 개체를 자동으로 만들었습니다. 자세한 내용은 [수신 shim 설명서][ingress-shim]를 참조하세요.

인증서가 성공적으로 만들어졌는지 확인하려면 `kubectl get certificate --namespace ingress-basic` 명령을 사용하고 *READY가* *True인지*확인하면 몇 분 정도 걸릴 수 있습니다.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>수신 구성 테스트

웹 브라우저를 열어 *헬로 월드-인그레스를 방문합니다. *Kubernetes 의 MY_CUSTOM_DOMAIN 컨트롤러를 입력합니다. HTTPS를 사용하도록 리디렉션하고 인증서를 신뢰할 수 있으며 데모 응용 프로그램이 웹 브라우저에 표시됩니다. */hello-world-two* 경로를 추가하고 사용자 지정 제목이 표시된 두 번째 데모 응용 프로그램이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소, 인증서 및 샘플 앱을 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 이러한 리소스를 정리하려면 전체 샘플 네임스페이스 또는 개별 리소스를 삭제할 수 있습니다.

### <a name="delete-the-sample-namespace-and-all-resources"></a>샘플 네임스페이스 및 모든 리소스 삭제

전체 샘플 네임스페이스를 삭제하려면 `kubectl delete` 명령을 사용하고 네임스페이스 이름을 지정합니다. 네임스페이스의 모든 리소스가 삭제됩니다.

```console
kubectl delete namespace ingress-basic
```

그런 다음 AKS hello World 앱의 헬름 리포지토리를 제거합니다.

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>리소스를 개별적으로 삭제

또는 보다 세분화된 방법은 생성된 개별 리소스를 삭제하는 것입니다. 먼저 클러스터 발급자 리소스를 제거합니다.

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

명령과 함께 투구 `helm list` 릴리스를 나열합니다. 다음 예제 출력과 같이 이름이 *nginx-ingress* 및 *aks-helloworld*인 차트를 찾습니다.

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

`helm delete` 명령으로 해당 릴리스를 삭제합니다. 다음 예제는 NGINX 수신 배포와 두 개의 샘플 AKS Hello World 앱을 삭제합니다.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

다음으로, AKS Hello World 앱에 대한 Helm 리포지토리를 제거합니다.

```console
helm repo remove azure-samples
```

트래픽을 샘플 앱으로 유도한 수신 경로를 제거합니다.

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

마지막으로 자체 네임스페이스를 삭제할 수 있습니다. `kubectl delete` 명령을 사용하고 네임스페이스 이름을 지정합니다.

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]
- [cert-manager][cert-manager]

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [고정 공용 IP를 사용하여 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt를 사용하는 수신 컨트롤러 만들기][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
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
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
