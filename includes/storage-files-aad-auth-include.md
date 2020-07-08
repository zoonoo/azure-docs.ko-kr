---
title: 파일 포함
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d470160a9b54af8751371aa4ca58202646c8fab8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84263362"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) [은 온-프레미스 Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 및 [Azure Active Directory Domain Services (AZURE AD DS)](../articles/active-directory-domain-services/overview.md)를 통해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 지원 합니다. 이 문서에서는 Azure 파일 공유에서 온-프레미스 또는 Azure에서 도메인 서비스를 사용 하 여 SMB를 통해 Azure 파일 공유에 대 한 id 기반 액세스를 지 원하는 방법에 대해 집중적으로 설명 합니다. Azure 파일 공유에 대해 id 기반 액세스를 사용 하도록 설정 하면 기존 디렉터리 서비스를 대체 하지 않고 기존 파일 서버를 Azure 파일 공유로 대체 하 여 원활한 사용자 액세스를 공유할 수 있습니다. 

Azure Files은 공유 및 디렉터리/파일 수준에 대 한 사용자 액세스 권한 부여를 적용 합니다. 공유 수준 권한 할당은 [RBAC (역할 기반 액세스 제어](../articles/role-based-access-control/overview.md) ) 모델을 통해 관리 되는 Azure Active Directory (Azure AD) 사용자 또는 그룹에서 수행할 수 있습니다. RBAC를 사용 하 여 파일 액세스에 사용 하는 자격 증명을 사용 하거나 Azure AD에 동기화 해야 합니다. Azure AD의 사용자 또는 그룹에 저장소 파일 데이터 SMB 공유 판독기와 같은 기본 제공 RBAC 역할을 할당 하 여 Azure 파일 공유에 대 한 읽기 액세스 권한을 부여할 수 있습니다.

디렉터리/파일 수준에서 Azure Files은 Windows 파일 서버와 마찬가지로 [Windows dacl](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) 을 유지, 상속 및 적용할 수 있도록 지원 합니다. 기존 파일 공유와 Azure 파일 공유 사이에서 SMB를 통해 데이터를 복사할 때 Windows Dacl을 유지 하도록 선택할 수 있습니다. 권한 부여를 적용할지 여부와 관계 없이 Azure 파일 공유를 사용 하 여 Acl을 데이터와 함께 백업할 수 있습니다. 
