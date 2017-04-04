---
title: "개인 Docker 컨테이너 레지스트리 만들기 - Azure CLI | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 개인 Docker 컨테이너 레지스트리 만들기 및 관리 시작"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: e37a3194bb65ccf3bb6168a2f456902a9c48edc5
ms.lasthandoff: 03/28/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 개인 Docker 컨테이너 레지스트리 만들기
[Azure CLI 2.0](https://github.com/Azure/azure-cli)의 명령을 사용하여 Linux, Mac 또는 Windows 컴퓨터에서 컨테이너 레지스트리를 만들고 설정을 관리합니다. [Azure Portal](container-registry-get-started-portal.md)을 사용하여 또는 Container Registry [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376)를 사용하여 프로그래밍 방식으로 컨테이너 레지스트리를 만들고 관리할 수도 있습니다.


* 배경 지식 및 개념은 [개요](container-registry-intro.md)를 참조하세요.
* Container Registry CLI 명령(`az acr` 명령)에 대한 도움말을 보려면, 모든 명령에 `-h` 매개 변수를 전달하세요.


## <a name="prerequisites"></a>필수 조건
* **Azure CLI 2.0**: CLI 2.0을 설치하고 시작하려면 [설치 지침](/cli/azure/install-azure-cli)을 참조하세요. `az login`을 실행하여 Azure 구독에 로그인합니다. 자세한 내용은 [CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.
* **리소스 그룹**: 컨테이너 레지스트리를 만들기 전에 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)을 만들거나 기존 리소스 그룹을 사용합니다. 리소스 그룹이 Container Registry 서비스를 [사용할 수 있는](https://azure.microsoft.com/regions/services/) 위치에 있도록 해야 합니다. CLI 2.0을 사용하여 리소스 그룹을 만들려면 [CLI 2.0 참조](/cli/azure/group)를 참조하세요.
* **저장소 계정**(선택 사항): 동일한 위치의 컨테이너 레지스트리를 백업할 표준 Azure [저장소 계정](../storage/storage-introduction.md)을 만듭니다. `az acr create`를 사용하여 레지스트리를 만들 때 저장소 계정을 지정하지 않으면 명령에서 자동으로 생성됩니다. CLI 2.0을 사용하여 저장소 계정을 만들려면 [CLI 2.0 참조](/cli/azure/storage/account)를 참조하세요. Premium Storage는 현재 지원되지 않습니다.
* **서비스 주체**(선택 사항): CLI를 사용하여 레지스트리를 만들 때 기본적으로 액세스가 가능하도록 설정되지 않습니다. 필요에 따라 레지스트리에 기존 Azure Active Directory 서비스 주체를 할당하거나(또는 새 주체를 만들어서 할당하거나) 레지스트리의 관리자 사용자 계정을 사용하도록 설정할 수 있습니다. 이 문서의 뒷부분에 나오는 섹션을 참조하세요. 레지스트리 액세스에 대한 자세한 내용은 [컨테이너 레지스트리로 인증](container-registry-authentication.md)을 참조하세요.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
`az acr create` 명령을 실행하여 컨테이너 레지스트리를 만듭니다.

> [!TIP]
> 레지스트리를 만들 때 전역적으로 고유한 최상위 도메인 이름(문자 및 숫자만 포함)을 지정합니다. 레지스트리 이름 예제가 `myRegistry1`이지만 자신만의 고유한 이름으로 대체합니다.
>
>

다음 명령은 미국 중남부 지역에 `myResourceGroup`이라는 리소스 그룹의 `myRegistry1`라는 컨테이너 레지스트리를 만들기 위해 최소의 매개 변수를 사용합니다.

```azurecli
az acr create -n myRegistry1 -g myResourceGroup -l southcentralus
```

* `--storage-account-name` 는 선택 사항입니다. 지정하지 않으면 지정된 리소스 그룹에서 레지스트리 이름 및 타임스탬프로 구성된 이름으로 저장소 계정이 만들어집니다.

다음과 유사하게 출력됩니다.

![az acr create 출력](./media/container-registry-get-started-azure-cli/acr_create.png)


특별 고려 사항:

* `id` - 구독 내 레지스트리에 대한 식별자이며, 서비스 주체를 할당하려는 경우 필요합니다.
* `loginServer` - [레지스트리에 로그인](container-registry-authentication.md)하기 위해 지정하는 정규화된 이름입니다. 이 예제의 경우 해당 이름은 `myregistry1.exp.azurecr.io`(모두 소문자)입니다.

## <a name="assign-a-service-principal"></a>서비스 주체 할당
CLI 2.0 명령을 사용하여 Azure Active Directory 서비스 주체를 레지스트리에 할당합니다. 이 예제의 경우 서비스 주체에 소유자 역할이 할당되어 있지만 필요하면 [다른 역할](../active-directory/role-based-access-control-configure.md)을 할당할 수 있습니다.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>서비스 주체를 만들고 레지스트리에 대한 액세스 할당
다음 명령에서는 새 서비스 주체에 `--scopes` 매개 변수를 통해 전달된 레지스트리 식별자에 대한 소유자 역할 액세스가 할당됩니다. `--password` 매개 변수를 통해 강력한 암호를 지정합니다.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>기존 서비스 주체 할당
이미 존재하는 서비스 주체에 레지스트리에 대한 소유자 역할 액세스를 할당하려면 다음 예제와 유사한 명령을 실행합니다. 서비스 주체 앱 ID는 `--assignee` 매개 변수를 사용하여 전달합니다.

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>관리자 자격 증명 관리
관리자 계정은 각 컨테이너 레지스트리에 대해 자동으로 생성되며 기본적으로 비활성화됩니다. 다음 예제는 컨테이너 레지스트리에 대한 관리자 자격 증명을 관리하는 `az acr` CLI 명령을 보여줍니다.

### <a name="obtain-admin-user-credentials"></a>관리자 사용자 자격 증명 가져오기
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>기존 레지스트리에 대한 관리자 사용자 활성화
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>기존 레지스트리에 대한 관리자 사용자 비활성화
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>이미지 및 태그 나열
리포지토리의 이미지 및 태그를 쿼리하려면 `az acr` CLI 명령을 사용합니다.

> [!NOTE]
> 현재 Container Registry는 `docker search` 명령으로 이미지 및 태그를 쿼리하도록 지원하지 않습니다.


### <a name="list-repositories"></a>리포지토리 나열
다음 예제는 레지스트리의 리포지토리를 JSON(JavaScript Object Notation) 형식으로 나열합니다.

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>태그 나열
다음 예제는 **samples/nginx** 리포지토리의 태그를 JSON 형식으로 나열합니다.

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>다음 단계
* [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)

