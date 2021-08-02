---
title: Cloud Shell을 사용하여 Bicep 파일 배포
description: Azure Resource Manager와 Azure Cloud Shell을 사용하여 Azure에 리소스를 배포합니다. 리소스는 Bicep 파일에 정의되어 있습니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: fa74250a290cc3e254e5c9f3757bb3e712c020da
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026847"
---
# <a name="deploy-bicep-files-from-azure-cloud-shell"></a>Azure Cloud Shell에서 Bicep 파일 배포

[Azure Cloud Shell](../../cloud-shell/overview.md)을 사용하여 Bicep 파일을 배포할 수 있습니다. 현재는 Cloud Shell에서 로컬 Bicep 파일만 배포할 수 있습니다.

어느 범위에든 배포할 수 있습니다. 이 문서에서는 리소스 그룹에 배포하는 방법을 보여줍니다.

## <a name="deploy-local-bicep-file"></a>로컬 Bicep 파일 배포

로컬 Bicep 파일을 배포하려면 먼저 Bicep 파일을 Cloud Shell 세션에 업로드해야 합니다.

1. [Cloud Shell](https://shell.azure.com)에 로그인합니다.
1. **PowerShell** 또는 **Bash** 를 선택합니다.

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-bash-powershell.png" alt-text="Bash 또는 PowerShell 선택":::

1. **파일 업로드/다운로드** 를 선택한 다음, **업로드** 를 선택합니다.

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-upload.png" alt-text="파일 업로드":::.

1. 업로드할 Bicep 파일을 선택한 다음, **열기** 를 선택합니다.
1. Bicep 파일을 배포하려면 다음 명령을 사용합니다.

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.bicep \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.bicep `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>다음 단계

- 배포 명령에 대한 자세한 내용은 [Bicep 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md) 및 [Bicep 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
- Bicep 파일을 배포하기 전에 변경 내용을 미리 보려면 [Bicep 배포 가상 작업](./deploy-what-if.md)을 참조하세요.
