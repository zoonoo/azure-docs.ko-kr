---
title: 컨테이너 및 RBAC (미리 보기)-Azure Storage를 사용 하 여 큐에 Azure AD 액세스 권한을 관리 하려면 Azure portal을 사용 합니다.
description: Azure portal에서 역할 기반 액세스 제어 (RBAC)를 사용 하 여 컨테이너 및 보안 주체에는 큐에 대 한 액세스를 할당 합니다. Azure Storage는 Azure AD 통해 인증에 대 한 기본 제공 및 사용자 지정 RBAC 역할을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a38cce7d15b4796bb66803d06f3589569985cb37
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319502"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal-preview"></a>Azure 컨테이너 및 Azure portal (미리 보기)에서 RBAC 사용 하 여 큐 액세스 권한 부여

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 컨테이너 또는 큐에 액세스하는 데 사용되는 사용 권한의 공통 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의합니다. 

RBAC 역할에는 Azure AD 보안 주체에 할당 된 Azure 부여 해당 보안 주체에 대 한 해당 리소스에 액세스 합니다. 액세스 권한은 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. 사용자, 그룹, 응용 프로그램 서비스 주체를 Azure AD 보안 주체 수 또는 [Azure 리소스에 대 한 id 관리](../../active-directory/managed-identities-azure-resources/overview.md)합니다.

이 문서에서는 RBAC 역할을 할당 하려면 Azure portal을 사용 하는 방법을 설명 합니다. Azure 포털에 RBAC 역할 할당 및 저장소 리소스에 대 한 액세스를 관리 하기 위한 간단한 인터페이스를 제공 합니다. 또한 Azure 명령줄 도구 또는 Azure Storage 관리 Api 사용 하 여 blob 및 큐 리소스에 대 한 RBAC 역할을 할당할 수 있습니다. 저장소 리소스에 대 한 RBAC 역할에 대 한 자세한 내용은 참조 하세요. [Authenticate 권한을 Azure blob 및 Azure Active Directory (미리 보기)를 사용 하 여 큐](storage-auth-aad.md)합니다. 

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 및 큐의 RBAC 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>리소스 범위를 결정 합니다. 

보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체가 있어야 하는 액세스의 범위를 결정 합니다. 모범 사례는 것이 가장 좋습니다 가능한 가장 좁은 범위로 권한을 부여 하려면을 지정 합니다.

다음 목록에는 수 범위를 Azure blob 및 큐 리소스에 대 한 액세스는 가장 좁은 범위를 사용 하 여 시작 하는 수준을 설명 합니다.

- **개별 컨테이너입니다.** 이 범위의 보안 주체의 모든 컨테이너 뿐만 아니라 컨테이너 속성 및 메타 데이터의 blob에 액세스할 수 있습니다.
- **개별 큐입니다.** 이 범위에서 보안 주체 메시지 큐와 큐 속성 및 메타 데이터에 액세스할 수 있습니다.
- **저장소 계정입니다.** 이 범위에서 보안 주체 또는 모든 큐 및 해당 메시지 모든 컨테이너 및 해당 blob에 액세스할 수 있습니다.
- **리소스 그룹입니다.** 이 범위에서 보안 주체를 사용 하면 컨테이너 또는 리소스 그룹에 저장소 계정의 모든 큐의 모든 권한이 있습니다.
- **구독입니다.** 이 범위에서 보안 주체를 사용 하면 컨테이너 또는 모든 구독에서 리소스 그룹에 저장소 계정의 모든 큐의 모든 권한이 있습니다.

역할 할당에 대 한 원하는 범위를 확인 한 경우 Azure portal에서 적절 한 리소스를 이동 합니다. 표시 된 **액세스 제어 (IAM)** 리소스에 대 한 설정을 역할 할당을 관리 하는 이후 섹션에서 지침을 따릅니다.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure portal을 사용 하 여 RBAC 역할 할당

Azure portal에서 Azure AD 자격 증명을 사용 하 여 blob 및 큐 리소스에 대 한 액세스 권한을 부여 단계는 다음과 같습니다. 

1. 컨테이너 또는 큐에 대 한 액세스 권한을 부여 하려면 적절 한 Azure Storage RBAC 역할을 할당 합니다. 읽기 액세스용으로 할당 합니다 **Blob 데이터 판독기 (미리 보기)** 하거나 **큐 데이터 판독기 (미리 보기)** 역할입니다. 읽기, 쓰기 및 삭제 액세스를 할당 합니다 **Blob 데이터 기여자 (미리 보기)** 하거나 **큐 데이터 기여자 (미리 보기)** 역할입니다. 또한 사용자 지정 역할을 할당할 수 있습니다.

1. Azure Resource Manager를 할당 [판독기](../../role-based-access-control/built-in-roles.md#reader) 컨테이너 또는 Azure AD 자격 증명을 사용 하 여 Azure portal 통해 큐에 액세스 해야 하는 사용자 역할. 

다음 섹션에서는 이러한 각 단계를 자세히 설명합니다.

### <a name="assign-a-built-in-rbac-role"></a>기본 제공 RBAC 역할 할당

보안 주체에 역할을 할당 하기 전에 권한을 부여 하는 사용 권한 범위를 고려해 야 합니다. 검토 합니다 [리소스 범위를 결정](#determine-resource-scope) 섹션 적절 한 범위를 결정 합니다.

여기에 표시된 프로시저는 컨테이너로 범위가 지정된 역할을 할당하지만 큐로 범위가 지정된 역할을 할당하기 위해 동일한 단계를 수행할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동하고 계정에 대한 **개요**를 표시합니다.
1. 서비스 아래에서 **Blob**을 선택합니다. 
1. 역할을 할당하려는 컨테이너를 찾아 컨테이너의 설정을 표시합니다. 
1. **액세스 제어(IAM)** 를 선택하여 컨테이너에 대한 액세스 제어 설정을 표시합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.

    ![컨테이너 액세스 제어 설정을 보여 주는 스크린샷](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. **역할 할당 추가** 단추를 클릭하여 새 역할을 추가합니다.
1. 에 **역할 할당 추가** 창 할당 하려는 Azure Storage 역할을 선택 합니다. 다음 해당 역할을 할당 하려면 보안 주체를 찾기 위해 검색 합니다.

    ![RBAC 역할을 할당하는 방법을 보여주는 스크린샷](media/storage-auth-aad-rbac/add-rbac-role.png)

1. **저장**을 클릭합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 추가된 사용자에게 이제 *sample-container*라는 컨테이너의 데이터에 대한 읽기 권한이 있음을 보여줍니다.

    ![역할에 할당 된 사용자의 표시 목록 스크린샷](media/storage-auth-aad-rbac/container-scoped-role.png)

저장소 계정, 리소스 그룹 또는 구독으로 범위가 지정 된 역할을 할당 하려면 유사한 단계를 따르면 됩니다.

> [!NOTE]
> Azure Storage 계정 소유자인 경우 자동으로 데이터에 액세스할 수 있는 권한이 할당되지 않습니다. Azure Storage에 RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 저장소 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.
> 
> 저장소 계정에는 계층적 네임 스페이스를 사용 하는 경우 컨테이너 또는 큐에 범위가 지정 된 역할을 할당할 수 없습니다.

### <a name="assign-the-reader-role-for-portal-access"></a>포털 액세스에 대 한 읽기 권한자 역할 할당

저장소 계정의 데이터에 대 한 작업을 수행 하는 보안 주체에 권한을 부여 하는 보안 주체에 Azure Storage에 대 한 기본 제공 또는 사용자 지정 역할을 할당 하면 합니다. 기본 제공 **데이터 판독기** 역할에는 컨테이너 또는 큐에서 데이터에 대 한 읽기 권한을 제공의 기본 제공 하는 동안 **데이터 기여자** 읽기, 쓰기 및 컨테이너에 대 한 권한 삭제를 제공 하는 역할 또는 큐입니다. 사용 권한은 지정된 된 리소스에 범위가 지정 됩니다.  

예를 들어, 할당 하는 경우는 **Storage Blob 데이터 기여자 (미리 보기)** 라는 컨테이너 수준에서 Mary에 게 역할 **샘플 컨테이너**다음 Mary 부여 읽기, 쓰기 및 삭제 권한을 모든 컨테이너의에서 blob에 해당 합니다.

그러나 Mary가 Azure portal에서 blob을 보려는 경우, 해당 **Storage Blob 데이터 기여자 (미리 보기)** 역할 자체는 보려면 blob 포털을 탐색할 수 있는 권한이 제공 하지 않습니다. 추가 포털을 통해 이동 하 고 있는 표시 되는 다른 리소스를 확인 하려면 Azure AD 권한이 필요 합니다.

사용자 추가 RBAC 역할을 할당 한 다음 Azure portal에서 blob에 액세스할 수 있어야 하는 경우는 [판독기](../../role-based-access-control/built-in-roles.md#reader) 저장소 계정 또는 그 이상 수준에서 해당 사용자에 게 역할을 합니다. 합니다 **판독기** 역할은 저장소 계정 리소스를 볼 수 있지만 수정 허용 하는 Azure Resource Manager 역할을 합니다. Azure Storage의 데이터에 있지만 계정 관리 리소스에만 읽기 권한을 제공 하지 않습니다.

에 할당 하려면 다음이 단계를 수행 합니다 **판독기** 역할 사용자는 Azure portal에서 blob에 액세스할 수 있도록 합니다. 이 예제에서는 저장소 계정에는 할당 범위가 지정 됩니다.

1. [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동합니다.
1. 선택 **액세스 제어 (IAM)** 저장소 계정에 대 한 액세스 제어 설정을 표시 합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
1. 에 **역할 할당 추가** 창에서 합니다 **판독기** 역할입니다. 
1. **에 대 한 액세스 할당** 드롭다운 목록에서 선택 **Azure AD 사용자, 그룹 또는 서비스 주체**합니다.
1. 역할을 할당 하려면 보안 주체를 찾기 위해 검색 합니다.
1. 역할 할당을 저장 합니다.

> [!NOTE]
> 읽기 권한자 역할을 할당 하는 것은 blob 또는 Azure portal을 사용 하 여 큐에 액세스 해야 하는 사용자에만 필요 합니다. 

## <a name="next-steps"></a>다음 단계

- RBAC에 대한 자세한 내용은 [RBAC(역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 RBAC 역할 할당을 할당하고 관리하는 방법을 알아보려면 다음과 같은 아티클을 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-rest.md)
- 스토리지 응용 프로그램 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [Azure Storage 응용 프로그램에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
- Azure 컨테이너 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물 [Azure Storage에 대한 Azure AD 인증 미리 보기 발표](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)를 참조하세요.
