---
title: Azure CLI를 사용하여 Azure 가상 머신 확장 집합에서 MSI 구성
description: Azure CLI를 사용하여 Azure 가상 머신 확장 집합에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 9cdf5225f2d87fffa2290e3edd09d4ae829aee21
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Azure CLI를 사용하여 가상 머신 확장 집합 MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure CLI를 사용하여 Azure 가상 머신 확장 집합에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
- 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.13 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합 생성 시 MSI 사용 설정

MSI 기반 가상 머신 확장 집합을 만들려면 다음을 수행합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az_login)을 사용하여 먼저 Azure에 로그인합니다. 가상 머신 확장 집합을 배포하려는 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az_group_create)를 사용하여 가상 머신 확장 집합 및 관련 리소스를 포함하고 배포하기 위한 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vmss create](/cli/azure/vmss/#az_vmss_create)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수에서 요청한 대로 MSI를 사용하여 *myVMSS*라는 가상 머신 확장 집합을 만듭니다. `--admin-username` 및 `--admin-password` 매개 변수는 가상 머신 로그인을 위한 관리자 이름 및 암호 계정을 지정합니다. 이러한 값은 사용자 환경에 적절하게 업데이트합니다. 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에서 MSI 사용 설정

기존 Azure 가상 머신 확장 집합에서 MSI를 사용하도록 설정해야 하는 경우 다음을 수행합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az_login)을 사용하여 먼저 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az vmss identity assign](/cli/azure/vmss/identity/#az_vmss_identity_assign) 명령을 사용하여 기존 VM에 MSI를 추가합니다.

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 MSI 제거

MSI가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az_login)을 사용하여 먼저 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az vmss identity remove](/cli/azure/vmss/identity/#az_vmss_remove_identity) 명령을 사용하여 MSI를 제거합니다.

   ```azurecli-interactive
   az vmss identity remove -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>다음 단계

- [관리 서비스 ID 개요](overview.md)
- Azure 가상 머신 확장 집합 만들기 빠른 시작은 다음을 참조하세요. 

  - [CLI를 사용하여 가상 머신 확장 집합 만들기](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
















