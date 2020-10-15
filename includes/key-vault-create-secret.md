---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512813"
---
값이 **Success!** 인 **mySecret**이라는 비밀을 만들어 보겠습니다. 비밀은 안전하게 유지하면서도 애플리케이션에서 사용할 수 있어야 하는 암호, SQL 연결 문자열 또는 기타 정보입니다. 

새로 만든 키 자격 증명 모음에 비밀을 추가하려면 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) 명령을 사용합니다.

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```