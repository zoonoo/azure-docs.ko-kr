---
title: Azure Container Registry - 역할 및 권한
description: Azure RBAC(역할 기반 액세스 제어)와 IAM(ID 및 액세스 관리)을 사용하여 Azure Container Registry에서 리소스에 대한 세분화된 사용 권한을 제공합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: 5dabab02758b4ade0f668adddf7644a654e1804b
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53756098"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry 역할 및 권한

Azure Container Registry 서비스는 Azure Container Registry에 대한 다양한 수준의 권한을 제공하는 Azure 역할 세트를 지원합니다. Azure RBAC([역할 기반 액세스 제어](../role-based-access-control/index.yml))를 사용하여 레지스트리를 조작해야 하는 사용자 또는 서비스 주체에 특정 권한을 할당합니다.

| 역할/권한       | [Resource Manager 액세스](#access-resource-manage)| [레지스트리 만들기/삭제](#create/delete-registry) | [이미지 푸시](#push-image) | [이미지 풀](#pull-image) | [정책 변경](#change-polices) |   [이미지 서명](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- |
| 소유자 | X | X | X | X | X |  |  
| 참가자 | X | X | X | X | X |  |  
| 판독기 | X |  |  | X |  |  | 
| AcrPush |  |  | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  
| AcrImageSigner |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>사용자 및 서비스 구별

권한을 적용할 때마다 작업을 수행하는 데 가장 제한적인 권한 집합을 사용자 또는 서비스에 대해 제공하는 것이 좋습니다. 다음 권한 집합은 사용자 및 헤드리스 서비스에서 사용할 수 있는 기능 세트를 나타냅니다.

### <a name="cicd-solutions"></a>CI/CD 솔루션

CI/CD 솔루션에서 `docker build` 명령을 자동화하는 경우 `docker push` 기능이 필요합니다. 이러한 헤드리스 서비스 시나리오의 경우 **AcrPush** 역할을 할당하는 것이 좋습니다. 이 역할은 보다 광범위한 **기여자** 역할과 달리 계정이 다른 레지스트리 작업을 수행하거나 Azure Resource Manager에 액세스하지 못하도록 합니다.

### <a name="container-host-nodes"></a>컨테이너 호스트 노드

마찬가지로, 컨테이너를 실행하는 노드에는 **AcrPull** 역할이 필요하지만 **읽기 권한자** 기능은 필요하지 않아야 합니다.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 확장

Visual Studio Code [Docker 확장](https://code.visualstudio.com/docs/azure/docker)과 같은 도구의 경우, 사용 가능한 Azure Container Registry를 나열하려면 추가 리소스 공급자에 액세스해야 합니다. 이 경우 **reader** 또는 **contributor** 역할에 대한 액세스 권한을 사용자에게 제공합니다. 이러한 역할은 `docker pull`, `docker push`, `az acr list`, `az acr build` 및 기타 기능을 허용합니다. 

## <a name="access-resource-manager"></a>Resource Manager 액세스

Azure Portal 및 [Azure CLI](/cli/azure/)에서는 Azure Resource Manager에 액세스해야 합니다. 예를 들어, `az acr list` 명령을 사용하여 레지스트리 목록을 가져오려면 이 권한 집합이 필요합니다. 

## <a name="createdelete-registry"></a>레지스트리 만들기/삭제

Azure Container Registry를 만들고 삭제하는 기능입니다.

## <a name="push-image"></a>푸시 이미지

이미지를 `docker push`하거나, 지원되는 다른 아티팩트를 레지스트리에 푸시하는 기능입니다. 인증된 ID를 사용하여 레지스트리에 [인증](container-registry-authentication.md)해야 합니다. 

## <a name="pull-image"></a>이미지 풀

보장되지 않은 이미지를 `docker pull`하거나 레지스트리에서 지원되는 다른 아티팩트를 풀하는 기능입니다. 인증된 ID를 사용하여 레지스트리에 [인증](container-registry-authentication.md)해야 합니다.

## <a name="change-policies"></a>정책 변경

레지스트리에 정책을 구성하는 기능입니다. 정책에는 이미지 제거, 격리 사용 및 이미지 서명이 포함됩니다.

## <a name="sign-images"></a>이미지 서명

일반적으로 자동화된 프로세스에 할당되는, 서비스 주체를 사용하는 이미지 서명 기능입니다. 일반적으로 이 권한은 [이미지 푸시](#push-image)와 결합되어 신뢰할 수 있는 이미지를 레지스트리에 푸시할 수 있도록 합니다. 자세한 내용은 [Azure Container Registry의 콘텐츠 신뢰](container-registry-content-trust.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md) 또는 기타 Azure 도구를 사용하여 Azure ID에 RBAC 역할을 할당하는 방법을 자세히 알아봅니다.

* Azure Container Registry의 [인증 옵션](container-registry-authentication.md)에 대해 알아봅니다.
