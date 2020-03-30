---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Samanage 구성 | Microsoft Docs'
description: Azure Active Directory를 구성하여 사용자 계정을 Samanage에 자동으로 프로비전및 프로비저닝 해제하도록 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060493"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Samanage 구성

이 자습서에서는 Samanage 및 Azure Active Directory(Azure AD)에서 수행하는 단계를 보여 주며 Azure AD를 구성하여 사용자 및 그룹을 Samanage에 자동으로 프로비전및 프로비저닝 해제하도록 구성합니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스의 작동 방식, 작동 방식 및 자주 묻는 질문에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS(서비스형 소프트웨어) 응용 프로그램에 대한 사용자 프로비저닝 및 프로비저닝 을 자동화합니다.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오는 다음과 같은 것을 가정합니다.

* Azure AD 테넌트.
* 프로페셔널 패키지가 있는 [Samanage 테넌트입니다.](https://www.samanage.com/pricing/)
* 관리자 권한이 있는 Samanage의 사용자 계정입니다.

> [!NOTE]
> Azure AD 프로비저닝 통합은 [Samanage Rest API에](https://www.samanage.com/api/)의존합니다. 이 API는 전문가 패키지가 있는 계정에 대해 Samanage 개발자가 사용할 수 있습니다.

## <a name="add-samanage-from-the-azure-marketplace"></a>Azure 마켓플레이스에서 Samanage 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위해 Samanage를 구성하기 전에 Azure 마켓플레이스에서 관리되는 SaaS 응용 프로그램 목록에 Samanage를 추가합니다.

마켓플레이스에서 Samanage를 추가하려면 다음 단계를 따르세요.

1. Azure [포털에서](https://portal.azure.com)왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active 디렉터리 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Samanage를** 입력하고 결과 패널에서 **Samanage를** 선택합니다. 응용 프로그램을 추가하려면 **에 추가를**선택합니다.

    ![결과 목록의 Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Samanage에 사용자 할당

Azure Active Directory는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD에서 Samanage에 액세스해야 하는 사용자 또는 그룹을 결정합니다. 이러한 사용자 또는 그룹을 Samanage에 할당하려면 [엔터프라이즈 앱에 사용자 또는 그룹 할당의](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)지침을 따릅니다.

### <a name="important-tips-for-assigning-users-to-samanage"></a>Samanage에 사용자를 할당하기 위한 주요 팁

*    현재 Samanage 역할은 Azure 포털 UI에 자동으로 동적으로 채워집니다. 사용자에게 Samanage 역할을 할당하기 전에 Samanage에 대해 초기 동기화가 완료되어 Samanage 테넌트의 최신 역할을 검색해야 합니다.

*    Samanage에 단일 Azure AD 사용자를 할당하여 초기 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 테스트가 성공한 후 나중에 추가 사용자 및 그룹을 할당할 수 있습니다.

*    사용자를 Samanage에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Samanage에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다. Azure AD의 사용자 또는 그룹 할당을 기반으로 Samanage에서 사용자 또는 그룹을 생성, 업데이트 및 비활성화하는 데 사용합니다.

> [!TIP]
> Samanage에 대해 SAML 기반 단일 사인온을 활성화할 수도 있습니다. [Samanage 단일 사인온 자습서의](samanage-tutorial.md)지침을 따르십시오. 단일 사인온은 자동 사용자 프로비저닝과 독립적으로 구성할 수 있지만 이러한 두 기능은 서로 보완됩니다.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Azure AD에서 Samanage에 대한 자동 사용자 프로비저닝 구성

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램** > 선택**모든 응용 프로그램** > **Samanage.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Samanage**를 선택합니다.

    ![응용 프로그램 목록의 Samanage 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![Samanage 프로비전](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 관리자 **자격 증명** 섹션에서 Samanage **테넌트 URL** 및 **비밀 토큰을**입력합니다. Azure AD가 Samanage에 연결할 수 있는지 확인하려면 **테스트 연결을** 클릭합니다. 연결에 실패하면 Samanage 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![Samanage 테스트 연결](./media/samanage-provisioning-tutorial/provisioning.png)

6. 알림 **전자 메일** 상자에 개인 또는 그룹의 전자 메일 주소를 입력하여 프로비저닝 오류 알림을 받습니다. 오류가 **발생할 때 전자 메일 보내기 확인란을** 선택합니다.

    ![사관리 알림 이메일](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. **저장**을 선택합니다.

8. 매핑 섹션에서 **Azure Active Directory 사용자를 Samanage로 동기화를** **선택합니다.**

    ![사용자 동기화 관리](./media/samanage-provisioning-tutorial/UserMappings.png)

9. **특성 매핑** 섹션에서 Azure AD에서 Samanage로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Samanage의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장을**선택합니다.

    ![일치하는 사용자 속성 관리](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. 그룹 매핑을 사용하려면 **매핑** 섹션에서 **Azure Active Directory 그룹을 Samanage에 동기화**를 선택합니다.

    ![Samanage 그룹 동기화](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. **사용**을 **예**로 설정하여 그룹을 동기화합니다. **특성 매핑** 섹션에서 Azure AD에서 Samanage로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Samanage의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장을**선택합니다.

    ![Samanage 매칭 그룹 속성](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)지침을 따릅니다.

13. Samanage에 대한 Azure AD 프로비저닝 서비스를 활성화하려면 **설정** 섹션에서 **프로비저닝 상태를** **을 로**변경합니다.

    ![사관리 프로비저닝 상태](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Samanage에 프로비전할 사용자 또는 그룹을 정의합니다. **설정** 섹션에서 **범위에서**원하는 값을 선택합니다. 모든 사용자 **및 그룹 동기화** 옵션을 선택하면 다음 섹션 "커넥터 제한"에 설명된 제한 사항이 고려됩니다.

    ![사관리 범위](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![사관리 저장](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 나중에 동기화하는 것보다 수행하는 데 시간이 오래 걸립니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생합니다. 

동기화 세부 **정보** 섹션을 사용하여 진행 상황을 모니터링하고 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다. 이 보고서는 Samanage의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

모든 사용자 **및 그룹 동기화** 옵션을 선택하고 Samanage **역할** 특성에 대한 값을 구성하는 경우 **null(선택 사항)** 상자가 다음 형식으로 표현되어야 하는 경우 기본값 아래의 값을 구성합니다.

- {"displayName":"역할"}, 여기서 역할은 당신이 원하는 기본값입니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
