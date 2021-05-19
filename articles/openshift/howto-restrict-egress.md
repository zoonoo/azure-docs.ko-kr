---
title: ARO(Azure Red Hat OpenShift) 클러스터에서 송신 트래픽 제한
description: ARO(Azure Red Hat OpenShift)에서 송신 트래픽을 제어하는 데 필요한 포트 및 주소에 대해 알아봅니다.
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: c15e7261587952c93d059cfcfadd06c7d204d80d
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108135676"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>ARO(Azure Red Hat OpenShift) 클러스터에 대한 송신 트래픽 제어(미리 보기)

이 문서에서는 ARO(Azure Red Hat OpenShift) 클러스터에서 아웃바운드 트래픽을 보호하는 데 필요한 세부 정보를 제공합니다. 여기에는 기본 ARO 배포를 위한 클러스터 요구 사항과 선택적 Red Hat 및 타사 구성 요소에 대한 추가 요구 사항이 포함되어 있습니다. Azure Firewall로 이러한 요구 사항을 구성하는 방법에 대한 [예](#private-aro-cluster-setup)가 마지막에 제공됩니다. Azure Firewall이나 모든 아웃 바운드 제한 방법 또는 어플라이언스에 이 정보를 적용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 새 클러스터를 만든다고 가정합니다. 기본 ARO 클러스터가 필요한 경우 [ARO 빠른 시작](./tutorial-create-cluster.md)을 참조하세요.

> [!IMPORTANT]
> ARO 미리 보기 기능은 셀프 서비스에서 사용할 수 있습니다(옵트인 방식). 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공되며 서비스 수준 계약 및 제한적 보증에서 제외됩니다. ARO 미리 보기의 일부는 고객 지원 팀에서 최선을 다해 지원합니다.

## <a name="minimum-required-fqdn--application-rules"></a>필요한 최소 FQDN / 애플리케이션 규칙

이 목록은 OpenShift 문서(https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html )에 있는 FQDN 목록을 기준으로 합니다.

다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 FQDN | 포트 | 사용 |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS:443** | 클러스터가 사용하며 설치에 필수입니다. 클러스터가 플랫폼 컨테이너 이미지를 다운로드하는 데 사용합니다. |
| **`registry.redhat.io`** | **HTTPS:443** | 핵심 추가 기능에 필수입니다. 클러스터가 개발 도구, 운영자 기반 추가 기능, Red Hat 제공 컨테이너 이미지와 같은 핵심 구성 요소를 다운로드하는 데 사용합니다.
| **`mirror.openshift.com`** | **HTTPS:443** | 미러된 설치 콘텐츠 및 이미지에 액세스하려면 VDI 환경 또는 노트북에 필요합니다. 이는 클러스터에서 quay.io에서 끌어올 이미지를 파악하기 위해 플랫폼 릴리스 서명을 다운로드하는 데 필요합니다. |
| **`api.openshift.com`** | **HTTPS:443** | 클러스터가 이미지 서명을 다운로드하기 전에 사용 가능한 업데이트가 있는지 확인하는 데 필요합니다. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | ARO 운영자를 위한 내부 프라이빗 레지스트리입니다.  서브넷에 서비스 엔드포인트 Microsoft.ContainerRegistry를 허용하지 않는 경우 필요합니다. |
| **`management.azure.com`** | **HTTPS:443** | 클러스터가 Azure API에 액세스하는 데 사용합니다. |
| **`login.microsoftonline.com`** | **HTTPS:443** | 클러스터가 Azure에 대해 인증하는 데 사용합니다. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | 이는 ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | 이는 ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | 이는 ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다. |
| **`*.table.core.windows.net`** | **HTTPS:443** | 이는 ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다. |

> [!NOTE] 
> *.blob, *.table 및 기타 큰 주소 공간을 노출하는 많은 고객에 대해서는 잠재적인 데이터 반출 문제가 발생합니다. 클러스터에 배포된 애플리케이션이 이러한 대상에 도달하지 못하도록 하고 특정 애플리케이션 요구 사항에 대해 Azure Private Link를 사용하려면 [OpenShift 송신 방화벽](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) 사용을 고려해볼 수도 있습니다.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>필수 및 선택적 FQDN의 전체 목록

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>첫 번째 그룹: 패키지와 도구 설치 및 다운로드

- **`quay.io`** : 클러스터가 사용하며 설치에 필수입니다. 클러스터가 플랫폼 컨테이너 이미지를 다운로드하는 데 사용합니다.
- **`registry.redhat.io`** : 핵심 추가 기능에 필수입니다. 클러스터가 개발 도구, 운영자 기반 추가 기능 또는 Red Hat 제공 컨테이너 이미지(예: 미들웨어, Universal Base Image 등)와 같은 핵심 구성 요소를 다운로드하는 데 사용합니다.
- **`sso.redhat.com`** : VDI 환경 또는 노트북에서 cloud.redhat.com에 연결하는 데 필요합니다. 이는 끌어오기 비밀을 다운로드할 수 있는 사이트이며 Red Hat에서 제공하는 일부 SaaS 솔루션을 사용하여 구독, 클러스터 인벤토리, 차지백 보고에 대한 모니터링을 용이하게 합니다.
- **`openshift.org`** : RH CoreOS 이미지를 다운로드하기 위해 VDI 환경 또는 노트북에서 연결하는 데 필요하지만 Azure에서는 마켓플레이스에서 다운로드되므로 OS 이미지를 다운로드할 필요가 없습니다.

---

### <a name="second-group-telemetry"></a>두 번째 그룹: 원격 분석

이 섹션은 모두 옵트아웃할 수 있는데 방법을 알아보기 전에 원격 분석이 무엇인지 먼저 확인하세요. https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`** : VDI 또는 노트북 환경에서 사용합니다.
- **`api.access.redhat.com`** : VDI 또는 노트북 환경에서 사용합니다.
- **`infogw.api.openshift.com`** : VDI 또는 노트북 환경에서 사용합니다.
- **`https://cloud.redhat.com/api/ingress`** : aaS Red Hat Insights와 통합을 수행하는 인사이트 운영자를 위해 클러스터에서 사용합니다.
OpenShift 컨테이너 플랫폼에서 고객은 상태 및 사용 현황 정보에 대한 보고를 옵트아웃할 수 있습니다. 그러나 연결된 클러스터를 통해 Red Hat은 문제에 보다 신속하게 대응하고 고객을 더 잘 지원하며 제품이 클러스터를 업그레이드하는 방법을 더 잘 이해할 수 있습니다. 세부 정보는 https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html 에서 확인합니다.

---

### <a name="third-group-cloud-apis"></a>세 번째 그룹: 클라우드 API

- **`management.azure.com`** : 클러스터가 Azure API에 액세스하는 데 사용합니다.

---

### <a name="fourth-group-other-openshift-requirements"></a>네 번째 그룹: 기타 OPENSHIFT 요구 사항

- **`mirror.openshift.com`** : VDI 환경 또는 노트북에서 미러된 설치 콘텐츠 및 이미지에 액세스하기 위해 필요하며 클러스터가 quay.io로부터 끌어올 이미지를 확인하는 데 사용되는 플랫폼 릴리스 서명을 다운로드하기 위해 클러스터에서 필요합니다.
- **`storage.googleapis.com/openshift-release`** : 클러스터가 quay.io로부터 끌어올 이미지를 확인하는 데 사용되는 플랫폼 릴리스 서명을 다운로드하는 대체 사이트입니다.
- **`*.apps.<cluster_name>.<base_domain>`** (또는 이와 동등한 ARO URL): 도메인을 허용 목록에 추가할 때 회사 네트워크에서 OpenShift에 배포된 애플리케이션에 도달하거나 OpenShift 콘솔에 액세스하는 데 사용합니다.
- **`api.openshift.com`** : 클러스터가 이미지 서명을 다운로드하기 전에 사용 가능한 업데이트가 있는지 확인하는 데 필요합니다.
- **`registry.access.redhat.com`** : ODO CLI 도구를 사용할 때 개발 이미지를 다운로드하려면 VDI 또는 노트북 환경에 레지스트리 액세스가 필요합니다. (이 CLI 도구는 Kubernetes에 익숙하지 않은 개발자를 위한 대체 CLI 도구입니다.) https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>다섯 번째 그룹: MICROSOFT & RED HAT ARO 모니터링 서비스

- **`login.microsoftonline.com`** : 클러스터가 Azure에 대해 인증하는 데 사용합니다.
- **`gcs.prod.monitoring.core.windows.net`** : ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다.
- **`*.blob.core.windows.net`** : ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다.
- **`*.servicebus.windows.net`** : ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다.
- **`*.table.core.windows.net`** : ARO 팀이 고객의 클러스터를 모니터링할 수 있도록 Microsoft Geneva 모니터링에 사용됩니다.

## <a name="aro-integrations"></a>ARO 통합

### <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor

컨테이너를 위한 Azure Monitor에 액세스할 수 있게 하는 옵션이 두 가지 있습니다. Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)를 허용할 수도 있고 **또는** 필수 FQDN/애플리케이션 규칙에 대한 액세스를 제공할 수도 있습니다.  기존 ARO 클러스터에 Azure Monitor를 추가하는 방법에 대한 [지침](../azure-monitor/containers/container-insights-azure-redhat4-setup.md)은 여기에 있습니다.

#### <a name="required-network-rules"></a>필수 네트워크 규칙

다음 FQDN/애플리케이션 규칙이 필요합니다.

| 대상 엔드포인트                                                             | 프로토콜 | 포트    | 사용  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureMonitor:443`**  | TCP           | 443      | 이 엔드포인트는 Azure Monitor 및 Log Analytics에 메트릭 데이터와 로그를 전송하는 데 사용됩니다. |

#### <a name="required-fqdn--application-rules"></a>필수 FQDN / 애플리케이션 규칙

컨테이너에 대해 Azure Monitor를 사용하도록 설정된 ARO 클러스터에는 다음 FQDN/애플리케이션 규칙이 필요합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | 이 엔드포인트는 Azure Monitor를 사용하는 모니터링 원격 분석 및 메트릭에 사용됩니다. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | 이 엔드포인트는 Azure Monitor가 로그 분석 데이터를 수집하는 데 사용됩니다. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | 이 엔드포인트는 로그 분석 서비스를 인증하는 데 사용되는 omsagent에서 사용합니다. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | 이 엔드포인트는 Azure Monitor에 메트릭 데이터를 전송하는 데 사용됩니다. |


## <a name="private-aro-cluster-setup"></a>프라이빗 ARO 클러스터 설정
Azure Firewall을 통해 송신 트래픽을 라우팅하여 ARO 클러스터를 보호하는 것이 목표입니다.
### <a name="before"></a>이전:
![이전](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>이후:
![이러한](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>프라이빗 ARO 클러스터 만들기

### <a name="set-up-vars-for-your-environment"></a>환경에 맞게 VARS 설정
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>가상 네트워크에 두 개의 비어 있는 서브넷 추가
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>가상 네트워크 및 서브넷의 Private Link 서비스에 대한 네트워크 정책을 사용하지 않도록 설정합니다. 이는 ARO 서비스가 클러스터를 액세스하고 관리하는 데 필요한 요구 사항입니다.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>방화벽 서브넷 만들기
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>점프-호스트 VM 만들기
### <a name="create-a-jump-subnet"></a>점프-서브넷 만들기
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>점프-호스트 VM 만들기
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터 만들기
### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat 풀 비밀 가져오기(선택 사항)

Red Hat 풀 비밀을 사용하면 클러스터에서 다른 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다. 이 단계는 선택 사항이지만 권장됩니다.

1. **[Red Hat OpenShift 클러스터 관리자 포털로 이동](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)하여 로그인합니다.**

   회사 이메일로 Red Hat 계정에 로그인하거나 새 Red Hat 계정을 만들고 사용 약관에 동의해야 합니다.

2. **풀 비밀 다운로드를 클릭합니다.**

저장된 `pull-secret.txt` 파일을 안전한 위치에 보관해 두세요. 각 클러스터 만들기에 사용됩니다.

`az aro create` 명령을 실행하는 경우 `--pull-secret @pull-secret.txt` 매개 변수를 사용하여 풀 비밀을 참조할 수 있습니다. `pull-secret.txt` 파일을 저장한 디렉터리에서 `az aro create`를 실행합니다. 그렇지 않으면 `@pull-secret.txt`를 `@<path-to-my-pull-secret-file`로 바꿉니다.

풀 비밀을 복사하거나 다른 스크립트에서 참조하는 경우에는 풀 비밀을 유효한 JSON 문자열로 포맷해야 합니다.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Azure Firewall 만들기

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Azure Firewall 확장 설치 업데이트
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Azure Firewall 만들기 IP 구성 설정
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>나중에 사용하기 위해 Azure Firewall IP 캡처
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Azure Firewall에 대해 UDR 및 라우팅 테이블 만들기
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Azure Firewall에 대해 애플리케이션 규칙 추가
이 [목록](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)을 기준으로 작업하기 위한 OpenShift 규칙:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Docker 이미지에 대한 선택적 규칙:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>ARO 서브넷을 FW에 연결
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Jumpbox에서 구성 테스트
이들 단계는 Docker 이미지에 대한 규칙을 추가한 경우에만 작업합니다. 
### <a name="configure-the-jumpbox"></a>Jumpbox 구성
Jumpbox VM에 로그인하여 `azure-cli`, `oc-cli` 및 `jq` 유틸리티를 설치합니다. openshift-cli 설치를 위해서는 Red Hat 고객 포털을 확인합니다.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>ARO 클러스터에 로그인
클러스터 자격 증명을 나열합니다.
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
API 서버 엔드포인트를 가져옵니다.
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Jumpbox에 oc CLI 다운로드
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

`oc login`을 사용하여 로그인합니다.
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>CentOS를 실행하여 외부 연결 테스트
Pod를 만듭니다.
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Pod가 실행되면 exec -it을 수행하여 외부 연결을 테스트합니다.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>프라이빗 클러스터의 웹 콘솔에 액세스

### <a name="set-up-ssh-forwards-commands"></a>SSH 전달 명령 설정

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>로컬 컴퓨터에서 etc. hosts 파일 수정
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>다른 옵션으로 sshuttle 사용

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>리소스 정리

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```