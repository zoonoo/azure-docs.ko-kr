---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013123"
---
애플리케이션의 DefaultAzureCredential 메서드는 `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`및 `AZURE_TENANT_ID`의 세 가지 환경 변수를 사용합니다. 이러한 변수를 `export VARNAME=VALUE` 형식을 사용하여 "서비스 주체 만들기" 단계에서 반환된 clientId, clientSecret 및 tenantId 값으로 설정합니다. (이 메서드는 현재 셸 및 셸에서 만든 프로세스에 대한 변수만 설정합니다. 이러한 변수를 환경에 영구적으로 추가하려면 `/etc/environment ` 파일을 편집합니다.) 

또한 Key Vault 이름을 `KEY_VAULT_NAME`라는 환경 변수로 저장해야 합니다.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
