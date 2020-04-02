---
title: Azure 포털을 사용하여 데이터 액세스에 RBAC 역할을 할당합니다.
titleSuffix: Azure Storage
description: AZURE 포털을 사용하여 RBAC(역할 기반 액세스 제어)를 사용하여 Azure Active Directory 보안 보안 보안 주체에 권한을 할당하는 방법을 알아봅니다. Azure Storage는 Azure AD를 통한 인증을 위해 기본 제공 및 사용자 지정 RBAC 역할을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d224bd9e9e7b1f8fc9eb45d85e78811d8642fc78
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519556"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Azure 포털을 사용하여 Blob 및 큐 데이터에 대한 액세스를 위해 RBAC 역할을 할당합니다.

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 Blob 또는 큐 데이터에 액세스하는 데 사용되는 일반적인 사용 권한 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의합니다.

RBAC 역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 Azure 리소스에 대한 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [관리되는 ID일](../../active-directory/managed-identities-azure-resources/overview.md)수 있습니다.

이 문서에서는 Azure 포털을 사용하여 RBAC 역할을 할당하는 방법을 설명합니다. Azure 포털은 RBAC 역할을 할당하고 저장소 리소스에 대한 액세스를 관리하기 위한 간단한 인터페이스를 제공합니다. Azure 명령줄 도구 또는 Azure 저장소 관리 API를 사용하여 Blob 및 큐 리소스에 RBAC 역할을 할당할 수도 있습니다. 저장소 리소스에 대한 RBAC 역할에 대한 자세한 내용은 Azure Active Directory 를 [사용하여 Azure Blob 및 큐에 대한 인증 액세스를](storage-auth-aad.md)참조하세요.

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 및 큐의 RBAC 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>리소스 범위 결정

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure 포털을 사용하여 RBAC 역할 할당

역할 할당에 대한 적절한 범위를 결정한 후 Azure Portal에서 해당 리소스로 이동합니다. 리소스에 대한 **IAM(액세스 제어)** 설정을 표시하고 다음 지침을 따라 역할 할당을 관리합니다.

1. Assign the appropriate Azure Storage RBAC role to grant access to an Azure AD security principal.

1. Azure AD 자격 증명을 사용하여 Azure 포털을 통해 컨테이너 또는 큐에 액세스해야 하는 사용자에게 Azure 리소스 관리자 [리더](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당합니다. 

다음 섹션에서는 이러한 각 단계를 자세히 설명합니다.

> [!NOTE]
> Azure Storage 계정 소유자인 경우 자동으로 데이터에 액세스할 수 있는 권한이 할당되지 않습니다. Azure Storage에 RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.
>
> 저장소 계정에 계층적 네임스페이스가 활성화되어 있는 경우 컨테이너 또는 큐에 범위가 지정된 역할을 할당할 수 없습니다.

### <a name="assign-a-built-in-rbac-role"></a>기본 제공 RBAC 역할 할당

보안 주체에 역할을 할당하기 전에 부여할 권한의 범위를 고려해야 합니다. 리소스 [범위 결정](#determine-resource-scope) 섹션을 검토하여 적절한 범위를 결정합니다.

여기에 표시된 프로시저는 컨테이너로 범위가 지정된 역할을 할당하지만 큐로 범위가 지정된 역할을 할당하기 위해 동일한 단계를 수행할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)저장소 계정으로 이동하여 계정에 대한 **개요를** 표시합니다.
1. 서비스 아래에서 **Blob**을 선택합니다.
1. 역할을 할당하려는 컨테이너를 찾아 컨테이너의 설정을 표시합니다.
1. 컨테이너에 대한 액세스 제어 설정을 표시하려면 **IAM(액세스 제어)을** 선택합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.

    ![컨테이너 액세스 제어 설정을 보여주는 스크린샷](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. **역할 할당 추가** 단추를 클릭하여 새 역할을 추가합니다.
1. 역할 **할당 추가** 창에서 할당할 Azure 저장소 역할을 선택합니다. 그런 다음 검색하여 해당 역할을 할당할 보안 주체를 찾습니다.

    ![RBAC 역할을 할당하는 방법을 보여주는 스크린샷](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. **저장**을 클릭합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 추가된 사용자에게 이제 *sample-container*라는 컨테이너의 데이터에 대한 읽기 권한이 있음을 보여줍니다.

    ![역할에 할당된 사용자 목록을 보여주는 스크린샷](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

비슷한 단계를 수행하여 저장소 계정, 리소스 그룹 또는 구독에 범위가 지정된 역할을 할당할 수 있습니다.

### <a name="assign-the-reader-role-for-portal-access"></a>포털 액세스에 대한 Reader 역할 할당

Azure Storage에 대한 기본 제공 또는 사용자 지정 역할을 보안 주체에 할당하면 해당 보안 주체에게 저장소 계정의 데이터에 대한 작업을 수행할 수 있는 권한을 부여합니다. 기본 제공 **데이터 판독기** 역할은 컨테이너 또는 큐의 데이터에 대한 읽기 권한을 제공하며, 기본 제공 **데이터 기여자** 역할은 컨테이너 또는 큐에 읽기, 쓰기 및 삭제 권한을 제공합니다. 사용 권한은 지정된 리소스로 범위가 지정됩니다.  
예를 들어 **샘플 컨테이너라는**컨테이너 수준에서 사용자 Mary에 **저장소 Blob 데이터 기여자** 역할을 할당하면 해당 컨테이너의 모든 Blob에 대한 읽기, 쓰기 및 삭제 액세스 권한이 부여됩니다.

그러나 MaryAzure 포털에서 Blob을 보려는 경우 **저장소 Blob 데이터 기여자** 역할 자체는 포털을 통해 Blob을 볼 수 있는 충분한 권한을 제공하지 않습니다. 포털을 탐색하고 볼 수 있는 다른 리소스를 보려면 추가 Azure AD 권한이 필요합니다.

사용자가 Azure Portal에서 Blob에 액세스할 수 있어야 하는 경우 저장소 계정 수준 또는 그 이상의 수준에서 해당 사용자에게 추가 RBAC 역할인 [Reader](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당합니다. **Reader** 역할은 사용자가 저장소 계정 리소스를 볼 수 있지만 수정할 수 없는 Azure 리소스 관리자 역할입니다. Azure Storage의 데이터에 대한 읽기 권한을 제공하지 않고 계정 관리 리소스에만 제공합니다.

사용자가 Azure 포털에서 Blob에 액세스할 수 있도록 **Reader** 역할을 할당하려면 다음 단계를 따릅니다. 이 예제에서는 할당이 저장소 계정으로 범위가 지정됩니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. **액세스 제어(IAM)** 를 선택하여 스토리지 계정에 대한 액세스 제어 설정을 표시합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
1. 역할 **할당 추가** 창에서 **판독기** 역할을 선택합니다. 
1. **액세스 할당** 필드에서 **Azure AD 사용자, 그룹 또는 서비스 주체**를 선택합니다.
1. 역할을 할당할 보안 주체를 찾으려면 검색합니다.
1. 역할 할당을 저장합니다.

**Reader** 역할 할당은 Azure 포털을 사용하여 Blob 또는 큐에 액세스해야 하는 사용자에게만 필요합니다.

> [!IMPORTANT]
> Azure 포털의 저장소 탐색기 미리 보기 버전은 Azure AD 자격 증명을 사용하여 Blob 또는 큐 데이터를 보고 수정하는 것을 지원하지 않습니다. Azure 포털의 저장소 탐색기는 항상 계정 키를 사용하여 데이터에 액세스합니다. Azure 포털에서 저장소 탐색기를 사용하려면 **Microsoft.Storage/StorageAccounts/listkeys/action을**포함하는 역할을 할당받아야 합니다.

## <a name="next-steps"></a>다음 단계

- 저장소 리소스에 대한 RBAC 역할에 대한 자세한 내용은 Azure Active Directory 를 [사용하여 Azure Blob 및 큐에 대한 인증 액세스를](storage-auth-aad.md)참조하세요. 
- RBAC에 대한 자세한 내용은 [RBAC(역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 RBAC 역할 할당을 할당하고 관리하는 방법을 알아보려면 다음과 같은 아티클을 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-rest.md)
- 스토리지 애플리케이션 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [Azure Storage 애플리케이션에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
