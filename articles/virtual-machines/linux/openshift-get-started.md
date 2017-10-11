---
title: "Azure에 OpenShift Origin 배포 | Microsoft Docs"
description: "Azure 가상 컴퓨터에 OpenShift Origin을 배포하는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>Azure 가상 컴퓨터에 OpenShift Origin 배포 

[OpenShift Origin](https://www.openshift.org/)은 [Kubernetes](https://kubernetes.io/)를 기반으로 하는 오픈 소스 컨테이너 플랫폼입니다. 다중 테넌트 응용 프로그램의 배포, 크기 조정 및 운영 프로세스를 간소화합니다. 

이 가이드에서는 Azure CLI 및 Azure Resource Manager 템플릿을 사용하여 OpenShift Origin을 Azure Virtual Machines에 배포하는 방법을 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * OpenShift 클러스터에 대한 SSH 키를 관리하는 KeyVault를 만듭니다.
> * Azure VM에 OpenShift 클러스터를 배포합니다. 
> * [OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index)를 설치 및 구성하여 클러스터를 관리합니다.
> * OpenShift 배포를 사용자 지정합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 빠른 시작을 위해서는 Azure CLI 버전 2.0.8 이상이 필요합니다. 버전을 찾으려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Azure에 로그인 
[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따르거나 **시도**를 클릭하여 Cloud Shell을 사용하세요.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기
[az keyvault create](/cli/azure/keyvault#create) 명령을 사용하여 클러스터에 대한 SSH 키를 저장할 KeyVault를 만듭니다.  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH 키 만들기 
SSH 키는 OpenShift Origin 클러스터에 대한 액세스를 보호하기 위해 필요합니다. `ssh-keygen` 명령을 사용하여 SSH 키 쌍을 만듭니다. 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 만드는 SSH 키 쌍에는 암호를 사용할 수 없습니다.

Windows에서의 SSH 키에 대한 자세한 내용은 [Windows에서 SSH 키를 만드는 방법](/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요.

## <a name="store-ssh-private-key-in-key-vault"></a>Key Vault에 SSH 개인 키 저장
OpenShift 배포는 사용자가 만든 SSH 키를 사용하여 OpenShift 마스터에 대한 액세스를 보호합니다. SSH 키를 안전하게 검색하기 위해 배포를 사용하도록 설정하려면 다음 명령을 사용하여 Key Vault에 키를 저장합니다.

# <a name="enabled-for-template-deployment"></a>템플릿 배포를 위한 사용
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기 
OpenShift는 사용자 이름 및 암호 또는 서비스 주체를 사용하여 Azure와 통신합니다. Azure 서비스 주체는 앱, 서비스 및 OpenShift와 같은 자동화 도구를 사용할 수 있는 보안 ID입니다. 서비스 주체가 Azure에서 수행할 수 있는 작업에 대한 사용 권한은 사용자가 제어하고 정의합니다. 사용자 이름 및 암호를 입력하는 것 이상으로 보안을 강화하기 위해 이 예제에서는 기본 서비스 주체를 만듭니다.

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac)를 사용하여 서비스 주체를 만들고 OpenShift가 필요로 하는 자격 증명을 출력합니다.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
명령에서 반환되는 appId 속성을 기록해 둡니다.
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 안전하지 않은 암호를 만들지 마세요.  [Azure AD 암호 규칙 및 제한 사항](/azure/active-directory/active-directory-passwords-policy) 지침을 따르세요.

서비스 주체에 대한 자세한 내용은 [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

## <a name="deploy-the-openshift-origin-template"></a>OpenShift Origin 템플릿 배포
다음으로 Azure Resource Manager 템플릿을 사용하여 OpenShift Origin을 배포합니다. 

> [!NOTE] 
> 다음 명령은 az CLI 2.0.8 이상이 필요합니다. `az --version` 명령으로 az CLI 버전을 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

`aadClientId` 매개 변수에 대해 이전에 만든 서비스 주체에서 `appId` 값을 사용합니다.

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
배포가 완료되는 데 최대 20분이 걸릴 수 있습니다. 배포가 완료되면 OpenShift 콘솔의 URL과 OpenShift 마스터의 DNS 이름이 터미널에 출력됩니다.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결
배포가 완료되면 브라우저에서 `OpenShift Console Uri`를 사용하여 OpenShift 콘솔에 연결합니다. 또는 다음 명령을 사용하여 OpenShift 마스터에 연결할 수 있습니다.

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.
> [!div class="checklist"]
> * OpenShift 클러스터에 대한 SSH 키를 관리하는 KeyVault를 만듭니다.
> * Azure VM에 OpenShift 클러스터를 배포합니다. 
> * [OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index)를 설치 및 구성하여 클러스터를 관리합니다.

이제 해당 OpenShift Origin 클러스터가 배포되었습니다. OpenShift 자습서를 수행하여 첫 번째 응용 프로그램을 배포하고 OpenShift 도구를 사용하는 방법에 대해 알아볼 수 있습니다. 시작하려면 [OpenShift Origin 시작](https://docs.openshift.org/latest/getting_started/index.html)을 참조하세요. 
