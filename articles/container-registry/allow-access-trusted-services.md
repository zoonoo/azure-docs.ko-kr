---
title: 신뢰할 수 있는 Azure 서비스를 사용 하 여 네트워크 제한 레지스트리 액세스
description: 신뢰할 수 있는 Azure 서비스 인스턴스가 네트워크 제한 컨테이너 레지스트리에 안전 하 게 액세스 하 여 이미지를 끌어오거나 푸시할 수 있도록 합니다.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 3cc32630ea689891e7ba75163c33bc499a38becd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716485"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>신뢰할 수 있는 서비스에서 네트워크 제한 컨테이너 레지스트리에 안전 하 게 액세스 하도록 허용 (미리 보기)

Azure Container Registry 신뢰할 수 있는 Azure 서비스 선택에서 네트워크 액세스 규칙으로 구성 된 레지스트리에 액세스할 수 있습니다. 신뢰할 수 있는 서비스를 사용할 수 있는 경우 신뢰할 수 있는 서비스 인스턴스는 레지스트리의 네트워크 규칙을 안전 하 게 우회 하 고 pull 또는 push 이미지와 같은 작업을 수행할 수 있습니다. 서비스 인스턴스의 관리 id는 액세스에 사용 되며, Azure 역할을 할당 하 고 레지스트리를 사용 하 여 인증 해야 합니다.

Azure CLI의 Azure Cloud Shell 또는 로컬 설치를 사용 하 여이 문서의 명령 예제를 실행 합니다. 로컬에서 사용 하려면 버전 2.18 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

신뢰할 수 있는 Azure 서비스의 레지스트리 액세스는 **미리 보기** 기능입니다.

## <a name="limitations"></a>제한 사항

* 네트워크 제한 컨테이너 레지스트리에 액세스 하려면 [신뢰할 수 있는 서비스](#trusted-services) 에서 사용 하도록 설정 된 시스템 할당 관리 id를 사용 해야 합니다. 사용자 할당 관리 id는 현재 지원 되지 않습니다.
* [서비스 끝점](container-registry-vnet.md)으로 구성 된 컨테이너 레지스트리에는 신뢰할 수 있는 서비스를 허용 하지 않습니다. 이 기능은 [개인 끝점](container-registry-private-link.md) 으로 제한 되거나 [공용 IP 액세스 규칙이](container-registry-access-selected-networks.md) 적용 되는 레지스트리에만 영향을 줍니다. 

## <a name="about-trusted-services"></a>신뢰할 수 있는 서비스 정보

Azure Container Registry에는 다음을 포함 하 여 레지스트리에 대 한 액세스를 제한 하는 여러 네트워크 구성을 지 원하는 계층화 된 보안 모델이 있습니다.

* [Azure 개인 링크를 사용 하는 개인 끝점](container-registry-private-link.md). 구성 된 경우에는 개인 IP 주소를 사용 하 여 가상 네트워크 내의 리소스에만 레지스트리의 개인 끝점에 액세스할 수 있습니다.  
* 레지스트리 [방화벽 규칙](container-registry-access-selected-networks.md)은 특정 공용 IP 주소 또는 주소 범위 에서만 레지스트리의 공용 끝점에 대 한 액세스를 허용 합니다. 또한 개인 끝점을 사용 하는 경우 공용 끝점에 대 한 모든 액세스를 차단 하도록 방화벽을 구성할 수 있습니다.

가상 네트워크에 배포 되거나 방화벽 규칙을 사용 하 여 구성 된 경우 레지스트리는 기본적으로 해당 원본 외부의 사용자 또는 서비스에 대 한 액세스를 거부 합니다. 

여러 다중 테 넌 트 Azure 서비스는 이러한 레지스트리 네트워크 설정에 포함 될 수 없는 네트워크에서 작동 하 여 레지스트리에 이미지를 끌어오거나 푸시하는 것을 방지 합니다. 특정 서비스 인스턴스를 "신뢰할 수 있는"로 지정 하면 레지스트리 소유자는 Azure 리소스 선택에서 레지스트리의 네트워크 설정을 안전 하 게 우회 하 여 이미지를 끌어오거나 푸시할 수 있습니다. 

### <a name="trusted-services"></a>신뢰할 수 있는 서비스

레지스트리의 **신뢰할 수 있는 서비스 허용** 설정을 사용 하는 경우 (기본값), 다음 서비스의 인스턴스는 네트워크 제한 컨테이너 레지스트리에 액세스할 수 있습니다. 시간이 지남에 따라 더 많은 서비스가 추가 될 예정입니다.

|신뢰할 수 있는 서비스  |지원 되는 사용 시나리오  |
|---------|---------|
|ACR 작업     | [ACR 작업에서 다른 레지스트리에 액세스](container-registry-tasks-cross-registry-authentication.md)       |
|Azure Container Registry | [다른 Azure container registry에서 이미지 가져오기](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> 아님, Azure Container Instances 및 Azure Security Center를 App Service 포함 하는 다른 관리 되는 Azure 서비스의 인스턴스는 네트워크 제한 된 컨테이너 레지스트리에 액세스할 수 있도록 허용 하지 않습니다.

## <a name="allow-trusted-services---cli"></a>신뢰할 수 있는 서비스 허용-CLI

기본적으로 새 Azure container registry에서 신뢰할 수 있는 서비스 허용 설정이 사용 됩니다. [Az acr update](/cli/azure/acr#az-acr-update) 명령을 실행 하 여 설정을 사용 하지 않도록 설정 하거나 사용 하도록 설정 합니다.

사용 하지 않도록 설정 하려면

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

기존 레지스트리 또는 이미 사용 하지 않도록 설정 된 레지스트리에서 설정을 사용 하도록 설정 하려면 다음을 수행 합니다.

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>신뢰할 수 있는 서비스 허용-포털

기본적으로 새 Azure container registry에서 신뢰할 수 있는 서비스 허용 설정이 사용 됩니다. 

포털에서 설정을 사용 하지 않도록 설정 하거나 다시 사용 하도록 설정 하려면:

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **설정** 에서 **네트워킹** 을 선택합니다. 
1. **공용 네트워크 액세스 허용** 에서 **선택한 네트워크** 또는 **사용 안 함** 을 선택 합니다.
1. 다음 중 하나를 수행합니다.
    * 신뢰할 수 있는 서비스에서 액세스를 사용 하지 않도록 설정 하려면 **방화벽 예외** 에서 **신뢰할 수 있는 Microsoft 서비스가이 컨테이너 레지스트리에 액세스 하도록 허용** 을 선택 취소 합니다. 
    * 트러스트 된 서비스를 허용 하려면 **방화벽 예외** 에서 **신뢰할 수 있는 Microsoft 서비스가이 컨테이너 레지스트리에 액세스 하도록 허용** 을 선택 합니다.
1. **저장** 을 선택합니다.

## <a name="trusted-services-workflow"></a>신뢰할 수 있는 서비스 워크플로

신뢰할 수 있는 서비스의 인스턴스에서 네트워크 제한 컨테이너 레지스트리에 액세스할 수 있도록 하는 일반적인 워크플로는 다음과 같습니다.

1. Azure Container Registry에 대해 [신뢰할 수 있는 서비스](#trusted-services) 중 하나의 인스턴스에서 [Azure 리소스에 대 한 시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하도록 설정 합니다.
1. [Azure 역할](container-registry-roles.md) 의 id를 레지스트리에 할당 합니다. 예를 들어 컨테이너 이미지를 가져오기 위해 ACRPull 역할을 할당 합니다.
1. 네트워크 제한 레지스트리에서 신뢰할 수 있는 서비스의 액세스를 허용 하는 설정을 구성 합니다.
1. 네트워크 제한 된 레지스트리를 사용 하 여 인증 하려면 id의 자격 증명을 사용 합니다. 
1. 레지스트리에서 이미지를 끌어오거나 역할에서 허용 하는 다른 작업을 수행 합니다.

### <a name="example-acr-tasks"></a>예: ACR 작업

다음 예제에서는 ACR 작업을 신뢰할 수 있는 서비스로 사용 하는 방법을 보여 줍니다. 작업 세부 정보는 [Azure에서 관리 되는 id를 사용 하 여 ACR 작업에서 크로스 레지스트리 인증](container-registry-tasks-cross-registry-authentication.md) 을 참조 하세요.

1. Azure container registry를 만들거나 업데이트 하 고 [샘플 기본 이미지](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) 를 레지스트리에 푸시합니다. 이 레지스트리는 시나리오에 대 한 *기본 레지스트리* 입니다.
1. 두 번째 Azure container registry에서 기본 레지스트리에서 이미지를 끌어올 ACR 작업을 [정의](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) 하 고 [만듭니다](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) . 태스크를 만들 때 시스템 할당 관리 id를 사용 하도록 설정 합니다.
1. [기본 레지스트리에 액세스 하기 위해 Azure 역할](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources)에 작업 id를 할당 합니다. 예를 들어 이미지를 끌어올 수 있는 권한이 있는 AcrPull 역할을 할당 합니다.
1. 작업에 [관리 되는 id 자격 증명을 추가](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) 합니다.
1. 태스크가 네트워크 제한을 우회 하는지 확인 하려면 기본 레지스트리에서 [공용 액세스를 사용 하지 않도록 설정](container-registry-access-selected-networks.md#disable-public-network-access) 합니다.
1. 작업을 실행 합니다. 기본 레지스트리 및 태스크가 올바르게 구성 된 경우 기본 레지스트리가 액세스를 허용 하므로 태스크가 성공적으로 실행 됩니다.

신뢰할 수 있는 서비스에의 한 액세스 비활성화를 테스트 하려면:

1. 기본 레지스트리에서 신뢰할 수 있는 서비스의 액세스를 허용 하는 설정을 사용 하지 않도록 설정 합니다.
1. 작업을 다시 실행 합니다. 이 경우 기본 레지스트리에서 태스크의 액세스를 더 이상 허용 하지 않기 때문에 태스크가 실행 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에서 프라이빗 엔드포인트를 사용하여 레지스트리에 대한 액세스를 제한하려면 [Azure 컨테이너에 대한 Azure Private Link 구성](container-registry-private-link.md)을 참조하세요.
* 레지스트리 방화벽 규칙을 설정 하려면 [공용 IP 네트워크 규칙 구성](container-registry-access-selected-networks.md)을 참조 하세요.
