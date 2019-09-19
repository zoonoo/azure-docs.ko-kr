---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078265"
---
Azure는 Azure AD 및 OAuth를 사용 하 여 blob 및 큐 데이터에 대 한 액세스 권한을 부여 하는 다음과 같은 기본 제공 RBAC 역할을 제공 합니다.

- [저장소 Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake Storage Gen2에 대 한 소유권을 설정 하 고 POSIX 액세스 제어를 관리 하는 데 사용 합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.
- [저장소 Blob 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob 스토리지 리소스에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [저장소 Blob 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob 스토리지 리소스에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [저장소 큐 데이터 참가자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure 큐에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [저장소 큐 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure 큐에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [저장소 큐 데이터 메시지 처리기](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): 를 사용 하 여 Azure Storage 큐의 메시지에 대 한 피킹 (peeking), 검색 및 삭제 권한을 부여할 수 있습니다.
- [저장소 큐 데이터 메시지 보낸 사람](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Azure Storage 큐의 메시지에 대 한 추가 권한을 부여 하는 데 사용 합니다.

> [!NOTE]
> RBAC 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있습니다.
>
> 데이터 액세스에 대해 명시적으로 정의 된 역할만 보안 주체가 blob 또는 큐 데이터에 액세스할 수 있도록 허용 합니다. **소유자**, **참가자**및 **저장소 계정 기여자** 와 같은 역할은 보안 주체가 저장소 계정을 관리 하는 것을 허용 하지만 해당 계정 내의 blob 또는 큐 데이터에 대 한 액세스 권한은 제공 하지 않습니다.
