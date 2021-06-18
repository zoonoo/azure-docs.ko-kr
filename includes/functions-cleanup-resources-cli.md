---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 50d9e0e8ed9e6ac3d406570aa25d3a14cedab9b5
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720378"
---
## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)를 계속 진행하고 Azure Storage 큐 출력 바인딩을 추가하는 경우 이미 수행한 작업을 기반으로 하여 빌드되는 모든 리소스를 그대로 유지합니다.

그렇지 않으면 추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 삭제합니다.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---
