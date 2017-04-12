---
title: "Azure Active Directory의 B2B 공동 작업 사용자 클레임 매핑 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업에 대한 클레임 매핑 참조"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.lasthandoff: 03/17/2017


---

# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory의 B2B 공동 작업 사용자 클레임 매핑

Azure AD(Azure Active Directory)에서는 B2B 공동 작업 사용자에 대해 SAML 토큰에 발급된 클레임을 사용자 지정할 수 있습니다. 사용자가 응용 프로그램에 인증할 때 Azure AD는 고유하게 식별하는 사용자에 대한 정보(또는 클레임)를 포함하는 앱에 SAML 토큰을 발급합니다. 기본적으로 사용자의 사용자 이름, 전자 메일 주소, 이름 및 성을 포함합니다. 특성 탭에서 응용 프로그램의 SAML 토큰에 전송된 클레임을 보거나 편집할 수 있습니다.

SAML 토큰에 발급된 클레임을 편집해야 하는 두 가지 가능한 이유는 다음과 같습니다.

1. 응용 프로그램이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다.

2. 응용 프로그램이 Azure Active Directory에 저장된 사용자 계정 이름 이외의 NameIdentifier 클레임을 요구합니다.

  ![SAML 토큰의 클레임 보기](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

클레임 추가 및 편집 방법에 대한 자세한 내용은 클레임 사용자 지정에 대한 [Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](develop/active-directory-saml-claims-customization.md) 문서를 참조하세요. B2B 공동 작업 사용자의 경우 보안상의 이유로 테넌트 간 NameID 및 UPN 매핑이 금지됩니다.


## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
* [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
* [B2B 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
* [B2B 공동 작업용 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
* [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)

