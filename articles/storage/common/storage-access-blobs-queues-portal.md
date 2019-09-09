---
title: Azure Portal를 사용 하 여 blob 또는 큐 데이터에 액세스 합니다. Azure Storage
description: Azure Portal를 사용 하 여 blob 또는 큐 데이터에 액세스 하는 경우 포털은 내부적으로 Azure Storage에 대 한 요청을 수행 합니다. 이러한 Azure Storage에 대 한 요청은 Azure AD 계정 또는 저장소 계정 액세스 키를 사용 하 여 인증 하 고 권한을 부여할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8d19724883e6c2b3630de6100b1b4ca9fd9250a1
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813362"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Azure Portal를 사용 하 여 blob 또는 큐 데이터에 액세스

[Azure Portal](https://portal.azure.com)를 사용 하 여 blob 또는 큐 데이터에 액세스 하는 경우 포털은 내부적으로 Azure Storage에 대 한 요청을 수행 합니다. Azure AD 계정 또는 저장소 계정 액세스 키 중 하나를 사용 하 여 Azure Storage에 대 한 요청을 인증할 수 있습니다. 포털은 사용 중인 방법을 나타내며, 적절 한 사용 권한이 있는 경우 둘 사이를 전환할 수 있습니다.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Blob 또는 큐 데이터에 액세스 하는 데 필요한 권한

Azure Portal에서 blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 방법에 따라 특정 권한이 필요 합니다. 대부분의 경우 이러한 권한은 RBAC (역할 기반 액세스 제어)를 통해 제공 됩니다. RBAC에 대 한 자세한 내용은 [rbac (역할 기반 액세스 제어) 란?](../../role-based-access-control/overview.md)을 참조 하세요.

### <a name="account-access-key"></a>계정 액세스 키

계정 액세스 키를 사용 하 여 blob 및 큐 데이터에 액세스 하려면 RBAC 작업을 포함 하는 RBAC 역할을 할당 해야 합니다 **. Storage/storageAccounts/listkeys/action**. 이 RBAC 역할은 기본 제공 또는 사용자 지정 역할 일 수 있습니다. **Microsoft. Storage/storageAccounts/listkeys/action** 을 지 원하는 기본 제공 역할은 다음과 같습니다.

- Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할
- Azure Resource Manager [참가자](../../role-based-access-control/built-in-roles.md#contributor) 역할
- [저장소 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 역할

Azure Portal에서 blob 또는 큐 데이터에 액세스 하려고 하면 포털은 먼저 **Microsoft. Storage/storageAccounts/listkeys/action**을 사용 하 여 역할이 할당 되었는지 여부를 확인 합니다. 이 작업을 사용 하 여 역할을 할당 한 경우 포털은 blob 및 큐 데이터에 액세스 하기 위해 계정 키를 사용 합니다. 이 작업을 사용 하 여 역할을 할당 하지 않은 경우 포털은 Azure AD 계정을 사용 하 여 데이터에 액세스 하려고 시도 합니다.

> [!NOTE]
> 클래식 구독 관리자 역할 서비스 관리자와 공동 관리자는 Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할에 해당 하는 항목을 포함 합니다. **Owner** 역할은 **Microsoft. Storage/storageaccounts/listkeys/action**을 비롯 한 모든 동작을 포함 하므로 이러한 관리 역할 중 하나가 있는 사용자는 계정 키를 사용 하 여 blob 및 큐 데이터에 액세스할 수도 있습니다. 자세한 내용은 [클래식 구독 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)을 참조 하세요.

### <a name="azure-ad-account"></a>Azure AD 계정

Azure AD 계정을 사용 하 여 Azure Portal에서 blob 또는 큐 데이터에 액세스 하려면 다음 두 문이 모두 true 여야 합니다.

- 최소한 저장소 계정 수준으로 범위가 지정 된 Azure Resource Manager [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할이 할당 되었습니다. **읽기 권한자** 역할은 가장 제한 된 권한을 부여 하지만 저장소 계정 관리 리소스에 대 한 액세스 권한을 부여 하는 다른 Azure Resource Manager 역할도 허용 됩니다.
- Blob 또는 큐 데이터에 대 한 액세스를 제공 하는 기본 제공 또는 사용자 지정 역할이 할당 되었습니다.

사용자가 Azure Portal에서 저장소 계정 관리 리소스를 보고 탐색할 수 있도록 **읽기** 역할 할당 또는 다른 Azure Resource Manager 역할 할당이 필요 합니다. Blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 RBAC 역할은 저장소 계정 관리 리소스에 대 한 액세스 권한을 부여 하지 않습니다. 포털에서 blob 또는 큐 데이터에 액세스 하려면 저장소 계정 리소스를 탐색할 수 있는 권한이 사용자에 게 필요 합니다. 이 요구 사항에 대 한 자세한 내용은 [포털 액세스를 위한 읽기 권한자 역할 할당](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)을 참조 하세요.

Blob 또는 큐 데이터에 대 한 액세스를 지 원하는 기본 제공 역할은 다음과 같습니다.

- [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake Storage Gen2에 대 한 POSIX 액세스 제어
- [저장소 Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob에 대 한 읽기/쓰기/삭제 권한입니다.
- [저장소 Blob 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob에 대 한 읽기 전용 권한입니다.
- [저장소 큐 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): 큐에 대 한 읽기/쓰기/삭제 권한입니다.
- [저장소 큐 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): 큐에 대 한 읽기 전용 권한입니다.
    
사용자 지정 역할은 기본 제공 역할에서 제공 하는 것과 동일한 권한의 여러 조합을 지원할 수 있습니다. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 [azure 리소스에 대 한 사용자 지정 역할](../../role-based-access-control/custom-roles.md) 및 [azure 리소스에 대 한 역할 정의 이해](../../role-based-access-control/role-definitions.md)를 참조 하세요.

> [!NOTE]
> 클래식 구독 관리자 역할을 사용 하 여 큐를 나열 하는 것은 지원 되지 않습니다. 큐를 나열 하려면 사용자가 Azure Resource Manager **읽기 권한자** 역할, **저장소 큐 데이터 판독기** 역할 또는 **저장소 큐 데이터 참가자** 역할에 할당 되어 있어야 합니다.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>포털에서 blob 또는 큐로 이동 합니다.

포털에서 blob 또는 큐 데이터를 보려면 저장소 계정에 대 한 **개요** 로 이동 하 고 **blob** 또는 **큐**에 대 한 링크를 클릭 합니다. 또는 메뉴의 **Blob service** 및 **큐 서비스** 섹션으로 이동할 수 있습니다. 

![Azure Portal에서 blob 또는 큐 데이터로 이동 합니다.](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>현재 인증 방법 결정

컨테이너 또는 큐로 이동 하는 경우 현재 계정 액세스 키를 사용 하 고 있는지, 아니면 Azure AD 계정을 사용 하 여 인증 하 고 있는지를 표시 Azure Portal.

이 섹션의 예제에서는 컨테이너 및 해당 blob에 액세스 하는 방법을 보여 주지만 큐 및 해당 메시지에 액세스 하거나 큐를 나열 하는 경우에는 포털에서 동일한 메시지를 표시 합니다.

### <a name="account-access-key"></a>계정 액세스 키

계정 액세스 키를 사용 하 여 인증 하는 경우 포털에서 인증 방법으로 지정 된 **액세스 키** 가 표시 됩니다.

![현재 계정 키를 사용 하 여 컨테이너 데이터에 액세스](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Azure AD 계정을 사용 하도록 전환 하려면 이미지에 강조 표시 된 링크를 클릭 합니다. 할당 된 RBAC 역할을 통해 적절 한 권한이 있는 경우 계속 진행할 수 있습니다. 그러나 적절 한 권한이 없으면 다음과 같은 오류 메시지가 표시 됩니다.

![Azure AD 계정이 액세스를 지원 하지 않는 경우 표시 되는 오류](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Azure AD 계정에이를 볼 수 있는 권한이 없는 경우 목록에 blob이 표시 되지 않습니다. 액세스 키를 사용 하 여 인증 **키로 전환** 링크를 클릭 하 여 인증에 대 한 액세스 키를 다시 사용 합니다.

### <a name="azure-ad-account"></a>Azure AD 계정

Azure AD 계정을 사용 하 여 인증 하는 경우 포털에서 인증 방법으로 지정 된 **AZURE Ad 사용자 계정이** 표시 됩니다.

![현재 Azure AD 계정을 사용 하 여 컨테이너 데이터에 액세스](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

계정 액세스 키를 사용 하도록 전환 하려면 이미지에 강조 표시 된 링크를 클릭 합니다. 계정 키에 대 한 액세스 권한이 있는 경우 계속 진행할 수 있습니다. 그러나 계정 키에 대 한 액세스 권한이 없는 경우 다음과 같은 오류 메시지가 표시 됩니다.

![계정 키에 대 한 액세스 권한이 없는 경우 표시 되는 오류](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

계정 키에 대 한 액세스 권한이 없는 경우 목록에 blob이 표시 되지 않습니다. Azure ad **사용자 계정으로 전환** 링크를 클릭 하 여 인증에 azure ad 계정을 다시 사용 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용 하 여 Azure blob 및 큐에 대 한 액세스 인증](storage-auth-aad.md)
- [Azure Portal에서 RBAC를 사용 하 여 Azure 컨테이너 및 큐에 대 한 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [PowerShell에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)
