---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218093"
---
Azure는 저장소 데이터에 액세스 하기 위한 다음과 같은 기본 제공 RBAC 역할을 제공합니다.

- [Storage Blob 데이터 소유자(미리 보기)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Azure Data Lake Storage Gen2(미리 보기)에 대한 소유권을 설정하고 POSIX 액세스 제어를 관리하는 데 사용합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.
- [Storage Blob 데이터 참가자(미리 보기)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Blob 스토리지 리소스에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage Blob 데이터 읽기 권한자(미리 보기)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Blob 스토리지 리소스에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 참가자(미리 보기)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Azure 큐에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 읽기 권한자(미리 보기)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Azure 큐에 대한 읽기 전용 권한을 부여하는 데 사용합니다.

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)를 참조하세요. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 [Access Control에 대 한 사용자 지정 역할 만들기](../articles/role-based-access-control/custom-roles.md)합니다. 
