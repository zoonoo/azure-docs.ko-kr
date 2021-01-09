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
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052727"
---
Azure 파일 공유에 대 한 SMB 다중 채널에는 현재 다음과 같은 제한 사항이 있습니다.
- 로컬 중복 FileStorage 계정에만 사용할 수 있습니다.
- Windows 클라이언트에만 지원 됩니다. 
- 채널의 최대 수는 4 개입니다.
- SMB 다이렉트는 지원 되지 않습니다.
- 저장소 계정에 대 한 개인 끝점은 지원 되지 않습니다.
- Azure Files에 대해 온-프레미스 Active Directory Domain Services (AD DS) 또는 Azure AD DS [id 기반 인증](../articles/storage/files/storage-files-active-directory-overview.md) 을 사용 하는 저장소 계정의 경우 SMB 클라이언트는 Windows 파일 탐색기를 사용 하 여 디렉터리 및 파일에 대 한 NTFS 권한을 구성할 수 없습니다.
    - 대신 Windows [icacls](/windows-server/administration/windows-commands/icacls) 도구나 [Set ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) 명령을 사용 하 여 사용 권한을 구성 합니다.

