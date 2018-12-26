---
title: Azure에 OpenShift Container Platform 배포 | Microsoft Docs
description: Azure에서 OpenShift Container Platform을 배포합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 21eebb6c27a83b939f321d38026da7d4c39b7071
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085889"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Azure에서 OpenShift Container Platform 배포

Azure에서 OpenShift Container Platform을 배포하는 몇 가지 방법 중 하나를 사용할 수 있습니다.

- 필요한 Azure 인프라 구성 요소를 수동으로 배포한 다음, [OpenShift Container Platform 설명서](https://docs.openshift.com/container-platform)를 따를 수 있습니다.
- OpenShift Container Platform 클러스터의 배포를 간소화하는 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-container-platform/)을 사용할 수도 있습니다.
- 다른 방법은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)을 사용하는 것입니다.

모든 옵션에 Red Hat 구독이 필요합니다. 배포하는 동안 Red Hat Enterprise Linux 인스턴스는 Red Hat 구독에 등록되고 OpenShift Container Platform에 대한 자격이 포함된 풀 ID에 연결됩니다.
유효한 RHSM(Red Hat Subscription Manager) 사용자 이름, 암호 및 풀 ID가 있어야 합니다. 활성화 키, 조직 ID 및 풀 ID를 사용할 수 있습니다. 이 정보는 https://access.redhat.com에 로그인하여 확인할 수 있습니다.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>OpenShift Container Platform Resource Manager 템플릿을 사용하여 배포

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
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
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
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "rhsmBrokerPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
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
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
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
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

매개 변수를 자신의 특정 정보로 바꿉니다.

릴리스마다 다른 매개 변수를 사용할 수 있으므로, 사용하는 분기에 필요한 매개 변수를 확인합니다.

### <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포

> [!NOTE] 
> 다음 명령은 Azure CLI 2.0.8 이상이 필요합니다. CLI 버전은 `az --version` 명령으로 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)를 참조하세요.

다음 예제에서는 openshiftrg라는 리소스 그룹에 OpenShift 클러스터 및 모든 관련 리소스를 배포하고 배포 이름을 myOpenShiftCluster로 지정합니다. 템플릿은 GitHub 리포지토리에서 직접 참조되며, azuredeploy.parameters.json이라는 로컬 매개 변수 파일이 사용됩니다.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

배포를 완료하려면 배포되는 전체 노드 수 및 구성된 옵션에 따라 30분 이상이 걸립니다. 배포를 마치면 OpenShift 콘솔의 Bastion DNS FQDN 및 URL이 터미널에 출력됩니다.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

배포가 완료될 때까지 명령줄을 대기하지 않으려면 그룹 배포에 대한 옵션 중 하나로 `--no-wait`를 추가합니다. 배포의 출력은 Azure Portal의 리소스 그룹에 대한 배포 섹션에서 검색할 수 있습니다.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>OpenShift Container Platform Azure Marketplace 제품을 사용하여 배포

OpenShift Container Platform을 Azure에 배포하는 가장 간단한 방법은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)을 사용하는 것입니다.

가장 단순한 옵션이긴 하지만 사용자 지정 기능이 제한됩니다. Marketplace 제품에는 다음 구성 옵션이 포함되어 있습니다.

- **마스터 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 마스터 노드입니다.
- **인프라 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 인프라 노드입니다.
- **노드**: 인스턴스 유형뿐 아니라 노드 수도 구성 가능합니다(2개에서 9개 사이).
- **디스크 유형**: 관리 디스크가 사용됩니다.
- **네트워킹**: 사용자 지정 CIDR 범위와 새 네트워크 또는 기존 네트워크가 지원됩니다.
- **CNS**: CNS를 사용하도록 설정할 수 있습니다.
- **메트릭**: 메트릭을 사용하도록 설정할 수 있습니다.
- **로깅**: 로깅을 사용하도록 설정할 수 있습니다.
- **Azure 클라우드 공급자**: 사용하도록 설정할 수 있습니다.

## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. 브라우저에서 `OpenShift Console URL`를 사용하여 OpenShift 콘솔에 연결합니다. 또는 Bastion 호스트에 대해 SSH를 수행할 수 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>문서 기여자

이 문서를 최신 상태로 유지할 수 있도록 도움을 주신 Vincent Power(vincepower) 및 Alfred Sin(asinn826)에게 감사드립니다.