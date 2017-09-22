---
title: "템플릿을 사용하여 Azure VM에서 MSI를 구성하는 방법"
description: "Azure Resource Manager 템플릿을 사용하여 Azure VM에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다."
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
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 266458323ca54d9805aea12108faed79e69d30b0
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-a-template"></a>템플릿을 사용하여 VM MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Resource Manager 배포 템플릿을 사용하여 Azure Windows VM에 대해 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Azure VM 생성 중에/기존 VM에 대해 MSI를 사용하도록 설정

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, Azure Resource Manager 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스/수정된 리소스를 배포하는 기능을 제공합니다. 로컬 방식과 포털/웹 기반 방식을 모두 포함하는 템플릿 편집과 배포를 위한 여러 가지 옵션이 제공됩니다. 그중 몇 가지 옵션은 다음과 같습니다.

   - [Azure Marketplace의 사용자 지정 템플릿을](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)을 사용하여 템플릿을 새로 만들거나, 기존의 일반 템플릿 또는 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 기반으로 템플릿을 만듭니다.
   - [원본 배포](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) 또는 [배포의 현재 상태](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
   - VS Code와 같은 로컬 [JSON 편집기](../azure-resource-manager/resource-manager-create-first-template.md)를 사용하여 템플릿을 편집한 다음 PowerShell 또는 CLI를 사용하여 업로드/배포합니다.
   - Visual Studio의 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 사용하여 템플릿을 만들고 배포합니다.  

어떤 경로를 사용하든 초기 배포와 재배포 중에 템플릿 구문이 동일하므로 새 VM이나 기존 VM에서 동일한 방식으로 MSI를 사용하도록 설정할 수 있습니다. 또한 기본적으로 Azure Resource Manager는 배포에 대해 [증분 업데이트](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)를 수행합니다.

1. 템플릿을 편집기에 로드한 후 `resources` 섹션 내에서 원하는 `Microsoft.Compute/virtualMachines` 리소스를 찾습니다. 사용 중인 편집기와 템플릿을 편집 중인 배포(새 배포 또는 기존 배포)에 따라 실제 리소스는 이 스크린샷과 약간 다를 수 있습니다.

   >[!NOTE] 
   > 또한 2단계에서는 템플릿에 `vmName`, `storageAccountName`, `nicName` 변수가 정의되어 있다고 가정합니다.
   >

   ![템플릿 편집 이전 스크린샷 - VM 찾기](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

2. 다음 구문을 사용하여 `"type": "Microsoft.Compute/virtualMachines"` 속성과 같은 수준에 `"identity"` 속성을 추가합니다.

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. 그런 후에 다음 구문을 사용하여 VM MSI 확장을 `resources` 요소로 추가합니다.

   >[!NOTE] 
   > 다음 예제에서는 Windows VM 확장(`ManagedIdentityExtensionForWindows`)을 배포한다고 가정합니다. 대신 `ManagedIdentityExtensionForLinux`를 사용하여 Linux용 구문을 구성할 수도 있습니다.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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

4. 작업을 완료하면 템플릿은 다음 예제와 같이 표시됩니다.

   ![템플릿 편집 이후 스크린샷](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM에서 MSI 제거

MSI가 더 이상 필요하지 않은 가상 컴퓨터가 있는 경우 이전 예제에서 추가한 두 요소, 즉 VM의 `"identity"` 속성과 `"Microsoft.Compute/virtualMachines/extensions"` 리소스를 제거합니다.

## <a name="related-content"></a>관련 콘텐츠

- [관리 서비스 ID 개요](msi-overview.md)

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.


