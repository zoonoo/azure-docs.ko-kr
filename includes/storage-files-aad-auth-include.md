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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565091"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) 는 [AD (Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) ) (미리 보기)를 통한 SMB (서버 메시지 블록) 및 GA ( [AD DS Azure Active Directory Domain Services](../articles/active-directory-domain-services/overview.md) )를 통해 id 기반 인증을 지원 합니다. 이 문서에서는 온-프레미스 또는 Azure에서 도메인 서비스를 활용 하 여 SMB를 통해 Azure Files에 대 한 id 기반 액세스를 지원 하기 위해 Azure Files 하는 방법에 대해 집중적으로 설명 합니다. 이렇게 하면 기존 파일 서버를 Azure Files 쉽게 바꾸고 기존 디렉터리 서비스를 계속 사용 하 여 공유에 대 한 원활한 사용자 액세스를 유지할 수 있습니다. 

Azure Files은 공유 및 디렉터리/파일 수준에 대 한 사용자 액세스 권한 부여를 적용 합니다. 공유 수준 권한 할당은 일반적인 [RBAC (역할 기반 액세스 제어)](../articles/role-based-access-control/overview.md) 모델을 통해 관리 되는 Azure AD 사용자 또는 그룹에 할당할 수 있습니다. RBAC를 사용 하 여 파일 액세스에 사용 하는 자격 증명을 사용 하거나 Azure AD에 동기화 해야 합니다. Azure AD의 사용자 또는 그룹에 저장소 파일 데이터 SMB 공유 판독기와 같은 기본 제공 RBAC 역할을 할당 하 여 Azure 파일 공유에 대 한 읽기 액세스 권한을 부여할 수 있습니다.

디렉터리/파일 수준에서 Azure Files은 Windows 파일 서버와 마찬가지로 [Windows dacl](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) 을 유지, 상속 및 적용할 수 있도록 지원 합니다. SMB를 통해 파일 공유에서 Azure Files로 또는 그 반대로 데이터를 복사 하는 경우 Windows Dacl을 유지 하도록 선택할 수 있습니다. 권한 부여를 적용할지 여부를 지정 하는 경우에는 Azure Files를 활용 하 여 Acl을 데이터와 함께 백업할 수 있습니다. 
