---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512811"
---
이 빠른 시작에서는 미리 만든 Azure Key Vault를 사용합니다. [Azure CLI 빠른 시작](/azure/key-vault/general/quick-create-cli), [Azure PowerShell 빠른 시작](/azure/key-vault/general/quick-create-powershell) 또는 [Azure Portal 빠른 시작](/azure/key-vault/general/quick-create-portal)의 단계에 따라 키 자격 증명 모음을 만들 수 있습니다. 

또는 아래의 Azure CLI 또는 Azure PowerShell 명령을 실행하기만 하면 됩니다.

> [!Important]
> 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서는 <your-unique-keyvault-name>을 Key Vault의 이름으로 바꿉니다.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```