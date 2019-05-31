---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0cfa0fdb51969c92e767adfa86a0065d11da56e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237773"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md)는 [Azure AD(Azure Active Directory) Domain Services](../articles/active-directory-domain-services/overview.md)를 사용하여 SMB(서버 메시지 블록)를 통한 ID 기반 인증(미리 보기)을 지원합니다. 도메인 가입 Windows VM(가상 머신)은 [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다. 

Azure AD는 [RBAC(역할 기반 액세스 제어)](../articles/role-based-access-control/overview.md)를 사용하여 사용자, 그룹 또는 서비스 주체와 같은 ID를 인증합니다. Azure Files에 액세스하는 데 사용되는 공통 권한 집합을 포함하는 사용자 지정 RBAC 역할을 정의할 수 있습니다. Azure AD ID에 사용자 지정 RBAC 역할을 할당하면 해당 권한에 따라 Azure 파일 공유 액세스 권한이 해당 ID에 부여됩니다.

Azure Files는 미리 보기의 일부로서, 파일 공유의 모든 파일 및 디렉터리에 대해 [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx)을 보존, 상속 및 적용하는 작업도 지원합니다. 파일 공유에서 Azure Files로 데이터를 복사하거나 그 반대로 복사하는 경우 해당 NTFS DACL을 유지 관리하도록 지정할 수 있습니다. 이 방법으로 Azure Files를 사용하여 백업 시나리오를 구현하면 온-프레미스 파일 공유와 클라우드 파일 공유 간에 NTFS DACL이 보존됩니다. 

> [!NOTE]
> - SMB를 통한 Azure AD 인증은 미리 보기 릴리스의 Linux VM에서 지원되지 않습니다. Windows Server VM만 지원됩니다.
> - SMB를 통한 Azure AD 인증은 Azure Files에 액세스하는 온-프레미스 머신에 지원되지 않습니다.
> - Azure AD 인증은 2018년 9월 24일 이후에 만든 저장소 계정에만 사용할 수 있습니다.
> - SMB를 통한 Azure AD 인증 및 NTFS ACL 영구는 Azure 파일 동기화 서비스에서 관리하는 Azure 파일 공유에서 지원되지 않습니다. 
