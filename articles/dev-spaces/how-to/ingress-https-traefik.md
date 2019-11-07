---
title: 사용자 지정 traefik 수신 컨트롤러 사용 및 HTTPS 구성
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: 사용자 지정 traefik 수신 컨트롤러를 사용 하 고 해당 수신 컨트롤러를 사용 하 여 HTTPS를 구성 하도록 Azure Dev Spaces를 구성 하는 방법을 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: c015fe8e7108f07d66d2464c4f8b6287e8f54446
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582316"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>사용자 지정 traefik 수신 컨트롤러 사용 및 HTTPS 구성

이 문서에서는 사용자 지정 traefik 수신 컨트롤러를 사용 하도록 Azure Dev Spaces를 구성 하는 방법을 보여 줍니다. 또한이 문서에서는 HTTPS를 사용 하도록 사용자 지정 수신 컨트롤러를 구성 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 계정이 없는 경우 [무료 계정][azure-account-create]에 만들 수 있습니다.
* [Azure CLI 설치][az-cli]
* [Azure Dev Spaces 사용 하도록 설정 된 AKS (Azure Kubernetes Service) 클러스터][qs-cli].
* [kubectl][kubectl] 가 설치 되었습니다.
* [Helm 2.13 이상 설치][helm-installed]
* AKS 클러스터와 동일한 리소스 그룹의 [DNS 영역][dns-zone] 을 사용 하는 [사용자 지정 도메인][custom-domain] 입니다.

## <a name="configure-a-custom-traefik-ingress-controller"></a>사용자 지정 traefik 수신 컨트롤러 구성

[Kubectl][kubectl], Kubernetes 명령줄 클라이언트를 사용 하 여 클러스터에 연결 합니다. Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Traefik 수신 컨트롤러에 대 한 Kubernetes 네임 스페이스를 만들고 `helm`를 사용 하 여 설치 합니다.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

[Kubectl get][kubectl-get]을 사용 하 여 traefik 수신 컨트롤러 서비스의 IP 주소를 가져옵니다.

```console
kubectl get svc -n traefik --watch
```

샘플 출력은 *traefik* 이름 공간에 있는 모든 서비스에 대 한 IP 주소를 보여 줍니다.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

[Az network DNS record-add 레코드를][az-network-dns-record-set-a-add-record]사용 하 여 traefik 서비스의 외부 IP 주소를 사용 하 여 DNS 영역에 *A* 레코드를 추가 합니다.

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

위의 예에서는 *MY_CUSTOM_DOMAIN* DNS 영역에 *A* 레코드를 추가 합니다.

이 문서에서는 [Azure Dev Spaces 자전거 공유 샘플 애플리케이션](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)을 사용하여 Azure Dev Spaces 사용하는 방법을 보여 줍니다. GitHub에서 애플리케이션을 복제하고 해당 디렉터리로 이동합니다.

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

*REPLACE_ME_WITH_HOST_SUFFIX > <* 모든 인스턴스를 traefik로 교체 하 고 값을 엽니다 [.][values-yaml]  *MY_CUSTOM_DOMAIN* 도메인을 사용 하 여 *MY_CUSTOM_DOMAIN*합니다. 또한 *kubernetes.io/ingress.class: traefik # Custom 수신*과 *kubernetes.io/ingress.class: traefik-Azds # Dev Spaces* 를 바꿉니다. 다음은 업데이트 된 `values.yaml` 파일의 예입니다.

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

변경 내용을 저장하고 파일을 닫습니다.

`helm install`를 사용 하 여 샘플 응용 프로그램을 배포 합니다.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

위의 예제에서는 *dev* 네임 스페이스에 샘플 응용 프로그램을 배포 합니다.

`azds space select`를 사용 하 여 샘플 응용 프로그램에서 *개발* 공간을 선택 하 고 `azds list-uris`를 사용 하 여 샘플 응용 프로그램에 액세스 하는 url을 표시 합니다.

```console
azds space select -n dev
azds list-uris
```

아래 출력은 `azds list-uris`의 Url 예제를 보여 줍니다.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

*명령에서 공용 URL을 열어*bikesharingweb`azds list-uris` 서비스로 이동합니다. 위의 예제에서 *bikesharingweb* 서비스에 대한 공용 URL은 `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`입니다.

`azds space select` 명령을 사용 하 여 *개발* 중인 하위 공간을 만들고 url을 나열 하 여 자식 dev 공간에 액세스 합니다.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

아래 출력은 *azureuser1* 자식 개발 공간의 샘플 응용 프로그램에 액세스 하기 위해 `azds list-uris`의 url 예제를 보여 줍니다.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

`azds list-uris` 명령에서 공용 URL을 열어 *azureuser1* 자식 개발 공간의 *bikesharingweb* 서비스로 이동 합니다. 위의 예제에서 *azureuser1* 자식 개발 공간의 *bikesharingweb* 서비스에 대 한 공용 URL은 `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`입니다.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>HTTPS를 사용 하도록 traefik 수신 컨트롤러 구성

아래 예제와 비슷한 `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` 파일을 만듭니다. 사용자 고유의 전자 메일을 사용 하 여 *전자 메일* 값을 업데이트 합니다. 그러면 암호화를 통해 인증서를 생성 하는 데 사용 됩니다.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

만든 *traefik* 파일을 포함 하 여 `helm repo update`를 사용 하 여 *traefik* 서비스를 업데이트 합니다.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

위의 명령은 *traefik* 의 값을 사용 하 여 새 버전의 traefik 서비스를 실행 하 고 이전 서비스를 제거 합니다. 또한 traefik 서비스는 HTTPS를 사용 하도록 웹 트래픽 리디렉션을 암호화 하 고 시작 하 여 TLS 인증서를 만듭니다.

> [!NOTE]
> 새 버전의 traefik 서비스를 시작 하는 데 몇 분 정도 걸릴 수 있습니다. `kubectl get pods --namespace traefik --watch`를 사용 하 여 진행률을 확인할 수 있습니다.

*Dev/azureuser1* 자식 공간의 샘플 응용 프로그램으로 이동 하 여 HTTPS를 사용 하도록 리디렉션됩니다. 또한 페이지가 로드 되지만 브라우저에 몇 가지 오류가 표시 됩니다. 브라우저 콘솔을 열면 HTTP 리소스를 로드 하려는 HTTPS 페이지와 관련 된 오류가 표시 됩니다. 예:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

이 오류를 해결 하려면 [BikeSharingWeb/azds][azds-yaml] 을 업데이트 하 여 *traefik* 에 *kubernetes.io/ingress.class* 를 사용 하 고 *$ (hostsuffix)* 사용자 지정 도메인을 사용 합니다. 예:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

*Url* 패키지에 대 한 종속성으로 [BikeSharingWeb/package][package-json] 를 업데이트 합니다.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

[BikeSharingWeb/pages/default.js][helpers-js] 에서 HTTPS를 사용 하도록 *getApiHostAsync* 메서드를 업데이트 합니다.

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

`BikeSharingWeb` 디렉터리로 이동 하 여 `azds up`를 사용 하 여 업데이트 된 BikeSharingWeb 서비스를 실행 합니다.

```console
cd BikeSharingWeb/
azds up
```

*Dev/azureuser1* 자식 공간의 샘플 응용 프로그램으로 이동 하면 오류 없이 HTTPS를 사용 하도록 리디렉션됩니다.

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
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml