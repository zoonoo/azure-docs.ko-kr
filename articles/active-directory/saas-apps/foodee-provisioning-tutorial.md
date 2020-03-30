---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 위한 Foodee 구성 | 마이크로 소프트 문서'
description: Foodee에 사용자 계정을 자동으로 프로비전 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057828"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Foodee 구성

이 문서에서는 Foodee 및 Azure AD에서 사용자 또는 그룹을 Foodee에 자동으로 프로비전하거나 프로비저닝해제하도록 Azure Active Directory(Azure AD)를 구성하는 방법을 보여 주며, 이 문서에서는

> [!NOTE]
> 이 문서에서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스가 수행하는 작업과 작동 방식을 알아보고 자주 묻는 질문에 대한 답변을 얻으려면 [Azure Active Directory를 사용하여 SaaS 응용 프로그램에 대한 사용자 프로비전 및 프로비저닝 자동화를](../app-provisioning/user-provisioning.md)참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 미리 보기 기능에 대한 Azure 사용 약관 기능에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관으로 이동하십시오.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 다음과 같은 필수 구성 조건을 충족했다고 가정합니다.

* Azure AD 테넌트
* [푸드 테넌트](https://www.food.ee/about/)
* 관리자 권한이 있는 Foodee의 사용자 계정

## <a name="assign-users-to-foodee"></a>Foodee에 사용자 할당 

Azure AD는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스를 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD에서 Foodee에 액세스할 필요가 있는 사용자 또는 그룹을 결정해야 합니다. 이 결정을 내린 후 [엔터프라이즈 앱에 사용자 또는 그룹 할당의](../manage-apps/assign-user-or-group-access-portal.md)지침에 따라 이러한 사용자 또는 그룹을 Foodee에 할당할 수 있습니다.

## <a name="important-tips-for-assigning-users-to-foodee"></a>사용자를 Foodee에 할당하기 위한 중요한 팁 

사용자를 할당할 때 다음 팁을 염두에 두십시오.

* 자동 사용자 프로비저닝의 구성을 테스트하기 위해 Foodee에 단일 Azure AD 사용자만 할당하는 것이 좋습니다. 나중에 추가 사용자 또는 그룹을 할당할 수 있습니다.

* Foodee에 사용자를 할당할 때 **할당** 창에서 유효한 응용 프로그램 별 역할을 선택합니다. *기본 액세스* 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-foodee-for-provisioning"></a>프로비저닝을 위한 Foodee 설정

Azure AD를 사용하여 자동 사용자 프로비저닝을 위해 Foodee를 구성하기 전에 Foodee에서 SCIM(도메인 간 ID 관리) 프로비저닝을 위한 시스템을 사용하도록 설정해야 합니다.

1. [Foodee에](https://www.food.ee/login/)로그인한 다음 테넌트 ID를 선택합니다.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. **엔터프라이즈 포털에서** **단일 사인온을**선택합니다.

    ![Foodee 엔터프라이즈 포털 왼쪽 창 메뉴](media/Foodee-provisioning-tutorial/scim.png)

1. 나중에 사용할 수 위해 **API 토큰** 상자에서 값을 복사합니다. Azure 포털에서 Foodee 응용 프로그램의 **프로비저닝** 탭에 **있는 비밀 토큰** 상자에 입력합니다.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>갤러리에서 음식추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 위한 Foodee를 구성하려면 Azure AD 응용 프로그램 갤러리에서 Foodee를 관리되는 SaaS 응용 프로그램 목록에 추가해야 합니다.

Azure AD 응용 프로그램 갤러리에서 Foodee를 추가하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure 활성 디렉터리 명령](common/select-azuread.png)

1. **엔터프라이즈 응용 프로그램** > 선택**모든 응용 프로그램**.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 새 응용 프로그램을 추가하려면 창 상단에서 **새 응용 프로그램을** 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

1. 검색 상자에서 **Foodee를**입력하고 결과 창에서 **Foodee를** 선택한 다음 응용 프로그램을 추가하기 위해 **추가를** 선택합니다.

    ![결과 목록에서 미식가](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Foodee에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당을 기반으로 Foodee에서 사용자 또는 그룹을 생성, 업데이트 및 사용하지 않도록 설정하도록 Azure AD 프로비저닝 서비스를 구성합니다.

> [!TIP]
> 또한 [Foodee 단일 사인온 자습서의](Foodee-tutorial.md)지침에 따라 Foodee에 대한 SAML 기반 단일 사인온을 활성화할 수도 있습니다. 자동 사용자 프로비저닝과 는 별개로 단일 사인온을 구성할 수 있지만 이 두 기능은 서로 보완됩니다.

다음을 수행하여 Azure AD에서 Foodee에 대한 자동 사용자 프로비저닝을 구성합니다.

1. Azure [포털에서](https://portal.azure.com) **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램을 선택합니다.**

    ![Enterprise 애플리케이션 창](common/enterprise-applications.png)

1. 응용 프로그램 목록에서 **Foode를** **선택합니다.**

    ![응용 프로그램 목록의 Foodee 링크](common/all-applications.png)

1. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

1. **프로비저닝 모드** 드롭다운 목록에서 **자동**을 선택합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

1. 관리자 자격 증명에서 다음을 **수행합니다.**

   a. **테넌트 URL** 상자에 이전에 검색한 **\/https: /concierge.food.ee/scim/v2** 값을 입력합니다.

   b. 비밀 **토큰** 상자에 이전에 검색한 **API 토큰** 값을 입력합니다.
   
   다. Azure AD가 Foodee에 연결할 수 있도록 하려면 **테스트 연결을**선택합니다. 연결이 실패하면 Foodee 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![테스트 연결 링크](common/provisioning-testconnection-tenanturltoken.png)

1. 알림 **전자 메일** 상자에 프로비저닝 오류 알림을 받아야 하는 개인 또는 그룹의 전자 메일 주소를 입력한 다음 오류가 발생할 때 전자 메일 알림 보내기 확인란을 선택합니다. **Send an email notification when a failure occurs**

    ![알림 이메일 텍스트 상자](common/provisioning-notification-email.png)

1. **저장**을 선택합니다.

1. **매핑에서**Azure **Active Directory 사용자를 Foodee로 동기화합니다.**

    ![푸드 사용자 매핑](media/Foodee-provisioning-tutorial/usermapping.png)

1. **특성 매핑에서**Azure AD에서 Foodee로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택된 특성은 업데이트 작업에 대 한 Foodee의 *사용자 계정을* 일치 하는 데 사용 됩니다. 

    ![푸드 사용자 매핑](media/Foodee-provisioning-tutorial/userattribute.png)

1. 변경 내용을 커밋하려면 **저장을**선택합니다.
1. **매핑에서**Azure **Active Directory 그룹 동기화를 Foodee로**선택합니다.

    ![푸드 사용자 매핑](media/Foodee-provisioning-tutorial/groupmapping.png)

1. **특성 매핑에서**Azure AD에서 Foodee로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택된 특성은 업데이트 작업에 대해 Foodee의 *그룹 계정과* 일치하는 데 사용됩니다.

    ![푸드 사용자 매핑](media/Foodee-provisioning-tutorial/groupattribute.png)

1. 변경 내용을 커밋하려면 **저장을**선택합니다.
1. 범위 지정 필터를 구성합니다. 방법을 알아보려면 [범위 지정 필터 자습서의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)지침을 참조하십시오.

1. Foodee에 대한 Azure AD 프로비저닝 서비스를 활성화하려면 **설정** 섹션에서 **프로비저닝 상태를** **을 로**변경합니다.

    ![프로비저닝 상태 스위치](common/provisioning-toggle-on.png)

1. **설정에서** **범위** 드롭다운 목록에서 Foodee에 프로비전할 사용자 또는 그룹을 정의합니다.

    ![프로비저닝 범위 드롭다운 목록](common/provisioning-scope.png)

1. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![프로비저닝 구성 저장 버튼](common/provisioning-configuration-save.png)

이전 작업은 **범위** 드롭다운 목록에서 정의한 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 후속 동기화보다 수행하는 데 시간이 오래 걸립니다. 자세한 내용은 [사용자를 프로비전하는 데 얼마나 걸하면 얼마나 걸릴지](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)확인하십시오.

**현재 상태** 섹션을 사용하여 진행 상황을 모니터링하고 프로비저닝 활동 보고서에 대한 링크를 팔로우할 수 있습니다. 이 보고서는 Foodee의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업에 대해 설명합니다. 자세한 내용은 [사용자 프로비저닝 의 상태 확인을](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)참조하십시오. Azure AD 프로비저닝 로그를 읽으려면 [자동 사용자 계정 프로비저닝에 대한 보고를](../app-provisioning/check-status-user-account-provisioning.md)참조하십시오.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
