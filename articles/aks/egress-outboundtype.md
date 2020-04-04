---
title: Azure Kubernetes 서비스(AKS)에서 사용자 정의 경로(UDR)를 사용자 지정
description: Azure Kubernetes 서비스(AKS)에서 사용자 지정 송신 경로를 정의하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 30b7b6bae92221b268d40977f5b299e9b0b267b0
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637818"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>사용자 정의 경로(미리 보기)로 클러스터 송신 사용자 지정

AKS 클러스터의 송신은 특정 시나리오에 맞게 사용자 지정할 수 있습니다. 기본적으로 AKS는 설정하여 송신에 사용할 표준 SKU 로드 밸러워기를 프로비전합니다. 그러나 공용 IP가 허용되지 않거나 송신에 추가 홉이 필요한 경우 기본 설정이 모든 시나리오의 요구 사항을 충족하지 못할 수 있습니다.

이 문서에서는 공용 IP를 허용하지 않으며 클러스터가 NVA(네트워크 가상 어플라이언스) 뒤에 있어야 하는 것과 같은 사용자 지정 네트워크 시나리오를 지원하기 위해 클러스터의 송신 경로를 사용자 지정하는 방법을 설명합니다.

> [!IMPORTANT]
> AKS 미리보기 기능은 셀프 서비스이며 옵트인 방식으로 제공됩니다. 미리 보기는 사용 *가능한* *대로* 제공되며 서비스 수준 계약(SLA) 및 제한 보증에서 제외됩니다. AKS 미리 보기는 *최선의 노력을* 기울여 고객 지원에 의해 부분적으로 다룹니다. 따라서 이 기능은 프로덕션용이 아닙니다. 자세한 내용은 다음 지원 문서를 참조하십시오.
>
> * [AKS 지원 정책](support-policies.md)
> * [Azure 지원 FAQ](faq.md)

## <a name="prerequisites"></a>사전 요구 사항
* Azure CLI 버전 2.0.81 이상
* Azure CLI 미리 보기 확장 버전 0.4.28 이상
* API 버전 `2020-01-01` 이상

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>최신 Azure CLI AKS 미리 보기 확장 설치
클러스터의 아웃바운드 유형을 설정하려면 Azure CLI AKS 미리 보기 확장 버전 0.4.18 이상이 필요합니다. az 확장 추가 명령을 사용하여 Azure CLI AKS 미리 보기 확장을 설치한 다음 다음 az 확장 업데이트 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>제한 사항
* 미리 보기 `outboundType` 중에는 클러스터 생성 시에만 정의할 수 있으며 나중에 업데이트할 수 없습니다.
* 미리 보기 `outboundType` 중에 AKS 클러스터는 Azure CNI를 사용해야 합니다. Kubenet은 구성 가능하며, 사용은 AKS 서브넷에 대한 경로 테이블의 수동 연결이 필요합니다.
* 을 `outboundType` 사용하려면 을 사용 `vm-set-type` `VirtualMachineScaleSets` 하지 `load-balancer-sku` `Standard`않은 AKS 클러스터가 필요합니다.
* 값을 `outboundType` `UDR` 설정하려면 클러스터에 대해 유효한 아웃바운드 연결이 있는 사용자 정의 경로가 필요합니다.
* 값을 `outboundType` `UDR` 설정하면 로드 밸런서에 라우팅된 받는 소스 IP가 클러스터의 나가는 송신 대상 주소와 **일치하지 않을** 수 있습니다.

## <a name="overview-of-outbound-types-in-aks"></a>AKS의 아웃바운드 유형 개요

AKS 클러스터는 고유한 `outboundType` 형식 로드 밸런서 또는 사용자 정의 라우팅으로 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> 아웃바운드 유형은 클러스터의 송신 트래픽에만 영향을 줍니다. 자세한 내용은 [수집 컨트롤러 설정을](ingress-basic.md) 참조하십시오.

### <a name="outbound-type-of-loadbalancer"></a>아웃바운드 유형의 로드밸슬러

`loadBalancer` 설정되면 AKS가 자동으로 다음 설정을 완료합니다. 로드 밸런서는 AKS에 할당된 공용 IP를 통해 송신하는 데 사용됩니다. 아웃바운드 유형은 `loadBalancer` AKS 리소스 공급자가 `loadBalancer`만든 로드 밸런서에서 송신될 것으로 예상되는 형식의 Kubernetes 서비스를 지원합니다.

다음 설정은 AKS에 의해 수행됩니다.
   * 클러스터 송신에 대해 공용 IP 주소가 프로비전됩니다.
   * 공용 IP 주소는 로드 밸런서 리소스에 할당됩니다.
   * 로드 밸런서에 대한 백엔드 풀은 클러스터의 에이전트 노드에 대해 설정됩니다.

다음은 를 사용하는 `outboundType` AKS 클러스터에 기본적으로 배포된 네트워크 `loadBalancer`토폴로지입니다.

![아웃바운드 타입-파운드](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>아웃바운드 유형 의 사용자정의 라우팅

> [!NOTE]
> 아웃바운드 유형을 사용하는 것은 고급 네트워킹 시나리오이며 적절한 네트워크 구성이 필요합니다.

`userDefinedRouting` 설정되면 AKS가 송신 경로를 자동으로 구성하지 않습니다. 다음은 **사용자가**수행할 것으로 예상됩니다.

클러스터는 구성된 서브넷이 있는 기존 가상 네트워크에 배포되어야 합니다. 아웃바운드 연결이 있는 서브넷에 유효한 사용자 정의 경로(UDR)가 있어야 합니다.

AKS 리소스 공급자는 표준 로드 밸러터(SLB)를 배포합니다. 로드 밸러블러는 규칙으로 구성되지 [않았으며 규칙이 배치될 때까지 요금이 부과되지 않습니다.](https://azure.microsoft.com/pricing/details/load-balancer/) AKS는 SLB 프런트 엔드에 대한 공용 IP 주소를 자동으로 **프로비전하지 않습니다.** AKS는 로드 밸러서 백엔드 풀을 자동으로 **구성하지 않습니다.**

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>아웃바운드 유형의 UDR 및 Azure 방화벽이 있는 클러스터 배포

사용자 정의 경로를 사용하여 아웃바운드 유형이 있는 클러스터의 응용 프로그램을 설명하기 위해 Azure 방화벽을 사용하는 가상 네트워크에서 클러스터를 구성할 수 있습니다.

![잠긴 토폴로지](media/egress-outboundtype/outboundtype-udr.png)

* 침입은 방화벽 필터를 통해 흐르도록 강요됩니다.
   * 격리된 서브넷에는 에이전트 노드로 라우팅하기 위한 내부 로드 밸런서가 있습니다.
   * 에이전트 노드는 전용 서브넷에서 격리됩니다.
* 아웃바운드 요청은 사용자 정의 경로를 사용하여 에이전트 노드에서 Azure 방화벽 내부 IP로 시작됩니다.
   * AKS 에이전트 노드의 요청은 AKS 클러스터가 배포된 서브넷에 배치된 UDR을 따릅니다.
   * Azure 방화벽은 공용 IP 프런트 엔드에서 가상 네트워크에서 송신됩니다.
   * AKS 제어 평면에 대한 액세스는 방화벽 프런트 엔드 IP 주소를 활성화한 NSG에 의해 보호됩니다.
   * 방화벽 프런트 엔드 IP 주소로 흐르는 공용 인터넷 또는 기타 Azure 서비스에 대한 액세스

### <a name="set-configuration-via-environment-variables"></a>환경 변수를 통한 구성 설정

리소스 생성에 사용할 환경 변수 집합을 정의합니다.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

다음으로 구독 아이디를 설정합니다.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>여러 서브넷이 있는 가상 네트워크 만들기

가상 네트워크에 세 개의 별도 서브넷(클러스터용 서브넷, 방화벽용 및 서비스 침입용)으로 가상 네트워크를 프로비전합니다.

![빈 네트워크 토폴로지](media/egress-outboundtype/empty-network.png)

모든 리소스를 보유할 리소스 그룹을 만듭니다.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

AKS 클러스터와 Azure 방화벽을 호스트하는 두 개의 가상 네트워크를 만듭니다. 각각은 자신의 서브넷을 가질 것이다. AKS 네트워크부터 살펴보겠습니다.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>UDR을 사용하여 Azure 방화벽 만들기 및 설정

Azure 방화벽 인바운드 및 아웃바운드 규칙을 구성해야 합니다. 방화벽의 주요 목적은 조직이 AKS 클러스터안팎으로 트래픽 규칙을 세분화하고 송신할 수 있도록 하는 것입니다.

![방화벽 및 UDR](media/egress-outboundtype/firewall-udr.png)

Azure 방화벽 프런트 엔드 주소로 사용할 표준 SKU 공용 IP 리소스를 만듭니다.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

미리 보기 cli 확장을 등록하여 Azure 방화벽을 만듭니다.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

이제 이전에 만든 IP 주소를 방화벽 프런트 엔드에 할당할 수 있습니다.
> [!NOTE]
> Azure 방화벽에 대한 공용 IP 주소를 설정하는 데 몇 분 정도 걸릴 수 있습니다.
> 
> 아래 명령에서 오류가 반복적으로 수신되면 기존 방화벽 및 공용 IP를 삭제하고 포털을 통해 공용 IP 및 Azure 방화벽을 동시에 프로비전합니다.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

이전 명령이 성공하면 나중에 구성을 위해 방화벽 프런트 엔드 IP 주소를 저장합니다.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Azure 방화벽에 홉을 사용하여 UDR 만들기

Azure는 Azure 서브넷, 가상 네트워크 및 온-프레미스 네트워크 간에 트래픽을 자동으로 라우트합니다. Azure의 기본 라우팅 중 하나를 변경하려면 경로 테이블을 만듭니다.

지정된 서브넷과 연결할 빈 라우트 테이블을 만듭니다. 경로 테이블은 위에서 만든 Azure 방화벽으로 다음 홉을 정의합니다. 각 서브넷에는 0 또는 하나의 경로 테이블이 연결될 수 있습니다.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Azure의 기본 시스템 경로를 재정의하거나 서브넷의 경로 테이블에 추가 경로를 추가하는 방법에 대한 [가상 네트워크 경로 테이블 설명서를](../virtual-network/virtual-networks-udr-overview.md#user-defined) 참조하세요.

## <a name="adding-network-firewall-rules"></a>네트워크 방화벽 규칙 추가

> [!WARNING]
> 다음은 방화벽 규칙을 추가하는 한 가지 예입니다. [필요한 송신 끝점에](egress.md) 정의된 모든 송신 끝점은 AKS 클러스터가 작동하려면 응용 프로그램 방화벽 규칙에 의해 활성화되어야 합니다. 이러한 끝점을 사용하도록 설정하지 않으면 클러스터를 작동할 수 없습니다.

다음은 네트워크 및 응용 프로그램 규칙의 예입니다. 모든 프로토콜, 소스 주소, 대상 주소 및 대상 포트를 허용하는 네트워크 규칙을 추가합니다. 또한 AKS에서 요구하는 **일부** 끝점에 대한 응용 프로그램 규칙을 추가합니다.

프로덕션 시나리오에서는 응용 프로그램에 필요한 끝점에 대해서만 액세스하고 [AKS에 정의된 송신은](egress.md)사용이 필요합니다.

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Azure 방화벽 서비스에 대해 자세히 알아보려면 [Azure 방화벽 설명서를](https://docs.microsoft.com/azure/firewall/overview) 참조하십시오.

## <a name="associate-the-route-table-to-aks"></a>경로 테이블을 AKS에 연결

클러스터를 방화벽과 연결하려면 클러스터의 서브넷에 대한 전용 서브넷이 위에서 만든 경로 테이블을 참조해야 합니다. 클러스터와 방화벽을 모두 보유한 가상 네트워크에 명령을 실행하여 클러스터 서브넷의 경로 테이블을 업데이트하여 연결을 수행할 수 있습니다.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>기존 네트워크에 아웃바운드 유형의 UDR을 사용하여 AKS배포

이제 AKS 클러스터를 기존 가상 네트워크 설정에 배포할 수 있습니다. 클러스터 아웃바운드 유형을 사용자 정의 라우팅으로 설정하려면 기존 서브넷을 AKS에 제공해야 합니다.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>기존 가상 네트워크 내에서 프로비전할 수 있는 액세스 권한이 있는 서비스 주체 만들기

서비스 주체는 AKS에서 클러스터 리소스를 만드는 데 사용됩니다. 생성 시 전달된 서비스 주체는 AKS에서 사용하는 VM, 저장소 및 로드 밸러서와 같은 기본 AKS 리소스를 만드는 데 사용됩니다. 사용 권한이 너무 적으면 AKS 클러스터를 프로비전할 수 없습니다.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

이제 이전 `APPID` `PASSWORD` 명령 출력에 의해 자동 생성된 서비스 주 앱 id 및 서비스 주 암호로 아래를 바꿉입니다. AkS가 리소스를 배포할 수 있도록 서비스 주체에 권한을 부여하기 위해 VNET 리소스 ID를 참조합니다.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>AKS 배포

마지막으로 AKS 클러스터를 클러스터 전용으로 전용한 기존 서브넷에 배포할 수 있습니다. 배포할 대상 서브넷은 환경 변수로 `$SUBNETID`정의됩니다. 이전 단계에서 변수를 `$SUBNETID` 정의하지 않았습니다. 서브넷 ID의 값을 설정하려면 다음 명령을 사용할 수 있습니다.

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

서브넷에 있는 UDR을 따르도록 아웃바운드 유형을 정의하여 AKS가 이제 엄격하게 내부가 될 수 있는 로드 밸러버에 대한 설정 및 IP 프로비저닝을 건너뛸 수 있도록 합니다.

[API 서버 공인 IP 범위에](api-server-authorized-ip-ranges.md) 대한 AKS 기능을 추가하여 API 서버 액세스를 방화벽의 공용 끝점으로만 제한할 수 있습니다. 승인된 IP 범위 기능은 제어 평면에 액세스하기 위해 전달되어야 하는 NSG로 다이어그램에 표시됩니다. 권한 있는 IP 범위 기능을 사용하여 API 서버 액세스를 제한할 때 개발자 도구는 방화벽의 가상 네트워크에서 점프박스를 사용하거나 모든 개발자 끝점을 승인된 IP 범위에 추가해야 합니다.

> [!TIP]
> (개인 클러스터)[]와 같은 추가 기능을 클러스터 배포에 추가할 수 있습니다. 권한 있는 IP 범위를 사용하는 경우 API 서버에 액세스하려면 클러스터 네트워크 내부에 점프박스가 필요합니다.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>개발자가 API 서버에 액세스할 수 있도록 설정

클러스터에 대한 승인된 IP 범위 설정으로 인해 API 서버에 액세스하려면 승인된 IP 범위의 AKS 클러스터 목록에 개발자 툴링 IP 주소를 추가해야 합니다. 또 다른 옵션은 방화벽의 가상 네트워크의 별도의 서브넷 내부에 필요한 툴링으로 점프박스를 구성하는 것입니다.

다음 명령을 사용하여 승인된 범위에 다른 IP 주소를 추가합니다.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 [az aks get-credentials][az-aks-get-credentials] 명령을 `kubectl` 사용하여 새로 만든 Kubernetes 클러스터에 연결하도록 구성합니다. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>내부 부하 분산기 설정

AKS는 내부 로드 밸런서로 설정할 수 있는 클러스터와 함께 [로드 밸런서를](internal-lb.md)배포했습니다.

내부 로드 밸런서를 만들려면 다음 예제와 같이 서비스 유형 LoadBalancer 및 azure-load-balancer-internal annotation을 사용하여 internal-lb.yaml이라는 서비스 매니페스트를 만듭니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

kubectl 적용을 사용하여 내부 로드 밸러버를 배포하고 YAML 매니페스트의 이름을 지정합니다.

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Kubernetes 서비스 배포

클러스터 아웃바운드 유형이 UDR로 설정되므로 클러스터 생성 시 AKS에서 로드 밸런서의 백 엔드 풀로 에이전트 노드를 연결하지 않습니다. 그러나 Kubernetes 서비스가 배포될 때 백 엔드 풀 연결은 Kubernetes Azure 클라우드 공급자에 의해 처리됩니다.

아래 yaml을 라는 `example.yaml`파일에 복사하여 Azure 투표 응용 프로그램 배포.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

다음을 실행하여 서비스를 배포합니다.

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Azure 방화벽에 DNAT 규칙 추가

인바운드 연결을 구성하려면 AZURE 방화벽에 DNAT 규칙을 기록해야 합니다. 클러스터에 대한 연결을 테스트하기 위해 내부 서비스에서 노출되는 내부 IP로 라우팅하기 위해 방화벽 프런트 엔드 공용 IP 주소에 대한 규칙이 정의됩니다.

대상 주소는 액세스할 방화벽의 포트이기 때문에 사용자 지정할 수 있습니다. 변환된 주소는 내부 로드 밸러버의 IP 주소여야 합니다. 번역된 포트는 Kubernetes 서비스에 노출된 포트여야 합니다.

Kubernetes 서비스에서 만든 로드 밸러저에 할당된 내부 IP 주소를 지정해야 합니다. 다음을 실행하여 주소를 검색합니다.

```bash
kubectl get services
```

필요한 IP 주소는 다음과 유사한 외부 IP 열에 나열됩니다.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>리소스 정리

> [!NOTE]
> Kubernetes 내부 서비스를 삭제할 때 내부 로드 밸런서가 서비스에서 더 이상 사용되지 않는 경우 Azure 클라우드 공급자는 내부 로드 밸런서를 삭제합니다. 다음 서비스 배포에서 요청된 구성을 찾을 수 없는 경우 로드 밸러터가 배포됩니다.

Azure 리소스를 정리하려면 AKS 리소스 그룹을 삭제합니다.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>연결 유효성 검사

브라우저에서 Azure 방화벽 프런트 엔드 IP 주소로 이동하여 연결의 유효성을 검사합니다.

Azure 투표 앱의 이미지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

[Azure 네트워킹 UDR 개요를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)참조하십시오.

[라우트 테이블을 생성, 변경 또는 삭제하는 방법을](https://docs.microsoft.com/azure/virtual-network/manage-route-table)알아봅니다.
