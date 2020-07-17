---
title: 파일 포함
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b49d757067d8ee888bee52f3931c8d48d786c044
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024901"
---
Azure는 Azure AD 및 OAuth를 사용 하 여 blob 및 큐 데이터에 대 한 액세스 권한을 부여 하는 다음과 같은 기본 제공 RBAC 역할을 제공 합니다.

- [저장소 Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): 소유권을 설정 하 고 Azure Data Lake Storage Gen2에 대 한 POSIX 액세스 제어를 관리 하는 데 사용 합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.
- [저장소 Blob 데이터 참가자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): blob storage 리소스에 대 한 읽기/쓰기/삭제 권한을 부여 하는 데 사용 합니다.
- [저장소 Blob 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): blob storage 리소스에 대 한 읽기 전용 권한을 부여 하는 데 사용 합니다.
- [Storage Blob 위임자](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): 컨테이너 또는 Blob에 대 한 Azure AD 자격 증명으로 서명 되는 공유 액세스 서명을 만드는 데 사용할 사용자 위임 키를 가져옵니다.
- [저장소 큐 데이터 참가자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure 큐에 대 한 읽기/쓰기/삭제 권한을 부여 하는 데 사용 합니다.
- [저장소 큐 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure 큐에 읽기 전용 권한을 부여 하는 데 사용 합니다.
- [Storage Queue Data Message Processor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Azure Storage 큐의 메시지에 대 한 피킹 (peeking), 검색 및 삭제 권한을 부여 하는 데 사용 합니다.
- [저장소 큐 데이터 메시지 보낸 사람](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Azure Storage 큐의 메시지에 대 한 추가 권한을 부여 하는 데 사용 합니다.

데이터 액세스에 대해 명시적으로 정의 된 역할만 보안 주체가 blob 또는 큐 데이터에 액세스할 수 있도록 허용 합니다. **소유자**, **참가자**및 **저장소 계정 참가자** 와 같은 기본 제공 역할은 보안 주체가 저장소 계정을 관리 하는 것을 허용 하지만 Azure AD를 통해 해당 계정 내에서 blob 또는 큐 데이터에 대 한 액세스는 제공 하지 않습니다. 그러나 역할이 **Microsoft. Storage/storageAccounts/listKeys/action**을 포함 하는 경우 해당 역할이 할당 된 사용자는 계정 액세스 키를 사용 하 여 공유 키 권한 부여를 통해 저장소 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 blob 또는 큐 데이터에 액세스](../articles/storage/common/storage-access-blobs-queues-portal.md)를 참조 하세요.

데이터 서비스 및 관리 서비스에 대 한 Azure Storage 기본 제공 RBAC 역할에 대 한 자세한 내용은 [AZURE RBAC에 대 한 azure 기본 제공 역할](../articles/role-based-access-control/built-in-roles.md#storage)의 **저장소** 섹션을 참조 하세요. 또한 Azure에서 사용 권한을 제공 하는 다양 한 유형의 역할에 대 한 자세한 내용은 [클래식 구독 관리자 역할, AZURE RBAC 역할 및 AZURE AD 역할](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)을 참조 하세요.

> [!IMPORTANT]
> RBAC 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있습니다.
