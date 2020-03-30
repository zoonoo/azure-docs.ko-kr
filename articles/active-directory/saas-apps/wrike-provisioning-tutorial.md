---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝에 대 한 Wrike 구성 | 마이크로 소프트 문서'
description: Azure Active Directory를 구성하여 사용자 계정을 Wrike에 자동으로 프로비전 및 프로비저닝 해제하도록 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064193"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위해 Wrike 구성

이 자습서의 목적은 Wrike 및 Azure Active Directory(Azure AD)에서 수행하는 단계를 보여 주어 사용자 또는 그룹을 Wrike에 자동으로 프로비전 및 프로비저닝 해제하도록 Azure AD를 구성하는 것입니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스의 작동 방식, 작동 방식 및 자주 묻는 질문에 대한 중요한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS(서비스형 소프트웨어) 응용 프로그램에 대한 사용자 프로비저닝 및 프로비저닝 을 자동화합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능에 대한 일반적인 Microsoft Azure 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [리크 테넌트](https://www.wrike.com/price/)
* 관리자 권한이 있는 Wrike의 사용자 계정

## <a name="assign-users-to-wrike"></a>사용자를 Wrike에 할당합니다.
Azure Active Directory는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD에서 Wrike에 액세스해야 하는 사용자 또는 그룹을 결정합니다. 그런 다음 다음 지침에 따라 이러한 사용자 또는 그룹을 Wrike에 할당합니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>사용자를 Wrike에 할당하기 위한 중요한 팁

* 자동 사용자 프로비저닝 구성을 테스트하려면 단일 Azure AD 사용자를 Wrike에 할당하는 것이 좋습니다. 나중에 추가 사용자 또는 그룹을 할당할 수 있습니다.

* 사용자를 Wrike에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-wrike-for-provisioning"></a>프로비저닝을 위한 Wrike 설정

Azure AD를 사용하여 자동 사용자 프로비전을 위해 Wrike를 구성하기 전에 Wrike에서 SCIM(도메인 간 ID 관리) 프로비전을 위한 시스템을 사용하도록 설정해야 합니다.

1. [Wrike 관리 콘솔에](https://www.Wrike.com/login/)로그인합니다. 테넌트 ID로 이동합니다. **통합에 & 앱을 선택합니다.**

    ![앱 & 통합](media/Wrike-provisioning-tutorial/admin.png)

2.  Azure **AD로** 이동하여 선택합니다.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  SCIM을 선택합니다. 기본 **URL을**복사합니다.

    ![기준 URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. **API** > **Azure SCIM을 선택합니다.**

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  팝업이 열립니다. 이전에 만든 것과 동일한 암호를 입력하여 계정을 만듭니다.

    ![리크 토큰 만들기](media/Wrike-provisioning-tutorial/password.png)

6.  비밀 **토큰을**복사하여 Azure AD에 붙여넣습니다. Wrike에서 프로비저닝 설정을 완료하려면 **저장을** 선택합니다.

    ![영구 액세스 토큰](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>갤러리에서 리크 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위해 Wrike를 구성하기 전에 Azure AD 응용 프로그램 갤러리에서 Wrike를 관리되는 SaaS 응용 프로그램 목록에 추가합니다.

Azure AD 응용 프로그램 갤러리에서 Wrike를 추가하려면 다음 단계를 따르십시오.

1. Azure [포털에서](https://portal.azure.com)왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가하려면 창 상단의 **새 응용 프로그램** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Wrike를**입력하고 결과 패널에서 **Wrike를** 선택한 다음 응용 프로그램을 추가하기 위해 **추가를** 선택합니다.

    ![결과 목록의 Wrike](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Wrike에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당을 기반으로 Wrike에서 사용자 또는 그룹을 생성, 업데이트 및 사용하지 않도록 설정하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> Wrike에 대한 SAML 기반 단일 사인온을 사용하려면 [Wrike 단일 사인온 자습서의](wrike-tutorial.md)지침을 따르십시오. 단일 사인온은 자동 사용자 프로비저닝과 독립적으로 구성할 수 있지만 이러한 두 기능은 서로 보완됩니다.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Azure AD에서 Wrike에 대한 자동 사용자 프로비저닝 구성

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램** > 선택**모든 응용 프로그램**.

    ![모든 애플리케이션](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Wrike**를 선택합니다.

    ![애플리케이션 목록의 Wrike 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비전 모드가 자동으로 설정되었습니다.](common/provisioning-automatic.png)

5. 관리자 자격 증명 섹션에서 **기본 URL** 및 영구 액세스 토큰 값을 **테넌트 URL** 및 비밀 **토큰에서** 각각 입력합니다. **Secret Token** Azure AD가 Wrike에 연결할 수 있는지 확인하려면 **테스트 연결을** 선택합니다. 연결이 실패하면 Wrike 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

7. 알림 **전자 메일** 상자에 프로비저닝 오류 알림을 받아야 하는 개인 또는 그룹의 전자 메일 주소를 입력합니다. 오류가 **발생할 때 전자 메일 보내기 확인란을** 선택합니다.

    ![전자 메일 알림](common/provisioning-notification-email.png)

8. **저장**을 선택합니다.

9. **매핑** 섹션에서 **Azure Active Directory 사용자를 Wrike로 동기화합니다.**

    ![사용자 매핑](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. 속성 매핑 섹션에서 Azure AD에서 Wrike로 동기화된 사용자 **특성을 검토합니다.** **일치** 속성으로 선택된 특성은 업데이트 작업에 대한 Wrike의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![사용자 속성 을 리크](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)지침을 따릅니다.

12. Wrike에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비저닝 상태 전환 켜기](common/provisioning-toggle-on.png)

13. **설정** 섹션의 **Scope에서** 원하는 값을 선택하여 Wrike에 프로비전할 사용자 또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

14. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![프로비저닝 구성 절약](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 후속 동기화보다 수행하는 데 시간이 오래 걸립니다. 사용자 또는 그룹이 프로비전하는 데 걸리는 시간(약)은 [사용자를 프로비전하는 데 걸리는 시간을 참조하세요.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

**현재 상태** 섹션을 사용하여 진행 상황을 모니터링하고 Wrike의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다. 자세한 내용은 [사용자 프로비저닝 의 상태 확인을](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)참조하십시오. Azure AD 프로비저닝 로그를 읽으려면 [자동 사용자 계정 프로비저닝에 대한 보고를](../app-provisioning/check-status-user-account-provisioning.md)참조하십시오.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
