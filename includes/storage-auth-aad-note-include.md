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
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372668"
---
> [!NOTE]
> - RBAC 역할 할당에 전파 하는 데 최대 2 분 정도 걸릴 수 있습니다.
>
> OAuth 토큰을 사용하여 BLOB 및 큐 작업에 권한을 부여하려면 HTTPS를 사용해야 합니다.
>
> - Azure Portal은 이제 미리 보기 릴리스의 일환으로 Azure AD 자격 증명을 사용하여 BLOB 및 큐 데이터 읽기 및 쓰기를 지원합니다. Azure portal에서 blob 및 큐 데이터에 액세스 하려면 사용자 할당 해야 Azure Resource Manager **판독기** blob 또는 큐 액세스에 대 한 적절 한 역할 외에도 역할을 합니다. 자세한 내용은 [Azure 컨테이너 및 Azure portal에서 RBAC 사용 하 여 큐 액세스 권한을 부여할](../articles/storage/common/storage-auth-aad-rbac.md)합니다. 
> 
> - [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)는 현재 스토리지 계정 키를 사용하여 Blob 및 큐 데이터에 액세스합니다. 키를 사용할 수 없는 경우 Azure AD 권한 부여는 blob에 대 한 액세스에 사용 됩니다. Azure AD 권한 부여 큐에 대 한 현재 지원 되지 않습니다. 
>
> - Azure Files는 도메인에 가입된 VM(미리 보기)에 대해 SMB를 통한 Azure AD 인증을 지원합니다. Azure Files용 SMB를 통한 Azure AD 사용에 대해 알아보려면 [Azure Files용 SMB(Preview)를 통한 Azure Active Directory 인증의 개요](../articles/storage/files/storage-files-active-directory-overview.md)를 참조하세요.