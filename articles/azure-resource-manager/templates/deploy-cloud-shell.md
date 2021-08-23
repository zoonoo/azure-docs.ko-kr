---
title: Cloud Shell을 사용하여 템플릿 배포
description: Azure Resource Manager와 Azure Cloud Shell을 사용하여 Azure에 리소스를 배포합니다. 리소스는 ARM 템플릿(Azure Resource Manager 템플릿)에 정의됩니다.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd597c2cb29120e680493a99aac33f146940bc19
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111957875"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Azure Cloud Shell에서 ARM 템플릿 배포

[Azure Cloud Shell](../../cloud-shell/overview.md)을 사용하여 ARM 템플릿(Azure Resource Manager 템플릿)을 배포할 수 있습니다. 원격으로 저장된 ARM 템플릿 또는 Cloud Shell에 대한 로컬 스토리지 계정에 저장된 ARM 템플릿을 배포할 수 있습니다.

어느 범위에든 배포할 수 있습니다. 이 문서에서는 리소스 그룹에 배포하는 방법을 보여 줍니다.

## <a name="deploy-remote-template"></a>원격 템플릿 배포

외부 템플릿을 배포하려면 모든 외부 배포에서와 정확히 동일한 형식으로 템플릿의 URI를 입력합니다. 외부 템플릿은 GitHub 리포지토리 또는 외부 스토리지 계정에 있을 수 있습니다.

1. Cloud Shell 프롬프트를 엽니다.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell 열기":::

1. 템플릿을 배포하려면 다음 명령을 사용합니다.

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>로컬 템플릿 배포

로컬 템플릿을 배포하려면 먼저 Cloud Shell 세션에 연결된 스토리지 계정에 템플릿을 업로드해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Cloud Shell 리소스 그룹을 선택합니다. 이름 패턴은 `cloud-shell-storage-<region>`입니다.

   ![리소스 그룹 선택](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Cloud Shell용 스토리지 계정을 선택합니다.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="스토리지 계정 선택":::

1. **파일 공유** 를 선택합니다.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="파일 공유 선택":::

1. Cloud Shell용 기본 파일 공유를 선택합니다. 파일 공유의 이름 형식은 `cs-<user>-<domain>-com-<uniqueGuid>`입니다.

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="기본 파일 공유":::

1. 템플릿을 보관할 새 디렉터리를 추가합니다. 해당 디렉터리를 선택합니다.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="디렉터리 추가":::

1. **업로드** 를 선택합니다.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="템플릿 업로드":::

1. 템플릿을 찾아서 업로드합니다.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="템플릿 선택":::

1. Cloud Shell 프롬프트를 엽니다.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell 열기":::

1. **clouddrive** 디렉터리로 이동합니다. 템플릿을 보관하기 위해 추가한 디렉터리로 이동합니다.

1. 템플릿을 배포하려면 다음 명령을 사용합니다.

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

- 배포 명령에 대한 자세한 내용은 [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md) 및 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
- 템플릿을 배포하기 전에 변경 내용을 미리 보려면 [ARM 템플릿 배포 가상 작업](./deploy-what-if.md)을 참조하세요.