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
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565091"
---
[Azure Files는](../articles/storage/files/storage-files-introduction.md) [AD(Active Directory)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 및 [Azure Active Directory 도메인 서비스(Azure AD DS)(GA)를](../articles/active-directory-domain-services/overview.md) 통해 SMB(서버 메시지 블록)를 통한 ID 기반 인증을 지원합니다. 이 문서에서는 Azure Files가 SMB를 통해 Azure 파일에 대한 ID 기반 액세스를 지원하기 위해 온-프레미스 또는 Azure에서 도메인 서비스를 활용하는 방법에 대해 중점합니다. 이렇게 하면 기존 파일 서버를 Azure Files로 쉽게 바꾸고 기존 디렉터리 서비스를 계속 사용하여 공유에 대한 원활한 사용자 액세스를 유지할 수 있습니다. 

Azure Files는 공유 및 디렉터리/파일 수준 모두에 대한 사용자 액세스권한 부여를 적용합니다. 공유 수준 권한 할당은 일반적인 [역할 기반 액세스 제어(RBAC)](../articles/role-based-access-control/overview.md) 모델을 통해 관리되는 Azure AD 사용자 또는 그룹에 할당할 수 있습니다. RBAC를 사용하면 파일 액세스에 사용하는 자격 증명을 Azure AD에 사용하거나 동기화할 수 있어야 합니다. 저장소 파일 데이터 SMB 공유 리더와 같은 기본 제공 RBAC 역할을 Azure AD의 사용자 또는 그룹에 할당하여 Azure 파일 공유에 대한 읽기 액세스 권한을 부여할 수 있습니다.

디렉터리/파일 수준에서 Azure Files는 모든 Windows 파일 서버와 마찬가지로 [Windows DACL을](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) 보존, 상속 및 적용합니다. SMB를 통해 데이터를 파일 공유에서 Azure 파일로 복사하거나 그 반대로 복사하는 경우 Windows DACL을 유지하도록 선택할 수 있습니다. 권한 부여를 적용할 계획이든 관계없이 Azure Files를 활용하여 데이터와 함께 ACL을 백업할 수 있습니다. 
