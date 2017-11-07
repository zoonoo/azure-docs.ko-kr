---
title: "Azure에 OpenShift Origin 배포 | Microsoft Docs"
description: "Azure에서 OpenShift Origin을 배포합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 1a40c4cc064b32aced7e976f40f6ed6a57e62204
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Azure에서 OpenShift Origin 배포

Azure에서 OpenShift Origin을 배포하는 방법에는 여러 가지가 있습니다. 필요한 모든 Azure 인프라 구성 요소를 수동으로 배포한 다음 OpenShift Origin [설명서](https://docs.openshift.org/3.6/welcome/index.html)를 따릅니다.
또한 OpenShift Origin 클러스터의 배포를 간소화하는 기존 Resource Manager 템플릿을 사용할 수 있습니다. 이러한 템플릿은 [여기](https://github.com/Microsoft/openshift-origin)에 나와 있습니다.

## <a name="deploy-using-the-openshift-origin-template"></a>OpenShift Origin 템플릿을 사용하여 배포

`aadClientId` 매개 변수에 대해 이전에 만든 서비스 주체에서 `appId` 값을 사용합니다.

다음 예제에서는 모든 필수 입력이 포함된 **azuredeploy.parameters.json**이라는 매개 변수 파일을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
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
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포


> [!NOTE] 
> 다음 명령은 Azure CLI 2.0.8 이상이 필요합니다. `az --version` 명령으로 az CLI 버전을 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

다음 예제에서는 myResourceGroup이라는 리소스 그룹에 배포 이름 myOpenShiftCluster를 사용하여 OpenShift 클러스터 및 모든 관련 리소스를 배포합니다. Github 리포지토리에서 직접 템플릿을 참조하고 이름이 **azuredeploy.parameters.json**인 로컬 매개 변수 파일이 사용됩니다.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

배포를 완료하려면 배포된 전체 노드 수에 따라 25분 이상이 걸립니다. 배포가 완료되면 OpenShift 콘솔의 URL과 OpenShift 마스터의 DNS 이름이 터미널에 출력됩니다.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 브라우저에서 `OpenShift Console Uri`를 사용하여 OpenShift 콘솔에 연결합니다. 또는 다음 명령을 사용하여 OpenShift 마스터에 연결할 수 있습니다.

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-post-deployment.md)
- [OpenShift 배포 문제 해결](./openshift-troubleshooting.md)
- [OpenShift Origin 시작](https://docs.openshift.org/latest/getting_started/index.html)
