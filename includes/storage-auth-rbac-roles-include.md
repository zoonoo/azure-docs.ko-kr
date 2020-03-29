---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279367"
---
Azure는 Azure AD 및 OAuth를 사용하여 Blob 및 큐 데이터에 대한 액세스를 승인하기 위한 다음 기본 제공 RBAC 역할을 제공합니다.

- [저장소 Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake 저장소 Gen2에 대한 소유권을 설정하고 POSIX 액세스 제어를 관리하는 데 사용합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.
- [저장소 Blob 데이터 참여자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): 읽기/쓰기/삭제 권한을 Blob 저장소 리소스에 부여하는 데 사용합니다.
- [저장소 Blob 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob 저장소 리소스에 읽기 전용 권한을 부여하는 데 사용합니다.
- [저장소 큐 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure 큐에 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [저장소 큐 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure 큐에 읽기 전용 권한을 부여하는 데 사용합니다.
- [저장소 큐 데이터 메시지 프로세서](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Azure Storage 큐의 메시지에 대한 엿보기, 검색 및 삭제 권한을 부여하는 데 사용합니다.
- [저장소 큐 데이터 메시지 보낸 자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Azure Storage 큐의 메시지에 추가 권한을 부여하는 데 사용합니다.

> [!NOTE]
> RBAC 역할 할당이 전파되는 데 최대 5분이 걸릴 수 있습니다.
>
> 데이터 액세스에 대해 명시적으로 정의된 역할만 보안 주체가 Blob 또는 큐 데이터에 액세스할 수 있도록 허용합니다. **소유자,** **기여자**및 **저장소 계정 기여자와** 같은 역할은 보안 주체가 저장소 계정을 관리할 수 있도록 허용하지만 해당 계정 내의 Blob 또는 큐 데이터에 대한 액세스를 제공하지는 않습니다.
>
> Azure 포털의 Blob 또는 큐 데이터에 대한 액세스는 Azure AD 계정 또는 저장소 계정 액세스 키를 사용하여 권한을 부여할 수 있습니다. 자세한 내용은 [Azure 포털 사용을 사용하여 Blob 또는 큐 데이터에 액세스합니다.](../articles/storage/common/storage-access-blobs-queues-portal.md)
