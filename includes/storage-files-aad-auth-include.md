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
ms.openlocfilehash: 84fa97ec964d490eb9571c7e030704562a4a81d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95555812"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md)는 [온-프레미스 AD DS(Active Directory Domain Services)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 및 [Azure AD DS(Azure Active Directory Domain Services)](../articles/active-directory-domain-services/overview.md)를 통해 SMB(서버 메시지 블록)를 통한 ID 기반 인증을 지원합니다. 이 문서에서는 Azure 파일 공유가 온-프레미스 또는 Azure에서 도메인 서비스를 사용하여 SMB를 통한 Azure 파일 공유에 대한 ID 기반 액세스를 지원하는 방법에 대해 집중적으로 설명합니다. Azure 파일 공유에 대한 ID 기반 액세스를 사용하도록 설정하면 기존 디렉터리 서비스를 대체하지 않고도 기존 파일 서버를 Azure 파일 공유로 대체하여 원활한 사용자 액세스를 공유할 수 있습니다. 

Azure Files는 공유 및 디렉터리/파일 수준에 대한 사용자 액세스 권한 부여를 적용합니다. 공유 수준 권한 할당은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../articles/role-based-access-control/overview.md) 모델을 통해 관리되는 Azure AD(Azure Active Directory) 사용자 또는 그룹에서 수행할 수 있습니다. RBAC를 통해 파일 액세스에 사용하는 자격 증명을 사용하거나 Azure AD에 동기화해야 합니다. Storage 파일 데이터 SMB 공유 리더와 같은 Azure 기본 제공 역할을 Azure AD의 사용자 또는 그룹에 할당하여 Azure 파일 공유에 대한 읽기 액세스 권한을 부여할 수 있습니다.

디렉터리/파일 수준에서 Azure Files는 Windows 파일 서버와 마찬가지로 [Windows DACL](/windows/win32/secauthz/access-control-lists)을 유지, 상속 및 적용할 수 있도록 지원합니다. 기존 파일 공유와 Azure 파일 공유 간에 SMB를 통해 데이터를 복사할 때 Windows DACL을 유지하도록 선택할 수 있습니다. 권한 부여를 적용할지 여부와 관계없이 Azure 파일 공유를 사용하여 데이터와 함께 ACL을 백업할 수 있습니다.