---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/27/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b15ea51de1ff4525931242f1360d83c7d4f09cf7
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901914"
---
Azure RBAC는 Azure AD 및 OAuth를 사용하여 BLOB 및 큐 데이터에 대한 액세스 권한을 부여할 수 있도록 여러 Azure 기본 역할을 제공합니다. Azure Storage의 데이터 리소스에 대한 권한을 제공하는 역할의 몇 가지 예는 다음과 같습니다.

- [Storage Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake Storage Gen2에 대한 소유권을 설정하고 POSIX 액세스 제어를 관리하는 데 사용됩니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.
- [Storage Blob 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob 스토리지 리소스에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage Blob 데이터 읽기 권한자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob 스토리지 리소스에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage Blob 위임자](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): 컨테이너 또는 BLOB의 Azure AD 자격 증명으로 서명된 공유 액세스 서명을 만드는 데 사용할 수 있는 사용자 위임 키를 가져옵니다.
- [Storage 큐 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure 큐에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 읽기 권한자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure 큐에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 메시지 처리자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Azure Storage 큐의 메시지에 대한 선택, 검색 및 삭제 권한을 부여하는 데 사용됩니다.
- [Storage 큐 데이터 메시지 보내는 사람](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Azure Storage 큐의 메시지에 대한 추가 권한을 부여하는 데 사용됩니다.

Azure RBAC 역할 및 해당 권한을 나열하는 방법을 알아보려면 [Azure 역할 정의 나열](../articles/role-based-access-control/role-definitions-list.md)을 참조하세요.

> [!IMPORTANT]
> 데이터 액세스에 대해 명시적으로 정의된 역할만 보안 주체가 BLOB 또는 큐 데이터에 액세스할 수 있도록 허용합니다. **소유자**, **기여자** 및 **스토리지 계정 기여자** 같은 기본 제공 역할은 보안 주체가 스토리지 계정을 관리하는 것을 허용하지만, Azure AD를 통해 해당 계정 내의 BLOB 또는 큐 데이터에 액세스할 수 있는 권한은 제공하지 않습니다. 그러나 역할에 **Microsoft.Storage/storageAccounts/listKeys/action** 이 포함되어 있는 경우에는 해당 역할이 할당된 사용자가 계정 액세스 키를 사용하여 공유 키 인증을 통해 스토리지 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 BLOB 또는 큐 데이터에 액세스](../articles/storage/blobs/authorize-data-operations-portal.md)를 참조하세요.

데이터 서비스 및 관리 서비스에 대한 Azure Storage의 Azure 기본 제공 역할에 대한 자세한 내용은 [Azure RBAC에 대한 Azure 기본 제공 역할](../articles/role-based-access-control/built-in-roles.md#storage)의 **스토리지** 섹션을 참조하세요. 또한 Azure에서 권한을 제공하는 여러 유형의 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 역할](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.
