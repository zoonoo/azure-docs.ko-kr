---
title: "Azure Portal에서 클래식 정책 마이그레이션 | Microsoft Docs"
description: "Azure Portal에서 클래식 정책을 마이그레이션합니다."
services: active-directory
keywords: "앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Azure Portal에서 클래식 정책 마이그레이션 


[조건부 액세스](active-directory-conditional-access-azure-portal.md)는 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있게 해 주는 Azure AD(Azure Active Directory)의 기능입니다. 그 목적은 동일하지만 조건부 액세스가 작동하는 방식이 크게 개선된 새로운 Azure Portal 릴리스도 도입되었습니다. Azure Portal 외부에서 구성한 조건부 액세스 정책은 Azure Portal에서 작성 중인 새 정책과 공존할 수 있습니다. 사용하지 않도록 설정하거나 제거하지 않는 한 계속 사용자 환경에 적용됩니다. 그러나 다음과 같은 이유로 클래식 정책을 새로운 Azure AD 조건부 액세스 정책으로 마이그레이션하는 것이 좋습니다.

- 새 정책을 사용하면 클래식 정책으로는 처리할 수 없는 시나리오를 해결할 수 있습니다.

- 통합하여 관리해야 하는 정책의 수를 줄일 수 있습니다.   

이 항목을 사용하면 더 편리하게 기존 클래식 정책을 새로운 Azure AD 조건부 액세스 정책으로 마이그레이션할 수 있습니다.


## <a name="classic-policies"></a>클래식 정책

Azure Portal에서 작성하지 않은 Azure AD 및 Intune의 조건부 액세스 정책을 **클래식 정책**이라고 합니다. 클래식 정책을 마이그레이션하기 위 Azure 클래식 포털에 액세스할 필요가 없습니다. Azure Portal은 클래식 정책을 검토할 수 있는 [**클래식 정책(미리 보기)** 보기](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)를 제공합니다.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>클래식 정책 열기

**클래식 정책을 열려면:**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/02.png)
 
2. **조건부 액세스 - 정책** 페이지의 **관리** 섹션에서 **클래식 정책(미리 보기)**을 클릭합니다.

3. 클래식 정책 목록에서 원하는 정책을 선택합니다.   

    ![조건부 액세스](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Azure AD 조건부 액세스 정책

이 항목에서는 Azure AD 조건부 액세스 정책에 익숙하지 않아도 클래식 정책을 마이그레이션할 수 있는 자세한 단계를 제공합니다. 그러나 Azure AD 조건부 액세스의 기본 개념과 용어에 익숙하면 마이그레이션 환경을 더 쉽게 향상시킬 수 있습니다.

다음을 참조하세요.

- [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md) - 기본 개념과 용어에 대해 알아 봅니다.

- [Azure Active Directory의 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md) - Azure Portal의 사용자 인터페이스에 대해 알아 봅니다.


 





## <a name="multi-factor-authentication-policy"></a>다단계 인증 정책 

이 예제에서는 클라우드 앱에 다단계 인증**이 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**클래식 정책을 마이그레이션하려면:**

1. [클래식 정책을 열어](#open-a-classic-policy) 구성 설정을 가져옵니다.
2. 클래식 정책을 대체할 새 Azure AD 조건부 액세스 정책을 만듭니다. 


### <a name="create-a-new-conditional-access-policy"></a>새 조건부 액세스 정책 만들기


1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/02.png)



3. **조건부 액세스** 페이지에서 **새로 만들기** 페이지를 열려면 위쪽의 도구 모음에서 **추가**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/03.png)

4. **새로 만들기** 페이지의 **이름** 텍스트 상자에서 정책 이름을 입력합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/29.png)

5. **할당** 섹션에서 **사용자 및 그룹**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/05.png)

    a. 클래식 정책에서 모든 사용자를 선택한 경우 **모든 사용자**를 클릭합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-migration/35.png)

    b. 클래식 정책에서 그룹을 선택한 경우 **사용자 및 그룹 선택**을 클릭한 다음 필요한 사용자 및 그룹을 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/36.png)

    c. 제외된 그룹이 있는 경우 **제외** 탭을 클릭한 다음 필요한 사용자 및 그룹을 선택합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-migration/37.png)

6. **새로 만들기** 페이지에서 **클라우드 앱** 페이지를 열려면 **할당** 섹션에서 **클라우드 앱**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. **클라우드 앱** 페이지에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/08.png)

    a. **앱 선택**을 클릭합니다.

    b. **선택**을 클릭합니다.

    c. **선택** 페이지에서 클라우드 앱을 선택한 다음 **선택**을 클릭합니다.

    d. **클라우드 앱** 페이지에서 **완료**를 클릭합니다.



9. **다단계 인증 필요**를 선택한 경우:

    ![조건부 액세스](./media/active-directory-conditional-access-migration/26.png)

    a. **액세스 제어** 섹션에서 **허용**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/27.png)

    b. **허용** 페이지에서 **액세스 허용**을 클릭하고 **다단계 인증 필요**를 클릭합니다.

    c. **선택**을 클릭합니다.


10. **설정**을 클릭하여 정책을 사용하도록 설정합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/30.png)

11. 클래식 정책을 사용하지 않도록 설정합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 
