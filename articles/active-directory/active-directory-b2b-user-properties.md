---
title: "Azure Active Directory B2B 공동 작업 사용자 속성 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업 사용자 속성은 구성 가능합니다."
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
ms.date: 02/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: ae154e09e3b9ef2182e74b1dc5a0d8da3922b0df


---

# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 공동 작업 사용자 속성

## <a name="defining-a-b2b-collaboration-user"></a>B2B 공동 작업 사용자 정의
`B2B 공동 작업 사용자는 UserType = Guest 또는 게스트 사용자인 사용자입니다. 이 사용자는 일반적으로 파트너 조직의 사용자를 나타내며, 기본적으로 초대 디렉터리에서 제한된 권한을 가집니다. 초대 조직의 요구에 따라 B2B 공동 작업 사용자는 다음 계정 상태 중 하나일 수 있습니다.
1. 호스트 조직의 게스트 사용자로 표시되는 Azure AD(Azure Active Directory)의 외부 인스턴스에 속함 - 이 경우 B2B 사용자는 자신의 테넌트에 속한 Azure AD 계정으로 로그인합니다. 사용자가 속한 외부 조직이 초대 시 Azure AD를 사용하지 않으면 사용자가 자신의 전자 메일 주소를 확인한 후 초대를 상환할 때 Azure AD의 게스트 사용자가 "적시에" 만들어집니다. 이를 JIT(Just In Time) 테넌트 또는 때때로 바이럴 테넌트라고도 합니다.
2. 호스트 조직의 게스트 사용자로 표시되는 Microsoft 계정에 속함 - 이 경우 게스트 사용자는 Microsoft 계정으로 로그인합니다. B2B 공동 작업의 Azure AD 공개 미리 보기 새로 고침에서 제안 상환 중에 적시에 초대된 사용자의 비MSA 소셜 ID(google.com 또는 유사한 ID)가 Microsoft 계정으로 만들어집니다.
3. 호스트 조직의 Azure AD와 동기화되는 호스트 조직의 온-프레미스 Active Directory에 속함 - 이 릴리스에서는 PowerShell을 사용하여 수동으로 클라우드에 있는 이러한 사용자의 UserType을 Guest로 변경해야 합니다. 이 작업은 이후 릴리스에서 Azure AD Connect의 일부로 자동으로 수행되도록 지원할 예정입니다.
4. UserType = Guest 및 호스트 조직에서 관리하는 자격 증명을 사용하는 호스트 조직의 AzureAD에 속함

  ![초대자의 이니셜 표시](media/active-directory-b2b-user-properties/redemption-diagram.png)


이제 Azure AD에서 상태 1의 B2B 공동 작업 사용자가 어떻게 보이는지 살펴보겠습니다.

### <a name="before-invitation-redemption"></a>초대 상환 전

![제안 상환 전](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>초대 상환 후

![제안 상환 후](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-b2b-collaboration-user"></a>B2B 공동 작업 사용자의 주요 속성

### <a name="usertype"></a>UserType
이 특성은 사용자와 호스트 테넌트 사이의 관계를 나타냅니다. 여기에는 다음 두 가지 값이 사용될 수 있습니다.
- 구성원: 호스트 조직의 직원이며, 조직의 급여 부서에 있는 사용자입니다. 예를 들어 내부 전용 사이트에 액세스할 수 있을 것으로 예상되는 사용자입니다. 이 사용자는 외부 공동 작업자로 간주되지 않습니다.
- 게스트: 회사 내부 사용자로 간주되지 않는 사용자를 나타냅니다. 외부 공동 작업자, 파트너, 고객 또는 CEO의 내부 메모를 가져올 것으로, 예를 들어 회사에서 혜택을 받을 것으로 예상되지 않는 유사한 사용자일 수 있습니다.

  > ![참고] UserType은 사용자가 로그인하는 방법이나 사용자가 속한 디렉터리 역할 등과 관계가 없습니다. 이 특성은 단순히 사용자와 호스트 조직 사이의 관계를 나타내며, 조직에서 이 특성에 속한 모든 정책을 시행할 수 있게 합니다.

### <a name="source"></a>원본
사용자가 로그인하는 방법은 다음과 같습니다.

- 초대된 사용자: 이 사용자는 초대되었지만 초대를 아직 상환하지 않았습니다.

- 외부 Active Directory: 이 사용자는 외부 조직에 속하며 다른 조직에 속한 Azure AD 계정으로 인증합니다. 위의 상태 1에 해당합니다.

- Microsoft 계정: 이 사용자는 MSA에 속하며 Microsoft 계정으로 인증합니다. 위의 상태 2에 해당합니다.

- Windows Server AD: 이 사용자는 이 조직에 속한 온-프레미스 Active Directory와 동기화됩니다. 위의 상태 3에 해당합니다.

- Azure Active Directory: 이 사용자는 이 조직에 속한 Azure AD 계정으로 인증합니다. 위의 상태 4에 해당합니다.

  > ![참고] Source와 UserType은 독립적인 특성입니다. source 값은 특정 UserType을 의미하지 않습니다.

## <a name="can-b2b-users-be-added-as-members-instead-of-guests"></a>B2B 사용자를 게스트 대신 구성원으로 추가할 수 있습니까?
일반적으로 B2B 사용자와 게스트 사용자는 동의어입니다. 따라서 B2B 공동 작업 사용자는 기본적으로 UserType = Guest인 사용자로 추가됩니다. 그러나 경우에 따라 파트너 조직은 실제로 호스트 조직이 속한 더 큰 규모의 상부 조직의 구성원입니다. 그렇다면 호스트 조직에서 파트너 조직의 사용자를 게스트가 아닌 구성원으로 처리할 수 있습니다. 이 경우 B2B 초대 관리자 API를 사용하여 호스트 조직에 파트너 조직의 사용자를 구성원으로 추가하거나 초대합니다.

## <a name="filtering-for-guest-users-in-the-directory"></a>디렉터리에 있는 게스트 사용자 필터링

![게스트 사용자 필터링](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="converting-usertype"></a>UserType 변환
현재 PowerShell에서는 사용자가 UserType을 Member에서 Guest로 변환할 수 있으며, 그 반대로도 가능합니다. 그러나 UserType 속성은 사용자와 조직 사이의 관계를 나타냅니다. 따라서 사용자와 조직 사이의 관계가 변경된 경우에만 이 속성을 변경해야 합니다. 사용자 관계가 변경되는 경우 UPN을 변경해야 합니까?, 사용자가 액세스 권한이 있는 리소스에 계속 액세스해야 합니까?, 사서함을 할당해야 합니까?와 같은 다른 질문에 답변해야 합니다. 따라서 PowerShell의 UserType을 원자성 작업으로 변경하지 않는 것이 좋습니다. 또한 이후 릴리스에서는 PowerShell을 통해 이 속성을 변경할 수 없으므로 이 값을 사용하지 않는 것이 좋습니다.

## <a name="removing-guest-user-limitations"></a>게스트 사용자 제한 제거
게스트 사용자에게 더 높은 권한을 부여하려는 경우가 있을 수 있습니다. 이를 위해 게스트 사용자를 모든 역할에 추가하고 디렉터리에서 기본 게스트 사용자 제한을 제거하여 구성원과 동일한 권한을 부여할 수 있습니다. 더 알아보려면 계속 읽어 보세요.

기본 게스트 사용자 제한을 해제하여 회사 디렉터리의 게스트 사용자에게 일반 사용자(구성원)와 동일한 디렉터리 권한을 부여할 수 있습니다.

![게스트 사용자 제한 제거](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
* [B2B 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
* [B2B 공동 작업을 위한 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
* [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


