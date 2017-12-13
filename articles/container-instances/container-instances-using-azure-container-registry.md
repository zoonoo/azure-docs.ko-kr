---
title: "Azure Container Registry에서 Azure Container Instances에 배포"
description: "Azure Container Registry에서 Azure Container Instances 배포"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 9667a5b840d6c1fab5087cfcf3ede34a732fbe01
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Azure Container Registry에서 Azure Container Instances에 배포

Azure Container Registry는 Docker 컨테이너 이미지를 위한 Azure 기반의 개인 레지스트리입니다. 이 문서에서는 Azure Container Registry에 저장된 컨테이너 이미지를 Azure Container Instances에 배포하는 방법에 대해 설명합니다.

## <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI에는 Azure Container Instances에서 컨테이너를 만들고 관리하는 명령이 포함되어 있습니다. `create` 명령에서 개인 이미지를 지정하는 경우 컨테이너 레지스트리를 사용하여 인증하는 데 필요한 이미지 레지스트리 암호를 지정할 수도 있습니다.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

`create` 명령은 `registry-login-server` 및 `registry-username`도 지정하도록 지원합니다. 그러나 Azure Container Registry의 로그인 서버는 단순히 *registryname*.azurecr.io이며 사용자 이름은 *registryname*입니다. 따라서 이러한 값을 명시적으로 제공하지 않으면 이미지 이름에서 유추됩니다.

## <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

컨테이너 그룹 정의에 `imageRegistryCredentials` 속성을 포함하여 Azure Resource Manager 템플릿에서 Azure Container Registry 속성을 지정할 수 있습니다.

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

템플릿에서 바로 컨테이너 레지스트리 암호를 저장하지 않도록 하려면 [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md)에 비밀로 저장하고 [Azure Resource Manager 및 Key Vault 간의 네이티브 통합](../azure-resource-manager/resource-manager-keyvault-parameter.md)을 사용하여 템플릿에서 참조하는 것이 좋습니다.

## <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure Container Registry에서 컨테이너 이미지를 유지할 경우 Azure Portal을 사용하여 Azure Container Instances에 컨테이너를 쉽게 생성할 수 있습니다.

1. Azure Portal에서 컨테이너 레지스트리로 이동합니다.

2. 리포지토리를 선택합니다.

    ![Azure Portal의 Azure Container Registry 메뉴][acr-menu]

3. 배포하려는 리포지토리를 선택합니다.

4. 배포하려는 컨테이너 이미지의 태그를 오른쪽 클릭합니다.

    ![Azure Container Instances를 사용하여 컨테이너를 시작하기 위한 상황에 맞는 메뉴][acr-runinstance-contextmenu]

5. 컨테이너에 대한 이름과 리소스 그룹에 대한 이름을 입력합니다. 원하는 경우 기본값을 변경할 수도 있습니다.

    ![Azure Container Instances의 만들기 메뉴][acr-create-deeplink]

6. 배포가 완료되면 알림 창에서 컨테이너 그룹으로 이동하여 해당 IP 주소 및 기타 속성을 찾을 수 있습니다.

    ![Azure Container Instances 컨테이너 그룹의 세부 정보 보기][aci-detailsview]

## <a name="next-steps"></a>다음 단계

컨테이너를 빌드하고 개인 컨테이너 레지스트리에 푸시하고 [자습서를 완료](container-instances-tutorial-prepare-app.md)하여 Azure Container Instances를 배포하는 방법을 알아봅니다.

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
