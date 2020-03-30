---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝을 위한 Zscaler One 구성 | 마이크로 소프트 문서'
description: Zscaler One에 사용자 계정을 자동으로 프로비전 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064175"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위해 Zscaler One 구성

이 자습서에서는 Zscaler One 및 Azure Active Directory(Azure AD)에서 수행하여 사용자 및 그룹을 Zscaler One으로 자동으로 프로비전 및 프로비저닝 해제하도록 Azure AD를 구성하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스의 작동 방식, 작동 방식 및 자주 묻는 질문에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS(서비스형 소프트웨어) 응용 프로그램에 대한 사용자 프로비저닝 및 프로비저닝 을 자동화합니다.](../active-directory-saas-app-provisioning.md)


## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오는 다음과 같은 것을 가정합니다.

* Azure AD 테넌트.
* Zscaler One 테넌트.
* 관리자 권한이 있는 Zscaler One의 사용자 계정입니다.

> [!NOTE]
> Azure AD 프로비저닝 통합은 Zscaler One SCIM API를 기반으로 합니다. 이 API는 엔터프라이즈 패키지가 있는 계정에 대해 Zscaler One 개발자가 사용할 수 있습니다.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Azure 마켓플레이스에서 Zscaler One 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위해 Zscaler One을 구성하기 전에 Azure 마켓플레이스의 Zscaler One을 관리되는 SaaS 응용 프로그램 목록에 추가합니다.

마켓플레이스에서 Zscaler One을 추가하려면 다음 단계를 따르십시오.

1. Azure [포털에서](https://portal.azure.com)왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active 디렉터리 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Zscaler One을** 입력하고 결과 패널에서 **Zscaler One을** 선택합니다. 응용 프로그램을 추가하려면 **에 추가를**선택합니다.

    ![결과 목록의 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Zscaler 하나에 사용자 할당

Azure Active Directory는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD에서 Zscaler One에 액세스해야 하는 사용자 또는 그룹을 결정합니다. 이러한 사용자 또는 그룹을 Zscaler One에 할당하려면 [엔터프라이즈 앱에 사용자 또는 그룹 할당의](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)지침을 따릅니다.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Zscaler One에 사용자를 할당하기 위한 중요한 팁

* 자동 사용자 프로비저닝 구성을 테스트하려면 단일 Azure AD 사용자를 Zscaler One에 할당하는 것이 좋습니다. 나중에 추가 사용자 또는 그룹을 할당할 수 있습니다.

* 사용자를 Zscaler One에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Zscaler One에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다. Azure AD의 사용자 또는 그룹 할당을 기반으로 Zscaler One에서 사용자 또는 그룹을 생성, 업데이트 및 사용하지 않도록 설정하는 데 사용합니다.

> [!TIP]
> Zscaler One에 대해 SAML 기반 단일 사인온을 활성화할 수도 있습니다. [Zscaler 하나의 단일 사인온 자습서의](zscaler-One-tutorial.md)지침을 따르십시오. 단일 사인온은 자동 사용자 프로비저닝과 독립적으로 구성할 수 있지만 이러한 두 기능은 서로 보완됩니다.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Azure AD에서 Zscaler One에 대한 자동 사용자 프로비저닝 구성

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램** > **선택 모든 응용 프로그램** > **Zscaler 하나**.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler One**을 선택합니다.

    ![응용 프로그램 목록의 Zscaler One 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![Zscaler 원 프로비저닝](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![Zscaler 원 프로비저닝 모드](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 관리자 **자격 증명** 섹션에서 6단계에 설명된 대로 **테넌트 URL** 및 비밀 **토큰** 상자를 Zscaler One 계정에 대한 설정으로 입력합니다.

6. 테넌트 URL 및 비밀 토큰을 가져오려면 Zscaler One 포털 UI의 **관리** > **인증 설정으로** 이동합니다. **인증 형식**에서 **SAML**을 선택합니다.

    ![Zscaler 하나의 인증 설정](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. **SAML 구성을** 선택하여 **SAML 구성** 옵션을 엽니다.

    ![Zscaler 하나 구성 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. **기본 URL** 및 **베어러 토큰의**설정을 얻으려면 **SCIM 기반 프로비저닝 활성화를** 선택합니다. 그런 다음 설정을 저장합니다. 기본 **URL** 설정을 Azure 포털의 **테넌트 URL에** 복사합니다. Azure 포털의 비밀 **토큰에** **Bearer 토큰** 설정을 복사합니다.

7. 5단계에 표시된 상자를 입력한 후 **테스트 연결을** 선택하여 Azure AD가 Zscaler One에 연결할 수 있는지 확인합니다. 연결이 실패하면 Zscaler One 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![Zscaler 원 테스트 연결](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 알림 **전자 메일** 상자에 개인 또는 그룹의 전자 메일 주소를 입력하여 프로비저닝 오류 알림을 받습니다. 오류가 **발생할 때 전자 메일 보내기 확인란을** 선택합니다.

    ![Zscaler 하나의 알림 이메일](./media/zscaler-one-provisioning-tutorial/notification.png)

9. **저장**을 선택합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자를 Zscaler One으로 동기화합니다.**

    ![Zscaler 한 사용자 동기화](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 속성 매핑 섹션에서 Azure AD에서 Zscaler One으로 동기화된 사용자 **특성을 검토합니다.** **일치** 속성으로 선택한 특성은 업데이트 작업에 대한 Zscaler One의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장을**선택합니다.

    ![Zscaler 하나 일치하는 사용자 속성](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. **매핑** 섹션에서 **Azure Active Directory 그룹 동기화를 Zscaler One으로**선택합니다.

    ![Zscaler 1 그룹 동기화](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 특성 매핑 섹션에서 Azure AD에서 Zscaler One으로 동기화된 그룹 **특성을 검토합니다.** **일치** 속성으로 선택한 특성은 업데이트 작업에 Zscaler One의 그룹을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장을**선택합니다.

    ![Zscaler 하나 일치 하는 그룹 속성](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서의](./../active-directory-saas-scoping-filters.md)지침을 따릅니다.

15. **Zscaler** One에 대한 Azure AD 프로비저닝 서비스를 설정 섹션에서 **설정** **으로 프로비저닝 상태를** 변경합니다.

    ![Zscaler 원 프로비저닝 상태](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zscaler One에 프로비전할 사용자 또는 그룹을 정의합니다. **설정** 섹션에서 **범위에서**원하는 값을 선택합니다.

    ![Zscaler 원 스코프](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![Zscaler 원 세이브](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 나중에 동기화하는 것보다 수행하는 데 시간이 오래 걸립니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생합니다. 

동기화 세부 **정보** 섹션을 사용하여 진행 상황을 모니터링하고 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다. 이 보고서는 Zscaler One에서 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../active-directory-saas-provisioning-reporting.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../active-directory-saas-provisioning-reporting.md).

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
