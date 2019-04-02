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
ms.openlocfilehash: 9b8418dba12748915666c6a91ee65b37c0f59ace
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807770"
---
Azure는 저장소 데이터에 액세스 하기 위한 다음과 같은 기본 제공 RBAC 역할을 제공합니다.

- [저장소 Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake Storage Gen2(미리 보기)에 대한 소유권을 설정하고 POSIX 액세스 제어를 관리하는 데 사용합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.
- [Storage Blob 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob 스토리지 리소스에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage Blob 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob 스토리지 리소스에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 기여자](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure 큐에 대한 읽기/쓰기/삭제 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 판독기](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure 큐에 대한 읽기 전용 권한을 부여하는 데 사용합니다.
- [Storage 큐 데이터 메시지 프로세서](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Azure Storage 큐의 메시지에 대 한 삭제 권한을 부여 피킹, 검색를 사용 합니다.
- [Storage 큐 데이터 메시지 보낸 사람](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): 부여를 사용 하 여 Azure Storage 큐의 메시지에 사용 권한을 추가 합니다.

> [!IMPORTANT]
> RBAC 역할 할당에 전파 하는 데 최대 5 분 정도 걸릴 수 있습니다.

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)를 참조하세요. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 [Access Control에 대 한 사용자 지정 역할 만들기](../articles/role-based-access-control/custom-roles.md)합니다. 
