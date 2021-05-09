---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102603373"
---
Azure 파일 공유에 대한 SMB 다중 채널에는 현재 다음과 같은 제한 사항이 있습니다.
- 로컬 중복 FileStorage 계정에서만 사용할 수 있습니다.
- Windows 클라이언트에서만 지원됩니다. 
- 채널의 최대 수는 4개입니다.
- SMB Direct는 지원되지 않습니다.
- 스토리지 계정에 대한 프라이빗 엔드포인트는 지원되지 않습니다.
- 온-프레미스 AD DS(Active Directory Domain Services) 또는 Azure Files에 대해 활성화된 Azure AD DS [ID 기반 인증](../articles/storage/files/storage-files-active-directory-overview.md)을 사용하는 스토리지 계정의 경우 SMB 클라이언트는 Windows 파일 탐색기를 사용하여 디렉터리 및 파일에 대한 NTFS 권한을 구성할 수 없습니다.
    - 대신 Windows [icacls](/windows-server/administration/windows-commands/icacls) 도구 또는 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) 명령을 사용하여 권한을 구성합니다.

