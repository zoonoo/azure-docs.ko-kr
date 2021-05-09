---
title: Open Service Mesh(미리 보기)
description: AKS(Azure Kubernetes Service)의 OSM(Open Service Mesh)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: bbc07a7ee3f996c778cfc1b9d1764f10a613c50b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782948"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS 추가 기능(미리 보기)

## <a name="overview"></a>개요

[OSM(Open Service Mesh)](https://docs.openservicemesh.io/)은 확장 가능한 네이티브 클라우드 서비스 메시이며, 사용자는 매우 동적인 마이크로 서비스 환경을 일관되게 관리하고, 안전하게 만들고, 즉시 사용 가능한 가시성 기능을 사용할 수 있습니다.

OSM은 Kubernetes에서 Envoy 기반 컨트롤 플레인을 실행하고, [SMI](https://smi-spec.io/) API를 사용하여 구성될 수 있으며, 애플리케이션의 각 인스턴스 옆에 사이드카 컨테이너로 Envoy 프록시를 삽입하여 작동합니다. Envoy 프록시는 액세스 제어 정책에 대한 규칙을 포함 및 실행하고 라우팅 구성을 구현하며 메트릭을 캡처합니다. 컨트롤 플레인은 정책 및 라우팅 규칙이 최신 상태를 유지하도록 프록시를 지속적으로 구성하고 프록시가 정상 상태인지 확인합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>기능 및 특징

OSM은 AKS(Azure Kubernetes Service) 클러스터에 대한 네이티브 클라우드 서비스 메시를 제공하는 다음과 같은 기능 및 기능 집합을 제공합니다.

- mTLS를 사용하도록 설정하여 서비스 간 통신 보호

- Envoy 프록시에 자동 사이드카 주입을 사용하도록 설정하여 애플리케이션을 메시에 쉽게 온보딩

- 배포 시 트래픽 이동에 대한 쉽고 투명한 구성

- 서비스에 대한 세부적인 액세스 제어 정책을 정의하고 실행하는 기능

- 디버깅 및 모니터링 서비스에 대한 애플리케이션 메트릭에 대한 가시성 및 인사이트

- 플러그형 인터페이스를 사용하여 외부 인증서 관리 서비스/솔루션과의 통합

## <a name="scenarios"></a>시나리오

OSM은 다음과 같은 시나리오를 통해 AKS 배포를 지원할 수 있습니다.

- 클러스터에 배포된 서비스 엔드포인트 간의 암호화된 통신 제공

- 메시의 HTTP/HTTPS 및 TCP 트래픽 모두에 대한 트래픽 권한 부여

- A/B 또는 카나리아 배포에 대해 둘 이상의 서비스 간에 가중치가 적용된 트래픽 컨트롤 구성

- 애플리케이션 트래픽에서 KPI 수집 및 보기

## <a name="osm-service-quotas-and-limits-preview"></a>OSM 서비스 할당량 및 한도(미리 보기)

서비스 할당량 및 한도에 대한 OSM 미리 보기 제한은 AKS [할당량 및 지역 제한 페이지](https://docs.microsoft.com/azure/aks/quotas-skus-regions)에서 찾을 수 있습니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> `osm install`을 사용하여 이진에서 OSM을 설치하지 마세요. 이렇게 하면 AKS에 대한 추가 기능으로 통합되지 않은 OSM 설치가 발생합니다.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>`AKS-OpenServiceMesh` 미리 보기 기능 등록

Open Service Mesh 추가 기능을 사용할 수 있는 AKS 클러스터를 만들려면 구독에서 `AKS-OpenServiceMesh` 기능 플래그를 사용하도록 설정해야 합니다.

`AKS-OpenServiceMesh`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

상태가 _Registered_ 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 _ContainerService_ 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>새 AKS 클러스터에 대한 OSM(Open Service Mesh) AKS (Azure Kubernetes Service) 추가 기능을 설치합니다.

새 AKS 클러스터 배포 시나리오의 경우 클러스터 만들기 작업에서 OSM 추가 기능을 사용하도록 설정하는 AKS 클러스터의 새 배포에서부터 시작합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예에서는 _eastus2_ 위치(지역)에 _myOsmAksGroup_ 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>OSM 추가 기능을 사용하도록 설정하여 AKS 클러스터 배포

이제 OSM 추가 기능을 사용하도록 설정하여 새 AKS 클러스터를 배포합니다.

> [!NOTE]
> 다음 AKS 배포 명령은 임시 OS 디스크를 활용합니다. [AKS용 임시 OS 디스크](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)에 대한 자세한 내용은 여기에서 찾을 수 있습니다.

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AKS 클러스터 액세스 자격 증명 가져오기

새 관리되는 Kubernetes 클러스터에 대한 액세스 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>기존 AKS 클러스터에 대해 OSM(Open Service Mesh) AKS(Azure Kubernetes Service) 추가 기능을 사용하도록 설정합니다.

기존 AKS 클러스터 시나리오의 경우 이미 배포된 기존 AKS 클러스터에 OSM 추가 기능을 사용하도록 설정합니다.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>기존 AKS 클러스터에 OSM 추가 기능 사용

AKS OSM 추가 기능을 사용하도록 설정하려면 `open-service-mesh` 매개 변수를 전달하는 `az aks enable-addons --addons` 명령을 실행해야 합니다.

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

AKS OSM 추가 기능이 설치되었는지 확인하려면 아래에 표시된 출력과 유사한 출력이 표시되어야 합니다.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>AKS OSM 추가 기능 설치의 유효성을 검사합니다.

AKS OSM 추가 기능이 사용 설정되고 실행되기 위한 모든 구성 요소를 확인하기 위해 실행할 수 있는 몇 가지 명령이 있습니다.

먼저 클러스터의 추가 기능 프로필을 쿼리하여 설치된 추가 기능의 사용 설정 상태를 확인할 수 있습니다. 다음 명령은 "true"를 반환해야 합니다.

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

다음 `kubectl` 명령은 osm-controller의 상태를 보고합니다.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>AKS OSM 추가 기능에 액세스

현재는 configmap을 통해 OSM 컨트롤러 구성에 액세스하여 구성할 수 있습니다. OSM 컨트롤러 구성 설정을 보려면 `kubectl`를 통해 OSM configmap을 쿼리하여 해당 구성 설정을 확인합니다.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap의 출력은 다음과 같습니다.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**Permissive_traffic_policy_mode** 가 **true** 로 구성되어 있는지 확인합니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

> [!WARNING]
> 계속하기 전에 허용 트래픽 정책 모드가 true로 설정되어 있는지 확인하세요. 그렇지 않으면 아래 명령을 사용하여 **true** 로 변경하세요.

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>OSM(Open Service Mesh) AKS (Azure Kubernetes Service) 추가 기능을 통해 관리할 새 애플리케이션을 배포합니다.

### <a name="before-you-begin"></a>시작하기 전에

이 연습에서 설명된 단계에서는 AKS 클러스터(Kubernetes RBAC가 사용 설정된 Kubernetes `1.19+` 이상)를 만들고 클러스터와의 `kubectl` 연결을 설정한 것으로 가정합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.8.0 이상
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>애플리케이션의 네임스페이스를 만듭니다.

이 연습에서는 다음 Kubernetes 서비스를 포함하는 OSM 애플리케이션을 사용합니다.

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

이러한 각 애플리케이션 구성 요소에 대한 네임스페이스를 만듭니다.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

다음 출력이 표시됩니다.

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM에서 관리할 네임스페이스를 온보딩합니다.

OSM 메시에 네임스페이스를 추가하는 경우 OSM 컨트롤러는 애플리케이션을 통해 Envoy 사이드카 프록시 컨테이너를 자동으로 삽입할 수 있습니다. 다음 명령을 실행하여 OSM bookstore 애플리케이션 네임스페이스를 온보딩합니다.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

다음 출력이 표시됩니다.

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Bookstore 애플리케이션을 AKS 클러스터에 배포합니다.

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

모든 배포 출력은 아래에 요약되어 있습니다.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>검사점: 설치된 항목

예 Bookstore 애플리케이션은 4개의 서비스(bookbuyer, bookthief, bookstore 및 bookwarehouse)로 구성된 다중 계층 앱입니다. bookbuyer와 bokthief 서비스는 모두 bookstore 서비스와 통신하여 bookstore 서비스에서 책을 검색합니다. bookstore 서비스는 bookwarehouse 서비스의 책을 검색하여 bookbuyer 및 bookthief에게 제공합니다. 이는 서비스 메시를 사용하여 애플리케이션 서비스 간의 통신을 보호하고 권한을 부여하는 방법을 보여 주는 간단한 다중 계층 애플리케이션입니다. 연습을 진행하면서 SMI(Service Mesh Interface) 정책을 사용하거나 사용하지 않도록 설정하여 서비스가 OSM을 통해 통신하도록 허용하거나 허용하지 않을 것입니다. 다음은 bookstore 애플리케이션에 대해 설치된 항목의 아키텍처 다이어그램입니다.

![OSM bookbuyer 앱 아키텍처](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS 클러스터 내에서 실행 중인 bookstore 애플리케이션 확인

지금 bookstore 다중 컨테이너 애플리케이션이 배포되어 있지만 AKS 클러스터 내에서만 액세스할 수 있습니다. 이후 자습서에서는 수신 컨트롤러를 통해 클러스터 외부에서 애플리케이션을 공개하는 데 도움을 줍니다. 이제 AKS 클러스터 내부의 bookbuyer 애플리케이션에 액세스하는 포트 전달을 활용하여 bookstore 서비스에서 책을 구입하는지 확인합니다.

애플리케이션이 클러스터 내에서 실행되고 있는지 확인하기 위해 포트 전달을 사용하여 bookbuyer와 bookthief의 구성 요소 UI를 모두 확인합니다.

먼저 bookbuyer Pod의 이름을 가져오겠습니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

다음과 비슷한 결과가 표시됩니다. Bookbuyer Pod에 고유한 이름이 추가됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod의 이름이 있으면 이제 port-forward 명령을 사용하여 로컬 시스템에서 AKS 클러스터 내부의 애플리케이션까지의 터널을 설정할 수 있습니다. 다음 명령을 실행하여 로컬 시스템 포트 8080에 대한 포트 전달을 설정합니다. 지정된 bookbuyer Pod 이름을 다시 사용합니다.

> [!NOTE]
> 모든 포트 전달 명령에 대해 이 연습을 계속 진행하고 터널의 연결을 끊지 않도록 추가 터미널을 사용하는 것이 가장 좋습니다. 또한 Azure Cloud Shell 외부에서 포트 전달 터널을 설정하는 것도 좋은 방법입니다.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

다음과 유사한 결과가 표시됩니다.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

포트 전달 세션이 준비되는 동안 브라우저에서 다음 URL로 이동합니다(`http://localhost:8080`). 이제 아래 이미지와 비슷한 브라우저에서 bookbuyer 애플리케이션 UI를 볼 수 있습니다.

![OSM bookbuyer 앱 UI 이미지](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

또한 총 구매한 책의 수가 계속해서 bookstore v1 서비스에 증분되고 있음을 알 수 있습니다. bookstore v2 서비스는 아직 배포되지 않았습니다. Microsoft는 SMI 트래픽 분할 정책을 설명할 때 bookstore v2 서비스를 배포할 것입니다.

또한 bookthief 서비스에 대해서도 동일함을 확인할 수 있습니다.

```azurecli-interactive
kubectl get pod -n bookthief
```

다음과 비슷한 결과가 표시됩니다. Bookbuyer Pod에는 고유한 이름이 추가됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Bookthief Pod로 전달되는 포트입니다.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

브라우저를 열고 다음 URL로 이동합니다(`http://localhost:8080`). Bookthief가 현재 bookstore 서비스의 책을 훔쳐가고 있는지 확인해야 합니다. 나중에는 bookthief를 멈출 트래픽 정책을 구현할 것입니다.

![OSM bookthief 앱 UI 이미지](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>메시에 대한 OSM 허용 트래픽 모드 사용 안 함

앞서 설명한 것처럼 OSM 클러스터 구성을 볼 때 OSM 구성에서는 허용되는 트래픽 모드 정책을 기본적으로 사용하도록 설정합니다. 이 모드에서는 트래픽 정책 적용이 우회되고 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

이제 허용되는 트래픽 모드 정책을 사용하지 않도록 설정하고 OSM은 각 서비스의 메시에서 통신할 수 있도록 클러스터에 명시적인 [SMI](https://smi-spec.io/) 정책을 배포해야 합니다. 허용되는 트래픽 모드를 사용하지 않도록 설정하려면 다음 명령을 실행하여 값을 `true`에서 `false`로 변경하는 configmap 속성을 업데이트합니다.

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

다음과 비슷한 결과가 표시됩니다. Bookbuyer Pod에는 고유한 이름이 추가됩니다.

```Output
configmap/osm-config patched
```

허용되는 트래픽 모드가 사용하지 않도록 설정되었는지 확인하려면 bookbuyer 또는 bookthief Pod으로 다시 포트 전달하여 브라우저에서 UI를 확인하고 구매한 책 또는 도난당한 책이 더 이상 증가하지 않는지 확인합니다. 브라우저를 새로 고쳐야 합니다. 증분이 중지되면 정책이 올바르게 적용된 것입니다. 이렇게 하면 bookthief가 책을 훔치는 것은 막을 수 있지만, bookbuyer는 bookstore에서 책을 구매할 수 없으며 bookstore도 bookwarehouse에서 책을 검색할 수 없게 됩니다. 다음으로, 통신하려는 메시의 서비스만 허용하도록 [SMI](https://smi-spec.io/) 정책을 구현합니다.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>SMI(Service Mesh Interface) 트래픽 액세스 정책 적용

이제 메시의 모든 통신을 사용하지 않도록 설정했으므로 bookbuyer 서비스에서 책 구매를 위해 bookstore 서비스와 통신하도록 허용하고, bookstore 서비스에서 bookwarehouse 서비스와 통신하여 판매할 책을 검색할 수 있도록 합니다.

다음 [SMI](https://smi-spec.io/) 정책을 배포합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

다음과 비슷한 결과가 표시됩니다.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

이제 bookbuyer 또는 bookstore Pod에서 포트 전달 세션을 설정할 수 있으며, 구매한 책 및 판매된 책 메트릭이 다시 증가하는 것을 확인할 수 있습니다. 또한 bookthief Pod에 대해서도 동일한 작업을 수행하여 아직도 책을 더 이상 훔칠 수 없는지 확인할 수 있습니다.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>SMI(Service Mesh Interface) 트래픽 액세스 정책 적용

최종 데모를 위해 [SMI](https://smi-spec.io/) 트래픽 분할 정책을 만들어 한 서비스에서 여러 서비스로의 통신 가중치를 백 엔드로 구성합니다. 트래픽 분할 기능을 사용하면 0에서 100 범위의 트래픽에 가중치를 설정하여 한 서비스에 대한 연결을 점진적으로 이동할 수 있습니다.

아래 그림은 배포할 [SMI](https://smi-spec.io/) 트래픽 분할 정책의 다이어그램입니다. Microsoft는 추가로 Bookstore 버전 2를 배포하고, bookbuyer에서 들어오는 트래픽 중 25%를 bookstore v1 서비스로, 75%를 bookstore v2 서비스로 분할합니다.

![OSM bookbuyer 트래픽 분할 다이어그램](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

bookstore v2 서비스를 배포합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

다음 출력이 표시되어야 합니다.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

이제 트래픽 분할 정책을 배포하여 bookstore v1 및 v2 서비스 두 개 간에 bookbuyer 트래픽을 분할합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

다음 출력이 표시되어야 합니다.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Bookbuyer Pod에 대한 포트 전달 터널을 설정하고, 이제 bookstore v2 서비스에서 구매한 책을 확인해야 합니다. 계속해서 구매의 증분을 관찰하면 bookstore v2 서비스를 통해 구매가 더 빨리 증가하는 것을 볼 수 있습니다.

![OSM bookbuyer books boough UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>OSM(Open Service Mesh) AKS(Azure Kubernetes Service) 추가 기능을 통해 관리할 배포되는 기존 애플리케이션 관리

### <a name="before-you-begin"></a>시작하기 전에

이 연습에 설명된 단계에서는 AKS 클러스터에 대해 이전에 OSM AKS 추가 기능을 사용하도록 설정했다고 가정합니다. 그렇지 않은 경우 계속 진행하기 전에 [기존 AKS 클러스터에 대해 OSM(Open Service Mesh) AKS(Azure Kubernetes Service) 추가 기능 사용](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) 섹션을 검토합니다. 또한 AKS 클러스터는 Kubernetes 버전 `1.19+` 이상이어야 하고, Kubernetes RBAC를 사용하도록 설정하고, 클러스터와의 `kubectl` 연결을 설정해야 합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.8.0 이상
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>OSM(Open Service Mesh) 허용 트래픽 모드 정책을 확인합니다.

OSM 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

클러스터에 대한 OSM의 현재 허용 트래픽 모드를 확인하려면 다음 명령을 실행합니다.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap의 출력은 다음과 같습니다.

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**permissive_traffic_policy_mode** 가 **true** 로 구성된 경우 서비스 간 통신을 중단하지 않고 네임스페이스를 안전하게 온보딩할 수 있습니다. **permissive_traffic_policy_mode** 가 **false** 로 구성된 경우에는 올바른 [SMI](https://smi-spec.io/) 트래픽 액세스 정책 매니페스트가 배포되었는지 확인하고 네임스페이스에 배포된 각 서비스를 나타내는 서비스 계정이 있는지 확인해야 합니다. [OSM(Open Service Mesh) 허용 트래픽 정책을 False로 구성하여 배포된 기존 애플리케이션 온보딩](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)을 위한 지침을 따르세요.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>OSM(Open Service Mesh) 허용 트래픽 정책을 True로 구성하여 배포된 기존 애플리케이션 온보딩

가장 먼저 수행할 작업은 배포된 애플리케이션 네임스페이스를 OSM에 추가하여 관리하는 것입니다.

```azurecli-interactive
osm namespace add bookstore
```

다음 출력이 표시됩니다.

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

다음으로는 네임스페이스에서 현재 Pod 배포를 살펴보겠습니다. 다음 명령을 실행하여 지정된 네임스페이스에서 Pod를 확인합니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

출력은 다음과 같이 표시됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

**준비** 열에 **1/1** 이 표시되면 애플리케이션 Pod에 하나의 컨테이너만 있다는 것을 의미합니다. 다음으로 애플리케이션 배포를 다시 시작해야 OSM이 애플리케이션 Pod를 사용하여 Envoy 사이드카 프록시 컨테이너를 주입할 수 있습니다. 네임스페이스의 배포 목록을 살펴보겠습니다.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

다음 출력이 표시됩니다.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

이제 배포를 다시 시작하여 애플리케이션 Pod와 함께 Envoy 사이드카 프록시 컨테이너를 삽입합니다. 다음 명령을 실행합니다.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

다음 출력이 표시됩니다.

```Output
deployment.apps/bookbuyer restarted
```

네임스페이스에서 Pod를 다시 살펴보면:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

이제 **준비** 열에 **2/2** 컨테이너가 Pod를 준비하는 중인 것으로 표시되는 것을 알 수 있습니다. 두 번째 컨테이너는 Envoy 사이드카 프록시입니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

describe 명령을 실행하여 구성을 확인하여 Envoy 프록시를 확인하기 위해 Pod를 자세히 검토할 수 있습니다.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Envoy 사이드카 프록시 삽입 후에도 애플리케이션이 계속 작동하는지 확인합니다.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>OSM(Open Service Mesh) 허용 트래픽 정책을 False로 구성하여 배포된 기존 애플리케이션 온보딩

허용 트래픽 정책에 대한 OSM 구성이 `false`로 설정된 경우 OSM은 클러스터 내에서 발생하는 서비스 간 통신을 위해 명시적 [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 배포해야 합니다. 현재 OSM은 서비스 간 통신에도 권한을 부여하는 과정에서 Kubernetes 서비스 계정을 사용합니다. 기존에 배포된 애플리케이션이 OSM 메시에서 관리되는 경우에도 통신하도록 하려면 서비스 계정이 있는지 확인하고 서비스 계정 정보를 사용하여 애플리케이션 배포를 업데이트한 다음 [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 적용해야 합니다.

#### <a name="verify-kubernetes-service-accounts"></a>Kubernetes 서비스 계정 확인

애플리케이션이 배포되는 네임스페이스에 Kubernetes 서비스 계정이 있는지 확인합니다.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

다음에는 bookbuyer 네임스페이스에 `bookbuyer`라는 서비스 계정이 있습니다.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

기본 계정이 아닌 다른 서비스 계정이 나열되어 있지 않은 경우 애플리케이션을 위해 서비스 계정을 하나 만들어야 합니다. 애플리케이션의 배포된 네임스페이스에서 서비스 계정을 만들려면 다음 명령을 예로 사용합니다.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>애플리케이션의 현재 배포 사양 보기

이전 섹션에서 서비스 계정을 만들어야 하는 경우 애플리케이션 배포가 배포 사양의 특정 `serviceAccountName`으로 구성되지 않았을 수 있습니다. 다음 명령을 사용하여 애플리케이션의 배포 사양을 볼 수 있습니다.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

배포 목록이 출력에 나열됩니다.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

여기서 배포를 통해 Pod 템플릿 섹션에 나열된 서비스 계정이 있는지 확인합니다.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

이 특정 배포에서는 Pod 템플릿 섹션에 나열된 배포와 연결된 서비스 계정이 있음을 볼 수 있습니다. 이 배포는 서비스 계정 bookbuyer를 사용하고 있습니다. **서비스 계정:** 속성이 표시되지 않으면 배포가 서비스 계정을 사용하도록 구성되지 않은 것입니다.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Kubernetes 서비스 계정을 추가하기 위해 배포를 업데이트하는 몇 가지 방법이 있습니다. 인라인으로 [배포를 업데이트](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment)하는 방법에 대한 Kubernetes 설명서를 검토하거나 [Pod에 대한 서비스 계정을 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)합니다. 서비스 계정으로 배포 사양을 업데이트한 후 배포를 클러스터에 다시 배포합니다(kubectl apply -f your-deployment.yaml).

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>필요한 SMI(Service Mesh Interface) 정책을 배포합니다.

메시에서 승인된 트래픽을 허용하는 마지막 단계는 애플리케이션에 필요한 [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 배포하는 것입니다. [SMI](https://smi-spec.io/) 트래픽 액세스 정책을 사용하여 달성할 수 있는 구성의 양은 이 연습에서 다루지 않지만, 사양의 몇 가지 공통 구성 요소에 대해 자세히 설명하고 애플리케이션에 대한 서비스 간 통신을 가능하게 하는 간단한 TrafficTarget 및 HTTPRouteGroup 정책을 구성하는 방법을 보여 드리겠습니다.

[SMI](https://smi-spec.io/) [**트래픽 액세스 제어**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) 사양을 통해 사용자는 애플리케이션에 대한 액세스 제어 정책을 정의할 수 있습니다. **TrafficTarget** 및 **HTTPRoutGroup** API 리소스에 대해 집중적으로 살펴보겠습니다.

TrafficTarget 리소스는 세 가지 주요 구성 설정 대상, 규칙 및 원본으로 구성됩니다. TrafficTarget 예는 아래에서 확인할 수 있습니다.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

위의 TrafficTarget 사양에서 `destination`은 대상 원본 서비스에 대해 구성된 서비스 계정을 나타냅니다. 이전에 배포에 추가된 서비스 계정은 연결된 배포에 대한 액세스 권한을 부여하는 데 사용됩니다. 이 특정 예에서 `rules` 섹션은 연결을 통해 허용되는 HTTP 트래픽 유형을 정의합니다. HTTP 헤더에 대한 세분화된 정규식 패턴을 HTTP를 통해 허용되는 트래픽에 맞게 구성할 수 있습니다. 이 `sources` 섹션은 서비스에서 시작하는 통신입니다. 이 사양에서는 bookbuyer가 bookstore에 통신해야 합니다.

HTTPRouteGroup 리소스는 HTTP 헤더 정보와 일치하는 항목 하나 또는 항목의 배열로 구성되며 TrafficTarget 사양에 대한 요구 사항입니다. 아래 예에서는 HTTPRouteGroup이 세 개의 HTTP 작업(두 개의 GET 및 하나의 POST)에 권한을 부여하는 것을 볼 수 있습니다.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

프런트 엔드 애플리케이션에서 애플리케이션의 다른 계층으로 수행하는 HTTP 트래픽 유형에 익숙하지 않은 경우 TrafficTarget 사양에 규칙이 필요하기 때문에 HTTPRouteGroup에 대해 아래 사양을 사용하여 모든 허용 규칙에 해당하는 항목을 만들 수 있습니다.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

TrafficTarget 및 HTTPRouteGroup 사양을 구성한 후에는 하나의 YAML 및 배포로 함께 배치할 수 있습니다. 다음은 bookstore 예 구성입니다.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

사양에 대한 자세한 내용은 [SMI](https://smi-spec.io/) 사이트를 참조하세요.

### <a name="manage-the-applications-namespace-with-osm"></a>OSM을 사용하여 애플리케이션의 네임스페이스 관리

다음에는 OSM을 구성하여 네임스페이스를 관리하고 배포를 다시 시작하여 애플리케이션을 통해 삽입된 Envoy 사이드카 프록시를 가져옵니다.

다음 명령을 실행하여 `azure-vote` 네임스페이스를 OSM에 의해 관리되도록 구성합니다.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

다음 명령을 사용하여 `azure-vote-front` 및 `azure-vote-back` 배포를 모두 다시 시작합니다.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

`azure-vote` 네임스페이스에 대한 Pod를 확인하는 경우 `azure-vote-front` 및 `azure-vote-back`의 **준비** 단계에서 2/2가 표시됩니다. 즉, Envoy 사이드카 프록시가 애플리케이션과 함께 삽입되었습니다.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>자습서: NGINX 수신으로 OSM(Open Service Mesh)에 의해 관리되는 애플리케이션 배포

OSM(Open Service Mesh)은 확장 가능한 네이티브 클라우드 서비스 메시로, 사용자는 매우 동적인 마이크로 서비스 환경을 일관되게 관리하고, 안전하게 만들고, 즉시 사용 가능한 가시성 기능을 사용할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> - 현재 OSM 클러스터 구성 보기
> - 네임스페이스에서 배포된 애플리케이션을 관리할 OSM에 대한 네임스페이스 만들기
> - OSM에서 관리할 네임스페이스를 온보딩합니다.
> - 샘플 애플리케이션 배포
> - AKS 클러스터 내에서 실행 중인 애플리케이션 확인
> - 애플리케이션에 사용되는 NGINX 수신 컨트롤러 만들기
> - 인터넷에 Azure Application Gateway 수신을 통해 서비스 공개

### <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 단계에서는 AKS 클러스터(Kubernetes RBAC가 사용 설정된 Kubernetes `1.19+` 이상)를 만들고 클러스터와의 `kubectl` 연결을 설정한 것으로 가정합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- OSM 버전 v0.8.0 이상
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>현재 OSM 클러스터 구성을 확인합니다.

AKS 클러스터에서 AKS에 대한 OSM 추가 기능을 사용하도록 설정하면 osm-config Kubernetes ConfigMap에서 현재 구성 매개 변수를 볼 수 있습니다. ConfigMap 속성을 보려면 다음 명령을 실행합니다.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

출력에는 클러스터에 대한 현재 OSM 구성이 표시됩니다.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**Permissive_traffic_policy_mode** 가 **true** 로 구성되어 있는지 확인합니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

### <a name="create-namespaces-for-the-application"></a>애플리케이션의 네임스페이스를 만듭니다.

이 자습서에서는 다음과 같은 애플리케이션 구성 요소를 포함하는 OSM으로 구성된 애플리케이션을 사용합니다.

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

이러한 각 애플리케이션 구성 요소에 대한 네임스페이스를 만듭니다.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

다음 출력이 표시됩니다.

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM에서 관리할 네임스페이스를 온보딩합니다.

OSM 메시에 네임스페이스를 추가하면 OSM 컨트롤러는 애플리케이션을 통해 Envoy 사이드카 프록시 컨테이너를 자동으로 삽입할 수 있습니다. 다음 명령을 실행하여 OSM bookstore 애플리케이션 네임스페이스를 온보딩합니다.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

다음 출력이 표시됩니다.

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Bookstore 애플리케이션을 AKS 클러스터에 배포합니다.

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

모든 배포 출력은 아래에 요약되어 있습니다.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>bookbuyer 서비스 업데이트

bookbuyer 서비스를 다음 서비스 매니페스트를 사용하여 올바른 인바운드 포트 구성으로 업데이트합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS 클러스터 내에서 실행 중인 bookstore 애플리케이션 확인

지금 bookstore 다중 컨테이너 애플리케이션이 배포되어 있지만 AKS 클러스터 내에서만 액세스할 수 있습니다. 나중에 AKS 클러스터 외부에서 애플리케이션을 공개하는 Azure Application Gateway 수신 컨트롤러를 추가합니다. 애플리케이션이 클러스터 내에서 실행되고 있는지 확인하기 위해 포트 전달을 사용하여 bookbuyer 구성 요소 UI를 확인합니다.

먼저 bookbuyer Pod의 이름을 가져오겠습니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

다음과 비슷한 결과가 표시됩니다. Bookbuyer Pod에 고유한 이름이 추가됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod의 이름이 있으면 이제 port-forward 명령을 사용하여 로컬 시스템에서 AKS 클러스터 내부의 애플리케이션까지의 터널을 설정할 수 있습니다. 다음 명령을 실행하여 로컬 시스템 포트 8080에 대한 포트 전달을 설정합니다. 지정된 bookbuyer Pod 이름을 다시 사용합니다.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

다음과 유사한 결과가 표시됩니다.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

포트 전달 세션이 준비되는 동안 브라우저에서 다음 URL로 이동합니다(`http://localhost:8080`). 이제 아래 이미지와 비슷한 브라우저에서 bookbuyer 애플리케이션 UI를 볼 수 있습니다.

![NGINX UI 이미지에 대한 OSM bookbuyer 앱](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 NGINX 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

OSM에서 관리하는 애플리케이션을 인터넷에 공개하기 위해 수신 컨트롤러를 활용합니다. 수신 컨트롤러를 만들려면 Helm을 사용하여 nginx-ingress를 설치합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러도 Linux 노드에서 예약해야 합니다. Windows Server 노드가 수신 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예에서는 _ingress-basic_ 이라는 수신 리소스에 대한 Kubernetes 네임스페이스를 만듭니다. 필요에 따라 사용자 환경에 대한 네임스페이스를 지정합니다.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

NGINX 수신 컨트롤러에 대해 Kubernetes 부하 분산 장치 서비스를 만든 경우 다음 예제 출력에 표시된 대로 동적 공용 IP 주소를 할당합니다.

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

아직 수신 규칙이 만들어지지 않았으므로 내부 IP 주소를 검색하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>bookbuyer 서비스를 인터넷에 공개

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

다음 출력이 표시됩니다.

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>NGINX 로그 보기

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

출력은 수신 규칙이 성공적으로 적용되었을 때 NGINX 수신 컨트롤러 상태를 표시합니다.

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>외부적으로 NGINX 서비스 및 bookbuyer 서비스 보기

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

수신 매니페스트의 호스트 이름이 테스트에 사용되는 가짜 이름이기 때문에 인터넷에서 DNS 이름을 사용할 수 없습니다. 또는 curl 프로그램을 사용하여 호스트 이름 헤더를 NGINX 공용 IP 주소로 넘겨 bookbuyer 서비스에 성공적으로 연결하는 200 코드를 수신할 수 있습니다.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

다음 출력이 표시됩니다.

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>자습서: Azure Application Gateway 수신 AKS 추가 기능을 사용하여 OSM(Open Service Mesh)으로 관리되는 애플리케이션 배포

OSM(Open Service Mesh)은 확장 가능한 네이티브 클라우드 서비스 메시로, 사용자는 매우 동적인 마이크로 서비스 환경을 일관되게 관리하고, 안전하게 만들고, 즉시 사용 가능한 가시성 기능을 사용할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> - 현재 OSM 클러스터 구성 보기
> - 네임스페이스에서 배포된 애플리케이션을 관리할 OSM에 대한 네임스페이스 만들기
> - OSM에서 관리할 네임스페이스를 온보딩합니다.
> - 샘플 애플리케이션 배포
> - AKS 클러스터 내에서 실행 중인 애플리케이션 확인
> - 애플리케이션에 대한 수신 컨트롤러로 사용할 Azure Application Gateway 만들기
> - 인터넷에 Azure Application Gateway 수신을 통해 서비스 공개

### <a name="before-you-begin"></a>시작하기 전에

이 연습에서 설명하는 단계에서는 AKS 클러스터(Kubernetes RBAC가 사용 설정된 Kubernetes `1.19+` 이상)를 만들고 클러스터와의 `kubectl` 연결을 설정한 것으로 가정합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하고 AKS OSM 추가 기능을 설치한 다음 수신을 위한 새 Azure Application Gateway를 만듭니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.20.0 이상
- `aks-preview` 확장 버전 0.5.5 이상
- AKS 클러스터 버전 1.19 이상, Azure CNI 네트워킹 사용(Azure Vnet에 연결됨)
- OSM 버전 v0.8.0 이상
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>현재 OSM 클러스터 구성을 확인합니다.

AKS 클러스터에서 AKS에 대한 OSM 추가 기능을 사용하도록 설정하면 osm-config Kubernetes ConfigMap에서 현재 구성 매개 변수를 볼 수 있습니다. ConfigMap 속성을 보려면 다음 명령을 실행합니다.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

출력에는 클러스터에 대한 현재 OSM 구성이 표시됩니다.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**Permissive_traffic_policy_mode** 가 **true** 로 구성되어 있는지 확인합니다. OSM의 허용 트래픽 정책 모드는 [SMI](https://smi-spec.io/) 트래픽 정책 적용을 우회하는 모드입니다. 이 모드에서 OSM은 서비스 메시의 일부인 서비스를 자동으로 검색하고 각 Envoy 프록시 사이드카에 트래픽 정책 규칙을 프로그래밍하여 이러한 서비스와 통신할 수 있도록 합니다.

### <a name="create-namespaces-for-the-application"></a>애플리케이션의 네임스페이스를 만듭니다.

이 자습서에서는 다음과 같은 애플리케이션 구성 요소를 포함하는 OSM으로 구성된 애플리케이션을 사용합니다.

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

이러한 각 애플리케이션 구성 요소에 대한 네임스페이스를 만듭니다.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

다음 출력이 표시됩니다.

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM에서 관리할 네임스페이스를 온보딩합니다.

OSM 메시에 네임스페이스를 추가하는 경우 OSM 컨트롤러는 애플리케이션을 통해 Envoy 사이드카 프록시 컨테이너를 자동으로 삽입할 수 있습니다. 다음 명령을 실행하여 OSM bookstore 애플리케이션 네임스페이스를 온보딩합니다.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

다음 출력이 표시됩니다.

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Bookstore 애플리케이션을 AKS 클러스터에 배포합니다.

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

모든 배포 출력은 아래에 요약되어 있습니다.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>bookbuyer 서비스 업데이트

bookbuyer 서비스를 다음 서비스 매니페스트를 사용하여 올바른 인바운드 포트 구성으로 업데이트합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS 클러스터 내에서 실행 중인 bookstore 애플리케이션 확인

지금 bookstore 다중 컨테이너 애플리케이션이 배포되어 있지만 AKS 클러스터 내에서만 액세스할 수 있습니다. 나중에 AKS 클러스터 외부에서 애플리케이션을 공개하는 Azure Application Gateway 수신 컨트롤러를 추가합니다. 애플리케이션이 클러스터 내에서 실행되고 있는지 확인하기 위해 포트 전달을 사용하여 bookbuyer 구성 요소 UI를 확인합니다.

먼저 bookbuyer Pod의 이름을 가져오겠습니다.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

다음과 비슷한 결과가 표시됩니다. Bookbuyer Pod에 고유한 이름이 추가됩니다.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod의 이름이 있으면 이제 port-forward 명령을 사용하여 로컬 시스템에서 AKS 클러스터 내부의 애플리케이션까지의 터널을 설정할 수 있습니다. 다음 명령을 실행하여 로컬 시스템 포트 8080에 대한 포트 전달을 설정합니다. 특정 bookbuyer Pod 이름을 다시 사용합니다.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

다음과 유사한 결과가 표시됩니다.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

포트 전달 세션이 준비되는 동안 브라우저에서 다음 URL로 이동합니다(`http://localhost:8080`). 이제 아래 이미지와 비슷한 브라우저에서 bookbuyer 애플리케이션 UI를 볼 수 있습니다.

![App Gateway UI 이미지에 대한 OSM bookbuyer 앱](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>AKS 클러스터 외부에서 bookbuyer 애플리케이션을 공개하는 Azure Application Gateway 만들기

> [!NOTE]
> 다음 지침에서는 수신에 사용할 Azure Application Gateway의 새 인스턴스를 만듭니다. 기존 Azure Application Gateway를 사용하려는 경우 Application Gateway 수신 컨트롤러 추가 기능 사용 설정에 대한 섹션으로 건너뜁니다.

#### <a name="deploy-a-new-application-gateway"></a>새 Application Gateway 배포

> [!NOTE]
> 기존 AKS 클러스터에 대한 Application Gateway 수신 컨트롤러 추가 기능을 사용하도록 설정하는 기존 설명서를 참조하고 있습니다. OSM 자료에 맞게 몇 가지 사항이 수정되었습니다. 이 주제에 대한 자세한 설명서는 [여기](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)에 있습니다.

이제 새 Application Gateway를 배포하고, AKS 클러스터 _myCluster_ 에 대한 트래픽 부하를 분산하는 데 사용하려는 기존 Application Gateway를 시뮬레이션합니다. Application Gateway 이름은 _myApplicationGateway_ 이지만, 먼저 _myPublicIp_ 라는 공용 IP 리소스, 주소 공간이 11.0.0.0/8인 _myVnet_ 이라는 새 가상 네트워크, 주소 공간이 11.1.0.0/16인 _mySubnet_ 이라는 서브넷을 만들고 _myPublicIp_ 를 사용하여 _mySubnet_ 에 Application Gateway를 배포해야 합니다.

별도의 가상 네트워크에서 AKS 클러스터 및 Application Gateway를 사용하는 경우 두 가상 네트워크의 주소 공간이 겹치지 않아야 합니다. AKS 클러스터가 배포하는 기본 주소 공간은 10.0.0.0/8이므로 Application Gateway 가상 네트워크 주소 접두사를 11.0.0.0/8로 설정합니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> AGIC(Application Gateway 수신 컨트롤러) 추가 기능은 Application Gateway v2 SKU(표준 및 WAF)**만** 지원하고 Application Gateway v1 SKU를 지원하지 **않습니다**.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Azure CLI를 통해 기존 AKS 클러스터에 대한 AGIC 추가 기능 사용

Azure CLI를 계속 사용하려면 만든 AKS 클러스터 _myCluster_ 에서 AGIC 추가 기능을 계속 사용하도록 설정하고, 앞에서 만든 기존 Application Gateway _myApplicationGateway_ 를 사용하도록 AGIC 추가 기능을 지정합니다.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

다음 명령을 사용하여 Azure Application Gateway AKS 추가 기능이 사용하도록 설정되었는지 확인할 수 있습니다.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

이 명령은 출력을 `true`로 표시합니다.

#### <a name="peer-the-two-virtual-networks-together"></a>두 가상 네트워크를 피어링

한 가상 네트워크에 AKS 클러스터를 배포하고 다른 가상 네트워크에 Application Gateway를 배포했으므로, 이제 트래픽이 Application Gateway에서 클러스터의 Pod로 흐르도록 두 가상 네트워크를 피어링해야 합니다. 두 가상 네트워크를 피어링하려면 Azure CLI 명령을 두 번 실행하여 양방향으로 연결해야 합니다. 첫 번째 명령은 Application Gateway 가상 네트워크에서 AKS 가상 네트워크로 피어링 연결을 만들고, 두 번째 명령은 반대 방향으로 피어링 연결을 만듭니다.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>bookbuyer 서비스를 인터넷에 공개

AKS 클러스터에 다음 수신 매니페스트를 적용하여 Azure Application Gateway를 통해 bookbuyer 서비스를 인터넷에 공개합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

다음과 같은 출력이 표시됩니다.

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

수신 매니페스트의 호스트 이름이 테스트에 사용되는 의사(pseudo) 이름이기 때문에 인터넷에서 DNS 이름을 사용할 수 없습니다. 또는 curl 프로그램을 사용하여 호스트 이름 헤더를 Azure Application Gateway 공용 IP 주소로 넘겨 bookbuyer 서비스에 성공적으로 연결하는 200 코드를 수신할 수 있습니다.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

다음과 같은 출력이 표시됩니다.

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>문제 해결

- [AGIC 문제 해결 설명서](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [추가적인 문제 해결 도구는 AGIC의 GitHub 리포지토리에서 사용할 수 있습니다.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Azure Monitor 및 Application Insights를 사용한 OSM(Open Service Mesh) 모니터링 및 가시성

Azure Monitor 및 Azure Application Insights 모두를 사용하면 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동하기 위한 종합적인 솔루션을 제공하여 애플리케이션 및 서비스의 가용성과 성능을 극대화할 수 있습니다.

OSM AKS 추가 기능은 이러한 Azure 서비스 둘 다에 긴밀하게 통합되며, OSM 메트릭에 의해 제공되는 중요 KPI를 보고 응답하기 위한 원활한 Azure 환경을 제공합니다. OSM AKS 추가 기능에 대해 이러한 서비스를 사용하도록 설정하고 구성하는 방법에 대한 자세한 내용은 [OSM에 대한 Azure Monitor](https://aka.ms/azmon/osmpreview) 페이지를 참조하세요.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>자습서: Grafana 및 Jaeger를 수동으로 배포하여 가시성에 대한 OSM(Open Service Mesh) 메트릭 보기

> [!WARNING]
> Prometheus, Grafana 및 Jaeger의 설치는 이러한 도구를 활용하여 OSM 메트릭 데이터를 보는 방법을 보여 주는 일반 지침으로 제공됩니다. 설치 지침은 프로덕션 설정에 활용되지 않습니다. 필요에 따라 가장 적절히 설치하는 방법은 각 도구의 설명서를 참조하세요. 가장 주목할 만한 것은 영구 스토리지가 없다는 것입니다. 즉, Prometheus Grafana 및/또는 Jaeger Pod가 종료되면 모든 데이터가 손실됩니다.

OSM(Open Service Mesh)은 메시 내의 모든 트래픽과 관련된 상세 메트릭을 생성합니다. 이러한 메트릭은 사용자가 애플리케이션의 문제를 해결하고, 유지 관리하고, 분석할 수 있도록 지원하는 메시의 애플리케이션 동작에 대한 정보를 제공합니다.

현재 OSM은 사이드카 프록시(Envoy)에서 직접 메트릭을 수집합니다. OSM은 메시의 모든 서비스에 대해 들어오고 나가는 트래픽에 대한 다양한 메트릭을 제공합니다. 이러한 메트릭을 사용하여 사용자는 전체 트래픽 볼륨, 트래픽 내의 오류 및 요청에 대한 응답 시간에 대한 정보를 얻을 수 있습니다.

OSM은 Prometheus를 사용하여 메시에서 실행 중인 모든 애플리케이션에 대해 일관된 트래픽 메트릭과 통계를 수집하고 저장합니다. Prometheus는 Kubernetes 및 서비스 메시 환경에서 일반적으로 사용되는 오픈 소스 모니터링 및 경고 도구 키트입니다.

메시의 일부인 각 애플리케이션은 Prometheus 형식으로 메트릭(프록시 메트릭)을 공개하는 Envoy 사이드카를 포함하는 Pod에서 실행됩니다. 또한 메시의 일부인 모든 Pod에는 Prometheus 주석이 있어 Prometheus 서버가 애플리케이션을 동적으로 스크래핑할 수 있습니다. 이 메커니즘은 새 네임스페이스/Pod/서비스가 메시에 추가될 때마다 자동으로 메트릭 스크래핑을 사용하도록 설정합니다.

OSM 메트릭은 오픈 소스 시각화 및 분석 소프트웨어인 Grafana를 사용하여 볼 수 있습니다. 이를 통해 메트릭을 쿼리, 시각화, 경고 및 탐색할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> - Prometheus 인스턴스 만들기 및 배포
> - OSM을 구성하여 Prometheus 스크래핑 허용
> - Prometheus Configmap 업데이트
> - Grafana 인스턴스 만들기 및 배포
> - Prometheus 데이터 원본을 사용하여 Grafana 구성
> - Grafana에 대한 OSM 대시보드 가져오기
> - Jaeger 인스턴스 만들기 및 배포
> - OSM에 대한 Jaeger 추적 구성

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>OSM에 대한 Prometheus 인스턴스 배포 및 구성

여기서는 Helm을 사용하여 Prometheus 인스턴스를 배포합니다. 다음 명령을 실행하여 Helm을 통해 Prometheus를 설치합니다.

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

설치가 완료되면 아래와 유사한 출력이 표시됩니다. Prometheus 서버 포트 및 클러스터 DNS 이름을 적어 둡니다. 이 정보는 나중에 Grafana에 대한 데이터 원본으로 Prometheus를 구성하는 데 사용됩니다.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>OSM을 구성하여 Prometheus 스크래핑 허용

OSM 구성 요소가 Prometheus 스크랩에 대해 구성되어 있는지 확인하기 위해 OSM 구성 파일에 있는 **prometheus_scraping** 구성을 확인하려고 합니다. 다음 명령을 사용하여 결과를 확인합니다.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

OSM이 Prometheus 스크래핑에 대해 구성된 경우 이전 명령의 출력에서 `true`가 반환되어야 합니다. 반환된 값이 `false`이면 구성을 `true`로 업데이트해야 합니다. 다음 명령을 실행하여 OSM Prometheus 스크래핑을 **설정** 합니다.

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

다음 출력이 표시되어야 합니다.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Prometheus Configmap 업데이트

Prometheus의 기본 설치에는 두 개의 Kubernetes configmaps가 포함됩니다. 다음 명령을 사용하여 Prometheus configmaps의 목록을 볼 수 있습니다.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

**stable-prometheus-server** configmap에 있는 prometheus.yml 구성을 다음 OSM 구성으로 바꾸어야 합니다. 이 작업을 수행하는 데는 여러 가지 파일 편집 기술이 있습니다. 간단하고 안전한 방법은 configmap을 내보내고, 백업용 복사본을 만든 다음, Visual Studio Code와 같은 편집기를 사용하여 편집하는 것입니다.

> [!NOTE]
> Visual Studio Code가 설치되어 있지 않은 경우 [여기](https://code.visualstudio.com/Download)에서 다운로드하여 설치할 수 있습니다.

먼저 **stable-prometheus-server** configmap을 내보낸 다음 백업용 복사본을 만듭니다.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

다음으로 Visual Studio Code를 사용하여 편집할 파일을 엽니다.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Visual Studio Code 편집기에서 configmap을 열면 아래의 OSM 구성으로 prometheus.yml 파일을 바꾸고 파일을 저장합니다.

> [!WARNING]
> yaml 파일의 들여쓰기 구조를 유지하는 것은 매우 중요합니다. yaml 파일 구조를 조금이라도 변경하면 configmap을 다시 적용할 수 없습니다.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

다음 명령을 사용하여 업데이트된 configmap yaml 파일을 적용합니다.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> 누락된 Kubernetes 주석에 대한 메시지가 표시될 수 있습니다. 지금은 이를 무시해도 됩니다.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>OSM 메시 및 API 엔드포인트를 스크래핑하도록 Prometheus가 구성되었는지 확인합니다.

Prometheus가 OSM 메시 및 API 엔드포인트를 스크래핑하도록 올바르게 구성되었는지 확인하기 위해 Prometheus Pod에 포트를 전달하고 대상 구성을 확인합니다. 다음 명령을 실행합니다.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

브라우저에서 `http://localhost:9090/targets`를 엽니다.

아래로 스크롤하면 모든 SMI 메트릭 엔드포인트 상태가 **UP** 으로 표시되고 아래 그림과 같이 정의된 다른 OSM 메트릭이 표시됩니다.

![OSM Prometheus 대상 메트릭 UI 이미지](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>OSM에 대한 Grafana 인스턴스 배포 및 구성

Helm을 사용하여 Grafana 인스턴스를 배포합니다. 다음 명령을 실행하여 Helm을 통해 Grafana를 설치합니다.

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

다음으로 Grafana 사이트에 로그인하기 위한 기본 Grafana 암호를 검색합니다.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Grafana 암호를 기록합니다.

다음으로 Grafana Pod를 검색하여 로그인할 Grafana 대시보드로 포트를 전달합니다.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

브라우저에서 `http://localhost:3000`을 엽니다.

아래 그림에 나와 있는 로그인 화면에서 **admin** 을 사용자 이름으로 입력하고 앞에서 캡처한 Grafana 암호를 사용합니다.

![OSM Grafana 로그인 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Grafana Prometheus 데이터 원본 구성

Grafana에 성공적으로 로그인한 다음 단계로 Grafana에 대한 데이터 원본으로 Prometheus를 추가합니다. 이렇게 하려면 왼쪽 메뉴의 구성 아이콘을 탐색하고 아래와 같이 데이터 원본을 선택합니다.

![OSM Grafana 데이터 원본 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

**데이터 원본 추가** 단추를 클릭하고 시계열 데이터베이스에서 Prometheus를 선택합니다.

![OSM Grafana 데이터 원본 선택 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

**아래에서 Prometheus 데이터 원본 구성** 페이지에서 HTTP URL 설정에 대한 Kubernetes 클러스터 FQDN을 Prometheus 서비스에 입력합니다. 기본 FQDN은 `stable-prometheus-server.default.svc.cluster.local`이어야 합니다. 해당 Prometheus 서비스 엔드포인트를 입력한 후 페이지 아래쪽으로 스크롤하고 **저장 및 테스트** 를 선택합니다. 데이터 원본이 작동 중임을 나타내는 녹색 확인란이 표시됩니다.

#### <a name="importing-osm-dashboards"></a>OSM 대시보드 가져오기

OSM 대시보드는 다음 두 가지 방법을 통해 사용할 수 있습니다.

- [리포지토리](https://github.com/grafana/grafana)에서 웹 관리 포털을 통해 json Blob으로 가져오거나
- [Grafana.com에서 온라인으로](https://grafana.com/grafana/dashboards/14145) 가져올 수 있습니다.

대시보드를 가져오려면 왼쪽 메뉴에서 `+` 기호를 찾아 `import`를 선택합니다.
`Grafana.com`에서 대시보드를 ID로 직접 가져올 수 있습니다. 예를 들어, `OSM Mesh Details` 대시보드에 ID(`14145`)를 사용하는 경우 이 ID를 양식에 직접 사용하고 `import`를 선택할 수 있습니다.

![OSM Grafana 대시보드 가져오기 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

가져오기를 선택하는 즉시 가져온 대시보드로 자동으로 이동합니다.

![OSM Grafana 대시보드 가져오기 페이지 UI 이미지](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>OSM에 대한 Kubernetes에 Jaeger 연산자 배포 및 구성

[Jaeger](https://www.jaegertracing.io/)는 분산 시스템을 모니터링하고 문제를 해결하는 데 사용되는 오픈 소스 추적 시스템입니다. OSM을 사용하여 새 인스턴스로 배포하거나 자체 인스턴스를 가져올 수 있습니다. 다음 지침에서는 AKS 클러스터의 `jaeger` 네임스페이스에 Jaeger의 새 인스턴스를 배포합니다.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>AKS 클러스터에 Jaeger 배포

Jaeger를 설치하려면 다음 매니페스트를 적용합니다.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>OSM 추가 기능에 대한 추적 사용

다음에는 OSM 추가 기능에 대한 추적을 사용하도록 설정해야 합니다.

> [!NOTE]
> 현재까지는 이 시점에 추적 속성이 osm-config configmap에 보이지 않습니다. OSM AKS 추가 기능에 대한 새 릴리스에서 보이게 됩니다.

다음 명령을 실행하여 OSM 추가 기능에 대한 추적을 사용하도록 설정합니다.

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>포트 전달이 있는 Jaeger UI 보기

Jaeger의 UI가 포트 16686에서 실행되고 있습니다. Kubectl port-forward를 사용하여 웹 UI를 볼 수 있습니다.

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

브라우저에서 서비스 드롭다운이 표시되며, 이를 통해 bookstore 데모에서 배포된 다양한 애플리케이션을 선택할 수 있습니다. 모든 범위에서 볼 서비스를 선택합니다. 예를 들어 1시간 동안 Lookback을 통해 bookbuyer를 선택하는 경우 시간에 따라 정렬된 bookstore-v1 및 bookstore-v2와의 상호 작용을 볼 수 있습니다.

![OSM Jaeger 추적 페이지 UI 이미지](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

항목을 선택하여 추가 세부 정보에서 확인합니다. 여러 항목을 선택하여 추적을 비교합니다. 예를 들어 특정 시점에서 bookstore 및 bookstore-v2와 bookbuyer의 상호 작용을 비교할 수 있습니다.

시스템 아키텍처 탭을 선택하여 다양한 애플리케이션이 상호 작용하고 통신하는 방법에 대한 그래프를 볼 수도 있습니다. 이를 통해 애플리케이션 간에 트래픽이 흐르는 방식을 알 수 있습니다.

![OSM Jaeger 시스템 아키텍처 UI 이미지](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>OSM(Open Service Mesh) AKS 추가 기능 문제 해결 가이드 열기

OSM AKS 추가 기능을 배포할 때 경우에 따라 문제가 발생할 수 있습니다. 다음 가이드에서는 오류 문제를 해결하고 일반적인 문제를 해결하는 방법을 설명합니다.

### <a name="verifying-and-troubleshooting-osm-components"></a>OSM 구성 요소 확인 및 문제 해결

#### <a name="check-osm-controller-deployment"></a>OSM 컨트롤러 배포 확인

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

정상 OSM 컨트롤러는 다음과 같습니다.

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>OSM 컨트롤러 Pod를 확인합니다.

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

정상 OSM Pod는 다음과 같습니다.

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

특정 시점에 하나의 컨트롤러를 제거한 경우에도 준비 열이 1/1이고 다시 시작한 횟수가 0번인 실행되는 다른 컨트롤러가 있습니다. 준비 열이 1/1이 아닌 경우 서비스 메시는 중단된 상태에 있게 됩니다.
준비 열이 0/1이면 컨트롤 플레인 컨테이너가 충돌함을 나타내는 것이며, 로그를 가져와야 합니다. 아래의 Azure 지원 센터에서 OSM 컨트롤러 로그 가져오기 섹션을 참조하세요. 준비 열의 / 기호 뒤에 1보다 큰 숫자가 있으면 사이드카가 설치되어 있는 것입니다. 사이드카가 연결되어 있는 경우 OSM 컨트롤러는 작동하지 않을 가능성이 높습니다.

> [!NOTE]
> 버전 v 0.8.2에서 OSM 컨트롤러는 HA 모드가 아니며 복제본이 하나의 단일 Pod인 배포에서 실행됩니다. Pod는 상태 프로브를 포함하며 필요한 경우 kubelet에 의해 다시 시작됩니다.

#### <a name="check-osm-controller-service"></a>OSM 컨트롤러 서비스 확인

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

정상 OSM 컨트롤러 서비스는 다음과 같습니다.

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> 클러스터 IP는 다를 수 있습니다. 서비스 이름과 포트는 위의 예와 동일해야 합니다.

#### <a name="check-osm-controller-endpoints"></a>OSM 컨트롤러 엔드포인트 확인

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

정상 OSM 컨트롤러 엔드포인트는 다음과 같습니다.

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>OSM 인젝터 배포 확인

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

정상 OSM 인젝터 배포는 다음과 같습니다.

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>OSM 인젝터 배포 확인

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

정상 OSM 인젝터 Pod는 다음과 같습니다.

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>OSM 인젝터 서비스 확인

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

정상 OSM 인젝터 서비스는 다음과 같습니다.

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>OSM 엔드포인트 확인

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

정상 OSM 엔드포인트는 다음과 같습니다.

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>유효성 검사 웹후크 및 변경 웹후크 확인

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

정상 OSM 유효성 검사 웹후크는 다음과 같습니다.

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

정상 OSM 변경 웹후크는 다음과 같습니다.

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>유효성 검사 웹후크의 서비스 및 CA 번들을 확인합니다.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

잘 구성된 유효성 검사 웹후크 구성은 다음과 같습니다.

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>변경 웹후크의 서비스 및 CA 번들을 확인합니다.

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

잘 구성된 변경 웹후크 구성은 다음과 같습니다.

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>OSM 컨트롤러에 유효성 검사(또는 변경) 웹후크가 제공되는지 확인합니다.

> [!NOTE]
> 버전 0.8.2에서부터는 AKS RP가 유효성 검사 웹후크를 설치한다는 것을 알고 있어야 합니다. AKS 조정자는 존재를 확인하지만 CA 번들을 채우는 역할을 하는 것은 OSM 컨트롤러입니다.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

이 숫자는 CA 번들의 바이트 수 또는 크기를 나타냅니다. 이 값이 비어 있거나, 0 또는 1000보다 작은 숫자이면 CA 번들이 올바르게 프로비저닝되지 않았음을 나타내는 것입니다. 올바른 CA 번들이 없으면 유효성 검사 웹후크에 오류가 발생하게 되고 사용자가 kube-system 네임스페이스의 osm-config ConfigMap을 변경하지 못하게 됩니다.

CA 번들이 잘못된 경우의 샘플 오류:

- osm-config ConfigMap을 변경하려고는 시도:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- 오류:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

**유효성 검사** 웹후크 구성에 잘못된 인증서가 있는 경우에 대한 해결 방법:

- 옵션 1- OSM 컨트롤러 다시 시작 - OSM 컨트롤러를 다시 시작합니다. 시작 시에는 변경 웹후크 및 유효성 검사 웹후크의 CA 번들을 덮어씁니다.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- 옵션 2 - 옵션 2. 유효성 검사 웹후크 삭제 - 유효성 검사 웹후크를 제거하면 `osm-config` ConfigMap의 변경은 더 이상 유효하지 않게 됩니다. 모든 패치가 진행됩니다. AKS 조정자는 이후 유효성 검사 웹후크가 있는지 확인하고 다시 만들 것입니다. CA 번들을 신속하게 다시 작성하려면 OSM 컨트롤러를 다시 시작해야 할 수 있습니다.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- 옵션 3 - 삭제 및 패치: 다음 명령은 유효성 검사 웹후크를 삭제하고, 값을 추가하도록 허용하고, 즉시 패치 적용을 시도합니다. AKS 조정자에게 유효성 검사 웹후크를 조정하고 복원할 시간이 충분하지 않으므로 이는 최후의 수단으로 변경 내용을 적용할 기회입니다.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>`osm-config` **Configmap** 확인

> [!NOTE]
> OSM 컨트롤러는 kube-system 네임스페이스에 `osm-config` ConfigMap이 있음을 요구하지 않습니다. 컨트롤러는 구성에 대한 적절한 기본값을 가지며, 이 값이 없어도 작동할 수 있습니다.

존재 여부를 확인합니다.

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

osm-config ConfigMap의 콘텐츠를 확인합니다.

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap 값:

| 키                              | 형식   | 허용되는 값                                          | 기본값                          | 함수                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| egress                           | 부울   | true, false                                             | `"false"`                              | 메시에서 송신을 사용하도록 설정합니다.                                                                                                                                                                                                             |
| enable_debug_server              | 부울   | true, false                                             | `"true"`                               | osm-controller Pod에서 디버그 엔드포인트를 사용하도록 설정하여 프록시 연결, 인증서 및 SMI 정책과 같은 메시 관련 정보를 나열합니다.                                                                                    |
| enable_privileged_init_container | 부울   | true, false                                             | `"false"`                              | 메시에서 Pod에 대해 권한 있는 init 컨테이너를 사용하도록 설정합니다. false인 경우 init 컨테이너에는 NET_ADMIN만이 있습니다.                                                                                                                                   |
| envoy_log_level                  | 문자열 | trace, debug, info, warning, warn, error, critical, off | `"error"`                              | 메시 프록시 사이드카의 로깅 세부 정보 표시를 설정합니다. 여기에는 메시를 조인하는 새로 만든 Pod가 적용됩니다. 기존 Pod에 대한 로그 수준을 업데이트하려면 `kubectl rollout restart`를 사용하여 배포를 다시 시작합니다.                            |
| outbound_ip_range_exclusion_list | 문자열 | a.b.c.d/x 형식의 IP 범위를 쉼표로 구분한 목록입니다. | `-`                                    | 사이드카 프록시에 의해 차단되는 아웃바운드 트래픽에서 제외되는 IP 주소 범위에 대한 전체 목록입니다.                                                                                                                                    |
| permissive_traffic_policy_mode   | 부울   | true, false                                             | `"false"`                              | `true`로 설정하면 메시에서 모두 허용 모드(예: 메시에 트래픽 정책이 적용되지 않음)를 사용하도록 설정합니다. `false`로 설정하면 메시에서 모두 거부 트래픽 정책을 사용하도록 설정합니다. 즉, `SMI Traffic Target`는 서비스 통신을 위해 필요합니다. |
| prometheus_scraping              | 부울   | true, false                                             | `"true"`                               | 사이드카 프록시에서 Prometheus 메트릭 스크래핑을 사용하도록 설정합니다.                                                                                                                                                                                 |
| service_cert_validity_duration   | 문자열 | 24시간, 1시간 30분(모든 기간)                          | `"24h"`                                | 선택적인 분수와 단위 접미사가 있는 10진수의 시퀀스로 표시되는 서비스 인증서 유효 기간을 설정합니다.                                                                                             |
| tracing_enable                   | 부울   | true, false                                             | `"false"`                              | 메시에 대해 Jaeger 추적을 사용하도록 설정합니다.                                                                                                                                                                                                    |
| tracing_address                  | 문자열 | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Jaeger 배포의 주소입니다(추적을 사용하는 경우).                                                                                                                                                                                |
| tracing_endpoint                 | 문자열 | /api/v2/spans                                           | /api/v2/spans                          | 추적 데이터에 대한 엔드포인트입니다(추적을 사용하는 경우).                                                                                                                                                                                          |
| tracing_port                     | int    | 0이 아닌 정수 값                              | `"9411"`                               | 추적을 사용하도록 설정한 포트입니다.                                                                                                                                                                                                       |
| use_https_ingress                | 부울   | true, false                                             | `"false"`                              | 메시에 HTTPS 수신을 사용하도록 설정합니다.                                                                                                                                                                                                      |
| config_resync_interval           | 문자열 | 1분 이내에 이를 사용하지 않도록 설정합니다.                            | 0(사용 안 함)                           | 1분(60초) 이상의 값이 제공되면 OSM 컨트롤러는 지정된 간격으로 모든 사용 가능한 구성을 연결된 각 Envoy로 보냅니다.                                                                                                    |

#### <a name="check-namespaces"></a>네임스페이스 확인

> [!NOTE]
> kube-system 네임스페이스는 서비스 메시에 참여하지 않으며 아래의 키/값으로 레이블이 지정되거나 주석이 추가되지 않습니다.

`osm namespace add` 명령을 사용하여 지정된 서비스 메시에 네임스페이스를 조인합니다.
k8s 네임스페이스가 메시의 일부이거나 메시의 일부가 될 경우 다음을 충족해야 합니다.

다음을 사용하여 주석 보기

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

다음과 같은 주석이 있어야 합니다.

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

다음을 사용하여 레이블 보기

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

다음과 같은 레이블이 있어야 합니다.

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

네임스페이스에 주석이 `"openservicemesh.io/sidecar-injection": "enabled"`로 지정되어 있지 않거나 레이블이 `"openservicemesh.io/monitored-by": "osm"`으로 지정되지 않은 경우 OSM 인젝터는 Envoy 사이드카를 추가하지 않습니다.

> 참고: `osm namespace add`를 호출한 후 **새** Pod에는 Envoy 사이드카가 삽입됩니다. 기존 Pod는 `kubectl rollout restart deployment ...`로 다시 시작해야 합니다.

#### <a name="verify-the-smi-crds"></a>SMI CRD를 확인합니다.

클러스터에 필요한 CRD가 있는지 확인합니다.

```azurecli-interactive
kubectl get crds
```

다음이 클러스터에 설치되어 있어야 합니다.

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

다음 명령을 사용하여 설치된 CRD의 버전을 가져옵니다.

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

예상 출력:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSM 컨트롤러 버전 0.8.2에는 다음 버전이 필요합니다.

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io- 지원되지 않음
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

CRD가 없으면 다음 명령을 사용하여 클러스터에 설치합니다.

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>AKS 클러스터에 대한 OSM(Open Service Mesh) 추가 기능 사용 안 함

OSM 추가 기능을 사용하지 않도록 설정하려면 다음 명령을 실행합니다.

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
