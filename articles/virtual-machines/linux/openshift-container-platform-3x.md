---
title: Azure에서 OpenShift 컨테이너 플랫폼 3.11 배포
description: Azure에서 OpenShift 컨테이너 플랫폼 3.11을 배포합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 615d9a3c5c359174ef15028e82044a85da0dd733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561289"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Azure에서 OpenShift 컨테이너 플랫폼 3.11 배포

여러 방법 중 하나를 사용하여 Azure에서 OpenShift 컨테이너 플랫폼 3.11을 배포할 수 있습니다.

- 필요한 Azure 인프라 구성 요소를 수동으로 배포한 다음 [OpenShift 컨테이너 플랫폼 설명서를](https://docs.openshift.com/container-platform)따를 수 있습니다.
- OpenShift Container Platform 클러스터의 배포를 간소화하는 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-container-platform/)을 사용할 수도 있습니다.
- 다른 방법은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)을 사용하는 것입니다.

모든 옵션에 Red Hat 구독이 필요합니다. 배포하는 동안 Red Hat Enterprise Linux 인스턴스는 Red Hat 구독에 등록되고 OpenShift Container Platform에 대한 자격이 포함된 풀 ID에 연결됩니다.
유효한 RHSM(Red Hat Subscription Manager) 사용자 이름, 암호 및 풀 ID가 있어야 합니다. 활성화 키, 조직 ID 및 풀 ID를 사용할 수 있습니다. 이 정보는 https://access.redhat.com에 로그인하여 확인할 수 있습니다.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>OpenShift 컨테이너 플랫폼 리소스 관리자 3.11 템플릿을 사용하여 배포

### <a name="private-clusters"></a>개인 클러스터

개인 OpenShift 클러스터를 배포하려면 마스터 로드 밸런서(웹 콘솔) 또는 인프라 로드 밸런서(라우터)에 연결된 공용 IP가 없는 것 이상이 필요합니다.  개인 클러스터는 일반적으로 사용자 지정 DNS 서버(기본 Azure DNS가 아님), 사용자 지정 도메인 이름(예: contoso.com) 및 미리 정의된 가상 네트워크를 사용합니다.  개인 클러스터의 경우 모든 적절한 서브넷 및 DNS 서버 설정을 사용하여 가상 네트워크를 미리 구성해야 합니다.  그런 다음 **기존MasterSubnetReference,** **기존InfraSubnetReference,** **기존 CnsSubnet참조**및 **기존NodeSubnetReference를** 사용하여 클러스터에서 사용할 기존 서브넷을 지정합니다.

개인 마스터를 선택한**경우(masterClusterType**=private) **masterPrivateClusterIp**에 대해 정적 개인 IP를 지정해야 합니다.  이 IP는 마스터 로드 밸러버의 프런트 엔드에 할당됩니다.  IP는 마스터 서브넷의 CIDR 내에 있어야 하며 사용 중이 아닙니다.  **masterClusterDnsType은** "사용자 지정"으로 설정되어야 하며 **masterClusterDns**에 대해 마스터 DNS 이름을 제공해야 합니다.  DNS 이름은 정적 개인 IP에 매핑되어야 하며 마스터 노드의 콘솔에 액세스하는 데 사용됩니다.

개인 라우터를 선택한 경우 **(라우터ClusterType**=private), 정적 개인 **IP는 라우터PrivateClusterIp**에 대해 지정해야 합니다.  이 IP는 인프라 로드 밸러버의 프런트 엔드에 할당됩니다.  IP는 인프라 서브넷의 CIDR 내에 있어야 하며 사용 중이 아닙니다.  **라우팅SubDomainType은** "사용자 지정"으로 설정되어야하며 라우팅을위한 와일드 카드 DNS 이름을 **라우팅SubDomain**.  

개인 마스터 및 개인 라우터를 선택한 경우 **도메인 이름에** 대해 사용자 지정 도메인 이름도 입력해야 합니다.

성공적으로 배포한 후 Bastion Node는 공용 IP를 사용할 수 있는 유일한 노드입니다.  마스터 노드가 공용 액세스를 위해 구성된 경우에도 ssh 액세스에 대해 노출되지 않습니다.

Resource Manager 템플릿을 사용하여 배포하려면 매개 변수 파일을 사용하여 입력 매개 변수를 제공합니다. 배포를 추가로 사용자 지정하려면 GitHub 리포지토리를 포크하고 적절한 항목을 변경합니다.

일반적인 사용자 지정 옵션에는 다음이 포함됩니다(이에 국한되지 않음).

- 요새 VM 크기(azuredeploy.json의 변수)
- 명명 규칙(azuredeploy.json의 변수)
- OpenShift 클러스터 세부 정보, 호스트 파일을 통해 수정 됨(deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>매개 변수 파일 구성

[OpenShift Container Platform 템플릿](https://github.com/Microsoft/openshift-container-platform)에는 각기 다른 OpenShift Container Platform 버전에 사용할 수 있는 여러 분기가 있습니다.  필요에 따라, 리포지토리에서 직접 배포하거나, 배포하기 전에 리포지토리를 포크하고 템플릿 또는 스크립트를 사용자 지정할 수 있습니다.

`aadClientId` 매개 변수에 대해 이전에 만든 서비스 주체에서 `appId` 값을 사용합니다.

다음 예제에서는 모든 필수 입력이 포함된 azuredeploy.parameters.json이라는 매개 변수 파일을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

매개 변수를 자신의 특정 정보로 바꿉니다.

릴리스마다 다른 매개 변수를 사용할 수 있으므로, 사용하는 분기에 필요한 매개 변수를 확인합니다.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. 매개 변수.json 파일 설명

| 속성 | 설명 | 유효한 옵션 | 기본값 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 아티팩트(json, 스크립트 등)에 대한 URL |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Azure 리전에서 리소스를 배포합니다. |  |  |
| `masterVmSize` | 마스터 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용된 VM 크기 중 하나를 선택합니다. |  | Standard_E2s_v3 |
| `infraVmSize` | 인프라 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용된 VM 크기 중 하나를 선택합니다. |  | Standard_D4s_v3 |
| `nodeVmSize` | 앱 노드 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용된 VM 크기 중 하나를 선택합니다. |  | Standard_D4s_v3 |
| `cnsVmSize` | 컨테이너 네이티브 저장소(CNS) 노드 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용된 VM 크기 중 하나를 선택합니다. |  | Standard_E4s_v3 |
| `osImageType` | 사용할 RHEL 이미지입니다. 기본 갤러리: 온디맨드; 마켓플레이스: 타사 이미지 | 기본 갤러리 <br> Marketplace | 기본 갤러리 |
| `marketplaceOsImage` | 마켓플레이스인 경우 `osImageType` 마켓플레이스 오퍼의 '퍼블리셔', '오퍼', '스쿠', '버전'에 적합한 값을 입력합니다. 이 매개 변수는 개체 형식입니다. |  |  |
| `storageKind` | 사용할 저장소 유형  | 관리<br> unmanaged | 관리 |
| `openshiftClusterPrefix` | 모든 노드에 대한 호스트 이름을 구성하는 데 사용되는 클러스터 접두사입니다.  1~20자 사이 |  | 마이클러스터 |
| `minoVersion` | 배포할 OpenShift 컨테이너 플랫폼 3.11의 부버전 |  | 69 |
| `masterInstanceCount` | 배포할 마스터 노드 수 | 1, 3, 5 | 3 |
| `infraInstanceCount` | 배포할 인프라 노드 수 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 배포할 노드 수 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | 배포할 CNS 노드 수 | 3, 4 | 3 |
| `osDiskSize` | VM용 OS 디스크 크기(GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | 도커 볼륨(GB)에 대한 노드에 연결할 데이터 디스크 크기 | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | 글루스터프에서 사용하기 위해 CNS 노드에 연결할 데이터 디스크 의 크기(GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | OS(VM) 로그인 및 초기 OpenShift 사용자 모두에 대한 관리자 사용자 이름 |  | 옥파드민 |
| `enableMetrics` | 메트릭을 사용하도록 설정합니다. 메트릭에는 더 많은 리소스가 필요하므로 인프라 VM에 적합한 크기를 선택하십시오. | true <br> false | false |
| `enableLogging` | 로깅을 활성화합니다. 탄성 검색 포드는 8GB RAM이 필요하므로 인프라 VM에 적합한 크기를 선택하십시오. | true <br> false | false |
| `enableCNS` | 컨테이너 네이티브 저장소 사용 | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat 구독 관리자 사용자 이름 또는 조직 ID |  |  |
| `rhsmPoolId` | 계산 노드에 대한 OpenShift 사용 권한이 포함된 Red Hat 구독 관리자 풀 ID |  |  |
| `rhsmBrokerPoolId` | 마스터 및 인프라 노드에 대한 OpenShift 사용 권한이 포함된 Red Hat 구독 관리자 풀 ID입니다. 다른 풀 ID가 없는 경우 'rhsmPoolId'와 동일한 풀 ID를 입력합니다. |  |
| `sshPublicKey` | 여기에서 SSH 공개 키 복사 |  |  |
| `keyVaultSubscriptionId` | 키 자격 증명 모음이 포함된 구독의 구독 ID |  |  |
| `keyVaultResourceGroup` | 키 자격 증명 모음이 포함된 리소스 그룹의 이름 |  |  |
| `keyVaultName` | 만든 키 볼트의 이름 |  |  |
| `enableAzure` | Azure 클라우드 공급자 사용 | true <br> false | true |
| `aadClientId` | 서비스 주체에 대 한 응용 프로그램 ID라고도 하는 Azure Active Directory 클라이언트 ID |  |  |
| `domainName` | 사용할 사용자 지정 도메인 이름(해당하는 경우)의 이름입니다. 완전히 개인 클러스터를 배포하지 않는 경우 "없음"으로 설정 |  | none |
| `masterClusterDnsType` | OpenShift 웹 콘솔용 도메인 유형입니다. '기본값'은 마스터 인프라 공용 IP의 DNS 레이블을 사용합니다. '사용자 지정'을 사용하면 자신의 이름을 정의할 수 있습니다. | default <br> 사용자 지정 | default |
| `masterClusterDns` | '사용자 지정'을 선택한 경우 OpenShift 웹 콘솔에 액세스하는 데 사용할 사용자 지정 DNS 이름`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | '니피오'로 `routingSubDomain` 설정하면 nip.io 사용합니다.  라우팅에 사용할 고유한 도메인이 있는 경우 '사용자 지정'을 사용합니다. | 니피오 (것) <br> 사용자 지정 | 니피오 (것) |
| `routingSubDomain` | '사용자 지정'을 선택한 경우 라우팅에 사용할 와일드카드 DNS 이름`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 기존 가상 네트워크를 사용할지 아니면 새 가상 네트워크를 만들지 선택합니다. | 기존 <br> 신규 | 신규 |
| `virtualNetworkResourceGroupName` | '새'를 선택한 경우 새 가상 네트워크에 대한 리소스 그룹의 이름`virtualNetworkNewOrExisting` |  | 리소스 그룹().이름 |
| `virtualNetworkName` | '새'를 선택한 경우 만들 새 가상 네트워크의 이름입니다.`virtualNetworkNewOrExisting` |  | 오픈 시프트브넷 |
| `addressPrefixes` | 새 가상 네트워크의 주소 접두사 |  | 10.0.0.0/14 |
| `masterSubnetName` | 마스터 서브넷의 이름 |  | 마스터 서브넷 |
| `masterSubnetPrefix` | 마스터 서브넷에 사용되는 CIDR - 주소사전의 하위 집합이어야 합니다. |  | 10.1.0.0/16 |
| `infraSubnetName` | 인프라 서브넷의 이름 |  | 인프라스바넷 |
| `infraSubnetPrefix` | 인프라 서브넷에 사용되는 CIDR - 주소사전의 하위 집합이어야 합니다. |  | 10.2.0.0/16 |
| `nodeSubnetName` | 노드 서브넷의 이름 |  | 노드 서브넷 |
| `nodeSubnetPrefix` | 노드 서브넷에 사용되는 CIDR - 주소사전의 하위 집합이어야 합니다. |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 마스터 노드에 대한 기존 서브넷에 대한 전체 참조입니다. 새 vNet / 서브넷을 만드는 경우 필요하지 않습니다. |  |  |
| `existingInfraSubnetReference` | 인프라 노드에 대한 기존 서브넷에 대한 전체 참조입니다. 새 vNet / 서브넷을 만드는 경우 필요하지 않습니다. |  |  |
| `existingCnsSubnetReference` | CNS 노드에 대한 기존 서브넷에 대한 전체 참조입니다. 새 vNet / 서브넷을 만드는 경우 필요하지 않습니다. |  |  |
| `existingNodeSubnetReference` | 계산 노드에 대한 기존 서브넷에 대한 전체 참조입니다. 새 vNet / 서브넷을 만드는 경우 필요하지 않습니다. |  |  |
| `masterClusterType` | 클러스터에서 개인 마스터 노드또는 공용 마스터 노드를 사용할지 여부를 지정합니다. 개인을 선택하면 마스터 노드는 공용 IP를 통해 인터넷에 노출되지 않습니다. 대신, 에 지정된 개인 IP를 사용합니다.`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | 개인 마스터 노드를 선택한 경우 마스터 노드의 내부 로드 밸러버에서 사용할 개인 IP 주소를 지정해야 합니다. 이 정적 IP는 마스터 서브넷의 CIDR 블록 내에 있어야 하며 아직 사용 중이 아닙니다. 공용 마스터 노드를 선택한 경우 이 값은 사용되지 않지만 여전히 지정해야 합니다. |  | 10.1.0.200 |
| `routerClusterType` | 클러스터가 개인 또는 공용 인프라 노드를 사용하는지 여부를 지정합니다. private을 선택하면 인프라 노드가 공용 IP를 통해 인터넷에 노출되지 않습니다. 대신, 에 지정된 개인 IP를 사용합니다.`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | 개인 인프라 노드를 선택한 경우 인프라 노드의 내부 로드 밸러버에서 사용할 개인 IP 주소를 지정해야 합니다. 이 정적 IP는 마스터 서브넷의 CIDR 블록 내에 있어야 하며 아직 사용 중이 아닙니다. 공용 인프라 노드를 선택한 경우 이 값은 사용되지 않지만 여전히 지정해야 합니다. |  | 10.2.0.200 |
| `routingCertType` | 라우팅 도메인 또는 기본 자체 서명 인증서에 사용자 지정 인증서 사용 - 사용자 지정 인증서 섹션의 지침 **따르기** | 자체 서명 <br> 사용자 지정 | 자체 서명 |
| `masterCertType` | 마스터 도메인 또는 기본 자체 서명 인증서에 대한 사용자 지정 인증서 사용 - 사용자 지정 인증서 섹션의 지침을 **따르십시오.** | 자체 서명 <br> 사용자 지정 | 자체 서명 |

<br>

### <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포

> [!NOTE] 
> 다음 명령은 Azure CLI 2.0.8 이상이 필요합니다. CLI 버전은 `az --version` 명령으로 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)를 참조하세요.

다음 예제에서는 openshiftrg라는 리소스 그룹에 OpenShift 클러스터 및 모든 관련 리소스를 배포하고 배포 이름을 myOpenShiftCluster로 지정합니다. 템플릿은 GitHub 리포지토리에서 직접 참조되며, azuredeploy.parameters.json이라는 로컬 매개 변수 파일이 사용됩니다.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

배포된 총 노드 수와 구성된 옵션에 따라 배포를 완료하는 데 60분 이상 걸립니다. 배포를 마치면 OpenShift 콘솔의 Bastion DNS FQDN 및 URL이 터미널에 출력됩니다.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

배포가 완료될 때까지 명령줄을 대기하지 않으려면 그룹 배포에 대한 옵션 중 하나로 `--no-wait`를 추가합니다. 배포의 출력은 Azure Portal의 리소스 그룹에 대한 배포 섹션에서 검색할 수 있습니다.

## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. OpenShift 콘솔 URL을 사용하여 브라우저와 **오픈 시프트 콘솔에**연결합니다. 또한 Bastion 호스트에 SSH를 할 수 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-container-platform-3x-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com)
