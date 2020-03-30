---
title: Azure 포털을 사용하여 Blob 또는 큐 데이터에 액세스
titleSuffix: Azure Storage
description: Azure 포털을 사용하여 Blob 또는 큐 데이터에 액세스하면 포털에서 Azure Storage에 대한 요청을 처리합니다. Azure 저장소에 대한 이러한 요청은 Azure AD 계정 또는 저장소 계정 액세스 키를 사용하여 인증및 인증될 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866904"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Azure 포털을 사용하여 Blob 또는 큐 데이터에 액세스

[Azure 포털을](https://portal.azure.com)사용하여 Blob 또는 큐 데이터에 액세스하면 포털에서 Azure Storage에 대한 요청을 처리합니다. Azure 저장소에 대한 요청은 Azure AD 계정 또는 저장소 계정 액세스 키를 사용하여 승인할 수 있습니다. 포털은 사용 중인 메서드를 나타내며 적절한 사용 권한이 있는 경우 둘 사이를 전환할 수 있습니다.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Blob 또는 큐 데이터에 액세스하는 데 필요한 권한

Azure 포털에서 Blob 또는 큐 데이터에 대한 액세스 권한을 부여하려는 방법에 따라 특정 권한이 필요합니다. 대부분의 경우 이러한 권한은 RBAC(역할 기반 액세스 제어)를 통해 제공됩니다. RBAC에 대한 자세한 내용은 [RBAC(역할 기반 액세스 제어)란 무엇입니까?](../../role-based-access-control/overview.md)

### <a name="account-access-key"></a>계정 액세스 키

계정 액세스 키를 사용하여 Blob 및 큐 데이터에 액세스하려면 RBAC 작업 **Microsoft.Storage/StorageAccounts/list키/작업을**포함하는 RBAC 역할이 할당되어 있어야 합니다. 이 RBAC 역할은 기본 제공 또는 사용자 지정 역할일 수 있습니다. **Microsoft.Storage/Storage계정/목록 키/작업을** 지원하는 기본 제공 역할은 다음과 같습니다.

- Azure 리소스 관리자 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할
- Azure 리소스 관리자 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할
- [저장소 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 역할

Azure 포털에서 Blob 또는 큐 데이터에 액세스하려고 하면 포털이 먼저 **Microsoft.Storage/StorageAccounts/listkeys/action을**사용하여 역할이 할당되었는지 여부를 확인합니다. 이 작업으로 역할이 할당된 경우 포털은 Blob 및 큐 데이터에 액세스하기 위해 계정 키를 사용합니다. 이 작업으로 역할이 할당되지 않은 경우 포털은 Azure AD 계정을 사용하여 데이터에 액세스하려고 시도합니다.

> [!NOTE]
> 클래식 구독 관리자 역할 서비스 관리자 및 공동 관리자에는 Azure 리소스 관리자 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할과 동일합니다. **소유자** 역할에는 **Microsoft.Storage/StorageAccounts/list키/작업을**비롯한 모든 작업이 포함되므로 이러한 관리 역할 중 하나를 가진 사용자는 계정 키를 사용하여 Blob 및 큐 데이터에 액세스할 수도 있습니다. 자세한 내용은 [클래식 구독 관리자 역할을](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)참조하십시오.

### <a name="azure-ad-account"></a>Azure AD 계정

Azure AD 계정을 사용하여 Azure 포털의 Blob 또는 큐 데이터에 액세스하려면 다음 두 명령문이 모두 해당되어야 합니다.

- 최소한 저장소 계정 수준 이상으로 범위가 지정된 Azure 리소스 관리자 [리더](../../role-based-access-control/built-in-roles.md#reader) 역할이 할당되었습니다. **Reader** 역할은 가장 제한된 권한을 부여하지만 저장소 계정 관리 리소스에 대한 액세스를 부여하는 다른 Azure Resource Manager 역할도 허용됩니다.
- Blob 또는 큐 데이터에 대한 액세스를 제공하는 기본 제공 또는 사용자 지정 역할이 할당되었습니다.

사용자가 Azure 포털에서 저장소 계정 관리 리소스를 보고 탐색할 수 있도록 **Reader** 역할 할당 또는 다른 Azure 리소스 관리자 역할 할당이 필요합니다. Blob 또는 큐 데이터에 대한 액세스 권한을 부여하는 RBAC 역할은 저장소 계정 관리 리소스에 대한 액세스 권한을 부여하지 않습니다. 포털에서 Blob 또는 큐 데이터에 액세스하려면 사용자가 저장소 계정 리소스를 탐색할 수 있는 권한이 필요합니다. 이 요구 사항에 대한 자세한 내용은 [포털 액세스에 대한 Reader 역할 할당을](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)참조하십시오.

Blob 또는 큐 데이터에 대한 액세스를 지원하는 기본 제공 역할은 다음과 같습니다.

- [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure 데이터 레이크 저장소 Gen2에 대한 POSIX 액세스 제어용입니다.
- [저장소 Blob 데이터 참여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob에 대한 읽기/쓰기/삭제 권한입니다.
- [저장소 Blob 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob에 대한 읽기 전용 권한입니다.
- [저장소 큐 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): 큐에 대한 사용 권한을 읽기/쓰기/삭제합니다.
- [저장소 큐 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): 큐에 대한 읽기 전용 권한입니다.
    
사용자 지정 역할은 기본 제공 역할에서 제공하는 동일한 사용 권한의 다양한 조합을 지원할 수 있습니다. 사용자 지정 RBAC 역할 만들기에 대한 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할](../../role-based-access-control/custom-roles.md) 및 Azure [리소스에 대한 역할 정의 이해를 참조합니다.](../../role-based-access-control/role-definitions.md)

클래식 구독 관리자 역할이 있는 리스팅 큐는 지원되지 않습니다. 큐를 나열하려면 사용자가 Azure 리소스 관리자 **리더** 역할, **저장소 큐 데이터 판독기** 역할 또는 **저장소 큐 데이터 기여자** 역할에 할당되어 있어야 합니다.

> [!IMPORTANT]
> Azure 포털의 저장소 탐색기 미리 보기 버전은 Azure AD 자격 증명을 사용하여 Blob 또는 큐 데이터를 보고 수정하는 것을 지원하지 않습니다. Azure 포털의 저장소 탐색기는 항상 계정 키를 사용하여 데이터에 액세스합니다. Azure 포털에서 저장소 탐색기를 사용하려면 **Microsoft.Storage/StorageAccounts/listkeys/action을**포함하는 역할을 할당받아야 합니다.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>포털의 Blob 또는 큐로 이동

포털에서 Blob 또는 큐 데이터를 보려면 저장소 계정의 **개요로** 이동한 다음 **Blob** 또는 Queue에 대한 링크를 **클릭합니다.** 또는 메뉴의 **Blob 서비스** 및 **Queue 서비스** 섹션으로 이동할 수 있습니다. 

![Azure 포털에서 Blob 또는 큐 데이터로 이동](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>현재 인증 방법 결정

컨테이너 또는 큐로 이동하면 Azure 포털은 현재 계정 액세스 키를 사용하고 있는지 또는 Azure AD 계정을 인증할지 여부를 나타냅니다.

이 섹션의 예제에서는 컨테이너 및 해당 Blob에 액세스하는 것을 보여 주지만 큐와 해당 메시지에 액세스하거나 큐를 나열할 때 포털에 동일한 메시지가 표시됩니다.

### <a name="account-access-key"></a>계정 액세스 키

계정 액세스 키를 사용하여 인증하는 경우 포털에서 인증 방법으로 지정된 **액세스 키가** 표시됩니다.

![현재 계정 키로 컨테이너 데이터에 액세스](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Azure AD 계정을 사용하여 전환하려면 이미지에서 강조 표시된 링크를 클릭합니다. 사용자에게 할당된 RBAC 역할을 통해 적절한 권한이 있는 경우 계속 진행할 수 있습니다. 그러나 올바른 사용 권한이 없는 경우 다음과 같은 오류 메시지가 표시됩니다.

![Azure AD 계정이 액세스를 지원하지 않는 경우 오류가 표시됩니다.](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Azure AD 계정에 볼 수 있는 권한이 없는 경우 목록에 Blob이 표시되지 않습니다. 스위치를 클릭하여 키 링크에 **액세스하여** 인증에 대한 액세스 키를 다시 사용합니다.

### <a name="azure-ad-account"></a>Azure AD 계정

Azure AD 계정을 사용하여 인증하는 경우 포털에서 인증 방법으로 지정된 **Azure AD 사용자 계정이** 표시됩니다.

![현재 Azure AD 계정으로 컨테이너 데이터에 액세스](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

계정 액세스 키를 사용하여 전환하려면 이미지에서 강조 표시된 링크를 클릭합니다. 계정 키에 액세스할 수 있는 경우 계속 진행할 수 있습니다. 그러나 계정 키에 액세스할 수 없는 경우 다음과 같은 오류 메시지가 표시됩니다.

![계정 키에 액세스할 수 없는 경우 오류가 표시됩니다.](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

계정 키에 액세스할 수 없는 경우 목록에 Blob이 나타나지 않습니다. **Azure AD 사용자 계정으로 전환** 링크를 클릭하여 Azure AD 계정을 다시 사용하여 인증을 다시 사용합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스를 인증합니다.](storage-auth-aad.md)
- [Azure 포털에서 RBAC를 사용하여 Azure 컨테이너 및 큐에 대한 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [PowerShell에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)
