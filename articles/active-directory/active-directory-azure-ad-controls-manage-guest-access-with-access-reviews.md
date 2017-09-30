---
title: "Azure AD 액세스 검토를 사용하여 게스트 액세스 관리 | Microsoft Docs"
description: "Azure Active Directory 액세스 검토를 사용하여 게스트 사용자를 그룹 구성원으로 관리하거나 응용 프로그램에 할당합니다."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---

# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 게스트 액세스 관리


Azure Active Directory를 사용하면 [Azure AD B2B 기능](active-directory-b2b-what-is-azure-ad-b2b.md)을 통해 조직의 경계를 넘나들며 공동 작업을 쉽게 수행할 수 있습니다. 다른 테넌트의 게스트 사용자는 [관리자](active-directory-b2b-admin-add-users.md) 또는 [최종 사용자](active-directory-b2b-how-it-works.md)가 초대할 수 있습니다.  이는 Microsoft 계정과 같은 소셜 ID에도 적용됩니다.

또한 게스트 사용자에게 적절한 액세스 권한이 있는지 쉽게 확인할 수도 있습니다.  게스트 또는 의사 결정자에게 액세스 검토에 참여하고 게스트 액세스에 대해 다시 인증(또는 "증명")하도록 요청하여 이 작업을 수행할 수 있습니다. 검토자는 Azure AD에서 제안 사항에 따라 지속적인 액세스에 대한 각 사용자의 요구에 입력을 제공할 수 있습니다. 액세스 검토가 완료되면 더 이상 필요하지 않은 게스트의 액세스를 변경하고 제거할 수 있습니다.

> [!NOTE]
> 이 문서에서는 게스트 사용자의 액세스 검토에 중점을 둡니다. 게스트뿐만 아니라 모든 사용자의 액세스 권한도 검토하려면 [액세스 검토를 사용하여 사용자 액세스 관리](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) 가이드를 참조하세요.  전역 관리자와 같은 관리 역할에서 사용자의 멤버 자격을 검토하려는 경우 [Azure AD PIM에서 액세스 검토를 시작하는 방법](active-directory-privileged-identity-management-how-to-start-security-review.md)을 참조하세요. 
>
>

## <a name="prerequisites"></a>필수 조건 

액세스 검토는 EMS E5에 포함된 Azure Active Directory의 Premium P2 버전에서 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.  검토 만들기, 액세스 검토 또는 리뷰 적용을 위해 이 기능과 상호 작용하는 각 사용자는 라이선스가 필요합니다.  

게스트 사용자에게 자신의 액세스를 검토하도록 요청하는 경우 [Azure AD B2B 공동 작업 라이선스](active-directory-b2b-licensing.md)의 게스트 사용자 라이선스를 자세히 참조하세요.

## <a name="creating-and-performing-an-access-review-for-guests"></a>게스트에 대한 액세스 검토 만들기 및 수행

먼저 검토자의 액세스 패널에 표시되도록 액세스 검토를 활성화합니다.  전역 관리자로서 [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동합니다. 

Azure AD는 게스트 사용자를 검토하기 위한 몇 가지 시나리오를 지원합니다.  

다음 중 하나를 선택합니다.
 - 하나 이상의 게스트가 구성원으로 있는 Azure Active Directory의 그룹 
 - 또는 하나 이상의 게스트 사용자가 할당된 Azure Active Directory에 연결된 응용 프로그램 - 각 게스트가 자신의 액세스를 검토할지 또는 하나 이상의 사용자가 모든 게스트 액세스를 검토할지 결정할 수 있습니다.

 이러한 시나리오 각각에 대해서는 다음 섹션 중 하나에서 설명됩니다.

* [게스트에게 그룹 구성원 자격을 검토하도록 요청](#asking-guests-to-review-their-own-membership-in-a-group)
* [스폰서에게 게스트의 그룹 구성원 자격을 검토하도록 요청](#asking-sponsors-to-review-guests-membership-in-a-group)
* [게스트에게 응용 프로그램에 대한 자신의 액세스를 검토하도록 요청](#asking-guests-to-review-their-own-access-to-an-application)
* [스폰서에게 응용 프로그램에 대한 게스트의 액세스를 검토하도록 요청](#asking-sponsors-to-review-guests-access-to-an-application) 
* [게스트에게 자신의 일반적 액세스 요구 사항을 검토하도록 요청](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>게스트에게 그룹 구성원 자격을 검토하도록 요청

액세스 검토를 사용하여 그룹에 초대되고 추가된 사용자가 계속 액세스해야 하는지 확인할 수 있습니다.  쉬운 방법은 게스트에게 해당 그룹의 구성원 자격을 직접 검토하도록 요청하는 것입니다.

1. 그룹에 대한 액세스 검토를 시작하고, 게스트 사용자 구성원만 포함하도록 검토를 선택하고, 해당 구성원이 직접 검토합니다. [액세스 검토를 만드는 방법](active-directory-azure-ad-controls-create-access-review.md)에서 자세히 알아봅니다.
2. 각 게스트에게 자신의 구성원 자격을 검토하도록 요청합니다.  기본적으로 초대를 수락한 각 게스트는 Azure AD에서 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다.  Azure AD에는 게스트의 [액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)에 대한 지침이 있습니다.
3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. [액세스 검토를 완료하는 방법](active-directory-azure-ad-controls-complete-access-review.md)에서 자세히 알아봅니다. 
4. 지속적인 액세스에 대한 자신의 요구 사항을 거부한 사용자 외에도 응답하지 않은 사용자를 제거할 수 있습니다.  이는 응답하지 않은 사용자가 잠재적으로 전자 메일을 더 이상 받지 않을 것이기 때문입니다.
5. 그룹이 액세스 관리에 사용되지 않는 경우 초대를 수락하지 않았기 때문에 검토에 참여하도록 선택되지 않은 사용자도 제거할 수 있습니다.  이 경우 초대된 사용자의 이메일 주소에 오타가 있었다고 표시될 수 있습니다.  그러나 그룹이 배포 목록으로 사용되는 경우 일부 게스트 사용자는 연락처 개체이므로 참가하도록 선택되지 않았을 수 있습니다.

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>스폰서에게 게스트의 그룹 구성원 자격을 검토하도록 요청

그룹 소유자와 같은 스폰서에게 지속적인 그룹 구성원 자격에 대한 게스트의 요구 사항을 검토하도록 요청할 수 있습니다.

1. 그룹에 대한 액세스 검토를 시작하고, 게스트 사용자 구성원만 포함하도록 검토를 선택하고, 하나 이상의 검토자를 지정합니다. [액세스 검토를 만드는 방법](active-directory-azure-ad-controls-create-access-review.md)에서 자세히 알아봅니다.
2. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 액세스 패널에 대한 링크와 함께 Azure AD에서 전자 메일을 받으며 여기에서 [자신의 액세스 검토를 수행](active-directory-azure-ad-controls-perform-access-review.md)합니다.
3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. [액세스 검토를 완료하는 방법](active-directory-azure-ad-controls-complete-access-review.md)에서 자세히 알아봅니다.

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>게스트에게 응용 프로그램에 대한 자신의 액세스를 검토하도록 요청

액세스 검토를 사용하여 특정 응용 프로그램에 초대된 사용자가 계속 액세스해야 하는지 확인할 수 있습니다.  쉬운 방법은 게스트에게 자신의 액세스 요구 사항을 검토하도록 요청하는 것입니다.

1. 응용 프로그램에 대한 액세스 검토를 시작하고, 게스트만 포함하도록 검토를 선택하고, 해당 사용자가 자신의 액세스를 검토합니다. [액세스 검토를 만드는 방법](active-directory-azure-ad-controls-create-access-review.md)에서 자세히 알아봅니다.
2. 각 게스트에게 응용 프로그램에 대한 자신의 액세스를 검토하도록 요청  기본적으로 초대를 수락한 각 게스트는 Azure AD에서 조직의 액세스 패널에 있는 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다.  Azure AD에는 게스트의 [액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)에 대한 지침이 있습니다.
3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. [액세스 검토를 완료하는 방법](active-directory-azure-ad-controls-complete-access-review.md)에서 자세히 알아봅니다.
4. 지속적인 액세스에 대한 자신의 요구 사항을 거부한 사용자 외에도, 응답하지 않은 사용자가 잠재적으로 전자 메일을 더 이상 받지 않을 것이므로 응답하지 않은 게스트 사용자를 제거할 수 있습니다.  또한 참여하도록 선택되지 않은 게스트 사용자도 제거할 수 있습니다. 특히 최근에 게스트를 초대하지 않은 경우 해당 사용자가 초대를 수락하지 않았으므로 해당 응용 프로그램에 액세스할 수 없습니다. 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>스폰서에게 응용 프로그램에 대한 게스트의 액세스를 검토하도록 요청


응용 프로그램 소유자와 같은 스폰서에게 지속적인 응용 프로그램 액세스에 대한 게스트의 요구 사항을 검토하도록 요청할 수 있습니다.

1. 응용 프로그램에 대한 액세스 검토를 시작하고, 게스트만 포함하도록 검토를 선택하고, 하나 이상의 사용자를 검토자로 지정합니다. [액세스 검토를 만드는 방법](active-directory-azure-ad-controls-create-access-review.md)에서 자세히 알아봅니다.
2. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 액세스 패널에 대한 링크와 함께 Azure AD에서 전자 메일을 받으며 여기에서 [자신의 액세스 검토를 수행](active-directory-azure-ad-controls-perform-access-review.md)합니다.
3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. [액세스 검토를 완료하는 방법](active-directory-azure-ad-controls-complete-access-review.md)에서 자세히 알아봅니다.

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>게스트에게 자신의 일반적 액세스 요구 사항을 검토하도록 요청

일부 조직에서는 게스트가 그룹 구성원 자격을 인식하지 못할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure Portal에서는 UserType이 Guest인 사용자의 관리 액세스를 허용하지 않았으며, 경우에 따라 디렉터리의 관리자가 PowerShell을 사용하여 게스트의 UserType 값을 Member로 변경했을 수도 있습니다.  이전에 디렉터리에서 이 문제가 발생한 경우 이전에 관리 액세스 권한이 있었던 모든 게스트 사용자가 위에서 언급된 쿼리에 포함되지 않았을 수 있으므로 해당 게스트의 UserType을 변경하거나 수동으로 그룹 구성원 자격에 포함시켜야 합니다.

1. 적합한 그룹이 아직 없는 경우 Azure AD에 해당 게스트를 구성원으로 하는 보안 그룹을 만듭니다.  예를 들어 수동으로 유지 관리되는 게스트의 구성원 자격으로 그룹을 만들 수 있습니다.  또는 UserType 특성 값이 Guest인 Contoso 테넌트의 사용자에 대해 "Contoso 게스트"와 같은 이름으로 동적 그룹을 만들 수도 있습니다.
2. 해당 그룹에 대한 액세스 검토를 시작하여 검토자가 구성원이 되도록 직접 선택합니다. [액세스 검토를 만드는 방법](active-directory-azure-ad-controls-create-access-review.md)에서 자세히 알아봅니다.
3. 각 게스트에게 자신의 구성원 자격을 검토하도록 요청합니다.  기본적으로 초대를 수락한 각 게스트는 Azure AD에서 조직의 액세스 패널에 있는 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다.  Azure AD에는 게스트의 [액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)에 대한 지침이 있습니다.
4. 검토자가 입력을 제공하면 액세스 검토를 중지합니다. [액세스 검토를 완료하는 방법](active-directory-azure-ad-controls-complete-access-review.md)에서 자세히 알아봅니다.
5. 거부되었거나 검토를 완료하지 않았거나 이전에 초대를 수락하지 않은 게스트에 대한 게스트 액세스를 제거합니다.   게스트 중 일부가 이전에 초대를 수락하지 않았기 때문에 검토에 참여하도록 선택된 연락처인 경우, Azure Portal 또는 PowerShell을 사용하여 로그온할 때 해당 계정을 사용하지 않도록 설정할 수 있습니다.  게스트가 더 이상 액세스할 필요가 없고 연락처가 아닌 경우, Azure Portal 또는 PowerShell을 사용하여 사용자 개체를 디렉터리에서 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹의 멤버 또는 응용 프로그램에 액세스에 대한 액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)








