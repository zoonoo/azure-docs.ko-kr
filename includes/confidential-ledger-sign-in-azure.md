---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: 985c50f39476c3b1c52670f06d51fde5b3156978
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387666"
---
Azure CLI [az login](/cli/azure/reference-index#az_login) 명령 또는 Azure PowerShell [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure에 로그인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzAccount
```
---

CLI 또는 PowerShell이 기본 브라우저를 열 수 있는 경우 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다. 그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)을 방문하여 터미널에 표시된 권한 부여 코드를 입력합니다.

메시지가 표시되면 브라우저에서 계정 자격 증명으로 로그인합니다.
