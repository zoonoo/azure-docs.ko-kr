---
title: 템플릿을 사용하여 Azure 가상 머신 확장 집합에서 관리 서비스 ID 구성
description: Azure Resource Manager 템플릿을 사용하여 Azure VMSS에서 관리 서비스 ID를 구성하기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 562bf5e5239114a8dad16727089f94f378db82ff
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258867"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>템플릿을 사용하여 가상 머신 확장 집합에서 관리 서비스 ID 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 아티클에서는 Azure Resource Manager 배포 템플릿을 사용하여 Azure 가상 머신 확장 집합에서 다음과 같은 관리 서비스 ID 작업을 수행하는 방법을 알아봅니다.
- Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하거나 사용하지 않도록 설정
- Azure 가상 머신 확장 집합에서 사용자 할당 ID 추가 및 제거

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 아티클의 관리 작업을 수행하려면 계정에 다음과 같은 역할이 할당되어야 합니다.
    - [Virtual Machine 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): 가상 머신 확장 집합을 만들고, 가상 머신 확장 집합에서 시스템 및/또는 사용자가 할당한 관리 ID를 사용하도록 설정하고 제거합니다.
    - [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할: 사용자 할당 ID를 만듭니다.
    - [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할: 가상 머신 확장 집합으로 사용자 할당 ID를 할당하거나 이 집합에서 사용자 할당 ID를 제거합니다.

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스 또는 수정된 리소스를 배포하는 기능을 제공합니다. 다음을 비롯한 로컬 및 포털 기반 템플릿 편집 및 배포에 여러 가지 옵션이 제공됩니다.

   - [Azure Marketplace의 사용자 지정 템플릿](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)을 사용하여 템플릿을 처음부터 만들거나 기존의 공통 템플릿 또는 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 기반으로 템플릿을 만듭니다.
   - [원본 배포](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) 또는 [배포의 현재 상태](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
   - 로컬 [JSON 편집기(예: VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)를 사용하는 경우 PowerShell 또는 CLI를 사용하여 템플릿을 업로드하고 배포합니다.
   - Visual Studio [Azure 리소스 그룹 프로젝트](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 사용하여 템플릿을 만들고 배포합니다.  

선택한 옵션에 관계 없이 초기 배포 및 재배포 시 템플릿 구문은 동일합니다. 새 VM이나 기존 VM에서 관리 서비스 ID를 사용하도록 설정하는 작업은 동일한 방식으로 수행됩니다. 또한 기본적으로 Azure Resource Manager는 배포에 대해 [증분 업데이트](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)를 수행합니다.

## <a name="system-assigned-identity"></a>시스템 할당 ID

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 시스템 할당 ID를 사용하거나 사용하지 않도록 설정합니다.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-or-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합을 만드는 동안 시스템 할당 ID를 사용하도록 설정

1. 템플릿을 편집기에 로드하고 `resources` 섹션 내에서 원하는 `Microsoft.Compute/virtualMachineScaleSets` 리소스를 찾습니다. 사용 중인 편집기와 템플릿을 편집 중인 배포(새 배포 또는 기존 배포)에 따라 실제 리소스는 다음 스크린샷과 약간 다를 수 있습니다.
   
   ![템플릿의 스크린샷 - VM 찾기](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. 시스템 할당 ID를 사용하도록 설정하려면 동일한 수준의 `"identity"` 속성을 `"type": "Microsoft.Compute/virtualMachineScaleSets"` 속성으로 추가합니다. 다음 구문을 사용합니다.

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (선택 사항) 가상 머신 확장 집합 관리 서비스 ID 확장을 `extensionsProfile` 요소로 추가합니다. 이 단계는 Azure IMDS(Instance Metadata Service) ID를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다.  다음 구문을 사용합니다.

   >[!NOTE] 
   > 다음 예제에서는 Windows 가상 머신 확장 집합 확장(`ManagedIdentityExtensionForWindows`)이 배포되고 있다고 가정합니다. 또한 `"name"` 및 `"type"` 요소에 `ManagedIdentityExtensionForLinux`를 대신 사용하여 Linux에 대해 구성할 수도 있습니다.
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
   ```

4. 작업을 완료하면 템플릿은 다음과 같이 표시됩니다.

   ![업데이트 이후 템플릿의 스크린샷](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하지 않도록 설정

관리 서비스 ID가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. Azure에 로컬로 로그인하든지, 아니면 Azure Portal을 통해 로그인하든지 간에 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

2. 템플릿을 [편집기](#azure-resource-manager-templates)에 로드하고 `resources` 섹션 내에서 관심 있는 `Microsoft.Compute/virtualMachineScaleSets` 리소스를 찾습니다. 가상 머신 확장 집합에 시스템 할당 ID만 있는 경우 ID 형식을 `None`으로 변경하여 VM을 사용하지 않도록 설정할 수 있습니다.  가상 머신 확장 집합에 시스템 할당 ID와 사용자 할당 ID가 둘 다 있는 경우 ID 형식에서 `SystemAssigned`를 제거하고 사용자 할당 ID의 `identityIds` 배열과 함께 `UserAssigned`를 유지합니다.  다음 예제에서는 사용자 할당 ID가 없는 가상 머신 확장 집합에서 시스템 할당 ID를 제거하는 방법을 보여줍니다.
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-identity"></a>사용자 할당 ID

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 Azure VMSS에 사용자 할당 ID를 할당합니다.

> [!Note]
> Azure Resource Manager 템플릿을 사용하여 사용자 할당 ID를 만들려면 [사용자 할당 ID 만들기](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)를 참조하세요.

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Azure VMSS에 사용자 할당 ID 할당

1. `resources` 요소 아래에 다음 항목을 추가하여 사용자 할당 ID를 VMSS에 할당합니다.  `<USERASSIGNEDIDENTITY>`를 직접 만든 사용자 할당 ID의 이름으로 바꿔야 합니다.

   > [!Important]
   > 다음 예제에 표시된 `<USERASSIGNEDIDENTITYNAME>` 값은 변수에 저장되어야 합니다.  또한 Resource Manager 템플릿에서 사용자 할당 ID를 가상 머신에 할당하는 현재 지원되는 구현의 경우, API 버전이 다음 예제의 버전과 일치해야 합니다. 

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
            ]
        }

    }
    ```

2. (선택 사항) `extensionProfile` 요소 아래에 다음 항목을 추가하여 관리 ID 확장을 VMSS에 추가합니다. 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다. 다음 구문을 사용합니다.
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
                    "properties": {
                        "publisher": "Microsoft.ManagedIdentity",
                        "type": "ManagedIdentityExtensionForWindows",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "port": 50342
                        },
                        "protectedSettings": {}
                    }
                }
    ```

3.  완료되면 템플릿은 다음과 같이 표시됩니다.
   
      ![사용자 할당 ID의 스크린샷](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 사용자 할당 ID 제거

관리 서비스 ID가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. Azure에 로컬로 로그인하든지, 아니면 Azure Portal을 통해 로그인하든지 간에 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

2. 템플릿을 [편집기](#azure-resource-manager-templates)에 로드하고 `resources` 섹션 내에서 관심 있는 `Microsoft.Compute/virtualMachineScaleSets` 리소스를 찾습니다. 가상 머신 확장 집합에 사용자 할당 ID만 있는 경우 ID 형식을 `None`으로 변경하여 VM을 사용하지 않도록 설정할 수 있습니다.  가상 머신 확장 집합에 시스템 할당 ID와 사용자 할당 ID가 둘 다 있고 시스템 할당 ID를 유지하려는 경우 사용자 할당 ID의 `identityIds` 배열과 함께 ID 형식에서 `UserAssigned`를 제거합니다.
    
   가상 머신 확장 집합에서 단일 사용자 할당 ID를 제거하려면 `identityIds` 배열에서 제거합니다.
   
   다음 예제에서는 시스템 할당 ID가 없는 가상 머신 확장 집합에서 모든 사용자 할당 ID를 제거하는 방법을 보여줍니다.
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="next-steps"></a>다음 단계

- 관리 서비스 ID에 대한 광범위한 관점은 [관리 서비스 ID 개요](overview.md)를 참조하세요.

