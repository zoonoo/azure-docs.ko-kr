---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Foodee 구성 | Microsoft Docs'
description: 사용자 계정을 Foodee로 자동으로 프로비저닝 및 프로비저닝 해제를 하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358683"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 Foodee 구성

이 문서에서는 Foodee 및 Azure AD(Azure Active Directory)에서 사용자 또는 그룹을 Foodee로 자동으로 프로비저닝 및 프로비저닝 해제를 하도록 Azure AD를 구성하는 방법을 알아봅니다.

> [!NOTE]
> 이 문서에서는 Azure AD 사용자 프로비저닝 서비스를 기반으로 하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능과 작동 방식 및 질문과 대답에 대한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션으로 사용자 프로비저닝/프로비저닝 해제 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 미리 보기 기능의 Azure 사용 약관 기능에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 다음과 같은 사전 요구 사항이 충족되었다고 가정합니다.

* Azure AD 테넌트
* [Foodee 테넌트](https://www.food.ee/about/)
* 관리자 권한이 있는 Foodee의 사용자 계정

## <a name="assign-users-to-foodee"></a>Foodee에 사용자 할당 

Azure AD는 *할당* 이라는 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 애플리케이션에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Foodee에 액세스해야 하는 Azure AD의 사용자 또는 그룹을 결정해야 합니다. 결정되면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 지침에 따라 이러한 사용자 또는 그룹을 Foodee에 할당할 수 있습니다.

## <a name="important-tips-for-assigning-users-to-foodee"></a>사용자를 Foodee에 할당하기 위한 주요 팁 

사용자를 할당하는 경우 다음 팁에 주의하세요.

* 단일 Azure AD 사용자만 Foodee에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 또는 그룹을 나중에 할당할 수 있습니다.

* 사용자를 Foodee에 할당할 때 **할당** 창에서 유효한 애플리케이션 특정 역할(사용 가능한 경우)을 선택합니다. *기본 액세스* 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-foodee-for-provisioning"></a>프로비저닝을 수행하도록 Foodee 설정

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Foodee를 구성하려면 먼저 Foodee에서 SCIM(System for Cross-domain Identity Management) 프로비저닝을 사용하도록 설정해야 합니다.

1. [Foodee](https://www.food.ee/login/)에 로그인한 다음, 테넌트 ID를 선택합니다.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Foodee 엔터프라이즈 포털의 주 메뉴에 대한 스크린샷. 메뉴에 테넌트 ID 자리 표시자가 표시되어 있습니다." border="false":::

1. **Enterprise Portal(엔터프라이즈 포털)** 아래에서 **Single Sign-on** 을 선택합니다.

    ![Foodee Enterprise Portal 왼쪽 창 메뉴](media/Foodee-provisioning-tutorial/scim.png)

1. 나중에 사용할 수 있도록 **API Token(API 토큰)** 상자의 값을 복사합니다. 이 값은 Azure Portal에 있는 Foodee 애플리케이션에 대한 **프로비저닝** 탭의 **비밀 토큰** 상자에 입력됩니다.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Foodee Enterprise Portal의 페이지에 대한 스크린샷. API 토큰 값이 강조 표시되어 있습니다." border="false":::

## <a name="add-foodee-from-the-gallery"></a>갤러리에서 Foodee 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Foodee를 구성하려면 Azure AD 애플리케이션 갤러리에서 Foodee를 관리형 SaaS 애플리케이션 목록에 추가해야 합니다.

Azure AD 애플리케이션 갤러리에서 Foodee를 추가하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 명령](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 새 애플리케이션을 추가하려면 창 위쪽에서 **새 애플리케이션** 을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

1. 검색 상자에서 **Foodee** 를 입력하고, 결과 패널에서 **Foodee** 를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Foodee](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Foodee에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당에 따라 Foodee에서 사용자 또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성합니다.

> [!TIP]
> [Foodee Single Sign-On 자습서](Foodee-tutorial.md)의 지침에 따라 SAML 기반 Single Sign-On을 Foodee에 사용하도록 설정할 수도 있습니다. Single Sign-On은 자동 사용자 프로비저닝과 별개로 구성할 수 있지만, 이러한 두 기능은 서로 보완적입니다.

다음을 수행하여 Azure AD에서 Foodee에 대한 자동 사용자 프로비저닝을 구성합니다.

1. [Azure Portal](https://portal.azure.com)에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.

    ![Enterprise 애플리케이션 창](common/enterprise-applications.png)

1. **애플리케이션** 목록에서 **Foodee** 를 선택합니다.

    ![애플리케이션 목록의 Foodee 링크](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

1. **프로비저닝 모드** 드롭다운 목록 **자동** 을 선택합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

1. **관리자 자격 증명** 아래에서 다음을 수행합니다.

   a. **테넌트 URL** 상자에서 이전에 검색한 **https:\//concierge.food.ee/scim/v2** 값을 입력합니다.

   b. **비밀 토큰** 상자에서 이전에 검색한 **API Token(API 토큰)** 값을 입력합니다.
   
   c. Azure AD에서 Foodee에 연결할 수 있는지 확인하려면 **연결 테스트** 를 선택합니다. 연결이 실패하면 Foodee 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![연결 테스트 링크](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력한 다음, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일 텍스트 상자](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 아래에서 **Azure Active Directory 사용자를 Foodee에 동기화** 를 선택합니다.

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="매핑 섹션의 스크린샷. 이름 아래에 Azure Active Directory 사용자를 Foodee에 동기화가 강조 표시되어 있습니다." border="false":::

1. **특성 매핑** 섹션 아래에서 Azure AD에서 Foodee로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Foodee의 *사용자 계정* 을 일치시키는 데 사용됩니다. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="특성 매핑 페이지의 스크린샷. 테이블에 Azure Active Directory 특성, Foodee 특성 및 일치하는 우선 순위가 나열되어 있습니다." border="false":::

1. 변경 내용을 적용하려면 **저장** 을 선택합니다.
1. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Foodee에 동기화** 를 선택합니다.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="매핑 섹션의 스크린샷. 이름 아래에 Azure Active Directory 그룹을 Foodee에 동기화가 강조 표시되어 있습니다." border="false":::

1. **특성 매핑** 섹션 아래에서 Azure AD에서 Foodee로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Foodee의 *그룹 계정* 을 일치시키는 데 사용됩니다.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="특성 매핑 페이지의 스크린샷. 테이블에 Azure Active Directory 특성, Foodee 특성 및 일치하는 우선 순위가 나열되어 있습니다." border="false":::

1. 변경 내용을 적용하려면 **저장** 을 선택합니다.
1. 범위 지정 필터를 구성합니다. 구성하는 방법을 알아보려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조하세요.

1. Azure AD 프로비저닝 서비스를 Foodee에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비저닝 상태 전환](common/provisioning-toggle-on.png)

1. **설정** 아래의 **범위** 드롭다운 목록에서 Foodee에 프로비저닝하려는 사용자 또는 그룹을 정의합니다.

    ![프로비저닝 범위 드롭다운 목록](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로비저닝 구성 저장 단추](common/provisioning-configuration-save.png)

이전 작업은 **범위** 드롭다운 목록에서 정의한 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화를 수행하는 경우 후속 동기화보다 더 오랜 시간이 걸립니다. 자세한 내용은 [사용자를 프로비저닝하는 데 걸리는 시간](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)을 참조하세요.

**현재 상태** 섹션을 사용하여 진행 상황을 모니터링하고, 프로비저닝 작업 보고서에 대한 링크를 따를 수 있습니다. 이 보고서는 Foodee의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명합니다. 자세한 내용은 [사용자 프로비저닝 상태 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조하세요. Azure AD 프로비저닝 로그를 읽으려면 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
