---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244697"
---
값이 **Success!** 인 **mySecret** 이라는 비밀을 만들어 보겠습니다. 비밀은 안전하게 유지하면서도 애플리케이션에서 사용할 수 있어야 하는 암호, SQL 연결 문자열 또는 기타 정보입니다. 

새로 만든 키 자격 증명 모음에 비밀을 추가하려면 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) 명령을 사용합니다.

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```