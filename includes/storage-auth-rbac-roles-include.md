---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66115573"
---
Azure는 Azure AD를 사용 하 여 blob 및 큐 데이터에 대 한 액세스 권한을 부여 하는 것에 대 한 다음 기본 제공 RBAC 역할을 제공 하 고 OAuth:

- [저장소 Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake Storage Gen2(미리 보기)에 대한 소유권을 설정하고 POSIX 액세스 제어를 관리하는 데 사용합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.
- [Storage Blob 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob 스토리지 리소스에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage Blob 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob 스토리지 리소스에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure 큐에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure 큐에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 메시지 프로세서](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Azure Storage 큐의 메시지에 대 한 삭제 권한을 부여 피킹, 검색를 사용 합니다.
- [Storage 큐 데이터 메시지 보낸 사람](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): 부여를 사용 하 여 Azure Storage 큐의 메시지에 사용 권한을 추가 합니다.

> [!NOTE]
> RBAC 역할 할당이 전파 하는 데 최대 5 분을 사용할 수 있는 점을 염두에 두십시오.
