---
title: "개인 Docker 레지스트리 만들기 - Azure Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 개인 Docker 컨테이너 레지스트리 만들기 및 관리 시작"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Azure Portal을 사용하여 관리 컨테이너 레지스트리 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하는 데 사용되는 관리되는 Docker 컨테이너 레지스트리 서비스입니다. 이 가이드에서는 Azure Portal을 사용하여 관리되는 Azure Container Registry 인스턴스를 만드는 방법에 대해 자세히 설명합니다.

관리되는 Azure 컨테이너 레지스트리는 미리 보기 상태이며 일부 지역에서는 사용할 수 없습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. 마켓플레이스에서 **Azure 컨테이너 레지스트리**를 검색하고 선택합니다.

3. **만들기**를 클릭하면 ACR 만들기 블레이드가 열립니다.

    ![컨테이너 레지스트리 설정](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. **Azure 컨테이너 레지스트리** 블레이드에서 다음 정보를 입력합니다. 완료하면 **만들기**를 클릭합니다.

    a. **레지스트리 이름**: 특정 레지스트리에 대한 전역적으로 고유한 최상위 도메인 이름입니다. 이 예제에서 레지스트리 이름은 *myAzureContainerRegistry1*이지만 자신만의 고유한 이름으로 바꿉니다. 이름은 문자와 숫자만 포함할 수 있습니다.

    b. **리소스 그룹**: 기존 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)을 선택하거나 새 리소스 그룹의 이름을 입력합니다.

    c. **위치**: **미국 중남부**와 같이 서비스를 [사용할 수 있는](https://azure.microsoft.com/regions/services/) Azure 데이터 센터 위치를 선택합니다.

    d. **관리 사용자**: 필요한 경우 관리 사용자가 레지스트리에 액세스할 수 있도록 설정합니다. 이 설정은 레지스트리를 만든 후에 변경할 수 있습니다.

    e. **관리되는 레지스트리 사용**: ACR에서 자동으로 레지스트리 저장소를 관리하고, 웹후크를 사용하고, AAD 인증을 사용하도록 하려면 '예'를 선택합니다.

    f. **가격 책정 계층**: 가격 책정 계층을 선택합니다. 자세한 내용은 ACR 가격 책정을 참조하세요.

## <a name="log-in-to-acr-instance"></a>ACR 인스턴스에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 

이렇게 하려면 Azure CLI 2.0을 사용합니다. 먼저 필요한 경우 [az login](/cli/azure/#login) 명령을 사용하여 Azure에 로그인합니다. 

```azurecli
az login
```

다음으로 [az acr login](/cli/azure/acr#login) 명령을 사용하여 Azure Container Registry에 로그인합니다.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Azure Container Registry 사용

### <a name="list-container-images"></a>컨테이너 이미지 나열

리포지토리의 이미지 및 태그를 쿼리하려면 `az acr` CLI 명령을 사용합니다.

> [!NOTE]
> 현재 Container Registry는 `docker search` 명령으로 이미지 및 태그를 쿼리하도록 지원하지 않습니다.

### <a name="list-repositories"></a>리포지토리 나열

다음 예제는 레지스트리의 리포지토리를 JSON(JavaScript Object Notation) 형식으로 나열합니다.

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>태그 나열

다음 예제는 **samples/nginx** 리포지토리의 태그를 JSON 형식으로 나열합니다.

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 관리되는 Azure Container Registry 인스턴스를 만들었습니다.

> [!div class="nextstepaction"]
> [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)
