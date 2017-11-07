---
title: "Azure의 OpenShift 필수 조건 | Microsoft Docs"
description: "Azure에서 OpenShift 배포에 대한 필수 조건입니다."
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
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Azure에서 OpenShift에 대한 일반적인 필수 조건

Azure에서 OpenShift를 배포할 때 OpenShift Origin 또는 OpenShift Container Platform 배포 여부에 관계없이 몇 가지 일반적인 필수 조건이 있습니다.

OpenShift 설치는 Ansible 플레이 북을 통해 수행됩니다. Ansible은 SSH를 사용하여 설치 단계를 완료하기 위해 클러스터의 일부가 되는 모든 호스트에 연결합니다.
원격 호스트에 SSH 연결이 시작되면 암호를 입력할 방법이 없습니다. 따라서 개인 키에는 연결된 암호를 사용할 수 없고 그렇지 않은 경우 배포가 실패합니다.
모든 VM은 Resource Manager 템플릿을 통해 배포되므로 동일한 공개 키를 사용하여 모든 VM에 액세스합니다. 마찬가지로 모든 플레이 북을 실행 중인 VM에 해당 개인 키를 삽입해야 합니다.
이 작업을 안전하게 수행하기 위해 Azure Key Vault를 사용하여 VM에 개인 키를 전달합니다.

컨테이너에 대한 영구적 저장소가 필요한 경우에는 PV(영구적 볼륨)가 필요합니다. 이러한 PV는 특정 형태의 영구적 저장소로 백업해야 합니다. OpenShift는 이 기능에 대한 Azure 디스크(VHD)를 지원하지만 먼저 Azure를 클라우드 공급자로 구성해야 합니다. 이 모델에서 OpenShift는 다음을 수행합니다.

- Azure Storage 계정에 VHD 개체를 만듭니다.
- VM에 VHD를 탑재하고 볼륨을 포맷합니다.
- Pod에 볼륨을 탑재합니다.

이 작업을 수행하기 위해 OpenShift는 Azure에서 이전 작업을 수행할 수 있는 권한이 필요합니다. 권한을 얻으려면 서비스 주체가 필요합니다. SP(서비스 주체)는 리소스에 대한 권한이 부여된 Azure Active Directory의 보안 계정입니다.
서비스 주체는 클러스터를 구성하는 VM 및 저장소 계정에 대한 액세스 권한이 있어야 합니다. 모든 OpenShift 클러스터 리소스가 단일 리소스 그룹에 배포된 경우 SP는 해당 리소스 그룹에 대한 권한을 부여받을 수 있습니다.

이 가이드에서는 필수 조건과 연결된 아티팩트를 만드는 방법을 설명합니다.

> [!div class="checklist"]
> * OpenShift 클러스터에 대한 SSH 키를 관리하는 KeyVault를 만듭니다.
> * Azure 클라우드 공급자가 사용할 서비스 주체를 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인 
[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따르거나 **시도**를 클릭하여 Cloud Shell을 사용하세요.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. OpenShift 클러스터 리소스가 배포되는 리소스 그룹과 별도로 Key Vault를 호스트하는 전용 리소스 그룹을 사용하는 것이 좋습니다. 

다음 예제에서는 *eastus* 위치에 *keyvaultrg*이라는 리소스 그룹을 만듭니다.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기
[az keyvault create](/cli/azure/keyvault#create) 명령을 사용하여 클러스터에 대한 SSH 키를 저장할 KeyVault를 만듭니다. Key Vault 이름은 전역적으로 고유해야 합니다.

다음 예제에서는 *keyvaultrg* 리소스 그룹에 *keyvault*라는 keyvault 모음을 만듭니다.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH 키 만들기 
SSH 키는 OpenShift Origin 클러스터에 대한 액세스를 보호하기 위해 필요합니다. `ssh-keygen` 명령(Linux 또는 Mac에서)을 사용하여 SSH 키 쌍을 만듭니다.
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 만드는 SSH 키 쌍에는 암호를 사용할 수 없습니다.

Windows에서의 SSH 키에 대한 자세한 내용은 [Windows에서 SSH 키를 만드는 방법](/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요.

## <a name="store-ssh-private-key-in-key-vault"></a>Key Vault에 SSH 개인 키 저장
OpenShift 배포는 사용자가 만든 SSH 키를 사용하여 OpenShift 마스터에 대한 액세스를 보호합니다. SSH 키를 안전하게 검색하기 위해 배포를 사용하도록 설정하려면 다음 명령을 사용하여 Key Vault에 키를 저장합니다.

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기 
OpenShift는 사용자 이름 및 암호 또는 서비스 주체를 사용하여 Azure와 통신합니다. Azure 서비스 주체는 앱, 서비스 및 OpenShift와 같은 자동화 도구를 사용할 수 있는 보안 ID입니다. 서비스 주체가 Azure에서 수행할 수 있는 작업에 대한 사용 권한은 사용자가 제어하고 정의합니다. 사용자 이름 및 암호를 입력하는 것 이상으로 보안을 강화하기 위해 이 예제에서는 기본 서비스 주체를 만듭니다.

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac)를 사용하여 서비스 주체를 만들고 OpenShift가 필요로 하는 자격 증명을 출력합니다.

다음 예제에서는 서비스 주체를 만들고 myResourceGroup이라는 리소스 그룹에 대한 참가자 권한을 할당합니다. Windows를 사용하는 경우 ```az group show --name myResourceGroup --query id```를 별도로 실행하고 --scopes 옵션을 제공하는 출력을 사용합니다.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

명령에서 반환되는 appId 속성을 기록해 둡니다.
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 안전하지 않은 암호를 만들지 마세요.  [Azure AD 암호 규칙 및 제한 사항](/azure/active-directory/active-directory-passwords-policy) 지침을 따르세요.

서비스 주체에 대한 자세한 내용은 [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서는 다음 항목을 설명합니다.
> [!div class="checklist"]
> * OpenShift 클러스터에 대한 SSH 키를 관리하는 KeyVault를 만듭니다.
> * Azure 클라우드 공급자가 사용할 서비스 주체를 만듭니다.

이제 OpenShift 클러스터 배포로 이동합니다.

- [OpenShift Origin 배포](./openshift-origin.md)
- [OpenShift Container Platform 배포](./openshift-container-platform.md)

