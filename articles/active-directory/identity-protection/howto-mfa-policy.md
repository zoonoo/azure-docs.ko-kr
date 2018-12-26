---
title: Azure Active Directory Identity Protection에서 다단계 인증 등록 정책을 구성하는 방법 | Microsoft Docs
description: Azure AD ID 보호 다단계 인증 등록 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 792a1fc2403e672c973577efd7a05c9c81d45ad4
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054084"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>방법: 다단계 인증 등록 정책 구성

Azure AD ID 보호를 사용하면 정책을 구성하여 MFA(Multi-Factor Authentication) 등록의 롤아웃을 관리할 수 있습니다. 이 문서에서는 어떤 정책을 어떻게 구성하는지에 대해 설명합니다.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>다단계 인증 등록 정책이란?

Azure Multi-Factor Authentication은 사용자 이름 및 암호 이외의 다른 내용을 사용해야 하는 사람인지를 확인하는 메서드입니다. 사용자 로그인 및 트랜잭션에 대한 보안의 두 번째 계층을 제공합니다.  

다음과 같은 이유로, 사용자 로그인에 Azure Multi-Factor Authentication을 요구하는 것이 좋습니다.

- 다양한 간편한 확인 옵션과 함께 강력한 인증 제공

- 조직에서 계정 손상을 방지 및 복구하도록 준비하는 데 중요한 역할 수행


자세한 내용은 [Azure Multi-Factor Authentication 정의](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>MFA 등록 정책에 액세스하려면 어떻게 할까요?
   
MFA 등록 정책은 **Azure AD ID 보호 페이지**의 [구성](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) 섹션에 있습니다.
   
![MFA 정책](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>정책 설정

로그인 위험 정책을 구성할 때 다음을 설정해야 합니다.

- 정책이 적용되는 사용자 및 그룹:

    ![개요](./media/howto-mfa-policy/11.png)

- 적용할 액세스 유형:  

    ![개요](./media/howto-mfa-policy/12.png)

- 정책 상태:

    ![정책 적용](./media/howto-mfa-policy/14.png)


정책 구성 대화 상자는 구성의 영향을 예상하는 옵션을 제공합니다.

![예상 영향](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>사용자 환경


관련 사용자 환경에 대한 개요는 다음을 참조하세요.

* [Multi-Factor Authentication 등록 흐름](flows.md#multi-factor-authentication-registration)  
* [Azure AD ID 보호를 사용하는 로그인 환경](flows.md)  



## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview.md)를 참조하세요.
