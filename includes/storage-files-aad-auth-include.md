---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269369"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md)는 [Azure AD(Azure Active Directory) Domain Services](../articles/active-directory-domain-services/overview.md)를 사용하여 SMB(서버 메시지 블록)를 통한 ID 기반 인증(미리 보기)을 지원합니다. 도메인 가입 Windows VM(가상 머신)은 [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다. 

사용자, 사용 하 여 Azure AD에서 그룹 id Azure 파일 공유 수준 액세스를 관리할 수 있습니다 [역할 기반 액세스 제어 (RBAC)](../articles/role-based-access-control/overview.md)합니다. Azure Files에 액세스하는 데 사용되는 공통 권한 집합을 포함하는 사용자 지정 RBAC 역할을 정의할 수 있습니다. Azure AD ID에 사용자 지정 RBAC 역할을 할당하면 해당 권한에 따라 Azure 파일 공유 액세스 권한이 해당 ID에 부여됩니다.

Azure Files는 미리 보기의 일부로서, 파일 공유의 모든 파일 및 디렉터리에 대해 [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx)을 보존, 상속 및 적용하는 작업도 지원합니다. 파일 공유에서 Azure Files로 데이터를 복사하거나 그 반대로 복사하는 경우 해당 NTFS DACL을 유지 관리하도록 지정할 수 있습니다. 이 방법으로 Azure Files를 사용하여 백업 시나리오를 구현하면 온-프레미스 파일 공유와 클라우드 파일 공유 간에 NTFS DACL이 보존됩니다. 

> [!NOTE]
> - Linux Vm에 대 한 SMB 액세스를 위한 azure AD 도메인 서비스 인증 지원 되지 않습니다. Windows VM만이 지원됩니다.
> - Active Directory 도메인에 가입 된 컴퓨터에 대 한 SMB 액세스를 위한 azure AD 도메인 서비스 인증 지원 되지 않습니다. 그동안에서 활용 하 여 고려할 수 있습니다 [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) AD 도메인에 컴퓨터 가입 된 Azure Files로 데이터 마이그레이션 시작에 계속에 온-프레미스에서 AD 자격 증명을 사용 하 여 액세스 제어를 적용 합니다. 
> - SMB 액세스를 위해 azure AD 도메인 서비스 인증은 2018 년 9 월 24 일 이후 생성 된 저장소 계정에 대해서만 제공 됩니다.
> - Azure File Sync 서비스에서 관리 하는 Azure 파일 공유에 대 한 SMB 액세스 및 영구 NTFS DACL에 대 한 azure AD 도메인 서비스 인증 지원 되지 않습니다. 
