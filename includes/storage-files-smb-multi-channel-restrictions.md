---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/26/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 04ded4340eef0bfe5bc91ee2e3f2552975df2e05
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065652"
---
Azure 파일 공유에 대한 SMB 다중 채널에는 현재 다음과 같은 제한 사항이 있습니다.
- Windows 클라이언트에서만 지원됩니다. 
- 채널의 최대 수는 4개입니다.
- SMB Direct는 지원되지 않습니다.
- 스토리지 계정에 대한 프라이빗 엔드포인트는 지원되지 않습니다.
- 온-프레미스 AD DS(Active Directory Domain Services) 또는 Azure Files에 대해 활성화된 Azure AD DS [ID 기반 인증](../articles/storage/files/storage-files-active-directory-overview.md)을 사용하는 스토리지 계정의 경우 SMB 클라이언트는 Windows 파일 탐색기를 사용하여 디렉터리 및 파일에 대한 NTFS 권한을 구성할 수 없습니다.
    - 대신 Windows [icacls](/windows-server/administration/windows-commands/icacls) 도구 또는 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) 명령을 사용하여 권한을 구성합니다.

