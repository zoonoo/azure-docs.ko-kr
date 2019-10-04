---
title: Azure Kubernetes Service(AKS)에서 Istio 설치
description: Istio를 설치 및 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법을 알아봅니다.
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9344d2832c37c34d5690dc8f3aae7394ca644276
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827318"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에서 Istio 설치 및 사용

[Istio][istio-github] 는 Kubernetes 클러스터의 마이크로 서비스에서 주요 기능 집합을 제공 하는 오픈 소스 서비스 메시입니다. 이 기능에는 트래픽 관리, 서비스 ID 및 보안, 정책 적용 및 관찰성을 포함됩니다. Istio에 대 한 자세한 내용은 [Istio 란 공식 이란?][istio-docs-concepts] 설명서를 참조 하세요.

이 문서에서는 Istio를 설치하는 방법을 보여줍니다. Istio `istioctl` 클라이언트 이진 파일은 클라이언트 컴퓨터에 설치 되 고 Istio 구성 요소는 AKS의 Kubernetes 클러스터에 설치 됩니다.

> [!NOTE]
> 이러한 지침은 Istio 버전 `1.1.3`을 참조 합니다.
>
> Istio @no__t 버전 `1.11`, `1.12`, `1.13`에 대 한 istio 팀에서 Istio-0 릴리스를 테스트 했습니다. [GitHub][istio-github-releases] 에서의 추가 istio 버전 및 [istio 뉴스][istio-release-notes]의 각 릴리스에 대 한 정보를 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Istio 다운로드
> * Istio istio 클라이언트 이진을 설치 합니다.
> * AKS에 Istio CRDs를 설치 합니다.
> * AKS에 Istio 구성 요소를 설치 합니다.
> * Istio 설치 유효성 검사
> * 추가 항목 액세스
> * AKS에서 Istio 제거

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서 설명 하는 단계에서는 AKS 클러스터 (RBAC를 사용 하 여 Kubernetes `1.11` 이상)를 만들고 클러스터와의 @no__t 1 연결을 설정 했다고 가정 합니다. 이러한 항목에 대 한 도움이 필요한 경우 [AKS 빠른][aks-quickstart]시작을 참조 하세요.

이러한 지침을 따르고 Istio를 설치 하려면 [투구][helm] 가 필요 합니다. 클러스터에 설치 하 고 구성 된 버전 `2.12.2` 이상 버전을 설치 하는 것이 좋습니다. 투구 설치와 관련 하 여 도움이 필요한 경우 [AKS 투구 설치 지침][helm-install]을 참조 하세요. 모든 Istio pod Linux 노드에서 실행 되도록 예약 되어야 합니다.

AKS 클러스터에서 Istio를 실행 하기 위한 추가 리소스 요구 사항을 이해 하려면 [Istio 성능 및 확장성](https://istio.io/docs/concepts/performance-and-scalability/) 설명서를 참조 하세요. 코어 및 메모리 요구 사항은 특정 워크 로드에 따라 달라 집니다. 설치에 사용할 적절 한 수의 노드 및 VM 크기를 선택 합니다.

이 문서에서는 몇 단계로 Istio 설치 지침을 구분합니다. 최종 결과는 공식 Istio 설치 [지침][istio-install-helm]과 동일한 구조에서 동일 합니다.

## <a name="download-istio"></a>Istio 다운로드

첫째, 최신 Istio 릴리스를 다운로드하여 압축을 풉니다. 이러한 단계는 MacOS, Linux 또는 Linux 용 Windows 하위 시스템 및 PowerShell 셸에서의 bash 셸에서 약간 다릅니다. 기본 설정 된 환경과 일치 하는 다음 설치 단계 중 하나를 선택 합니다.

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

이제 섹션으로 이동 하 여 [Istio istio 클라이언트 이진을 설치](#install-the-istio-istioctl-client-binary)합니다.

### <a name="powershell"></a>PowerShell

PowerShell에서 `Invoke-WebRequest`을 사용 하 여 최신 Istio 릴리스를 다운로드 한 후 다음과 같이 `Expand-Archive`을 사용 하 여 추출 합니다.

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

이제 섹션으로 이동 하 여 [Istio istio 클라이언트 이진을 설치](#install-the-istio-istioctl-client-binary)합니다.

## <a name="install-the-istio-istioctl-client-binary"></a>Istio istio 클라이언트 이진을 설치 합니다.

> [!IMPORTANT]
> 다운로드 하 여 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 했는지 확인 합니다.

@No__t-0 클라이언트 이진은 클라이언트 컴퓨터에서 실행 되며 Istio 서비스 메시와 상호 작용할 수 있습니다. 설치 단계는 클라이언트 운영 체제 마다 약간 다릅니다. 기본 설정 된 환경과 일치 하는 다음 설치 단계 중 하나를 선택 합니다.

* [MacOS](#macos)
* [Linux 또는 linux 용 Windows 하위 시스템](#linux-or-windows-subsystem-for-linux)
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

이제 다음 섹션으로 이동 하 여 [AKS에 Istio CRDs를 설치](#install-the-istio-crds-on-aks)합니다.

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux 또는 Windows Subsystem for Linux

다음 명령을 사용 하 여 linux의 bash 기반 셸 또는 [linux 용 Windows 하위 시스템][install-wsl]에 istio `istioctl` 클라이언트 이진을 설치 합니다. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

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

이제 다음 섹션으로 이동 하 여 [AKS에 Istio CRDs를 설치](#install-the-istio-crds-on-aks)합니다.

### <a name="windows"></a>Windows

Windows의 **Powershell**기반 셸에서 istio `istioctl` 클라이언트 이진을 설치 하려면 다음 명령을 사용 합니다. 이 명령은 `istioctl` 클라이언트 이진 파일을 Istio 폴더에 복사한 다음, `PATH`을 통해 즉시 (현재 셸에서)와 영구적으로 (셸 다시 시작에서) 모두 사용할 수 있도록 설정 합니다. 이러한 명령을 실행 하는 데에는 상승 된 (관리자) 권한이 필요 하지 않으며 셸을 다시 시작 하지 않아도 됩니다.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

이제 다음 섹션으로 이동 하 여 [AKS에 Istio CRDs를 설치](#install-the-istio-crds-on-aks)합니다.

## <a name="install-the-istio-crds-on-aks"></a>AKS에 Istio CRDs를 설치 합니다.

> [!IMPORTANT]
> 다운로드 하 여 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 했는지 확인 합니다.

Istio는 [사용자 지정 리소스 정의 (CRDs)][kubernetes-crd] 를 사용 하 여 런타임 구성을 관리 합니다. Istio 구성 요소에 대 한 종속성이 있기 때문에 Istio CRDs를 먼저 설치 해야 합니다. 투구 및 `istio-init` 차트를 사용 하 여 AKS 클러스터의 `istio-system` 네임 스페이스에 Istio CRDs를 설치 합니다.

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[작업][kubernetes-jobs] 은 @no__t 1 투구 차트의 일부로 배포 되어 crds를 설치 합니다. 이러한 작업은 클러스터 환경에 따라 완료 하는 데 1 ~ 2 분 정도 걸립니다. 다음과 같이 작업이 성공적으로 완료 되었는지 확인할 수 있습니다.

```azurecli
kubectl get jobs -n istio-system
```

다음 예제 출력에서는 성공적으로 완료 된 작업을 보여 줍니다.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

이제 작업이 성공적으로 완료 되었음을 확인 했으므로 올바른 Istio CRDs 수가 설치 되어 있는지 확인 합니다. 사용자 환경에 적합 한 명령을 실행 하 여 모든 53 Istio CRDs가 설치 되었는지 확인할 수 있습니다. 이 명령은-0 @no__t 수를 반환 해야 합니다.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

이 시점에 도달한 경우에는 Istio CRDs가 성공적으로 설치 되었음을 의미 합니다. 이제 다음 섹션으로 이동 하 여 [AKS에 Istio 구성 요소를 설치](#install-the-istio-components-on-aks)합니다.

## <a name="install-the-istio-components-on-aks"></a>AKS에 Istio 구성 요소를 설치 합니다.

> [!IMPORTANT]
> 다운로드 하 여 추출한 Istio 릴리스의 최상위 폴더에서이 섹션의 단계를 실행 했는지 확인 합니다.

[Grafana][grafana] 및 [Kiali][kiali] 를 설치 하는 과정을 istio 설치의 일부로 설치 합니다. Grafana는 분석 및 모니터링 대시보드를 제공 하 고 Kiali는 서비스 메시 관찰성 대시보드를 제공 합니다. 설치 프로그램에서 이러한 각 구성 요소에는 [암호로][kubernetes-secrets]제공 되어야 하는 자격 증명이 필요 합니다.

Istio 구성 요소를 설치 하기 전에 Grafana 및 Kiali 모두에 대 한 암호를 만들어야 합니다. 사용자 환경에 적합 한 명령을 실행 하 여 이러한 비밀을 만듭니다.

### <a name="add-grafana-secret"></a>Grafana 암호 추가

@No__t-0 토큰을 암호로 바꾸고 다음 명령을 실행 합니다.

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

### <a name="add-kiali-secret"></a>Kiali 암호 추가

@No__t-0 토큰을 암호로 바꾸고 다음 명령을 실행 합니다.

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

### <a name="install-istio-components"></a>Istio 구성 요소 설치

이제 AKS 클러스터에 Grafana 및 Kiali 비밀을 성공적으로 만들었으므로 Istio 구성 요소를 설치 해야 합니다. 투구 및 `istio` 차트를 사용 하 여 AKS 클러스터의 `istio-system` 네임 스페이스에 Istio 구성 요소를 설치 합니다. 사용자 환경에 적절 한 명령을 사용 합니다.

> [!NOTE]
> 설치의 일부로 다음 옵션을 사용 하 고 있습니다.
> - `global.controlPlaneSecurityEnabled=true`-제어 평면에 대해 상호 TLS 사용
> - @no__t-사용 되지 않으므로 믹서 어댑터 CRDs에서 감시를 제거 하 고 성능이 향상 됩니다.
> - `grafana.enabled=true`-분석 및 모니터링 대시보드에 대해 Grafana 배포를 사용 하도록 설정
> - `grafana.security.enabled=true`-Grafana에 대 한 인증 사용
> - `tracing.enabled=true`-추적에 Jaeger 배포를 사용 하도록 설정
> - `kiali.enabled=true`-서비스 메시 관찰성 대시보드에 대해 Kiali 배포를 사용 하도록 설정 합니다.

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

@No__t-0 투구 차트는 많은 수의 개체를 배포 합니다. 위의 `helm install` 명령의 출력에서 목록을 볼 수 있습니다. Istio 구성 요소의 배포는 클러스터 환경에 따라 완료 하는 데 4 ~ 5 분 정도 걸릴 수 있습니다.

> [!NOTE]
> 모든 Istio pod는 Linux 노드에서 실행 되도록 예약 되어야 합니다. 클러스터의 Linux 노드 풀 외에도 Windows Server 노드 풀이 있는 경우 모든 Istio pod Linux 노드에서 실행 되도록 예약 되었는지 확인 합니다.

이제 AKS 클러스터에 Istio를 배포 했습니다. Istio를 성공적으로 배포 하기 위해 다음 섹션으로 이동 하 여 [istio 설치의 유효성을 검사](#validate-the-istio-installation)해 보겠습니다.

## <a name="validate-the-istio-installation"></a>Istio 설치 유효성 검사

먼저 예상 서비스가 만들어졌는지 확인합니다. [Kubectl get svc][kubectl-get] 명령을 사용 하 여 실행 중인 서비스를 볼 수 있습니다. @No__t 1 투구 차트에서 Istio 및 추가 기능 구성 요소가 설치 된 `istio-system` 네임 스페이스를 쿼리 합니다.

```console
kubectl get svc --namespace istio-system --output wide
```

다음 예제 출력에서는 실행되어야 하는 서비스를 보여줍니다.

- `istio-*` 서비스
- `jaeger-*`, `tracing`, `zipkin` 추가 기능 추적 서비스
- `prometheus` 추가 기능 메트릭 서비스
- `grafana` 추가 기능 분석 및 모니터링 대시보드 서비스
- `kiali` 추가 기능 서비스 메시 대시보드 서비스

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

다음으로 필요한 Pod가 만들어졌는지 확인합니다. [Kubectl get pod][kubectl-get] 명령을 사용 하 여 `istio-system` 네임 스페이스를 다시 쿼리 합니다.

```console
kubectl get pods --namespace istio-system
```

다음 예제 출력에서는 실행 중인 Pod을 보여줍니다.

- `istio-*` pod
- `prometheus-*` 추가 기능 메트릭 pod
- `grafana-*` 추가 기능 분석 및 모니터링 대시보드 pod
- `kiali` 추가 기능 서비스 메시 대시보드 pod

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

@No__t-1 상태의 두 `istio-init-crd-*` pod이 있어야 합니다. 이러한 pod는 이전 단계에서 CRDs를 만든 작업을 실행 해야 했습니다. 다른 모든 pod는 `Running` 상태를 표시 해야 합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고 하는 경우 [kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 출력 및 상태를 검토 합니다.

## <a name="accessing-the-add-ons"></a>추가 항목 액세스

추가 기능을 제공하는 설정에서 Istio가 다양한 추가 항목을 설치했습니다. 추가 항목에 대한 사용자 인터페이스는 외부 IP 주소를 통해 공개적으로 노출되지 않습니다. 추가 기능 사용자 인터페이스에 액세스 하려면 [kubectl][kubectl-port-forward] 명령을 사용 합니다. 이 명령은 클라이언트 컴퓨터와 AKS 클러스터의 관련 pod 간에 보안 연결을 만듭니다.

이 문서 앞부분에서 Grafana 및 Kiali에 대 한 자격 증명을 지정 하 여 추가 보안 계층을 추가 했습니다.

### <a name="grafana"></a>Grafana

Istio에 대 한 분석 및 모니터링 대시보드는 [Grafana][grafana]에서 제공 합니다. 클라이언트 컴퓨터의 로컬 포트 `3000`을 AKS 클러스터에서 Grafana를 실행 하는 pod의 포트 `3000`로 전달 합니다.

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

다음 예제 출력에서는 Grafana를구성하는 포트- 착신 전환을 보여줍니다.

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

이제 클라이언트 머신 - [http://localhost:3000](http://localhost:3000)에서 다음 URL에 Grafana를 연결할 수 있습니다. 메시지가 표시 되 면 이전에 Grafana 암호를 통해 만든 자격 증명을 사용 해야 합니다.

### <a name="prometheus"></a>Prometheus

Istio에 대 한 메트릭은 [프로메테우스][prometheus]에서 제공 합니다. 클라이언트 컴퓨터의 로컬 포트 `9090`을 AKS 클러스터에서 프로메테우스를 실행 하는 pod의 `9090` 포트로 전달 합니다.

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

Istio 내의 추적은 [Jaeger][jaeger]에서 제공 합니다. 클라이언트 컴퓨터의 로컬 포트 `16686`을 AKS 클러스터에서 Jaeger를 실행 하는 pod의 포트 `16686`로 전달 합니다.

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

서비스 메시 관찰성 대시보드는 [Kiali][kiali]에서 제공 합니다. 클라이언트 컴퓨터의 로컬 포트 `20001`을 AKS 클러스터에서 Kiali를 실행 하는 pod의 포트 `20001`로 전달 합니다.

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

다음 예제 출력에서는 Kiali를구성하는 포트- 착신 전환을 보여줍니다.

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

클라이언트 머신 - [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/)의 다음 URL에서 Kiali 서비스 메시 관찰성 대시보드를 연결할 수 있습니다. 메시지가 표시 되 면 이전에 Kiali 암호를 통해 만든 자격 증명을 사용 해야 합니다.

## <a name="uninstall-istio-from-aks"></a>AKS에서 Istio 제거

> [!WARNING]
> 실행 중인 시스템에서 Istio를 삭제 하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속 하기 전에 Istio 없이 제대로 작동 하도록 시스템에 대 한 프로 비전을 수행 했는지 확인 합니다.

### <a name="remove-istio-components-and-namespace"></a>Istio 구성 요소 및 네임 스페이스 제거

AKS 클러스터에서 Istio를 제거 하려면 다음 명령을 사용 합니다. @No__t-0 명령은 `istio` 및 `istio-init` 차트를 제거 하 고 `kubectl delete ns` 명령은 `istio-system` 네임 스페이스를 제거 합니다.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Istio CRDs 제거

위의 명령은 Istio 구성 요소와 네임 스페이스를 모두 삭제 하지만 Istio CRDs는 계속 남아 있습니다. CRDs를 삭제 하려면 다음 방법 중 하나를 사용할 수 있습니다.

#1 방법-이 명령은 Istio를 설치 하는 데 사용한 Istio의 다운로드 및 추출 된 릴리스의 최상위 폴더에서이 단계를 실행 하 고 있다고 가정 합니다.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

#2 방법-Istio를 설치 하는 데 사용한 Istio의 다운로드 및 압축 해제 릴리스에 대 한 액세스 권한이 더 이상 없는 경우 이러한 명령 중 하나를 사용 합니다. 이 명령을 완료 하는 데 몇 분 정도 걸릴 것입니다.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>다음 단계

다음 설명서에서는 Istio를 사용 하 여 카나리아 릴리스를 롤아웃하기 위한 지능형 라우팅을 제공 하는 방법을 설명 합니다.

> [!div class="nextstepaction"]
> [AKS Istio 인텔리전트 라우팅 시나리오][istio-scenario-routing]

Istio에 대한 자세한 설치 및 구성 옵션을 탐색하려면 다음 공식 Istio 문서를 참조하세요.

- [Istio-투구 설치 가이드][istio-install-helm]
- [Istio-투구 설치 옵션][istio-install-helm-options]

[Istio Bookinfo 응용 프로그램 예제][istio-bookinfo-example]를 사용 하 여 추가 시나리오를 따를 수도 있습니다.

Application Insights 및 Istio를 사용 하 여 AKS 응용 프로그램을 모니터링 하는 방법에 대 한 자세한 내용은 다음 Azure Monitor 설명서를 참조 하세요.
- [Kubernetes 호스팅된 응용 프로그램에 대 한 제로 계측 응용 프로그램 모니터링][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
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
