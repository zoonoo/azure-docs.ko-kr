---
title: "하이브리드 조직에 대한 Azure Active Directory B2B 공동 작업 | Microsoft Docs"
description: "Azure AD B2B 공동 작업을 사용하여 로컬 및 클라우드 리소스에 파트너 액세스 권한을 부여합니다."
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>하이브리드 조직에 대한 Azure Active Directory B2B 공동 작업

온-프레미스 및 클라우드 리소스를 모두 포함하는 하이브리드 조직에서는 로컬 및 클라우드 리소스에 대한 외부 파트너 액세스 권한이 필요할 수 있습니다. 로컬 리소스에 대한 액세스의 경우 온-프레미스 Active Directory 환경에서 파트너 계정을 로컬로 관리할 수 있습니다. 파트너는 조직의 리소스에 액세스하기 위해 파트너 계정 자격 증명을 사용하여 로그인합니다. 이제 이렇게 동일한 자격 증명을 사용하여 클라우드 리소스에 파트너 액세스 권한을 부여하려면 Azure AD(Azure Active Directory) Connect를 사용하여 클라우드에 대한 파트너 계정을 Azure AD B2B 사용자로 동기화할 수 있습니다(예: UserType이 게스트인 사용자).

## <a name="identify-unique-attributes-for-usertype"></a>UserType에 대한 고유한 특성 식별

UserType 특성의 동기화를 사용하도록 설정하기 전에 먼저 온-프레미스 Active Directory에서 UserType 특성을 파생시키는 방법을 결정해야 합니다. 즉, 외부 협력자에 대해 고유한 온-프레미스 환경의 매개 변수를 알아봅니다. 해당 조직의 멤버와 이러한 외부 협력자를 구별하는 매개 변수를 확인합니다.

이를 위한 두 가지 일반적인 방법은 다음과 같습니다.

- 사용하지 않는 온-프레미스 Active Directory 특성(예: extensionAttribute1)을 원본 속성으로 사용하도록 지정합니다. 
- 또는 UserType 특성의 값을 다른 속성에서 파생시킵니다. 예를 들어 온-프레미스 Active Directory UserPrincipalName 특성이 도메인 *@partners.fabrikam123.org*로 끝나는 경우 모든 사용자를 게스트로 동기화하는 것이 좋습니다.
 
자세한 특성 요구 사항은 [UserType의 동기화 사용](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)을 참조하세요. 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>클라우드로 사용자를 동기화하도록 Azure AD Connect 구성

고유한 특성을 식별한 다음 클라우드에 이러한 사용자를 Azure AD B2B 사용자로 동기화하도록 Azure AD Connect를 구성할 수 있습니다(즉, UserType이 게스트인 사용자). 권한 부여라는 관점에서 이러한 사용자는 Azure AD B2B 공동 작업 초대 프로세스를 통해 만든 B2B 사용자와 구분될 수 없습니다.

구현 지침은 [UserType의 동기화 사용](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure AD B2B 공동 작업의 개요는 [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)을 참조하세요.
- Azure AD Connect 개요는 [Azure Active Directory와 온-프레미스 디렉터리 통합](connect/active-directory-aadconnect.md)을 참조하세요.

