---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5d0e919159569e7512b6e7dc0458a4a3e397943b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251985"
---
> [!NOTE]
> - Blob 및 큐에 대한 Azure AD 인증 미리 보기는 비프로덕션 용도로만 사용됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다. 사용자 시나리오에서 Azure AD 인증이 아직 지원되지 않는 경우, 애플리케이션에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다.
>
> - 미리 보기 동안 RBAC 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.
>
> - OAuth 토큰을 사용하여 BLOB 및 큐 작업에 권한을 부여하려면 HTTPS를 사용해야 합니다.
>
> - Azure Portal은 이제 미리 보기 릴리스의 일환으로 Azure AD 자격 증명을 사용하여 BLOB 및 큐 데이터 읽기 및 쓰기를 지원합니다. Azure portal에서 blob 및 큐 데이터에 액세스 하려면 사용자 blob 또는 큐 액세스에 대 한 적절 한 미리 보기 역할 외에도 Azure Resource Manager 판독기 RBAC 역할에 할당 되어야 합니다. 자세한 내용은 [Azure 컨테이너 및 Azure portal (미리 보기)에서 RBAC 사용 하 여 큐 액세스 권한을 부여할](../articles/storage/common/storage-auth-aad-rbac.md)합니다. 
> 
> - [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)는 현재 스토리지 계정 키를 사용하여 Blob 및 큐 데이터에 액세스합니다. OAuth 액세스는 Blob에 대해 지원됩니다.
>
> - Azure Files는 도메인에 가입된 VM(미리 보기)에 대해 SMB를 통한 Azure AD 인증을 지원합니다. Azure Files용 SMB를 통한 Azure AD 사용에 대해 알아보려면 [Azure Files용 SMB(Preview)를 통한 Azure Active Directory 인증의 개요](../articles/storage/files/storage-files-active-directory-overview.md)를 참조하세요.