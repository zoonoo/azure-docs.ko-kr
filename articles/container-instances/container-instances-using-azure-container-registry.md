---
title: "Azure Container Registry에서 Azure Container Instances에 배포"
description: "Azure Container Registry의 컨테이너 이미지를 사용하여 Azure Container Instances에 컨테이너를 배포하는 방법을 알아봅니다."
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/24/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c69b95f66bf2eaf4975961da5b25f5ac6172798c
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Registry에서 Azure Container Instances에 배포

Azure Container Registry는 Docker 컨테이너 이미지를 위한 Azure 기반의 개인 레지스트리입니다. 이 문서에서는 Azure Container Registry에 저장된 컨테이너 이미지를 Azure Container Instances에 배포하는 방법에 대해 설명합니다.

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI에는 Azure Container Instances에서 컨테이너를 만들고 관리하는 명령이 포함되어 있습니다. [az container create][az-container-create] 명령에서 개인 이미지를 지정하는 경우 컨테이너 레지스트리를 사용하여 인증하는 데 필요한 이미지 레지스트리 암호를 지정할 수도 있습니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

[az container create][az-container-create] 명령을 사용하여 `--registry-login-server` 및 `--registry-username`을 지정할 수도 있습니다. 그러나 Azure Container Registry의 로그인 서버는 단순히 *registryname*.azurecr.io이며 사용자 이름은 *registryname*입니다. 따라서 이러한 값을 명시적으로 제공하지 않으면 이미지 이름에서 유추됩니다.

## <a name="deploy-with-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 배포

컨테이너 그룹 정의에 `imageRegistryCredentials` 속성을 포함하여 Azure Resource Manager 템플릿에서 Azure Container Registry 속성을 지정할 수 있습니다.

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

템플릿에서 바로 컨테이너 레지스트리 암호를 저장하지 않도록 하려면 [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md)에 비밀로 저장하고 [Azure Resource Manager 및 Key Vault 간의 네이티브 통합](../azure-resource-manager/resource-manager-keyvault-parameter.md)을 사용하여 템플릿에서 참조하는 것이 좋습니다.

## <a name="deploy-with-azure-portal"></a>Azure Portal을 사용하여 배포

Azure Container Registry에서 컨테이너 이미지를 유지할 경우 Azure Portal을 사용하여 Azure Container Instances에 컨테이너를 쉽게 생성할 수 있습니다.

1. Azure Portal에서 컨테이너 레지스트리로 이동합니다.

1. **리포지토리**를 선택하고, 배포할 리포지토리를 선택하고, 배포하려는 컨테이너 이미지의 태그를 마우스 오른쪽 단추로 클릭하고, **인스턴스 실행**을 선택합니다.

    ![Azure Portal의 Azure Container Registry에 있는 "인스턴스 실행"][acr-runinstance-contextmenu]

1. 컨테이너에 대한 이름과 리소스 그룹에 대한 이름을 입력합니다. 원하는 경우 기본값을 변경할 수도 있습니다.

    ![Azure Container Instances의 만들기 메뉴][acr-create-deeplink]

1. 배포가 완료되면 알림 창에서 컨테이너 그룹으로 이동하여 해당 IP 주소 및 기타 속성을 찾을 수 있습니다.

    ![Azure Container Instances 컨테이너 그룹의 세부 정보 보기][aci-detailsview]

## <a name="service-principal-authentication"></a>서비스 주체 인증

Azure 컨테이너 레지스트리에 대한 관리 사용자가 비활성화된 경우에는 컨테이너 인스턴스를 만들 때 Azure Active Directory [서비스 주체](../container-registry/container-registry-auth-service-principal.md)를 사용하여 레지스트리를 인증할 수 있습니다. 헤드리스 시나리오(예: 무인 방식으로 컨테이너 인스턴스를 만드는 스크립트나 응용 프로그램)의 경우에서 서비스 주체를 인증에 사용하는 것이 좋습니다.

자세한 내용은 [Azure Container Instances의 Azure Container Registry를 사용하여 인증](../container-registry/container-registry-auth-aci.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

컨테이너를 빌드하고 개인 컨테이너 레지스트리에 푸시하고 [자습서를 완료](container-instances-tutorial-prepare-app.md)하여 Azure Container Instances를 배포하는 방법을 알아봅니다.

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create