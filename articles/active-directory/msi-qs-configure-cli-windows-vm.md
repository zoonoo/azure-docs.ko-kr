---
title: "Azure CLI를 사용하여 Azure VM에서 MSI를 구성하는 방법"
description: "Azure CLI를 사용하여 Azure VM에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: fe276fe802eceb1f062ed8bda685dd44a1e3d175
ms.contentlocale: ko-kr
ms.lasthandoff: 09/26/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Azure CLI를 사용하여 VM MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure CLI를 사용하여 Azure VM에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
- 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.13 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 MSI를 사용하도록 설정

MSI 기반 VM을 만들려면:

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/#login)을 사용하여 먼저 Azure에 로그인합니다. VM을 배포하려는 Azure 구독과 연결된 계정을 사용하세요.

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#create)를 사용하여 VM 및 관련 리소스를 포함하고 배포하는 데 사용할 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vm create](/cli/azure/vm/#create)를 사용하여 VM을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수의 요청에 따라 MSI를 사용하여 *myVM*이라는 VM을 만듭니다. `--admin-username` 및 `--admin-password` 매개 변수는 가상 컴퓨터 로그인을 위한 관리자 이름 및 암호 계정을 지정합니다. 이러한 값은 사용자 환경에 적절하게 업데이트합니다. 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>기존 Azure VM에서 MSI를 사용하도록 설정

기존 가상 컴퓨터에서 MSI를 사용하도록 설정해야 하는 경우 다음을 수행합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/#login)을 사용하여 먼저 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “가상 컴퓨터 참여자”:

   ```azurecli-interactive
   az login
   ```

2. `--assign-identity` 매개 변수가 포함된 [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity)를 사용하여 기존 VM에 MSI를 추가합니다.

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM에서 MSI 제거

MSI가 더 이상 필요하지 않은 가상 컴퓨터가 있는 경우 다음을 수행합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/#login)을 사용하여 먼저 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다. 예, “가상 컴퓨터 참여자”:

   ```azurecli-interactive
   az login
   ```

2. `-n ManagedIdentityExtensionForWindows` 또는 `-n ManagedIdentityExtensionForLinux` 스위치(VM 형식에 따라)를 [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity)와 함께 사용하여 MSI 제거:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>관련 콘텐츠

- [관리 서비스 ID 개요](msi-overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조: 

  - [CLI를 사용하여 Windows 가상 컴퓨터 만들기](../virtual-machines/windows/quick-create-cli.md)  
  - [CLI를 사용하여 Linux 가상 컴퓨터 만들기](../virtual-machines/linux/quick-create-cli.md) 

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

















