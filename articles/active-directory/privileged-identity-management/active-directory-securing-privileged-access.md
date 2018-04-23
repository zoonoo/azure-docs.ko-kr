---
title: Azure AD에서 권한 있는 액세스 보안 | Microsoft Docs
description: Azure, Azure Active Directory 및 Microsoft Online Services에서 권한 있는 액세스 보안에 대한 접근 방법을 설명하는 항목입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: f49d5a629b9643df81253c57ee8e3458b43b0c59
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="securing-privileged-access-in-azure-ad"></a>Azure AD에서 권한 있는 액세스 보안
최신 조직에서 비즈니스 자산 보호를 위해 중요한 첫 번째 단계는 권한 있는 액세스 보안입니다. 권한 있는 계정은 IT 시스템을 운영하고 관리하는 계정입니다. 사이버 공격자는 조직의 데이터와 시스템에 대한 액세스 권한을 얻기 위해 이러한 계정을 대상으로 합니다. 권한 있는 액세스를 보호하려면 계정과 시스템을 악의적 사용자에게 노출될 위험으로부터 격리해야 합니다.

더 많은 사용자가 클라우드 서비스를 통해 권한 있는 액세스를 얻기 시작했습니다. 여기에는 Office365의 전역 관리자, Azure 구독 관리자 및 SaaS 앱 또는 VM에서 관리 액세스 권한이 있는 사용자가 포함됩니다.

[권한 있는 액세스 보안](https://technet.microsoft.com/library/mt631194.aspx)에서 이 로드맵을 수행하는 것이 좋습니다.

Azure Active Directory, Office 365 또는 다른 Microsoft 서비스 및 응용 프로그램을 사용하는 고객의 경우 이러한 원칙은 Active Directory 또는 Azure Active Directory에서 사용자 계정을 관리하고 인증하는지 여부에 관계없이 적용됩니다. 다음 섹션에서는 권한 있는 액세스 보안을 지원하는 Azure AD 기능에 대한 자세한 내용을 제공합니다.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication
관리자 인증의 보안을 강화하려면 권한을 부여하기 전에 2단계 인증이 필요합니다. 2단계 인증은 사용자 이름과 암호 이외의 다른 항목을 사용해야 하는 사람인지를 확인하는 방법입니다. 사용자 로그인 및 트랜잭션에 대한 보안의 두 번째 계층을 제공합니다.

Azure MFA(Multi-Factor Authentication)는 간단한 로그인 프로세스에 대한 사용자의 요구를 충족하면서 데이터와 응용 프로그램에 대한 액세스를 보호하는 Microsoft의 2단계 인증 솔루션입니다. 다음과 같이 다양하고 손쉬운 인증 옵션을 통해 강력한 인증을 제공합니다.

- 전화 통화
- 문자 메시지
- 모바일 앱 알림
- 모바일 앱 확인 코드
- 타사 OATH 토큰

Azure Multi-Factor Authentication의 작동 방식에 대한 개요는 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

자세한 내용은 [Office 365용 MFA 및 Azure용 MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/)(영문)를 참조하세요.

## <a name="time-bound-privileges"></a>시간 제한 권한
일부 조직에서는 높은 권한이 있는 역할의 사용자가 너무 많은 것을 볼 수 있습니다. 서비스 등록 등의 특정 작업에 대한 역할에 사용자가 추가되었을 수 있지만 이후 그러한 권한을 자주 사용하지 않았습니다.

권한의 노출 시간을 줄이고 용도에 대한 가시성을 증가하려면 해당 권한 "JIT"(just in time)만 사용하도록 사용자를 제한하거나 확신을 가지고 축약된 기간에 대해 이러한 역할을 할당합니다. 권한은 자동으로 취소됩니다. Azure Active Directory, Azure Resources(미리 보기) 및 Microsoft Online Services에서 [Azure AD PIM(Privileged Identity Management)](https://aka.ms/AzurePIM)을 사용할 수 있습니다.

![PIM 대시보드][2]

## <a name="attack-detection"></a>공격 탐지
[Azure Active Directory ID 보호](../active-directory-identityprotection.md)는 조직의 ID에 영향을 주는 위험 이벤트와 잠재적 취약성에 대한 통합된 뷰를 제공합니다. ID 보호는 위험 이벤트에 따라 각 사용자에 대한 위험 수준을 계산하며 이는 위험 기반 정책을 구성하여 조직의 ID를 자동으로 보호할 수 있도록 합니다. 이 정책은 Azure Active Directory 및 EMS에서 제공하는 다른 조건부 액세스 제어와 함께 자동적으로 사용자를 차단하거나 암호 재설정 및 다단계 인증 적용을 포함한 제안 사항을 제공할 수 있습니다.

![Azure AD ID 보호][3]

## <a name="conditional-access"></a>조건부 액세스
조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다.

## <a name="related-articles"></a>관련 문서
* [Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md) 사용
* [Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) 사용
* [Azure AD ID 보호](../active-directory-identityprotection.md) 사용
* [조건부 액세스 제어](../active-directory-conditional-access-azure-portal.md) 사용

완벽한 보안 로드맵 작성에 대한 자세한 내용은 [Microsoft Cloud Security for Enterprise Architects(엔터프라이즈 설계자를 위한 Microsoft 클라우드 보안)](https://aka.ms/securecustomer) 문서의 "Customer responsibilities and roadmap(고객 책임 및 로드맵)" 섹션을 참조하세요. 이러한 항목에 도움이 되는 Microsoft 서비스 참여에 대한 자세한 내용은 Microsoft 담당자에게 문의하거나 [사이버 보안 솔루션 페이지](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)를 방문하세요.

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
