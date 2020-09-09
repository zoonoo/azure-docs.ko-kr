---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: deb2eb877743d533c5daeee8b6636edd62418fe0
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89494323"
---
클라우드 기반 애플리케이션을 인증하는 가장 간단한 방법은 관리 ID를 사용하는 것입니다. 자세한 내용은 [Key Vault에 인증](/azure/key-vault/general/authentication)을 참조하세요.

그러나 간략한 설명을 위해 이 빠른 시작에서는 서비스 주체 및 액세스 제어 정책을 사용해야 하는 데스크톱 애플리케이션을 만듭니다. 서비스 주체에는 "http://&lt;my-unique-service-principal-name&gt;" 형식의 고유한 이름이 필요합니다.

Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용하여 서비스 주체를 만듭니다.

```azurecli
az ad sp create-for-rbac --skip-assignment -n "http://<my-unique-service-principal-name>" --sdk-auth
```

이 작업을 수행하면 일련의 키/값 쌍이 반환됩니다.

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
