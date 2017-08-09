---
title: "Azure Container Registry에서 Azure Container Instances에 배포 | Azure Docs"
description: "Azure Container Registry에서 Azure Container Instances에 배포"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: fcb21785584c3c5fb41f5ceb70346e3e05a30d93
ms.contentlocale: ko-kr
ms.lasthandoff: 07/26/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Azure Container Registry에서 Azure Container Instances에 배포

Azure Container Registry는 Docker 컨테이너 이미지를 위한 Azure 기반의 개인 레지스트리입니다. 이 문서에서는 Azure Container Registry에 저장된 컨테이너 이미지를 Azure Container Instances에 배포하는 방법에 대해 설명합니다.

## <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI에는 Azure Container Instances에서 컨테이너를 만들고 관리하는 명령이 포함되어 있습니다. `create` 명령에서 개인 이미지를 지정하는 경우 컨테이너 레지스트리를 사용하여 인증하는 데 필요한 이미지 레지스트리 암호를 지정할 수도 있습니다.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

`create` 명령은 `image-registry-login-server` 및 `image-registry-username`도 지정하도록 지원합니다. 그러나 기본적으로 Azure Container Registry의 로그인 서버는 단순히 *registryname*.azurecr.io이며 사용자 이름은 *registryname*입니다. 따라서 이러한 값을 명시적으로 제공하지 않으면 이미지 이름에서 유추됩니다.

## <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager 템플릿에서 Azure Container Registry 속성을 지정할 수 있습니다. 컨테이너 그룹의 정의에서 `imageRegistryCredentials` 속성을 포함합니다.

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

템플릿에서 직접 암호 컨테이너 레지스트리에 저장하지 않도록 방지하려면 [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md)에 암호로 저장하고 [Azure Resource Manager 및 Key Vault 간의 네이티브 통합](../azure-resource-manager/resource-manager-keyvault-parameter.md)을 사용하여 템플릿에서 참조하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

컨테이너를 빌드하고 개인 컨테이너 레지스트리에 푸시하고 [자습서를 완료](container-instances-tutorial-prepare-app.md)하여 Azure Container Instances를 배포하는 방법을 알아봅니다.
