---
title: '자습서: Azure Active Directory를 사용하여 Zscaler One에 대한 자동 사용자 프로비저닝 구성 | Microsoft Docs'
description: 사용자 계정을 Zscaler One으로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b8b6383c7808fd6c298d7776fc10572631bc6ddc
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359564"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>자습서: Zscaler One에 대한 자동 사용자 프로비저닝 구성

이 자습서에서는 사용자 및 그룹을 Zscaler One으로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Zscaler One 및 Azure AD(Azure Active Directory)에서 수행하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스를 기반으로 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 정보는 [Azure Active Directory를 사용하여 SaaS(Software as a Service) 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명한 시나리오에서는 사용자에게 다음 항목이 있다고 가정합니다.

* Azure AD 테넌트.
* Zscaler One 테넌트
* 관리자 권한이 있는 Zscaler One의 사용자 계정

> [!NOTE]
> Azure AD 프로비저닝 통합은 Zscaler One SCIM API에 의존합니다. 이 API는 Enterprise 패키지를 사용하는 계정에 대해 Zscaler One 개발자가 사용할 수 있습니다.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Azure Marketplace에서 Zscaler One 추가

Azure AD를 사용하여 사용자를 자동으로 프로비저닝하도록 Zscaler One을 구성하기 전에 Zscaler One을 Azure Marketplace에서 관리형 SaaS 애플리케이션 목록으로 추가해야 합니다.

Azure Marketplace에서 Zscaler One을 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Zscaler One** 을 입력하고 결과 패널에서 **Zscaler One** 을 선택합니다. 애플리케이션을 추가하려면 **추가** 를 선택합니다.

    ![결과 목록의 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Zscaler One에 사용자 할당

Azure Active Directory는 *할당* 이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Zscaler One에 액세스해야 하는 Azure AD의 사용자 또는 그룹을 결정합니다. 이러한 사용자 또는 그룹을 Zscaler One에 할당하려면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 지침을 따르세요.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>사용자를 Zscaler One에 할당하기 위한 주요 팁

* 먼저 단일 Azure AD 사용자를 Zscaler One에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 또는 그룹을 나중에 할당할 수 있습니다.

* 사용자를 Zscaler One에 할당할 때 할당 대화 상자에서 유효한 애플리케이션별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Zscaler One에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다. 이는 Azure AD의 사용자 또는 그룹 할당을 기반으로 Zscaler One에서 사용자 또는 그룹을 만들고, 업데이트하고, 사용하지 않도록 설정하는 데 사용됩니다.

> [!TIP]
> Zscaler One에 대해 SAML 기반 Single Sign-On을 사용하도록 설정할 수도 있습니다. [Zscaler One Single Sign-On 자습서](zscaler-One-tutorial.md)의 지침을 따르세요. Single Sign-On은 자동 사용자 프로비저닝과 별개로 구성할 수 있지만, 이러한 두 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Azure AD에서 Zscaler One에 대한 자동 사용자 프로비저닝 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **Zscaler One** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler One** 을 선택합니다.

    ![애플리케이션 목록의 Zscaler One 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Zscaler One 프로비저닝](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![Zscaler One 프로비저닝 모드](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. **관리자 자격 증명** 섹션에서 6단계에서 설명한 대로 Zscaler One 계정에 대한 설정으로 **테넌트 URL** 및 **비밀 토큰** 상자를 채웁니다.

6. 테넌트 URL 및 비밀 토큰을 가져오려면 Zscaler One 포털 UI에서 **관리** > **인증 설정** 으로 이동합니다. **인증 형식** 에서 **SAML** 을 선택합니다.

    ![Zscaler One 인증 설정](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. **SAML 구성** 을 선택하여 **SAML 구성** 옵션을 엽니다.

    ![Zscaler One SAML 구성](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. **SCIM 기반 프로비저닝 사용** 을 선택하여 **기본 URL** 및 **전달자 토큰** 의 설정을 가져옵니다. 그런 다음, 설정을 저장합니다. **기본 URL** 설정을 Azure Portal의 **테넌트 URL** 로 복사합니다. **전달자 토큰** 설정을 Azure Portal의 **비밀 토큰** 으로 복사합니다.

7. 5단계에 표시된 상자를 채운 후 **연결 테스트** 를 선택하여 Azure AD가 Zscaler One에 연결할 수 있는지 확인합니다. 연결이 실패하면 Zscaler One 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![Zscaler One 테스트 연결](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사람 또는 그룹의 이메일 주소를 입력합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![Zscaler One 알림 이메일](./media/zscaler-one-provisioning-tutorial/notification.png)

9. **저장** 을 선택합니다.

10. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Zscaler One으로 동기화** 를 선택합니다.

    ![Zscaler One 사용자 동기화](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. **특성 매핑** 섹션에서 Azure AD에서 Zscaler One으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Zscaler One의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장** 을 선택합니다.

    ![Zscaler One 일치 사용자 특성](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Zscaler One으로 동기화** 를 선택합니다.

    ![Zscaler One 그룹 동기화](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. **특성 매핑** 섹션에서 Azure AD에서 Zscaler One으로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택된 특성은 업데이트 작업을 위해 Zscaler One의 그룹을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장** 을 선택합니다.

    ![Zscaler One 일치 그룹 특성](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 따르세요.

15. Zscaler One에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![Zscaler One 프로비전 상태](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zscaler One에 프로비저닝할 사용자 또는 그룹을 정의합니다. **설정** 섹션의 **범위** 에 원하는 값을 선택합니다.

    ![Zscaler One 범위](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![Zscaler One 저장](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 이후 동기화보다 더 오랜 시간이 걸립니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분 간격으로 발생합니다. 

**동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 통해 프로비저닝 활동 보고서를 확인할 수 있습니다. 이 보고서는 Zscaler One에서 Azure AD 프로비저닝 서비스가 수행하는 모든 작업을 설명합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png