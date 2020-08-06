---
title: Azure Portal를 사용 하 여 데이터 액세스를 위한 Azure 역할을 할당 합니다.
titleSuffix: Azure Storage
description: Azure Portal를 사용 하 여 RBAC (역할 기반 액세스 제어)를 통해 Azure Active Directory 보안 주체에 권한을 할당 하는 방법에 대해 알아봅니다. Azure Storage는 Azure AD를 통해 인증에 대 한 기본 제공 및 Azure 사용자 지정 역할을 지원 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 21120239c44dbdd812dfb3d3208990db4372ec29
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828016"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure Portal를 사용 하 여 blob 및 큐 데이터에 액세스 하기 위한 Azure 역할을 할당 합니다.

Azure AD (Azure Active Directory)는 azure [역할 기반 access control (AZURE RBAC)](../../role-based-access-control/overview.md)을 통해 보안 리소스에 대 한 액세스 권한을 부여 합니다. Azure Storage는 blob 또는 큐 데이터에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 Azure 기본 제공 역할 집합을 정의 합니다.

Azure AD 보안 주체에 azure 역할을 할당 하는 경우 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

이 문서에서는 Azure Portal를 사용 하 여 Azure 역할을 할당 하는 방법을 설명 합니다. Azure Portal는 Azure 역할을 할당 하 고 저장소 리소스에 대 한 액세스를 관리 하기 위한 간단한 인터페이스를 제공 합니다. Azure 명령줄 도구 또는 Azure Storage 관리 Api를 사용 하 여 blob 및 큐 리소스에 대 한 Azure 역할을 할당할 수도 있습니다. 저장소 리소스의 Azure 역할에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 azure blob 및 큐에 대 한 액세스 인증](storage-auth-aad.md)을 참조 하세요.

## <a name="azure-roles-for-blobs-and-queues"></a>Blob 및 큐에 대 한 Azure 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>리소스 범위 결정

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 역할 할당

역할 할당에 적절 한 범위를 결정 한 후 Azure Portal에서 해당 리소스로 이동 합니다. 리소스의 **IAM (Access Control** ) 설정을 표시 하 고 다음 지침에 따라 역할 할당을 관리 합니다.

1. Azure AD 보안 주체에 대 한 액세스 권한을 부여 하려면 적절 한 Azure Storage Azure 역할을 할당 합니다.

1. Azure AD 자격 증명을 사용 하 여 Azure Portal를 통해 컨테이너 또는 큐에 액세스 해야 하는 사용자에 게 Azure Resource Manager [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당 합니다. 

다음 섹션에서는 이러한 각 단계에 대해 자세히 설명 합니다.

> [!NOTE]
> Azure Storage 계정 소유자인 경우 자동으로 데이터에 액세스할 수 있는 권한이 할당되지 않습니다. Azure Storage에 대 한 Azure 역할을 명시적으로 할당 해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.
>
> 저장소 계정에 계층 네임 스페이스가 사용 하도록 설정 된 경우 컨테이너 또는 큐에 범위가 지정 된 역할을 할당할 수 없습니다.

### <a name="assign-an-azure-built-in-role"></a>Azure 기본 제공 역할 할당

보안 주체에 역할을 할당 하기 전에 부여 하는 사용 권한의 범위를 고려해 야 합니다. [리소스 범위 결정](#determine-resource-scope) 섹션을 검토 하 여 적절 한 범위를 결정 합니다.

여기에 표시된 프로시저는 컨테이너로 범위가 지정된 역할을 할당하지만 큐로 범위가 지정된 역할을 할당하기 위해 동일한 단계를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동 하 여 계정에 대 한 **개요** 를 표시 합니다.
1. 서비스 아래에서 **Blob**을 선택합니다.
1. 역할을 할당하려는 컨테이너를 찾아 컨테이너의 설정을 표시합니다.
1. **액세스 제어 (IAM)** 를 선택 하 여 컨테이너에 대 한 액세스 제어 설정을 표시 합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.

    ![컨테이너 액세스 제어 설정을 보여 주는 스크린샷](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. **역할 할당 추가** 단추를 클릭하여 새 역할을 추가합니다.
1. **역할 할당 추가** 창에서 할당 하려는 Azure Storage 역할을 선택 합니다. 그런 다음 검색 하 여 해당 역할을 할당 하려는 보안 주체를 찾습니다.

    ![Azure 역할을 할당 하는 방법을 보여 주는 스크린샷](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. **저장**을 클릭합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 추가된 사용자에게 이제 *sample-container*라는 컨테이너의 데이터에 대한 읽기 권한이 있음을 보여줍니다.

    ![역할에 할당 된 사용자 목록을 보여 주는 스크린샷](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

비슷한 단계를 수행 하 여 저장소 계정, 리소스 그룹 또는 구독에 범위가 지정 된 역할을 할당할 수 있습니다.

### <a name="assign-the-reader-role-for-portal-access"></a>포털 액세스에 대 한 읽기 권한자 역할 할당

Azure Storage에 대 한 기본 제공 또는 사용자 지정 역할을 보안 주체에 할당 하면 해당 보안 주체에 게 저장소 계정의 데이터에 대 한 작업을 수행할 수 있는 권한을 부여 하는 것입니다. 기본 제공 **데이터 판독기** 역할은 컨테이너 또는 큐에 있는 데이터에 대 한 읽기 권한을 제공 하 고, 기본 제공 **데이터 참가자** 역할은 컨테이너 또는 큐에 대 한 읽기, 쓰기 및 삭제 권한을 제공 합니다. 사용 권한은 지정 된 리소스로 범위가 지정 됩니다.  
예를 들어 **sample 컨테이너**라는 컨테이너 수준에서 사용자 mary에 게 **저장소 Blob 데이터 참가자** 역할을 할당 하는 경우 mary에 게 해당 컨테이너의 모든 blob에 대 한 읽기, 쓰기 및 삭제 권한이 부여 됩니다.

그러나 Mary가 Azure Portal blob을 보려는 경우에는 **저장소 Blob 데이터 참가자** 역할 자체에서 포털을 통해 blob로 이동 하는 데 필요한 권한이 제공 되지 않습니다. 포털을 탐색 하 고 여기에 표시 되는 다른 리소스를 보려면 추가 Azure AD 권한이 필요 합니다.

사용자가 Azure Portal의 blob에 액세스할 수 있어야 하는 경우 해당 사용자에 게 저장소 계정 이상의 수준에서 추가 Azure 역할인 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당 합니다. **읽기 권한자** 역할은 사용자가 저장소 계정 리소스를 볼 수 있지만 수정할 수는 없도록 허용 하는 Azure Resource Manager 역할입니다. Azure Storage 데이터에 대 한 읽기 권한은 제공 하지 않고 계정 관리 리소스만 제공 합니다.

사용자가 Azure Portal에서 blob에 액세스할 수 있도록 다음 단계를 수행 하 여 **판독기** 역할을 할당 합니다. 이 예제에서 할당의 범위는 저장소 계정입니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. **액세스 제어(IAM)** 를 선택하여 스토리지 계정에 대한 액세스 제어 설정을 표시합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
1. **역할 할당 추가** 창에서 **판독기** 역할을 선택 합니다. 
1. **액세스 할당** 필드에서 **Azure AD 사용자, 그룹 또는 서비스 주체**를 선택합니다.
1. 역할을 할당 하려는 보안 주체를 검색 하 여 검색 합니다.
1. 역할 할당을 저장합니다.

**판독기** 역할 할당은 Azure Portal를 사용 하 여 blob 또는 큐에 액세스 해야 하는 사용자에 게 필요 합니다.

> [!IMPORTANT]
> Azure Portal Storage 탐색기의 미리 보기 버전은 Azure AD 자격 증명을 사용 하 여 blob 또는 큐 데이터를 확인 하 고 수정 하는 것을 지원 하지 않습니다. Azure Portal Storage 탐색기는 항상 계정 키를 사용 하 여 데이터에 액세스 합니다. Azure Portal에서 Storage 탐색기를 사용 하려면 **Microsoft. Storage/storageAccounts/listkeys/action**이 포함 된 역할이 할당 되어야 합니다.

## <a name="next-steps"></a>다음 단계

- 저장소 리소스의 Azure 역할에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 azure blob 및 큐에 대 한 액세스 인증](storage-auth-aad.md)을 참조 하세요. 
- RBAC에 대 한 자세한 내용은 [azure 역할 기반 액세스 제어 (AZURE RBAC) 란?](../../role-based-access-control/overview.md)을 참조 하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 Azure 역할 할당을 할당 하 고 관리 하는 방법을 알아보려면 다음 문서를 참조 하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-rest.md)
- 스토리지 애플리케이션 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [Azure Storage 애플리케이션에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
