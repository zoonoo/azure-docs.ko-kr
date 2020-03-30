---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Tableau Online 구성 | Microsoft Docs'
description: 사용자 계정을 Tableau Online에 자동으로 프로비전 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064220"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Tableau Online 구성

이 자습서에서는 Tableau Online 및 Azure Active Directory(Azure AD)에서 수행하여 사용자 및 그룹을 Tableau Online에 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure AD를 구성하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스의 작동 방식, 작동 방식 및 자주 묻는 질문에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS(서비스형 소프트웨어) 응용 프로그램에 대한 사용자 프로비저닝 및 프로비저닝 을 자동화합니다.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오는 다음과 같은 것을 가정합니다.

*   Azure AD 테넌트.
*   [Tableau 온라인 테넌트.](https://www.tableau.com/)
*   관리자 권한이 있는 Tableau Online의 사용자 계정입니다.

> [!NOTE]
> Azure AD 프로비저닝 통합은 [Tableau 온라인 나머지 API에](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)의존합니다. 이 API는 Tableau Online 개발자가 사용할 수 있습니다.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Azure 마켓플레이스에서 Tableau 온라인 추가
Azure AD를 사용하여 자동 사용자 프로비전을 위해 Tableau Online을 구성하기 전에 Azure 마켓플레이스의 Tableau Online을 관리되는 SaaS 응용 프로그램 목록에 추가합니다.

마켓플레이스에서 Tableau Online을 추가하려면 다음 단계를 따르세요.

1. Azure [포털에서](https://portal.azure.com)왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active 디렉터리 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Tableau Online을** 입력하고 결과 패널에서 **Tableau Online을** 선택합니다. 응용 프로그램을 추가하려면 **에 추가를**선택합니다.

    ![결과 목록의 Tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Tableau 온라인에 사용자 할당

Azure Active Directory는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD에서 Tableau Online에 액세스해야 하는 사용자 또는 그룹을 결정합니다. 이러한 사용자 또는 그룹을 Tableau Online에 할당하려면 [엔터프라이즈 앱에 사용자 또는 그룹 할당의](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)지침을 따릅니다.

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Tableau Online에 사용자를 할당하기 위한 주요 팁

*   자동 사용자 프로비저닝 구성을 테스트하려면 단일 Azure AD 사용자를 Tableau Online에 할당하는 것이 좋습니다. 나중에 추가 사용자 또는 그룹을 할당할 수 있습니다.

*   사용자를 Tableau Online에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Tableau Online에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다. Azure AD의 사용자 또는 그룹 할당을 기반으로 Tableau Online에서 사용자 또는 그룹을 생성, 업데이트 및 사용하지 않도록 설정하는 데 사용합니다.

> [!TIP]
> 또한 Tableau Online에 대해 SAML 기반 단일 사인온을 활성화할 수도 있습니다. [Tableau Online 단일 사인온 자습서의 지침을 따릅니다.](tableauonline-tutorial.md) 단일 사인온은 자동 사용자 프로비저닝과 독립적으로 구성할 수 있지만 이러한 두 기능은 서로 보완됩니다.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Azure AD에서 Tableau Online에 대한 자동 사용자 프로비저닝 구성

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램** > 선택**모든 응용 프로그램** > **Tableau 온라인**.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Tableau Online**을 선택합니다.

    ![응용 프로그램 목록의 Tableau 온라인 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![Tableau Online 프로비전](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![Tableau 온라인 프로비저닝 모드](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 관리자 **자격 증명** 섹션에서 Tableau Online 계정의 도메인, 관리자 사용자 이름, 관리자 암호 및 콘텐츠 URL을 입력합니다.

   * **도메인** 상자에서 6단계를 기준으로 하위 도메인을 입력합니다.

   * 관리자 **사용자 이름** 상자에 Clarizen 테넌트의 관리자 계정 의 사용자 이름을 입력합니다. 예제는 admin@contoso.com입니다.

   * 관리 **암호** 상자에 관리자 사용자 이름에 해당하는 관리자 계정의 암호를 입력합니다.

   * 콘텐츠 **URL** 상자에 6단계를 기준으로 하위 도메인을 입력합니다.

6. Tableau Online의 관리 계정에 로그인하면 관리자 페이지의 URL에서 **도메인** 및 **콘텐츠 URL값을** 얻을 수 있습니다.

    * Tableau Online 계정에 대한 **도메인**을 URL의 이 부분에서 복사할 수 있습니다.

        ![Tableau 온라인 도메인](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Tableau Online 계정의 **콘텐츠 URL은** 이 섹션에서 복사할 수 있습니다. 계정 설정 중에 정의된 값입니다. 이 예제에서 값은 “contoso”입니다.

        ![Tableau 온라인 컨텐더 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > **도메인이** 여기에 표시된 도메인과 다를 수 있습니다.

7. 5단계에 표시된 상자를 입력한 후 **테스트 연결을** 선택하여 Azure AD가 Tableau Online에 연결할 수 있는지 확인합니다. 연결이 실패하면 Tableau Online 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![Tableau 온라인 테스트 연결](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 알림 **전자 메일** 상자에 개인 또는 그룹의 전자 메일 주소를 입력하여 프로비저닝 오류 알림을 받습니다. 오류가 **발생할 때 전자 메일 보내기 확인란을** 선택합니다.

    ![Tableau 온라인 알림 이메일](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. **저장**을 선택합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자를 Tableau Online에 동기화**를 선택합니다.

    ![Tableau 온라인 사용자 동기화](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 속성 매핑 섹션에서 Azure AD에서 Tableau Online으로 동기화된 사용자 **특성을 검토합니다.** **일치** 속성으로 선택한 특성은 업데이트 작업 시 Tableau Online의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장을**선택합니다.

    ![Tableau 온라인 매칭 사용자 속성](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. **매핑** 섹션에서 **Azure Active Directory 그룹을 Tableau에 동기화**를 선택합니다.

    ![Tableau 온라인 그룹 동기화](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 특성 매핑 섹션에서 Azure AD에서 Tableau Online으로 동기화된 그룹 **특성을 검토합니다.** **일치** 속성으로 선택한 특성은 업데이트 작업 시 Tableau Online의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장을**선택합니다.

    ![Tableau 온라인 매칭 그룹 특성](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)지침을 따릅니다.

15. Tableau Online에 대한 Azure AD 프로비저닝 서비스를 활성화하려면 **설정** 섹션에서 **프로비저닝 상태를** **을 로**변경합니다.

    ![Tableau 온라인 프로비저닝 상태](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Tableau Online에 프로비전할 사용자 또는 그룹을 정의합니다. **설정** 섹션에서 **범위에서**원하는 값을 선택합니다.

    ![Tableau 온라인 스코프](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![Tableau 온라인 저장](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 나중에 동기화하는 것보다 수행하는 데 시간이 오래 걸립니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생합니다. 

동기화 세부 **정보** 섹션을 사용하여 진행 상황을 모니터링하고 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다. 이 보고서는 Tableau Online에서 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
