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
ms.openlocfilehash: 0182df40a4e7815560a85e60fe9062ccd8001c18
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978702"
---
> [!NOTE]
> - Blob 및 큐에 대한 Azure AD 인증 미리 보기는 비프로덕션 용도로만 사용됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다. 사용자 시나리오에서 Azure AD 인증이 아직 지원되지 않는 경우, 애플리케이션에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다.
>
> - 미리 보기 동안 RBAC 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.
>
> - OAuth 토큰을 사용하여 BLOB 및 큐 작업에 권한을 부여하려면 HTTPS를 사용해야 합니다.
>
> - Azure Portal은 이제 미리 보기 릴리스의 일환으로 Azure AD 자격 증명을 사용하여 BLOB 및 큐 데이터 읽기 및 쓰기를 지원합니다.
> 
> - [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)는 현재 저장소 계정 키를 사용하여 Blob 및 큐 데이터에 액세스합니다. OAuth 액세스는 Blob에 대해 지원됩니다.
>
> - Azure Files는 도메인에 가입된 VM(미리 보기)에 대해 SMB를 통한 Azure AD 인증을 지원합니다. Azure Files용 SMB를 통한 Azure AD 사용에 대해 알아보려면 [Azure Files용 SMB(Preview)를 통한 Azure Active Directory 인증의 개요](../articles/storage/files/storage-files-active-directory-overview.md)를 참조하세요.