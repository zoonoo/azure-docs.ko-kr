---
title: "Azure Active Directory B2B 공동 작업을 위한 셀프 서비스 등록 포털 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업은 비즈니스 파트너가 선택적으로 회사 응용 프로그램에 액세스할 수 있게 함으로써 회사 간 관계를 지원합니다."
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
ms.date: 05/24/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017


---


# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B 공동 작업 등록을 위한 셀프 서비스 포털

고객은 최종 사용자를 위한 IT 관리 [Azure Portal](https://portal.azure.com) 및 [응용 프로그램 액세스 패널](https://myapps.microsoft.com)을 통해 제공되는 기본 제공 기능으로 많은 작업을 수행할 수 있습니다. 그렇지만 기업에서는 조직의 요구에 맞게 B2B 사용자에 대한 온보딩 워크플로를 사용자 지정해야 합니다. 이러한 작업은 [API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)를 통해 수행할 수 있습니다.

고객들과 이러한 점을 논의하면서 일반적인 요구가 있다는 것을 알게 되었습니다. 초대하는 조직이 리소스에 액세스할 필요가 있는 개별 외부 공동 작업자가 누구인지를 미리 알지 못할 수도 있습니다. 고객들은 파트너 회사의 사용자가 초대하는 조직이 제어하는 정책 집합으로 자체 등록할 수 있는 방식을 원했습니다. 이 시나리오는 API를 통해 구현될 수 있습니다. 따라서 이러한 작업을 수행하는 [샘플 Github 프로젝트](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)를 GItHub에 게시했습니다.

Github 프로젝트는 조직이 API를 사용하고, 액세스해야 하는 앱을 결정하는 규칙을 통해 신뢰할 수 있는 파트너에게 정책 기반의 셀프 서비스 등록 기능을 제공하는 방법을 보여 줍니다. 초대하는 조직이 수동으로 온보딩할 필요 없이 파트너 사용자는 필요할 때 안전하게 적절한 리소스에 액세스할 수 있습니다. 사용자가 선택한 Azure 구독에 프로젝트를 쉽게 게시할 수 있습니다.

## <a name="as-is-code"></a>원본 코드

이 코드는 Azure Active Directory B2B 초대 API의 사용법을 보여 주기 위해 샘플로 사용할 수 있게 제공됩니다. 따라서 개발 팀 또는 파트너가 코드를 사용자 지정해야 하고 프로덕션 시나리오에서 배포하기 전에 검토해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:
* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-admin-add-users.md)
* [정보 근로자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-iw-add-users.md)
* [B2B 공동 작업 초대 전자 메일의 요소](active-directory-b2b-invitation-email.md)
* [B2B 공동 작업 초대 상환](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 공동 작업 라이선스](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 공동 작업 문제 해결](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](active-directory-b2b-faq.md)
* [B2B 공동 작업 사용자에 대한 다단계 인증](active-directory-b2b-mfa-instructions.md)
* [초대 없이 B2B 공동 작업 사용자 추가](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
