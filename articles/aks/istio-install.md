---
title: Azure Kubernetes Service(AKS)에서 Istio 설치
description: Istio를 설치 및 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법을 알아봅니다.
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465345"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에서 Istio 설치 및 사용

[Istio][istio-github]는 Kubernetes 클러스터에서 마이크로서비스에서 기능 키 세트를 제공하는 오픈 소스 서비스 메시입니다. 이 기능에는 트래픽 관리, 서비스 ID 및 보안, 정책 적용 및 관찰성을 포함됩니다. Istio에 대한 자세한 내용은 [Istio는 무엇인가?][istio-docs-concepts] 공식 설명서를 참조하세요.

이 문서에서는 Istio를 설치하는 방법을 보여줍니다. Istio `istioctl` 클라이언트 이진 파일이 클라이언트 머신에 설치된 다음 Istio 구성 요소는 AKS의 Kubernetes 클러스터에 설치됩니다. 이 지침은 Istio 버전 *1.0.4*를 참조합니다. [GitHub-Istio 릴리스][istio-github-releases]에서 추가 Istio 버전을 확인할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Istio 다운로드
> * Istio 클라이언트 이진 파일 설치
> * Istio Kubernetes 구성 요소 설치
> * 설치 유효성 검사

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 자세히 설명된 단계에서는 AKS 클러스터(Kubernetes 1.10 이상, RBAC 사용 설정됨)를 만들고 클러스터와 `kubectl`을 연결했다고 가정합니다. 이 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

Istio를 설치하려면 클러스터에서 [Helm][helm] 버전 *2.10.0* 이상을 올바르게 설치하고 구성해야 합니다. Helm 설치에 대한 도움이 필요한 경우 [AKS Helm 설치 지침][helm-install]을 참조하세요. Helm에 대한 최소 *2.10.0* 버전이 설치되지 않은 경우 업그레이드하거나, 다른 설치 옵션에 대한 [Istio - Helm 설치 지침][istio-install-helm]을 참조하세요.

이 문서에서는 몇 단계로 Istio 설치 지침을 구분합니다. 각 단계에서는 Istio를 설치하는 방법과 Istio가 Kubernetes에서 작동하는 방법에 대해 설명합니다. 최종 결과는 공식 설치 [지침][istio-install-k8s-quickstart]과 동일한 구조입니다.

## <a name="download-istio"></a>Istio 다운로드

첫째, 최신 Istio 릴리스를 다운로드하여 압축을 풉니다. 단계는 MacOS, Linux 또는 PowerShell 셸 및 Linux 용 Windows 하위 시스템에서 bash 셸용 약간 다릅니다. 선호하는 환경에 따라 다음 설치 단계 중 하나를 선택합니다.

* [MacOS, Linux, 또는 Windows Subsystem for Linux의 Bash](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

MacOS에서 `curl`을 사용하여 최신 Istio 릴리스를 다운로드한 후 다음과 같이 `tar`를 추출합니다.

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Linux 또는 Windows Subsystem for Linux에서 `curl`을 사용하여 최신 Istio 릴리스를 다운로드한 후 다음과 같이 `tar`를 추출합니다.

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

PowerShell에서 [Invoke-WebRequest][Invoke-WebRequest]를 사용하여 최신 Istio 릴리스를 다운로드한 후 다음과 같이 [Expand-Archive][Expand-Archive]를 추출합니다.

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Istio 클라이언트 이진 파일 설치

`istioctl` 클라이언트 이진 파일은 클라이언트 머신에서 실행되어 Istio 라우팅 규칙 및 정책을 관리할 수 있습니다. 또 설치 단계는 클라이언트 운영 체제 간에 약간 차이가 납니다. 사용자 환경에 따라 다음 설치 단계 중 하나를 선택 합니다.

> [!IMPORTANT]
> 다운로드 및 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 하는 것을 확인 합니다.

### <a name="macos"></a>MacOS

MacOS의 Bash 기반 셸에서 Istio `istioctl` 클라이언트 이진 파일을 설치하려면 다음 명령을 사용합니다. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Istio `istioctl` 클라이언트 이진 파일에 대해 명령줄 완료를 사용할 경우 다음과 설정합니다.

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

이제 [Istio Kubernetes 구성 요소 설치](#install-the-istio-kubernetes-components) 섹션으로 이동합니다.

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux 또는 Windows Subsystem for Linux

다음 명령을 사용하여 Linux 또는 [Windows Subsystem for Linux][install-wsl]의 Bash 기반 셸에 Istio `istioctl` 클라이언트 이진 파일을 설치합니다. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Istio `istioctl` 클라이언트 이진 파일에 대해 명령줄 완료를 사용할 경우 다음과 설정합니다.

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

이제 [Istio Kubernetes 구성 요소 설치](#install-the-istio-kubernetes-components) 섹션으로 이동합니다.

### <a name="windows"></a>Windows

Windows의 Powershell 기반 셸에서 Istio `istioctl` 클라이언트 이진 파일을 설치하려면 다음 명령을 사용합니다. 다음 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 새 사용자 프로그램 위치에 복사하고 `PATH`를 통해 사용할 수 있습니다.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Istio Kubernetes 구성 요소 설치

> [!IMPORTANT]
> 다운로드 및 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 하는 것을 확인 합니다.

> [!NOTE]
> 버전 `1.0.6` Istio Helm 차트의 최신 있고 주요 변경 내용입니다. 이 버전을 설치 하려면을 선택 하면 이제 해야 Kiali에 대 한 암호를 수동으로 만들려고 합니다. 수동으로 설정한 경우 Grafana에 대 한 암호를 만들 해야 `grafana.security.enabled=true`합니다. Istio Helm 차트를 볼 [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart) 이러한 암호를 만드는 방법에 대 한 자세한 내용은 합니다.

AKS 클러스터에 Istio 구성 요소를 설치하려면 Helm을 사용합니다. Istio 리소스를 `istio-system` 네임스페이스에 설치하고 다음과 같이 보안 및 모니터링에 대한 추가 옵션을 사용하도록 설정합니다.

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Helm 차트는 다수의 개체를 배포합니다. 클러스터 환경에 따라 배포를 완료하는 데 2~3분이 걸릴 수 있습니다.

## <a name="validate-the-installation"></a>설치 유효성 검사

Istio를 성공적으로 배포했는지 확인하려면, 설치유효성 검사를 합니다.

먼저 예상 서비스가 만들어졌는지 확인합니다. [kubectl get svc][kubectl-get] 명령을 사용하여 실행 중인 서비스를 봅니다. Helm 차트에서 Istio 및 추가 항목 구성 요소를 설치한 *istio-system* 네임스페이스를 쿼리합니다.

```console
kubectl get svc --namespace istio-system --output wide
```

다음 예제 출력에서는 실행되어야 하는 서비스를 보여줍니다.

- *istio-** 서비스
- *jaeger-**, *추적* 및 *zipkin* 추가 항목 추적 서비스
- *prometheus* 추가 항목 메트릭 서비스
- *grafana* 추가 항목 분석 및 모니터링 대시보드 서비스
- *kiali* 추가 항목 서비스 메시 대시보드 서비스

`istio-ingressgateway`에서 `<pending>`의 외부 IP를 표시하는 경우 Azure 네트워킹에서 IP 주소가 할당될 때까지 몇 분이 걸립니다.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

다음으로 필요한 Pod가 만들어졌는지 확인합니다. [kubectl get pods][kubectl-get] 명령을 사용하여 다시 *istio-system* 네임스페이스를 쿼리합니다.

```console
kubectl get pods --namespace istio-system
```

다음 예제 출력에서는 실행 중인 Pod을 보여줍니다.

- *istio-** Pod
- *prometheus-** 추가 항목 메트릭 Pod
- *grafana* 추가 항목 분석 및 모니터링 대시보드 Pod
- *kiali* 추가 항목 서비스 메시 대시보드 Pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

모든 Pod은 `Running` 상태를 보여줍니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고하는 경우 [kubectl describe pod][kubectl-describe] 명령을 사용하여 출력과 상태를 검토합니다.

## <a name="accessing-the-add-ons"></a>추가 항목 액세스

추가 기능을 제공하는 설정에서 Istio가 다양한 추가 항목을 설치했습니다. 추가 항목에 대한 사용자 인터페이스는 외부 IP 주소를 통해 공개적으로 노출되지 않습니다. 추가 항목 사용자 인터페이스에 액세스하려면 [kubectl 포트-착신 전환][kubectl-port-forward] 명령을 사용합니다. 이 명령은 클라이언트 머신의 로컬 포트 및 AKS 클러스터의 관련 pod 간에 안전한 연결을 만듭니다.

### <a name="grafana"></a>Grafana

Istio에 대한 분석 및 모니터링 대시보드는 [Grafana][grafana]에서 제공됩니다. 클라이언트 머신의 로컬 포트 *3000*을 AKS 클러스터에서 Grafana를 실행 중인 Pod의 *3000* 포트로 착신 전환합니다.

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

다음 예제 출력에서는 Grafana를구성하는 포트- 착신 전환을 보여줍니다.

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

이제 클라이언트 머신 - [http://localhost:3000](http://localhost:3000)에서 다음 URL에 Grafana를 연결할 수 있습니다.

### <a name="prometheus"></a>Prometheus

메트릭에 대한 식 브라우저는 [Prometheus][prometheus]에서 제공합니다. 클라이언트 머신의 로컬 포트 *9090*을 AKS 클러스터에서 Prometheus를 실행 중인 Pod의 *9090* 포트로 착신 전환합니다.

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

다음 예제 출력에서는 Prometheus를 구성하는 포트- 착신 전환을 보여줍니다.

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

이제 클라이언트 머신 - [http://localhost:9090](http://localhost:9090)에서 다음 URL의 Prometheus 식 브라우저를 연결할 수 있습니다.

### <a name="jaeger"></a>Jaeger

사용자 인터페이스 추적은 [Jaeger][jaeger]에서 제공합니다. 클라이언트 머신의 로컬 포트 *16686*을 AKS 클러스터에서 Jaeger를 실행 중인 Pod의 *16686* 포트로 착신 전환합니다.

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

다음 예제 출력에서는 Jaeger를구성하는 포트- 착신 전환을 보여 줍니다.

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

이제 클라이언트 머신 - [http://localhost:16686](http://localhost:16686)에서 다음 URL의 Jaeger 추적 사용자 인터페이스를 연결할 수 있습니다.

### <a name="kiali"></a>Kiali

서비스 메시 관찰성 대시보드는 [Kiali][kiali]에서 제공됩니다. 클라이언트 머신의 로컬 포트 *20001*을 AKS 클러스터에서 Kiali를 실행 중인 Pod의 *20001* 포트로 착신 전환합니다.

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

다음 예제 출력에서는 Kiali를구성하는 포트- 착신 전환을 보여줍니다.

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

클라이언트 머신 - [http://localhost:20001](http://localhost:20001)의 다음 URL에서 Kiali 서비스 메시 관찰성 대시보드를 연결할 수 있습니다.

Kiali 대시보드에 대한 기본 사용자 이름 및 암호는 *username:admin/password:admin*입니다. 이러한 자격 증명은 *username:admin/password:admin* 및 *kiali.dashboard.passphrase* Helm 값을 통해 구성될 수 있습니다.

## <a name="next-steps"></a>다음 단계

Istio를 사용하여 사용자 애플리케이션의 다중 버전 간에 인텔리전트 라우팅을 제공하고 카나리 릴리스를 출시하는 방법을 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [AKS Istio 인텔리전트 라우팅 시나리오][istio-scenario-routing]

Istio에 대한 자세한 설치 및 구성 옵션을 탐색하려면 다음 공식 Istio 문서를 참조하세요.

- [Istio-Kubernetes 설치 빠른 시작][istio-install-k8s-quickstart]
- [Istio - Helm 설치 지침][istio-install-helm]
- [Istio - Helm 설치 옵션][istio-install-helm-options]

또한 [Istio Bookinfo 애플리케이션 예제][istio-bookinfo-example]를 사용하여 추가 시나리오를 수행할 수 있습니다.

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
