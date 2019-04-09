---
title: 템플릿을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성
description: Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID를 구성하는 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ecbac8af86c3c2c76b7710eb61f71481b86291b
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009872"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>템플릿을 사용 하는 Azure 가상 머신 확장에서 Azure 리소스에 대 한 관리 되는 id를 구성 합니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Resource Manager 배포 템플릿을 사용하여 Azure 가상 머신 확장 집합에서 Azure 리소스에 대한 다음과 같은 관리 ID 작업을 수행하는 방법을 알아봅니다.
- Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하거나 사용하지 않도록 설정
- Azure 가상 머신 확장 집합에서 사용자 할당 관리 ID 추가 및 제거

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 Azure 역할 기반 액세스 제어가 할당되어야 합니다.

    > [!NOTE]
    > 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

    - [가상 머신 참가자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): 가상 머신 확장 집합을 만들고, 가상 머신 확장 집합에서 시스템 및/또는 사용자가 할당한 관리 ID를 사용하도록 설정하고 제거합니다.
    - [관리 ID 참가자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할: 사용자 할당 관리 ID를 만듭니다.
    - [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할: 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하거나 이 집합에서 사용자 할당 관리 ID를 제거합니다.

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스 또는 수정된 리소스를 배포하는 기능을 제공합니다. 다음을 비롯한 로컬 및 포털 기반 템플릿 편집 및 배포에 여러 가지 옵션이 제공됩니다.

   - 사용 하는 [Azure Marketplace에서 사용자 지정 템플릿을](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), 템플릿을 처음부터 만들거나 기존의 공통 기반으로 할 수 있는 또는 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)합니다.
   - [원본 배포](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) 또는 [배포의 현재 상태](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
   - 로컬 [JSON 편집기(예: VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)를 사용하는 경우 PowerShell 또는 CLI를 사용하여 템플릿을 업로드하고 배포합니다.
   - Visual Studio [Azure 리소스 그룹 프로젝트](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 사용하여 템플릿을 만들고 배포합니다.  

선택한 옵션에 관계 없이 초기 배포 및 재배포 시 템플릿 구문은 동일합니다. 새 VM이나 기존 VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 작업은 동일한 방식으로 수행됩니다. 또한 기본적으로 Azure Resource Manager는 배포에 대해 [증분 업데이트](../../azure-resource-manager/deployment-modes.md)를 수행합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 시스템 할당 관리 ID를 사용하거나 사용하지 않도록 설정합니다.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>시스템이 할당 하는 사용 하도록 설정 관리 id를 만드는 동안 가상 머신 확장 집합을 만들거나 기존 가상 머신 확장 집합의

1. Azure에 로컬로 로그인하든지, 아니면 Azure Portal을 통해 로그인하든지 간에 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.
2. 시스템 할당 관리 ID를 사용하도록 설정하려면 편집기에 템플릿을 로드하고 리소스 섹션 내에서 관심이 있는 `Microsoft.Compute/virtualMachinesScaleSets` 리소스를 찾아서 `"type": "Microsoft.Compute/virtualMachinesScaleSets"` 속성과 같은 수준으로 `identity` 속성을 추가합니다. 다음 구문을 사용합니다.

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Azure 리소스 가상 머신 확장 집합에서 지정 하 여 확장에 대 한 관리 되는 id를 프로 비전 필요에 따라 수는 `extensionProfile` 템플릿의 요소입니다. 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다.  자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS로 마이그레이션](howto-migrate-vm-extension.md)합니다.


4. 완료되면 다음과 같은 모양으로 템플릿의 리소스 섹션에 다음 섹션을 추가해야 합니다.

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ``` 

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하지 않도록 설정

시스템 할당 관리 ID가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. Azure에 로컬로 로그인하든지, 아니면 Azure Portal을 통해 로그인하든지 간에 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

2. 템플릿을 [편집기](#azure-resource-manager-templates)에 로드하고 `resources` 섹션 내에서 관심 있는 `Microsoft.Compute/virtualMachineScaleSets` 리소스를 찾습니다. VM에 시스템 할당 관리 ID만 있는 경우, ID 형식을 `None`으로 변경하여 VM을 사용하지 않도록 설정할 수 있습니다.

   **Microsoft.compute/virtualmachinescalesets API-VERSION 2018-06-01**

   apiVersion이 `2018-06-01`이고 VM에 시스템 할당 ID와 사용자 할당 관리 ID가 둘 다 있는 경우, ID 유형에서 `SystemAssigned`를 제거하고 userAssignedIdentities 사전 값과 함께 `UserAssigned`를 유지합니다.

   **Microsoft.compute/virtualmachinescalesets API-VERSION 2018-06-01**

   apiVersion이 `2017-12-01`이고 가상 머신 확장 집합에 시스템 할당 관리 ID와 사용자 할당 ID가 둘 다 있는 경우 ID 형식에서 `SystemAssigned`를 제거하고 사용자 할당 관리 ID의 `identityIds` 배열과 함께 `UserAssigned`를 유지합니다. 
   
    

   다음 예제에서는 사용자 할당 관리 ID가 없는 가상 머신 확장 집합에서 시스템 할당 관리 ID를 제거하는 방법을 보여줍니다.
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당합니다.

> [!Note]
> Azure Resource Manager 템플릿을 사용하여 사용자 할당 관리 ID를 만들려면 [사용자 할당 관리 ID 만들기](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)를 참조하세요.

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에 사용자 할당 관리 ID 할당

1. `resources` 요소 아래에 다음 항목을 추가하여 사용자 할당 관리 ID를 가상 머신 확장 집합에 할당합니다.  `<USERASSIGNEDIDENTITY>`를 직접 만든 사용자 할당 관리 ID의 이름으로 바꿔야 합니다.
   
   **Microsoft.compute/virtualmachinescalesets API-VERSION 2018-06-01**

   apiVersion이 `2018-06-01`이고 사용자 할당 관리 ID가 `userAssignedIdentities` 사전 형식으로 저장되는 경우에는 템플릿의 `variables` 섹션에 정의된 변수에 `<USERASSIGNEDIDENTITYNAME>` 값이 저장되어야 합니다.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }
    
   }
   ```   

   **Microsoft.compute/virtualmachinescalesets API 버전 2017-12-01**
    
   `apiVersion`이 `2017-12-01` 또는 이전 버전이고 사용자 할당 관리 ID가 `identityIds` 배열에 저장되는 경우에는 템플릿의 변수 섹션에 정의된 변수에 `<USERASSIGNEDIDENTITYNAME>` 값이 저장되어야 합니다.

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
> [!NOTE]
> Azure 리소스 가상 머신 확장 집합에서 지정 하 여 확장에 대 한 관리 되는 id를 프로 비전 필요에 따라 수는 `extensionProfile` 템플릿의 요소입니다. 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다.  자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS로 마이그레이션](howto-migrate-vm-extension.md)합니다.

3. 완료되면 템플릿은 다음과 같이 표시됩니다.
   
   **Microsoft.compute/virtualmachinescalesets API-VERSION 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.compute/virtualmachines API 버전 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 사용자 할당 관리 ID 제거

사용자 할당 관리 ID가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. Azure에 로컬로 로그인하든지, 아니면 Azure Portal을 통해 로그인하든지 간에 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

2. 템플릿을 [편집기](#azure-resource-manager-templates)에 로드하고 `resources` 섹션 내에서 관심 있는 `Microsoft.Compute/virtualMachineScaleSets` 리소스를 찾습니다. 가상 머신 확장 집합에 사용자 할당 관리 ID만 있는 경우 ID 형식을 `None`으로 변경하여 VM을 사용하지 않도록 설정할 수 있습니다.

   다음 예제에서는 시스템 할당 관리 ID가 없는 VM에서 모든 사용자 할당 관리 ID를 제거하는 방법을 보여줍니다.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```
   
   **Microsoft.compute/virtualmachinescalesets API-VERSION 2018-06-01**
    
   가상 머신 확장 집합에서 단일 사용자 할당 관리 ID를 제거하려면 `userAssignedIdentities` 사전에서 제거합니다.

   시스템 할당 ID가 있는 경우에는 `identity` 값 아래 `type` 값에 보관합니다.

   **Microsoft.compute/virtualmachinescalesets API 버전 2017-12-01**

   가상 머신 확장 집합에서 단일 사용자 할당 관리 ID를 제거하려면 `identityIds` 배열에서 제거합니다.

   시스템 할당 관리 ID가 있는 경우에는 `identity` 값 아래 `type` 값에 보관합니다.
   
## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)

