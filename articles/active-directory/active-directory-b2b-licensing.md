---
title: "Azure Active Directory B2B 공동 작업 라이선스 지침 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업을 수행하려면 B2B 공동 작업 사용자에 대해 원하는 기능에 따라 유료 Azure AD 라이선스가 필요합니다."
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
ms.date: 04/12/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 4e620f3d76caa25ac0e5afb134f37ffe263935f0
ms.contentlocale: ko-kr
ms.lasthandoff: 04/13/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 공동 작업 라이선스 지침

Azure AD(Azure Active Directory) B2B 공동 작업은 선택한 기존 Azure AD 기능 집합을 Azure AD 테넌트에 초대 받은 게스트 사용자에게로 확장합니다. 따라서 Azure AD B2B 공동 작업 게스트 사용자는 Azure AD 라이선스를 통해 사용이 허가되고 https://azure.microsoft.com/pricing/details/active-directory/ 사이트에 표시되는 것처럼 기존의 무료, 기본 및 프리미엄 P1/P2 라이선스 계층에 맞춰 정렬됩니다.

B2B 사용자를 초대한 후 Azure AD에서 응용 프로그램에 할당하는 과정은 무료입니다. 또한 게스트 사용자당 최대 10개 앱과 3개의 기본 보고서도 Azure AD '무료' 계층의 일부이므로 B2B 사용자에게 무료입니다.
B2B 공동 작업 기능을 통해 B2B 사용자에게 확장되는 모든 유료 Azure AD 기능은 Azure AD 유료 라이선스(사용되는 기능에 따라 Basic, Premium P1 또는 Premium P2)로 사용이 허가되어야 합니다. 초대하는 테넌트는 각 Azure AD 유료 라이선스가 있는 5명의 B2B 사용자 권한을 얻습니다. 즉, 테넌트의 한 사용자에게 Azure AD 유료 기능에 대한 권한을 제공하는 각 Azure AD 유료 라이선스는 이제 테넌트에 초대받은 추가 B2B 사용자 5명에게 동일한 Azure AD 유료 기능에 대한 권한을 제공합니다.

## <a name="licensing-examples"></a>라이선스 예제
- 한 고객은 해당 Azure AD 테넌트에 100명의 B2B 사용자를 초대하려고 하며 모든 사용자에 대해 그룹 기반 액세스 관리 및 프로비저닝을 사용하지만 해당 사용자 중 50명은 MFA 및 조건부 액세스 권한을 요구합니다. 여기서 고객은 모든 해당 B2B 사용자를 제대로 처리하기 위해 10개의 Azure AD 기본 라이선스와 10개의 Azure AD Premium P1 라이선스를 구입해야 합니다. 마찬가지로 초대하는 테넌트가 B2B 사용자에게 ID 보호 기능을 사용하려는 경우 이러한 모든 B2B 사용자를 5:1 비율로 처리하기 위해 충분한 Azure AD Premium P2 라이선스가 있어야 합니다.
- 한 명의 고객에게 Azure AD Premium P1으로 사용이 허가된 10명의 직원이 있습니다. 이제 MFA(다단계 인증)가 필요한 60명의 B2B 사용자를 초대하려고 합니다. 5:1 라이선스 규칙에 따라 고객은 60명의 모든 B2B 공동 작업 사용자를 처리하기 위해 12개 이상의 Azure AD Premium P1 라이선스가 있어야 합니다. 10명의 직원에 대한 10개의 Premium P1 라이선스가 이미 있으므로 MFA와 같은 Premium P1 기능을 사용하여 50명의 B2B 사용자를 초대할 권한이 이미 있는 것입니다. 따라서 이 예제에서는 나머지 10명의 B2B 공동 작업 사용자를 처리하기 위해 2개의 추가 Premium P1 라이선스를 구입해야 합니다.

> [!NOTE]
> 이러한 B2B 공동 작업 사용자 권한을 사용하도록 하기 위해 B2B 사용자에게 라이선스를 할당하기 위한 기능이나 필요는 없습니다.

초대 테넌트를 소유하는 고객은 유료 Azure AD 기능이 필요한 B2B 공동 작업 사용자 수를 결정해야 하며, 고객에게는 Basic, Premium P1 또는 Premium P2 수준 기능 중 어떤 기능을 사용하는지에 따라 5:1 비율로 B2B 공동 작업 사용자를 처리하기 위해 충분한 수의 적절한 Azure AD 유료 라이선스가 있어야 합니다. 회사는 추가적인 B2B 공동 작업 사용자 권한이 필요한 경우 필요한 Azure AD 유료 라이선스를 구입해야 합니다.

## <a name="additional-licensing-details"></a>추가 라이선스 정보
- B2B 공동 작업 기능은 Azure AD 버전의 기존 모델에 따라 다른 B2B 공동 작업 사용자에게 다양한 기능을 제공할 수 있습니다.
- 모든 Azure AD 유료 라이선스에는 5명의 B2B 공동 작업 사용자(5:1 모델)에 대한 권한이 포함됩니다.
- 실제로 B2B 사용자 계정에 라이선스를 할당할 필요는 없습니다. 자동 계산 및 보고 기능도 제공됩니다.
- 초대 받은 모든 사용자에게는 테넌트에 유효 Azure AD 라이선스가 없는 경우 Azure AD Free 버전이 제공하는 권한을 얻게 됩니다.
- B2B 공동 작업 사용자가 조직의 직원으로서 유료 Azure AD B2B 라이선스를 갖는 경우 초대하는 테넌트의 B2B 공동 작업 라이선스 중 하나를 소비하지 않습니다.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>고급 토론: API를 사용하여 대기업 조직의 사용자를 “구성원”으로 추가할 때 라이선싱에 대해 고려할 사항은 무엇인가요?
B2B 게스트 사용자는 호스트 조직과 함께 작업하도록 파트너 조직에서 초대된 사용자입니다. 일반적으로 이외의 다른 경우는 B2B 기능을 사용하더라도 B2B로 인정되지 않습니다. 특히 다음 두 가지 경우를 살펴보겠습니다.

1. 호스트가 소비자 주소를 사용하는 직원을 초대하는 경우
  1. 이 경우는 라이선싱 정책을 준수하지 않고 현재 권장되지 않습니다.

2. 호스트 조직이 다른 대기업 조직의 사용자를 추가하는 경우
  1. 이 경우 사용자는 B2B API를 사용하여 초대되지만 이는 전통적으로 B2B가 아닙니다. 이상적으로는 이러한 조직이 다른 조직의 사용자를 구성원으로 초대하도록 해야 합니다(API에서 이를 허용함). 이 경우 초대하는 조직의 리소스에 액세스하려면 이러한 구성원에게 라이선스를 할당해야 합니다.

  2. 일부 조직에서는 다른 조직의 사용자를 정책에 “게스트”로 추가해야 할 수 있습니다. 다음 두 가지 경우가 있습니다.
      * 대기업 조직에서 이미 Azure AD를 사용하고 있고 초대된 사용자는 다른 조직에서 사용이 허가된 경우: 이 경우 초대된 사용자가 이 문서의 앞부분에 있는 1:5 수식을 따를 것이라고 기대하지 않습니다. 

      * 대기업 조직에서 Azure AD를 사용하고 있지 않거나 해당 조직에 적절한 라이선스가 없는 경우: 이 경우 이 문서의 앞부분에 있는 1:5 수식을 따릅니다.


## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-admin-add-users.md)
* [정보 근로자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-iw-add-users.md)
* [B2B 공동 작업 초대 전자 메일의 요소](active-directory-b2b-invitation-email.md)
* [B2B 공동 작업 초대 상환](active-directory-b2b-redemption-experience.md)
* [Azure Active Directory B2B 공동 작업 문제 해결](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 공동 작업 API 및 사용자 지정](active-directory-b2b-api.md)
* [B2B 공동 작업 사용자에 대한 다단계 인증](active-directory-b2b-mfa-instructions.md)
* [초대 없이 B2B 공동 작업 사용자 추가](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

