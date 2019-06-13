---
title: AKS(Azure Kubernetes Service)에 기본 수신 컨트롤러 만들기
description: AKS(Azure Kubernetes Service) 클러스터에 기본 NGINX 수신 컨트롤러를 설치하고 구성하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: b0dfe69a77d236e7a811ca5c7407428327c62ff3
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430989"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. 두 애플리케이션이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 애플리케이션에 액세스할 수 있습니다.

또한 다음을 수행할 수 있습니다.

- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 개인 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- Let’s Encrypt를 사용하여 [동적 공용 IP 주소][aks-ingress-tls] 또는 [고정 공용 IP 주소][aks-ingress-static-tls]로 TLS 인증서를 자동으로 생성하는 수신 컨트롤러 만들기

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Helm을 사용하여 NGINX 수신 컨트롤러, cert-manager 및 샘플 웹앱을 설치합니다. AKS 클러스터 내에서, Tiller의 서비스 계정을 사용하여 Helm이 초기화되어 있어야 합니다. Helm을 구성하고 사용하는 방법에 대한 자세한 내용은 [Helm을 사용하여 AKS(Azure Kubernetes Service)에 애플리케이션 설치][use-helm]를 참조하세요.

이 문서에서는 Azure CLI 버전 2.0.64 중인지 필요 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

수신 컨트롤러를 만들려면 `Helm`을 사용하여 *nginx-ingress*를 설치합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러는 또한 Linux 노드에서 예약 해야 합니다. (현재 AKS에서 미리 보기)는에서 Windows 서버 노드에서 수신 컨트롤러를 실행 해서는 안 됩니다. 노드 선택기를 사용 하 여 지정 된 `--set nodeSelector` Kubernetes 스케줄러에서 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행 하도록 지시 하려면 매개 변수입니다.

> [!TIP]
> 다음 예제에서는 명명 된 수신 리소스에 대 한 Kubernetes 네임 스페이스를 만듭니다 *수신 basic*합니다. 필요에 따라 사용자 고유의 환경에 대 한 네임 스페이스를 지정 합니다. AKS 클러스터 RBAC를 사용할 수 없는 경우 추가 `--set rbac.create=false` Helm 명령입니다.

> [!TIP]
> 사용 하도록 설정 하려는 경우 [클라이언트 소스 IP 보존] [ client-source-ip] 클러스터에서 컨테이너에 대 한 요청에 대 한 추가 `--set controller.service.externalTrafficPolicy=Local` 를 Helm 설치 명령을 합니다. 클라이언트 원본 IP에서 요청 헤더에 저장 됩니다 *X-전달 기능에 대 한*합니다. 사용 하도록 설정 하는 클라이언트 소스 IP 보존을 사용 하 여 수신 컨트롤러를 사용 하는 경우 SSL 통과 작동 하지 않습니다.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

NGINX 수신 컨트롤러에 대해 Kubernetes 부하 분산 장치 서비스를 만든 경우 다음 예제 출력에 표시된 대로 동적 공용 IP 주소를 할당합니다.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

아직 수신 규칙이 만들어지지 않았으므로 내부 IP 주소를 검색하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

## <a name="run-demo-applications"></a>데모 애플리케이션 실행

작동 중인 수신 컨트롤러를 확인하기 위해 AKS 클러스터에서 두 개의 데모 애플리케이션을 실행하겠습니다. 이 예제에서는 Helm을 사용하여 간단한 ‘Hello world’ 애플리케이션의 두 인스턴스를 배포합니다.

샘플 Helm 차트를 설치하려면, 먼저 다음과 같이 Azure 샘플 리포지토리를 Helm 환경에 추가합니다.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

다음 명령을 사용하여 Helm 차트에서 첫 번째 데모 애플리케이션을 만듭니다.

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

이제 데모 애플리케이션의 두 번째 인스턴스를 설치합니다. 두 번째 인스턴스에서, 두 애플리케이션을 시각적으로 구분할 수 있도록 새 제목을 지정합니다. 고유한 서비스 이름도 지정합니다.

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>수신 경로 만들기

이제 두 애플리케이션이 모두 Kubernetes 클러스터에서 실행됩니다. 트래픽을 각 애플리케이션으로 라우팅하려면 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 애플리케이션 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서 주소 `http://40.117.74.8/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우트됩니다. 주소 `http://40.117.74.8/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다.

`hello-world-ingress.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

`kubectl apply -f hello-world-ingress.yaml` 명령을 사용하여 수신 리소스를 만듭니다.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>수신 컨트롤러 테스트

수신 컨트롤러의 경로를 테스트하려면 두 개의 애플리케이션으로 이동합니다. 웹 브라우저에서 NGINX 수신 컨트롤러의 IP 주소(예: *http://40.117.74.8* )를 엽니다. 첫 번째 데모 애플리케이션은 다음 예제와 같이 웹 브라우저에 표시됩니다.

![수신 컨트롤러 뒤에서 실행 중인 첫 번째 앱](media/ingress-basic/app-one.png)

이제 *http://40.117.74.8/hello-world-two* 와 같은 IP 주소에 */hello-world-two* 경로를 추가합니다. 사용자 지정 제목이 있는 두 번째 데모 애플리케이션이 표시됩니다.

![수신 컨트롤러 뒤에서 실행 중인 두 번째 앱](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소 및 샘플 앱을 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 하거나 이러한 리소스를 정리 하려면 전체 샘플 네임 스페이스 또는 개별 리소스를 삭제할 수 있습니다.

### <a name="delete-the-sample-namespace-and-all-resources"></a>샘플 네임 스페이스 및 모든 리소스 삭제

전체 샘플 네임 스페이스를 삭제 하려면 사용 된 `kubectl delete` 명령 및 네임 스페이스 이름을 지정 합니다. 네임 스페이스의 모든 리소스가 삭제 됩니다.

```console
kubectl delete namespace ingress-basic
```

그런 다음 AKS hello world 앱에 대 한 Helm 리포지토리를 제거 합니다.

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>리소스를 개별적으로 삭제

또는 만든 개별 리소스를 삭제 하는 보다 세분화 된 방법이입니다. 목록을 사용 하 여 Helm 릴리스를 `helm list` 명령입니다. 다음 예제 출력과 같이 이름이 *nginx-ingress* 및 *aks-helloworld*인 차트를 찾습니다.

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

`helm delete` 명령으로 해당 릴리스를 삭제합니다. 다음 예제는 NGINX 수신 배포와 두 개의 샘플 AKS Hello World 앱을 삭제합니다.

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
```

다음으로, AKS Hello World 앱에 대한 Helm 리포지토리를 제거합니다.

```console
helm repo remove azure-samples
```

트래픽을 샘플 앱으로 유도한 수신 경로를 제거합니다.

```console
kubectl delete -f hello-world-ingress.yaml
```

마지막으로, 네임 스페이스는 자체 것을 삭제할 수 있습니다. 사용 된 `kubectl delete` 명령 및 네임 스페이스 이름을 지정 합니다.

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]

또한 다음을 수행할 수 있습니다.

- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 개인 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- Let’s Encrypt를 사용하여 [동적 공용 IP 주소][aks-ingress-tls] 또는 [고정 공용 IP 주소][aks-ingress-static-tls]로 TLS 인증서를 자동으로 생성하는 수신 컨트롤러 만들기

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
