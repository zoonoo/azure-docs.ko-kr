---
title: Azure Kubernetes Service(AKS)에서 Istio 설치
description: Istio를 설치 및 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법을 알아봅니다.
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: c7c234e181e10499e532436bfde05ed89bdc7d28
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465704"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에서 Istio 설치 및 사용

[Istio][istio-github] is an open-source service mesh that provides a key set of functionality across the microservices in a Kubernetes cluster. These features include traffic management, service identity and security, policy enforcement, and observability. For more information about Istio, see the official [What is Istio?][istio-docs-concepts] 설명서.

이 문서에서는 Istio를 설치하는 방법을 보여줍니다. Istio `istioctl` 클라이언트 이진를 클라이언트 컴퓨터에 설치 되어 있고 Istio 구성 요소는 AKS에서 Kubernetes 클러스터에 설치 됩니다.

> [!NOTE]
> 이들이 명령은 Istio 버전 참조 `1.1.3`합니다.
>
> Istio `1.1.x` 릴리스 Kubernetes 버전에 대해 Istio 팀에서 테스트 되었습니다 `1.11`를 `1.12`, `1.13`합니다. 추가 Istio 버전을 찾을 수 있습니다 [GitHub-Istio 릴리스][istio-github-releases] and information about each of the releases at [Istio - Release Notes][istio-release-notes]합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Istio 다운로드
> * 이진 Istio istioctl 클라이언트를 설치 합니다.
> * AKS에서 Istio CRDs 설치
> * AKS에서 Istio 구성 요소를 설치 합니다.
> * Istio 설치 유효성 검사
> * 추가 항목 액세스
> * AKS에서 Istio를 제거 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에 설명 된 단계 가정 AKS 클러스터를 만든 (Kubernetes `1.11` 이상에서 RBAC를 사용 하 여 사용 하도록 설정 하 고) 설정 하 고는 `kubectl` 클러스터와 연결 합니다. 이러한 항목 중 하나를 사용 하 여 도움이 필요한 경우 다음 참조를 [AKS 빠른 시작][aks-quickstart]합니다.

해야 [Helm][helm] 이러한 지침 및 Istio를 설치 합니다. 버전을 갖고 있는 것이 좋습니다. `2.12.2` 또는 나중에 올바르게 설치 하 고 클러스터에서 구성 합니다. Helm을 설치 하 여 도움이 필요한 경우 다음 참조를 [AKS Helm 설치 지침은][helm-install]합니다. 또한 모든 Istio pod Linux 노드에서 실행 되도록 예약 해야 합니다.

이 문서에서는 몇 단계로 Istio 설치 지침을 구분합니다. 최종 결과 공식 Istio 설치와 구조의 동일 [지침][istio-install-helm]합니다.

## <a name="download-istio"></a>Istio 다운로드

첫째, 최신 Istio 릴리스를 다운로드하여 압축을 풉니다. 단계는 MacOS, Linux 또는 PowerShell 셸 및 Linux 용 Windows 하위 시스템에서 bash 셸용 약간 다릅니다. 기본 환경에 맞는 다음 설치 단계 중 하나를 선택 합니다.

* [MacOS, Linux, 또는 Windows Subsystem for Linux의 Bash](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

MacOS에서 `curl`을 사용하여 최신 Istio 릴리스를 다운로드한 후 다음과 같이 `tar`를 추출합니다.

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Linux 또는 Windows Subsystem for Linux에서 `curl`을 사용하여 최신 Istio 릴리스를 다운로드한 후 다음과 같이 `tar`를 추출합니다.

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

이제 섹션으로 이동할 [Istio istioctl 클라이언트 이진 설치](#install-the-istio-istioctl-client-binary)합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용 하 여 `Invoke-WebRequest` 최신 Istio 릴리스를 다운로드 하 고 사용 하 여 다음 압축을 해제 하려면 `Expand-Archive` 다음과 같습니다.

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

이제 섹션으로 이동할 [Istio istioctl 클라이언트 이진 설치](#install-the-istio-istioctl-client-binary)합니다.

## <a name="install-the-istio-istioctl-client-binary"></a>이진 Istio istioctl 클라이언트를 설치 합니다.

> [!IMPORTANT]
> 다운로드 및 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 하는 것을 확인 합니다.

`istioctl` 클라이언트 이진 클라이언트 컴퓨터에서 실행 되 고 Istio 서비스 메시를 사용 하 여 상호 작용할 수 있습니다. 설치 단계는 클라이언트 운영 체제 간에 약간 다릅니다. 기본 환경에 맞는 다음 설치 단계 중 하나를 선택 합니다.

* [MacOS](#macos)
* [Linux 또는 Linux 용 Windows 하위 시스템](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

MacOS의 Bash 기반 셸에서 Istio `istioctl` 클라이언트 이진 파일을 설치하려면 다음 명령을 사용합니다. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

이제 다음 섹션으로 이동할 [AKS에 Istio CRDs 설치](#install-the-istio-crds-on-aks)합니다.

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux 또는 Windows Subsystem for Linux

다음 명령을 사용 하 여 설치를 Istio `istioctl` 클라이언트 linux 기반 bash 셸에서 이진 또는 [Linux 용 Windows 하위 시스템][install-wsl]. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

이제 다음 섹션으로 이동할 [AKS에 Istio CRDs 설치](#install-the-istio-crds-on-aks)합니다.

### <a name="windows"></a>Windows

Istio를 설치 하려면 `istioctl` 클라이언트에서 이진을 **Powershell**-Windows 기반으로 셸에서 다음 명령을 사용 합니다. 다음이 명령을 복사 합니다 `istioctl` Istio 폴더에 이진 클라이언트 통해 영구적으로 사용할 수 있도록 프로그램 `PATH`합니다. 다음이 명령을 실행 하려면 상승 된 (관리자) 권한이 필요 하지 않습니다.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

이제 다음 섹션으로 이동할 [AKS에 Istio CRDs 설치](#install-the-istio-crds-on-aks)합니다.

## <a name="install-the-istio-crds-on-aks"></a>AKS에서 Istio CRDs 설치

> [!IMPORTANT]
> 다운로드 및 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 하는 것을 확인 합니다.

Istio를 사용 하 여 [사용자 지정 리소스 정의 (CRDs)][kubernetes-crd] 해당 런타임 구성을 관리 합니다. Istio 구성 요소에 대 한 종속성 없으므로 Istio CRDs를 먼저 설치 해야 합니다. Helm을 사용 하며 `istio-init` Istio CRDs에 설치 하는 차트를 `istio-system` AKS 클러스터에 대 한 네임 스페이스:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[작업][kubernetes-jobs] 의 일부로 배포 되는 `istio-init` Helm 차트는 CRDs를 설치 합니다. 이러한 작업은 클러스터 환경에 따라 완료 하는 데 1 ~ 2 분 사이가 소요 됩니다. 작업을 다음과 같이 완료를 확인할 수 있습니다.

```azurecli
kubectl get jobs -n istio-system
```

다음 예제 출력에서는 성공적으로 완료 된 작업을 보여 줍니다.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

성공적으로 완료 된 작업의 확인 했으므로, 이제는 보겠습니다 Istio CRDs 설치의 정확한 수 있다는 것을 확인 합니다. 사용자 환경에 대 한 적절 한 명령을 실행 하 여 모든 53 Istio CRDs 설치 되어 있는지 확인할 수 있습니다. 명령 수를 반환 해야 `53`합니다.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

이 지점에 있다면 다음 즉 Istio CRDs 설치 했습니다. 이제 다음 섹션으로 이동할 [AKS에 Istio 구성 요소 설치](#install-the-istio-components-on-aks)합니다.

## <a name="install-the-istio-components-on-aks"></a>AKS에서 Istio 구성 요소를 설치 합니다.

> [!IMPORTANT]
> 다운로드 및 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 하는 것을 확인 합니다.

에서는 설치 하겠습니다 [Grafana][grafana] and [Kiali][kiali] 우리의 Istio 설치의 일부로. Grafana를 분석 및 모니터링 대시보드를 제공 하 고 Kiali 서비스 메시 관찰성 대시보드를 제공 합니다. 당사 설정을 통해 이러한 각 구성이 요소 필요으로 제공 해야 하는 자격 증명을 [비밀][kubernetes 비밀]합니다.

Istio 구성 요소를 설치할 수 있습니다, 전에 Grafana와 Kiali에 대 한 암호를 만들어야 합니다. 사용자 환경에 대 한 적절 한 명령을 실행 하 여 이러한 비밀을 만듭니다.

### <a name="add-grafana-secret"></a>Grafana 비밀 추가

대체는 `REPLACE_WITH_YOUR_SECURE_PASSWORD` 암호를 사용 하 여 토큰 및 다음 명령을 실행 합니다.

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Kiali 비밀 추가

대체는 `REPLACE_WITH_YOUR_SECURE_PASSWORD` 암호를 사용 하 여 토큰 및 다음 명령을 실행 합니다.

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Istio 구성 요소를 설치 합니다.

이제 AKS 클러스터에서 Grafana 및 Kiali 암호를 성공적으로 만들었습니다 Istio 구성 요소를 설치 하는 시간입니다. Helm을 사용 하며 `istio` Istio 구성 요소를 설치 하는 차트를 `istio-system` AKS 클러스터에 대 한 네임 스페이스입니다. 사용자 환경에 대 한 적절 한 명령을 사용 합니다.

> [!NOTE]
> 이 설치의 일부분으로 다음 옵션을 사용 하는 것:
> - `global.controlPlaneSecurityEnabled=true` -제어 평면에 대 한 사용 하도록 설정 하는 상호 TLS
> - `mixer.adapters.useAdapterCRDs=false` -감시 CRDs mixer 어댑터에서 제거 되지 않으며이 성능이 향상 됩니다
> - `grafana.enabled=true` -분석용 Grafana 배포를 사용 하도록 설정 하 고 모니터링 대시보드
> - `grafana.security.enabled=true` -Grafana에 대 한 인증을 사용 하도록 설정
> - `tracing.enabled=true` -추적 Jaeger 배포 사용
> - `kiali.enabled=true` -서비스 메시 관찰성 대시보드에 대 한 Kiali 배포 사용

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

`istio` Helm 차트는 다 수의 개체를 배포 합니다. 출력의 목록을 볼 수 있습니다 프로그램 `helm install` 위의 명령입니다. Istio 구성 요소의 배포는 클러스터 환경에 따라 완료 하는 데 4 ~ 5 분 정도 걸릴 수 있습니다.

> [!NOTE]
> 모든 Istio pod Linux 노드에서 실행 되도록 예약 해야 합니다. Windows Server 노드 풀 Linux 노드 풀 외에도 클러스터에 있는 경우 모든 Istio pod Linux 노드에서 실행 되도록 예약 된가 있는지 확인 합니다.

이 시점에서 Istio AKS 클러스터에 배포 했습니다. Istio 성공적으로 배포 된 것을 보장 하려면 다음 섹션을 보겠습니다으로 이동 [Istio 설치 유효성 검사](#validate-the-istio-installation)합니다.

## <a name="validate-the-istio-installation"></a>Istio 설치 유효성 검사

먼저 예상 서비스가 만들어졌는지 확인합니다. 사용 합니다 [kubectl get svc][kubectl-get] 실행 중인 서비스를 보려면 명령입니다. 쿼리는 `istio-system` 네임 스페이스에서 Istio 및 추가 기능 구성 요소를 설치한 위치를 `istio` Helm 차트:

```console
kubectl get svc --namespace istio-system --output wide
```

다음 예제 출력에서는 실행되어야 하는 서비스를 보여줍니다.

- `istio-*` 서비스
- `jaeger-*`하십시오 `tracing`, 및 `zipkin` 추가 기능 추적 서비스
- `prometheus` 추가 기능 metrics 서비스
- `grafana` 추가 분석 및 모니터링 대시보드 서비스
- `kiali` 추가 기능 서비스 메시 대시보드

`istio-ingressgateway`에서 `<pending>`의 외부 IP를 표시하는 경우 Azure 네트워킹에서 IP 주소가 할당될 때까지 몇 분이 걸립니다.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

다음으로 필요한 Pod가 만들어졌는지 확인합니다. 사용 된 [kubectl get pod][kubectl-get] 명령을 실행 하 고 다시 쿼리를 `istio-system` 네임 스페이스:

```console
kubectl get pods --namespace istio-system
```

다음 예제 출력에서는 실행 중인 Pod을 보여줍니다.

- `istio-*` pod
- `prometheus-*` 메트릭 pod 추가 기능
- `grafana-*` 추가 분석 및 모니터링의 대시보드 포드
- `kiali` 추가 기능 서비스 메시의 대시보드 포드

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

두 있어야 `istio-init-crd-*` 사용 하 여 포드를 `Completed` 상태입니다. 이러한 pod를 CRDs 이전 단계에서 만든 작업을 실행 하는 일을 담당 했습니다. 모든 다른 pod의 상태를 표시 해야 `Running`합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. 모든 pod는 문제를 보고 하는 경우 사용 합니다 [kubectl pod에 설명][kubectl-describe] 상태와 출력을 검토 하려면 명령 합니다.

## <a name="accessing-the-add-ons"></a>추가 항목 액세스

추가 기능을 제공하는 설정에서 Istio가 다양한 추가 항목을 설치했습니다. 추가 항목에 대한 사용자 인터페이스는 외부 IP 주소를 통해 공개적으로 노출되지 않습니다. 추가 기능 사용자 인터페이스에 액세스 하려면 사용 합니다 [kubectl 포트 전달][kubectl-port-forward] 명령입니다. 이 명령은 AKS 클러스터의 클라이언트 컴퓨터와 관련 된 pod 간에 보안 연결을 만듭니다.

이 문서의 앞부분에 자격 증명을 지정 하 여 Grafana 및 Kiali에 대 한 추가 보안 계층을 추가 했습니다.

### <a name="grafana"></a>Grafana

분석 및 대시보드 Istio에 대 한 모니터링 정보를 제공 [Grafana][grafana]합니다. 로컬 포트 전달 `3000` 포트에 클라이언트 컴퓨터에서 `3000` AKS 클러스터에서 Grafana를 실행 중인 pod에서:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

다음 예제 출력에서는 Grafana를구성하는 포트- 착신 전환을 보여줍니다.

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

이제 클라이언트 머신 - [http://localhost:3000](http://localhost:3000)에서 다음 URL에 Grafana를 연결할 수 있습니다. 이전에 만든 Grafana를 통해 비밀 메시지가 표시 되 면 자격 증명을 사용 해야 합니다.

### <a name="prometheus"></a>Prometheus

제공 하는 Istio에 대 한 메트릭을 [Prometheus][prometheus]합니다. 로컬 포트 전달 `9090` 포트에 클라이언트 컴퓨터에서 `9090` Prometheus AKS 클러스터에서 실행 중인 pod에서:

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

제공 하는 추적 Istio 내 [Jaeger][jaeger]합니다. 로컬 포트 전달 `16686` 포트에 클라이언트 컴퓨터에서 `16686` Jaeger AKS 클러스터에서 실행 중인 pod에서:

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

서비스 메시 관찰성 대시보드가 제공한 [Kiali][kiali]합니다. 로컬 포트 전달 `20001` 포트에 클라이언트 컴퓨터에서 `20001` Kiali AKS 클러스터에서 실행 중인 pod에서:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

다음 예제 출력에서는 Kiali를구성하는 포트- 착신 전환을 보여줍니다.

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

클라이언트 머신 - [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/)의 다음 URL에서 Kiali 서비스 메시 관찰성 대시보드를 연결할 수 있습니다. 이전에 만든 된 Kiali 통해 비밀 메시지가 표시 되 면 자격 증명을 사용 해야 합니다.

## <a name="uninstall-istio-from-aks"></a>AKS에서 Istio를 제거 합니다.

> [!WARNING]
> 트래픽이 발생할 Istio 실행 중인 시스템에서 삭제 관련 서비스 간 문제입니다. 여전히 제대로 작동 하려면 Istio 하지 않고 계속 하기 전에 시스템에 대 한 프로 비전 했다고 확인 합니다.

### <a name="remove-istio-components-and-namespace"></a>Istio 구성 요소 및 네임 스페이스를 제거 합니다.

Istio와 AKS 클러스터에서 제거 하려면 다음 명령을 사용 합니다. `helm delete` 명령을 제거 합니다는 `istio` 및 `istio-init` 차트 및 `kubectl delete ns` 명령 제거 됩니다는 `istio-system` 네임 스페이스입니다.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Istio CRDs 제거

위의 명령은 모든 Istio 구성 요소와 네임 스페이스를 삭제 합니다. 하지만 Istio CRDs를 사용 하 여 왼쪽 계속 합니다. CRDs를 삭제 하려면 사용할 수 있습니다 하나는 다음 방법 중 하나.

방법 #1-이 명령은 릴리스의 다운로드 및 추출한 Istio와 Istio를 설치 하는 데는 최상위 폴더에서이 단계를 실행 하는 가정 합니다.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Istio와 Istio를 설치 하는 데는 다운로드 및 추출한 출시에 대 한 액세스를 더 이상 있는 경우 다음이 명령 중 하나를 사용 하는 #2-접근 방식입니다. 이 명령은-약간 더 오래 걸립니다. 완료 하려면 몇 분 정도 걸립니다.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>다음 단계

다음 설명서에서는 Istio를 사용 하 여 카나리아 릴리스를 롤아웃 하려면 지능형 라우팅을 제공 하는 방법을 설명 합니다.

> [!div class="nextstepaction"]
> [AKS Istio 지능형 라우팅 시나리오][istio-scenario-routing]

Istio에 대한 자세한 설치 및 구성 옵션을 탐색하려면 다음 공식 Istio 문서를 참조하세요.

- [Istio-Helm 설치 가이드][istio-install-helm]
- [Istio-Helm 설치 옵션][istio-install-helm-options]

추가 시나리오를 사용 하 여 수행할 수 있습니다 합니다 [Istio Bookinfo 응용 프로그램 예제][istio-bookinfo-example]합니다.

Application Insights와 Istio를 사용 하 여 AKS 응용 프로그램을 모니터링 하는 방법에 알아보려면 다음 Azure Monitor 설명서를 참조 합니다.
- [호스트 응용 프로그램을 Kubernetes 0 계측 응용 프로그램 모니터링][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
