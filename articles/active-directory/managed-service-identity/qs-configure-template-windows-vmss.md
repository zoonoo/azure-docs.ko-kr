---
title: "템플릿을 사용하여 Azure 가상 머신 확장 집합에서 MSI 구성"
description: "Azure Resource Manager 템플릿을 사용하여 Azure VMSS에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침입니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>템플릿을 사용하여 VM 관리 서비스 ID 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

MSI(관리 서비스 ID)는 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Resource Manager 배포 템플릿을 사용하여 Azure 가상 머신 확장 집합에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합 생성 시 또는 기존 Azure 가상 머신 확장 집합에서 MSI 사용 설정

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, Azure Resource Manager 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스 또는 수정된 리소스를 배포하는 기능을 제공합니다. 다음을 비롯한 로컬 및 포털 기반 템플릿 편집 및 배포에 여러 가지 옵션이 제공됩니다.

   - [Azure Marketplace의 사용자 지정 템플릿](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)을 사용하여 템플릿을 처음부터 만들거나 기존의 공통 템플릿 또는 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 기반으로 템플릿을 만듭니다.
   - [원본 배포](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) 또는 [배포의 현재 상태](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
   - 로컬 [JSON 편집기(예: VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)를 사용하는 경우 PowerShell 또는 CLI를 사용하여 템플릿을 업로드하고 배포합니다.
   - Visual Studio [Azure 리소스 그룹 프로젝트](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 사용하여 템플릿을 만들고 배포합니다.  

선택한 옵션에 관계 없이 초기 배포 및 재배포 시 템플릿 구문은 동일합니다. 기존 또는 새 가상 머신 확장 집합에서 동일한 방식으로 MSI를 사용하도록 설정합니다. 또한 기본적으로 Azure Resource Manager는 배포에 대해 [증분 업데이트](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)를 수행합니다.

1. Azure에 로컬로 로그인하든지, 아니면 Azure Portal을 통해 로그인하든지 간에 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

2. 템플릿을 편집기에 로드한 후 `resources` 섹션 내에서 원하는 `Microsoft.Compute/virtualMachineScaleSets` 리소스를 찾습니다. 사용 중인 편집기와 템플릿을 편집 중인 배포(새 배포 또는 기존 배포)에 따라 실제 리소스는 다음 스크린샷과 약간 다를 수 있습니다.
   
   ![템플릿의 스크린샷 - VM 찾기](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. `"type": "Microsoft.Compute/virtualMachineScaleSets"` 속성과 동일한 수준에서 `"identity"` 속성을 추가합니다. 다음 구문을 사용합니다.

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. 그런 다음, 가상 머신 확장 집합 MSI 확장을 `extensionsProfile` 요소로 추가합니다. 다음 구문을 사용합니다.

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

5. 작업을 완료하면 템플릿은 다음과 같이 표시됩니다.

   ![업데이트 이후 템플릿의 스크린샷](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 MSI 제거

MSI가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. Azure에 로컬로 로그인하든지, 아니면 Azure Portal을 통해 로그인하든지 간에 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

2. 이전 섹션에서 추가된 두 요소, 즉 가상 머신 확장 집합의 `"identity"`및 `"extensionsProfile"` 속성을 제거합니다.

## <a name="next-steps"></a>다음 단계

- MSI에 대한 광범위한 관점은 [관리 서비스 ID 개요](overview.md)를 참조하세요.

