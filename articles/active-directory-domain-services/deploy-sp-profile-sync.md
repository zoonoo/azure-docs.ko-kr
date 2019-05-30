---
title: 'Azure Active Directory Domain Services: SharePoint User Profile Service에 대한 지원을 사용하도록 설정 | Microsoft Docs'
description: SharePoint Server에 대한 프로필 동기화를 지원하도록 Azure Active Directory Domain Services 관리 도메인 구성
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: deef9b317f394213eabb5ce0ce31dd294bc0dfd1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246151"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>SharePoint Server에 대한 프로필 동기화를 지원하도록 관리 도메인 구성
SharePoint Server에는 사용자 프로필 동기화에 사용되는 User Profile Service가 포함되어 있습니다. User Profile Service를 설정하려면 Active Directory 도메인에서 적절한 사용 권한을 부여해야 합니다. 자세한 내용은 [SharePoint Server 2013에서 프로필 동기화를 위해 Active Directory Domain Services 사용 권한 부여](https://technet.microsoft.com/library/hh296982.aspx)를 참조하세요.

이 문서에서는 SharePoint Server User Profile Sync Service를 배포하도록 Azure AD 도메인 서비스 관리 도메인을 구성하는 방법을 설명합니다.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>'AAD DC 서비스 계정' 그룹
'**AAD DC 서비스 계정**'이라는 보안 그룹을 관리되는 도메인의 '사용자’ 조직 구성 단위 내에서 사용할 수 있습니다. 관리되는 도메인의 **Active Directory 사용자 및 컴퓨터** MMC 스냅인에서 이 그룹을 확인할 수 있습니다.

![AAD DC 서비스 계정 보안 그룹](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

이 보안 그룹의 구성원에게 다음 권한이 위임됩니다.
- 관리되는 도메인의 루트 DSE에 대한 ‘디렉터리 변경 내용 복제' 권한
- 관리되는 도메인의 구성 명명 컨텍스트에 대한 ‘디렉터리 변경 내용 복제' 권한.

이 보안 그룹은 기본 제공 그룹 **Pre-Windows 2000 Compatible Access**의 구성원이기도 합니다.

![AAD DC 서비스 계정 보안 그룹](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>관리되는 도메인이 SharePoint Server 사용자 프로필 동기화를 지원하도록 설정
SharePoint 사용자 프로필 동기화에 사용되는 서비스 계정을 **AAD DC 서비스 계정** 그룹에 추가할 수 있습니다. 결과적으로 동기화 계정은 디렉터리에 대한 변경 내용을 복제할 수 있는 적절한 권한을 얻게 됩니다. 이 구성 단계를 수행하면 SharePoint Server 사용자 프로필 동기화가 제대로 작동될 수 있습니다.

![AAD DC 서비스 계정 - 구성원 추가](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC 서비스 계정 - 구성원 추가](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>관련 콘텐츠
* [기술 참조 - SharePoint Server 2013에서 프로필 동기화를 위해 Active Directory Domain Services 사용 권한 부여](https://technet.microsoft.com/library/hh296982.aspx)
