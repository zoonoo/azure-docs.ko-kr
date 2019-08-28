---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854561"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) 은 [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md)를 통해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 지원 합니다. 도메인에 가입 된 Windows Vm (가상 머신)은 [Azure Active Directory (AZURE AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) 자격 증명을 사용 하 여 azure 파일 공유에 액세스할 수 있습니다.

[RBAC (역할 기반 액세스 제어)](../articles/role-based-access-control/overview.md)를 사용 하 여 Azure AD의 사용자 또는 그룹과 같은 id에 대 한 Azure Files 공유 수준 액세스를 관리할 수 있습니다. Azure Files에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 사용자 지정 RBAC 역할을 정의할 수 있습니다. Azure AD ID에 사용자 지정 RBAC 역할을 할당하면 해당 권한에 따라 Azure 파일 공유 액세스 권한이 해당 ID에 부여됩니다.

또한 Azure Files은 파일 공유의 모든 파일 및 디렉터리에서 [NTFS dacl](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) 을 유지, 상속 및 적용할 수 있도록 지원 합니다. 파일 공유에서 Azure Files로 데이터를 복사하거나 그 반대로 복사하는 경우 해당 NTFS DACL을 유지 관리하도록 지정할 수 있습니다. 이러한 방식으로 온-프레미스 파일 공유와 클라우드 파일 공유 간에 NTFS DACL을 유지 하 여 Azure Files를 사용 하 여 백업 시나리오를 구현할 수 있습니다. 

> [!NOTE]
> - Linux Vm의 경우 SMB (서버 메시지 블록) 액세스에 대 한 Azure AD DS 인증이 지원 되지 않습니다. Windows VM만이 지원됩니다.
> - Active Directory 도메인에 가입 된 컴퓨터에는 SMB 액세스를 위한 Azure AD DS 인증이 지원 되지 않습니다. 그 중에는 [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) 를 사용 하 여 데이터를 Azure Files으로 마이그레이션하고, 온-프레미스 Active Directory 도메인에 가입 된 컴퓨터에서 Active Directory 자격 증명을 사용 하 여 액세스 제어를 계속 적용 하는 것이 좋습니다. 
> - SMB 액세스를 위한 Azure AD DS 인증은 2018 년 9 월 24 일 이후에 만들어진 저장소 계정에 대해서만 사용할 수 있습니다.
> - SMB 액세스 및 NTFS DACL 지 속성에 대 한 azure AD DS 인증은 Azure File Sync에서 관리 하는 Azure 파일 공유에서 지원 되지 않습니다.
> - Azure AD DS 인증은 Azure AD DS에서 만든 컴퓨터 계정에 대 한 인증을 지원 하지 않습니다.
