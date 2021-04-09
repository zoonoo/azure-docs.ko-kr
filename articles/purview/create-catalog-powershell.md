---
title: '빠른 시작: Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정 만들기(미리 보기)'
description: 이 빠른 시작에서는 Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정을 만드는 방법을 설명합니다.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 0698295688a4587a704e8cdba0a4796e8d1e6fcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880002"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>빠른 시작: Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정 만들기

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기로 제공됩니다. [Microsoft Azure Preview용 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.

이 빠른 시작에서는 Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Azure에 로그인하는 데 사용하는 사용자 계정은 기여자 또는 소유자 역할의 구성원이거나, Azure 구독의 관리자여야 합니다.

* 사용자 고유의 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* 클라이언트 머신에 Azure PowerShell 또는 Azure CLI를 설치하여 템플릿을 배포합니다. [명령줄 배포](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="configure-your-subscription"></a>구독 구성

필요한 경우 다음 단계에 따라 구독에서 Azure Purview가 실행될 수 있도록 구독을 구성합니다.

   1. Azure Portal에서 **구독** 을 검색하고 선택합니다.

   1. 구독 목록에서 사용하려는 구독을 선택합니다. 구독에 대한 관리자 액세스 권한이 필요합니다.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Azure Portal에서 구독을 선택하는 방법을 보여 주는 스크린샷":::

   1. 구독의 경우 **리소스 공급자** 를 선택합니다. **리소스 공급자** 창에서 다음 세 가지 리소스 공급자를 모두 검색하고 등록합니다. 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      등록되지 않은 경우 **등록** 을 선택하여 등록합니다.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Azure Portal에서 Microsoft.Azure Purview 리소스 공급자를 등록하는 방법을 보여 주는 스크린샷":::

## <a name="create-an-azure-purview-account-instance"></a>Azure Purview 계정 인스턴스 만들기

1. Azure 자격 증명으로 로그인

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Azure 구독이 여러 개 있으면 사용할 구독을 선택합니다.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Purview 계정에 대한 리소스 그룹을 만듭니다. 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. `purviewtemplate.json`과 같은 Purview 템플릿 파일을 만듭니다. `name`, `location` 및 `capacity`(`4`또는 `16`)를 업데이트할 수 있습니다.

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Purview 템플릿 배포

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. 배포 명령에서 결과를 반환합니다. `ProvisioningState`를 찾아서 배포에 성공했는지 확인합니다.
    
## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Purview 계정을 만드는 방법을 알아보았습니다.

다음 문서로 계속 진행하여 사용자가 Azure Purview 계정에 액세스할 수 있도록 허용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [Azure Purview 계정에 사용자 추가](catalog-permissions.md)