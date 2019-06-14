---
title: 빠른 시작 - Azure Active Directory Identity Protection을 사용하여 세션 위험 검색 시 액세스 차단 | Microsoft Docs
description: 이 빠른 시작에서는 Azure Active Directory (Azure AD) Identity Protection 로그인 위험을 세션 위험 기반 로그인을 차단 하는 조건부 액세스 정책을 구성 하는 방법에 대해 알아봅니다.
services: active-directory
keywords: id 보호, Azure AD에서 회사 리소스에 조건부 액세스 정책에 대 한 보안 액세스를 사용 하 여 조건부 액세스 앱에 조건부 액세스
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c04d1a01c0ffd69e70dfa3b88b4f3c7f4b3576d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108796"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>빠른 시작: Azure Active Directory Identity Protection을 사용하여 세션 위험 검색 시 액세스 차단  

환경을 보호하려는 경우 의심스러운 사용자의 로그인을 차단할 수 있습니다. Azure AD(Azure Active Directory) Identity Protection은 각 로그인을 분석하고 사용자 계정의 합법적인 소유자가 수행하지 않은 로그인 시도 가능성을 계산합니다. 가능성(낮음, 중간, 높음)은 로그인 위험 수준이라는 계산된 값 형식으로 표시됩니다. 로그인 위험 조건으로 설정 하면 로그인 위험이 특정 로그인 위험 수준에 응답 하는 조건부 액세스 정책을 구성할 수 있습니다. 

이 빠른 시작에서는 로그인 위험을 로그인에는 보통 때 차단 하는 조건부 액세스 정책을 구성 하는 방법 및 수준 이상의 로그인 위험이 감지 되었습니다. 

![정책 만들기](./media/quickstart-sign-in-risk-policy/1004.png)


Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.



## <a name="prerequisites"></a>필수 조건 

이 자습서의 시나리오를 완료하려면 다음이 필요합니다.

- **Azure AD Premium P2 버전 액세스** - Azure AD Identity Protection은 Azure AD Premium P2의 기능입니다. 

- **Identity Protection** - 이 빠른 시작의 시나리오에서는 Identity Protection을 사용하도록 설정해야 합니다. Identity Protection을 사용하도록 설정하는 방법을 모르면 [Azure Active Directory Identity Protection 활성화](../identity-protection/enable.md)를 참조하세요.

- **Tor 브라우저** - [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en)는 온라인 개인 정보를 유지할 수 있도록 설계되었습니다. Identity Protection은 Tor 브라우저에서의 로그인을 보통 위험 수준인 **익명 IP 주소에서의 로그인**으로 감지합니다. 자세한 내용은 [Azure Active Directory 위험 이벤트](../reports-monitoring/concept-risk-events.md)를 참조하세요.  

- **Alain Charon 테스트 계정** - 테스트 계정을 만드는 방법을 모르는 경우 [새 사용자 추가](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)를 참조하세요.


## <a name="test-your-sign-in"></a>로그인 테스트 

이 단계의 목표는 테스트 계정이 Tor 브라우저를 사용하여 테넌트에 액세스할 수 있도록 하는 것입니다.

**로그인을 테스트하려면:**

1. [Azure Portal](https://portal.azure.com)에 **Alain Charon**으로 로그인합니다.

2. 로그아웃합니다. 


## <a name="create-your-conditional-access-policy"></a>조건부 액세스 정책 만들기 

이 빠른 시작에서 시나리오는 Tor 브라우저에서의 로그인을 사용하여 감지된 **익명 IP 주소에서 로그인** 위험 이벤트를 생성합니다. 이 위험 이벤트의 위험 수준은 보통입니다. 이 위험 이벤트에 응답하려면 로그인 위험 조건을 보통으로 설정합니다. 

이 섹션에서는 필요한 로그인 위험 조건부 액세스 정책을 만드는 방법을 보여 줍니다. 정책에서 다음을 설정합니다.

|설정 |값|
|---     | --- |
| 사용자  | Alain Charon  |
| 조건 | 로그인 위험, 중간 이상 |
| 컨트롤 | 액세스 차단 |
 

![정책 만들기](./media/quickstart-sign-in-risk-policy/201.png)

 


**에 대 한 조건부 액세스 정책을 구성 하려면:**

1. 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. [Azure AD Identity Protection 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview)로 이동합니다.
 
3. **Azure AD Identity Protection** 페이지의 **구성** 섹션에서 **로그인 위험 정책**을 클릭합니다.
 
4. 정책 페이지의 **할당** 섹션에서 **사용자**를 클릭합니다.

5. **사용자** 페이지에서 **사용자 선택**을 클릭합니다.

6. **사용자 선택** 페이지에서 **Alain Charon**을 선택하고 **선택**을 클릭합니다.

7. **사용자** 페이지에서 **완료**를 클릭합니다. 

8. 정책 페이지의 **할당** 섹션에서 **조건**을 클릭합니다.

9. **조건** 페이지에서 **로그인 위험**을 클릭합니다.

10. **로그인 위험** 페이지에서 **보통 이상**을 선택하고 **선택**을 클릭합니다. 

11. **조건** 페이지에서 **완료**를 클릭합니다.

12. 정책 페이지의 **컨트롤** 섹션에서 **액세스**를 클릭합니다.

13. **액세스** 페이지에서 **액세스 허용**을 클릭하고 **다단계 인증 기능 필요**를 선택한 후에 **선택**을 클릭합니다.

14. 정책 페이지에서 **저장**을 클릭합니다.  


## <a name="test-your-conditional-access-policy"></a>조건부 액세스 정책 테스트

정책을 테스트하려면 Tor 브라우저를 사용하여 [Azure Portal](https://portal.azure.com)에 **Alan Charon**으로 로그인을 시도합니다. 조건부 액세스 정책에 따라 로그인 시도 차단 되어야 합니다.

![Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는, Tor 브라우저를 테스트 사용자를 삭제 하 고 로그인 위험 조건부 액세스 정책을 사용 하지 않도록 설정 합니다.

- Azure AD 사용자를 삭제하는 방법을 모르면 [사용자를 추가하거나 삭제하는 방법](../fundamentals/add-users-azure-active-directory.md#delete-a-user)을 참조하세요.

- Tor 브라우저를 제거할 지침은 [제거](https://tb-manual.torproject.org/uninstalling/)를 참조하세요.


