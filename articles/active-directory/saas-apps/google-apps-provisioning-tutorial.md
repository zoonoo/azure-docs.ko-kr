---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 G Suite 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 G Suite로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057722"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 G Suite 구성

이 자습서의 목적은 G Suite 및 Azure Active Directory(Azure AD)에서 수행할 단계를 보여 주어 사용자 및/또는 그룹을 G Suite에 자동으로 프로비전 및 프로비저닝 해제하도록 Azure AD를 구성하는 것입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

> [!NOTE]
> G Suite 커넥터는 2019년 10월에 최근에 업데이트되었습니다. G Suite 커넥터의 변경 사항은 다음과 같습니다.
> - 추가 G Suite 사용자 및 그룹 특성에 대한 지원이 추가되었습니다. 
> - [여기에](https://developers.google.com/admin-sdk/directory)정의된 내용과 일치하도록 G Suite 대상 특성 이름이 업데이트되었습니다.
> - 기본 특성 매핑을 업데이트했습니다.

## <a name="prerequisites"></a>사전 요구 사항

G Suite와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 테넌트
- [G 스위트 테넌트](https://gsuite.google.com/pricing.html)
- 관리자 권한이 있는 G Suite의 사용자 계정입니다.

## <a name="assign-users-to-g-suite"></a>G Suite에 사용자 할당

Azure Active Directory는 할당이라는 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 활성화하기 전에 Azure AD의 G Suite에 액세스해야 하는 사용자 및/또는 그룹을 결정해야 합니다. 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 G Suite에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>G Suite에 사용자를 할당하기 위한 중요한 팁

* 자동 사용자 프로비저닝 구성을 테스트하기 위해 단일 Azure AD 사용자가 G Suite에 할당되는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 G Suite에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="setup-g-suite-for-provisioning"></a>프로비저닝을 위한 설치 G 스위트

Azure AD를 사용하여 자동 사용자 프로비전을 위해 G Suite를 구성하기 전에 G Suite에서 SCIM 프로비전을 사용하도록 설정해야 합니다.

1. 관리자 계정으로 [G Suite 관리 콘솔에](https://admin.google.com/) 로그인한 다음 **보안을**선택합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

    ![[보안]을 선택합니다.][10]

1. **보안** 페이지에서 **API 참조**를 선택합니다.

    ![[API 참조]를 선택합니다.][15]

1. **API 액세스 사용**을 선택합니다.

    ![[API 참조]를 선택합니다.][16]

   > [!IMPORTANT]
   > G Suite에 프로비전하려는 모든 사용자의 경우 Azure AD의 사용자 이름은 사용자 지정 도메인에 연결되어야 **합니다.** 예를 들면 bob@contoso.onmicrosoft.com과 같은 사용자 이름은 G Suite에서 허용되지 않습니다. 반면에 bob@contoso.com은 허용됩니다. 여기에 있는 지침에 따라 기존 사용자의 도메인을 변경할 수 [있습니다.](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)

1. Azure AD를 통해 원하는 사용자 지정 도메인을 추가하고 확인한 후에는 G Suite를 통해 다시 확인해야 합니다. G Suite에서 도메인을 확인하려면 다음 단계를 참조하십시오.

    a. G [Suite 관리 콘솔에서](https://admin.google.com/) **도메인을 선택합니다.**

    ![도메인 선택][20]

    b. **Add a domain or a domain alias(도메인 또는 도메인 별칭 추가)** 를 선택합니다.

    ![새 도메인 추가][21]

    다. **다른 도메인 추가**를 선택하고 추가하려는 도메인 이름을 입력합니다.

    ![사용자의 도메인 이름을 입력합니다.][22]

    d. **Continue and verify domain ownership(계속해서 도메인 소유권 확인)** 을 선택합니다. 그런 다음 도메인 이름을 소유하고 있는지 확인하는 단계를 따릅니다. Google에서 도메인을 인증하는 방법에 대한 포괄적인 지침은 [사이트 소유권 확인을 참조하세요.](https://support.google.com/webmasters/answer/35179)

    e. G Suite에 추가하려는 추가 도메인에 대해 이전 단계를 반복합니다.

1. 그런 다음 G Suite에서 사용자 프로비저닝을 관리하는 데 사용할 관리자 계정을 결정합니다. 관리자 **역할로**이동합니다.

    ![Google Apps 선택][26]

1. 해당 계정의 **관리자 역할에** 대해 해당 역할에 대한 **권한을 편집합니다.** 이 계정을 프로비전에 사용할 수 있도록 모든 **관리자 API 권한**이 사용하도록 설정되어 있는지 확인합니다.

    ![Google Apps 선택][27]

## <a name="add-g-suite-from-the-gallery"></a>갤러리에서 G 스위트 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위해 G Suite를 구성하려면 Azure AD 응용 프로그램 갤러리에서 관리되는 SaaS 응용 프로그램 목록에 G Suite를 추가해야 합니다. 

1. Azure **[포털에서](https://portal.azure.com)** 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 새 응용 프로그램을 추가하려면 창 상단의 **새 응용 프로그램** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

1. 검색 상자에서 **G Suite를**입력하고 결과 패널에서 **G Suite를** 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 G Suite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>G Suite에 자동 사용자 프로비전 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당을 기반으로 G Suite에서 사용자 및/또는 그룹을 생성, 업데이트 및 비활성화하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> G Suite에 대해 SAML 기반 단일 사인온을 사용하도록 선택할 수도 [있습니다.](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

> [!NOTE]
> G Suite의 디렉터리 API 끝점에 대해 자세히 알아보려면 [디렉터리 API를](https://developers.google.com/admin-sdk/directory)참조하십시오.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD에서 G Suite에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **G Suite**를 선택합니다.

    ![애플리케이션 목록의 G Suite 링크](common/all-applications.png)

1. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

1. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에서 **권한 부여**를 선택합니다. 그러면 새 브라우저 창에서 Google 권한 부여 대화 상자가 열립니다.

    ![G 스위트 승인](media/google-apps-provisioning-tutorial/authorize.png)

1. G Suite 테넌트를 변경할 Azure AD 권한을 부여할지 확인합니다. **수락**을 선택합니다.

    ![사용 권한을 확인합니다.][28]

1. Azure Portal에서 **연결 테스트**를 선택하여 Azure AD가 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 G Suite 계정에 팀 관리자 권한이 있는지 확인합니다. 그런 다음 **권한 부여** 단계를 다시 시도합니다.

1. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **저장**을 클릭합니다.

1. **매핑** 섹션에서 **Azure Active Directory 사용자를 G Suite로 동기화합니다.**

    ![G 스위트 사용자 매핑](media/google-apps-provisioning-tutorial/usermappings.png)

1. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택된 특성은 업데이트 작업에 대한 G Suite의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![G 스위트 사용자 속성](media/google-apps-provisioning-tutorial/userattributes.png)

1. **매핑** 섹션에서 **Azure Active Directory 그룹 동기화를 G Suite로**선택합니다.

    ![G 스위트 그룹 매핑](media/google-apps-provisioning-tutorial/groupmappings.png)

1. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화된 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업에 대한 G Suite의 그룹과 일치하는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다. UI에는 Azure AD와 G Suite 간의 기본 특성 매핑 집합이 표시됩니다. 새 매핑 추가를 클릭하여 조직 단위와 같은 추가 속성을 추가하도록 선택할 수 있습니다.

    ![G 스위트 그룹 속성](media/google-apps-provisioning-tutorial/groupattributes.png)

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

1. G Suite에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위에서** 원하는 값을 선택하여 G Suite에 프로비전할 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. 동기화 세부 **정보** 섹션을 사용하여 진행 상황을 모니터링하고 G Suite에서 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다.

> [!NOTE]
> 사용자가 Azure AD 사용자의 전자 메일 주소를 사용하여 기존 개인/소비자 계정을 이미 가지고 있는 경우 디렉터리 동기화를 수행하기 전에 Google 이전 도구를 사용하여 해결할 수 있는 몇 가지 문제가 발생할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>일반적인 문제
* 연결을 설정하는 데 사용된 계정이 GSuite의 관리자를 위한 계정이 아닌 경우 권한 부여 오류가 발생할 수 있습니다. 액세스 권한을 부여하는 데 사용되는 계정에 사용자가 프로비전해야 하는 **모든 도메인에** 대한 관리자 권한이 있는지 확인합니다. 
* Azure AD는 응용 프로그램에 액세스할 수 없도록 GSuite의 사용자 비활성화를 지원하지만 GSuite의 사용자는 삭제하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
