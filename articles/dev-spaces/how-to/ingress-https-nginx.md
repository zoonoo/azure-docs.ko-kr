---
title: 사용자 지정 NGINX 사용 컨트롤러를 사용하고 HTTPS 구성
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: 사용자 지정 NGINX 받는 컨트롤러를 사용 하 여 Azure 개발자 공간을 구성 하 고 해당 받는 컨트롤러를 사용 하 여 HTTPS를 구성 하는 방법에 대해 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: 0fe9fec263b72ac06839b58fdc5b0142a724718c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155450"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>사용자 지정 NGINX 사용 컨트롤러를 사용하고 HTTPS 구성

이 문서에서는 사용자 지정 NGINX 받는 컨트롤러를 사용하도록 Azure 개발자 공간을 구성하는 방법을 보여 주며 있습니다. 이 문서에서는 HTTPS를 사용하도록 사용자 지정 받는 컨트롤러를 구성하는 방법도 보여 주며 이 문서에서는

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 계정이 없는 경우 [무료 계정][azure-account-create]에 만들 수 있습니다.
* [Azure CLI 설치][az-cli]
* [Azure 개발자 공간을 사용하도록 설정한 AZURE Kubernetes 서비스(AKS) 클러스터.][qs-cli]
* [kubectl설치.][kubectl]
* [Helm 3이 설치되었습니다][helm-installed].
* [DNS 영역이][dns-zone]있는 [사용자 지정 도메인입니다.][custom-domain]  이 문서에서는 사용자 지정 도메인과 DNS 영역이 AKS 클러스터와 동일한 리소스 그룹에 있다고 가정하지만 다른 리소스 그룹에서 사용자 지정 도메인 및 DNS 영역을 사용할 수 있습니다.

## <a name="configure-a-custom-nginx-ingress-controller"></a>사용자 지정 NGINX 인서스 컨트롤러 구성

Kubernetes 명령줄 클라이언트인 [kubectl을][kubectl]사용하여 클러스터에 연결합니다. Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

NGINX 의 컨트롤러 헬름 차트가 포함된 [공식 안정 투구 저장소를][helm-stable-repo]추가합니다.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

NGINX 침투 컨트롤러에 대한 Kubernetes 네임스페이스를 만들고 `helm`을 사용하여 설치합니다.

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> 위의 예제는 인서스 컨트롤러에 대한 공용 끝점을 만듭니다. 대신 받는 컨트롤러에 전용 끝점을 사용해야 하는 경우 *--set controller.service.annotations를 추가합니다." service\\\\.beta\\.kubernetes .io/azure-load-밸러서-내부"=helm* *설치* 명령에 대한 진정한 매개 변수입니다. 예를 들어:
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> 이 개인 끝점은 AKS 클러스터가 배포된 가상 네트워크 내에 노출됩니다.

[kubectl][kubectl-get]get을 사용하여 NGINX 받는 컨트롤러 서비스의 IP 주소를 가져옵니다.

```console
kubectl get svc -n nginx --watch
```

샘플 출력은 *nginx* 이름 공간의 모든 서비스에 대한 IP 주소를 표시합니다.

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

az 네트워크 dns 레코드를 사용하여 NGINX 서비스의 외부 IP 주소로 DNS 영역에 *A* [레코드를 추가합니다.][az-network-dns-record-set-a-add-record]

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

위의 예제는 *MY_CUSTOM_DOMAIN* DNS 영역에 *A* 레코드를 추가합니다.

이 문서에서는 [Azure Dev Spaces 자전거 공유 샘플 애플리케이션](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)을 사용하여 Azure Dev Spaces 사용하는 방법을 보여 줍니다. GitHub에서 애플리케이션을 복제하고 해당 디렉터리로 이동합니다.

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

[value.yaml을][values-yaml] 열고 다음 을 업데이트합니다.
* <REPLACE_ME_WITH_HOST_SUFFIX *>* 모든 인스턴스를 *nginx로 바꿉습니다. MY_CUSTOM_DOMAIN* *도메인을 사용하여 MY_CUSTOM_DOMAIN.* 
* *kubernetes.io/ingress.class 바꾸기: traefik-azds # 개발자 공간 별* *kubernetes.io/ingress.class: nginx # 사용자 지정 인그레스*. 

다음은 업데이트된 `values.yaml` 파일의 예입니다.

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

변경 내용을 저장하고 파일을 닫습니다.

을 *dev* 사용하여 `azds space select`샘플 응용 프로그램으로 개발 공간을 만듭니다.

```console
azds space select -n dev -y
```

을 사용하여 `helm install`샘플 응용 프로그램을 배포합니다.

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

위의 예제에서는 샘플 응용 프로그램을 *dev* 네임스페이스에 배포합니다.

을 사용하여 `azds list-uris`샘플 응용 프로그램에 액세스하는 URL을 표시합니다.

```console
azds list-uris
```

아래 출력에는 의 예제 `azds list-uris`URL이 표시됩니다.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

`azds list-uris` 명령에서 공용 URL을 열어 *bikesharingweb* 서비스로 이동합니다. 위의 예제에서 *bikesharingweb* 서비스에 대한 공용 URL은 `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`입니다.

> [!NOTE]
> *bikesharingweb* 서비스 대신 오류 페이지가 표시되면 *value.yaml* 파일에서 *kubernetes.io/ingress.class* 어너표와 호스트를 **모두** 업데이트했는지 확인합니다.

`azds space select` 명령을 사용하여 *개발자* 아래에 자식 공간을 만들고 URL을 나열하여 자식 개발 공간에 액세스할 수 있습니다.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

아래 출력은 *azureuser1* `azds list-uris` 자식 개발 공간에서 샘플 응용 프로그램에 액세스하는 예제 URL을 보여 주며 있습니다.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

명령에서 공용 URL을 열어 *azureuser1* 자식 개발 공간에서 자전거 `azds list-uris` *공유 웹* 서비스로 이동합니다. 위의 예에서 *azureuser1* 자식 개발 공간에서 *자전거 공유 웹* 서비스에 `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`대한 공용 URL은 .

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>HTTPS를 사용하도록 NGINX 사용 컨트롤러 구성

[인증서 관리자를][cert-manager] 사용하여 HTTPS를 사용하도록 NGINX 인그레스 컨트롤러를 구성할 때 TLS 인증서 관리를 자동화합니다. `helm` *certmanager* 차트를 설치하는 데 사용합니다.

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

`letsencrypt-clusterissuer.yaml` 파일을 만들고 이메일 주소로 이메일 필드를 업데이트합니다.

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

> [!NOTE]
> 테스트를 위해 *ClusterIssue.에*사용할 수 있는 [스테이징 서버도][letsencrypt-staging-issuer] 있습니다.

을 `kubectl` 적용하는 `letsencrypt-clusterissuer.yaml`데 사용합니다.

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

*cert 관리자* 및 HTTPS 사용에 대한 세부 정보를 포함하도록 [value.yaml을][values-yaml] 업데이트합니다. 다음은 업데이트된 `values.yaml` 파일의 예입니다.

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

다음을 사용하여 `helm`샘플 응용 프로그램을 업그레이드합니다.

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

*dev/azureuser1* 자식 공간의 샘플 응용 프로그램으로 이동하면 HTTPS를 사용하도록 리디렉션됩니다. 또한 페이지가 로드되지만 브라우저에 약간의 오류가 표시됩니다. 브라우저 콘솔을 열면 HTTP 리소스를 로드하려는 HTTPS 페이지와 관련된 오류가 표시됩니다. 예를 들어:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

이 오류를 해결하려면 아래와 유사한 [BikeSharingWeb/azds.yaml을][azds-yaml] 업데이트하십시오.

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
...
```

[BikeSharingWeb/package.jsonURL][package-json] 패키지에 대한 종속성으로 업데이트합니다. *url*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

[바이크쉐어링웹/lib/helpers.js에서][helpers-js] *getApiHostAsync* 방법을 업데이트하여 HTTPS를 사용합니다.

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

디렉터리로 `BikeSharingWeb` 이동하여 `azds up` 업데이트된 *BikeSharingWeb* 서비스를 실행하는 데 사용합니다.

```console
cd ../BikeSharingWeb/
azds up
```

*dev/azureuser1* 자식 공간의 샘플 응용 프로그램으로 이동하면 오류 없이 HTTPS를 사용하도록 리디렉션됩니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml