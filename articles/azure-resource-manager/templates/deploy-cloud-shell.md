---
title: Cloud Shell를 사용 하 여 템플릿 배포
description: Azure Resource Manager 및 Cloud Shell를 사용 하 여 Azure에 리소스를 배포 합니다. 리소스는 Azure Resource Manager 템플릿에 정의 됩니다.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d0b519955cb877f5fb9640cc0cf95a898e1743ef
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681506"
---
# <a name="deploy-arm-templates-from-cloud-shell"></a>Cloud Shell에서 ARM 템플릿 배포

[Cloud Shell](../../cloud-shell/overview.md) 를 사용 하 여 Azure Resource Manager 템플릿 (ARM 템플릿)을 배포할 수 있습니다. 원격으로 저장 된 ARM 템플릿이나 Cloud Shell의 로컬 저장소 계정에 저장 된 ARM 템플릿을 배포할 수 있습니다.

모든 범위에를 배포할 수 있습니다. 이 문서에서는 리소스 그룹에 배포 하는 방법을 보여 줍니다.

## <a name="deploy-remote-template"></a>원격 템플릿 배포

외부 템플릿을 배포하려면 모든 외부 배포에서와 정확히 동일한 형식으로 템플릿의 URI를 입력합니다. 외부 템플릿은 GitHub 리포지토리 또는 외부 저장소 계정에 있을 수 있습니다.

1. Cloud Shell 프롬프트를 엽니다.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell 열기":::

1. 템플릿을 배포 하려면 다음 명령을 사용 합니다.

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>로컬 템플릿 배포

로컬 템플릿을 배포 하려면 먼저 Cloud Shell 세션에 연결 된 저장소 계정에 템플릿을 업로드 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Cloud Shell 리소스 그룹을 선택합니다. 이름 패턴은 `cloud-shell-storage-<region>`입니다.

   ![리소스 그룹 선택](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Cloud Shell용 스토리지 계정을 선택합니다.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Cloud Shell 열기":::

1. **파일 공유** 를 선택 합니다.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Cloud Shell 열기":::

1. Cloud Shell에 대 한 기본 파일 공유를 선택 합니다. 파일 공유의 이름 형식은 `cs-<user>-<domain>-com-<uniqueGuid>` 입니다.

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Cloud Shell 열기":::

1. 템플릿을 보관할 새 디렉터리를 추가 합니다. 해당 디렉터리를 선택 합니다.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Cloud Shell 열기":::

1. **업로드** 를 선택합니다.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Cloud Shell 열기":::

1. 템플릿을 찾아서 업로드합니다.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Cloud Shell 열기":::

1. Cloud Shell 프롬프트를 엽니다.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell 열기":::

1. **Clouddrive** 디렉터리로 이동 합니다. 템플릿을 보관 하기 위해 추가한 디렉터리로 이동 합니다.

1. 템플릿을 배포 하려면 다음 명령을 사용 합니다.

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>다음 단계

- 배포 명령에 대 한 자세한 내용은 [arm 템플릿을 사용 하 여 리소스 배포 및 Azure CLI](deploy-cli.md) 하 고 [arm 템플릿과 Azure PowerShell를 사용 하 여 리소스 배포](deploy-powershell.md)를 참조 하세요.
- 템플릿을 배포 하기 전에 변경 내용을 미리 보려면 [ARM 템플릿 배포 가상 작업](template-deploy-what-if.md)을 참조 하세요.
