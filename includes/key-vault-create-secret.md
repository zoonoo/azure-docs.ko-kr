---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6745d1f7d97a8f4d08078bf93b61762ab04aad46
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073451"
---
값이 **Success!** 인 **mySecret** 이라는 비밀을 만들어 보겠습니다. 비밀은 안전하게 유지하면서도 애플리케이션에서 사용할 수 있어야 하는 암호, SQL 연결 문자열 또는 기타 정보입니다. 

새로 만든 키 자격 증명 모음에 비밀을 추가하려면 다음 명령을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$secret = ConvertTo-SecureString -String 'Success!' -AsPlainText
Set-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret -SecretValue $secret
```
---