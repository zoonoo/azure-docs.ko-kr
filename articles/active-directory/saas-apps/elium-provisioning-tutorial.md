---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝을 위한 Elium 구성 | 마이크로 소프트 문서'
description: Azure Active Directory를 구성하여 사용자 계정을 Elium에 자동으로 프로비전 및 프로비저닝 해제하도록 구성하는 방법을 알아봅니다.
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058490"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Elium 구성

이 자습서에서는 Elium 및 Azure Active Directory(Azure AD)를 구성하여 사용자 또는 그룹을 Elium에 자동으로 프로비전하고 프로비저닝 해제하는 방법을 보여 주며, 이 자습서에서는 Elium에 대한 사용자 또는 그룹을 자동으로 프로비전해제합니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스의 작동 및 작동 방식에 대한 중요한 세부 정보 및 자주 묻는 질문은 [Azure Active Directory를 사용하여 SaaS 응용 프로그램에 대한 사용자 프로비전 및 프로비저닝 자동화를](../app-provisioning/user-provisioning.md)참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 미리 보기에서 Azure 기능에 대한 일반적인 사용 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 이미 다음과 같은 필수 구성 조건이 있다고 가정합니다.

* Azure AD 테넌트
* [엘리움 테넌트](https://www.elium.com/pricing/)
* 관리자 권한이 있는 Elium의 사용자 계정

## <a name="assigning-users-to-elium"></a>사용자를 Elium에 할당

Azure AD는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 및 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD에서 Elium에 액세스해야 하는 사용자 및 그룹을 결정합니다. 그런 다음 [엔터프라이즈 앱에 사용자 또는 그룹 할당의](../manage-apps/assign-user-or-group-access-portal.md)단계에 따라 해당 사용자 및 그룹을 Elium에 할당합니다.

## <a name="important-tips-for-assigning-users-to-elium"></a>사용자를 Elium에 할당하기 위한 중요한 팁 

자동 사용자 프로비저닝 구성을 테스트하려면 단일 Azure AD 사용자를 Elium에 할당하는 것이 좋습니다. 나중에 더 많은 사용자와 그룹을 할당할 수 있습니다.

사용자를 Elium에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-elium-for-provisioning"></a>프로비저닝을 위한 Elium 설정

Azure AD를 사용하여 자동 사용자 프로비전을 위해 Elium을 구성하기 전에 Elium에서 SCIM 간 도메인 ID 관리(SCIM) 프로비저닝을 위한 시스템을 사용하도록 설정해야 합니다. 다음 단계를 수행하세요.

1. Elium에 로그인하고 **내 프로필** > **설정으로**이동합니다.

    ![엘리움의 설정 메뉴 항목](media/Elium-provisioning-tutorial/setting.png)

1. 왼쪽 아래 모서리에서 **고급**에서 **보안**을 선택합니다.

    ![엘리움의 보안 링크](media/Elium-provisioning-tutorial/security.png)

1. **테넌트 URL** 및 **비밀 토큰** 값을 복사합니다. 나중에 Azure 포털에서 Elium 응용 프로그램의 **프로비저닝** 탭의 해당 필드에서 이러한 값을 사용합니다.

    ![Elium의 테넌트 URL 및 비밀 토큰 필드](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>갤러리에서 엘리움 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 위해 Elium을 구성하려면 Azure AD 응용 프로그램 갤러리의 Elium을 관리되는 서비스형 소프트웨어(SaaS) 응용 프로그램 목록에 추가해야 합니다. 다음 단계를 수행하세요.

1. Azure [포털에서](https://portal.azure.com)왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active 디렉터리 메뉴 항목](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

     ![Azure AD 엔터프라이즈 응용 프로그램 블레이드](common/enterprise-applications.png)

1. 새 응용 프로그램을 추가하려면 창 상단에서 **새 응용 프로그램을** 선택합니다.

    ![새 응용 프로그램 링크](common/add-new-app.png)

1. 검색 상자에서 **Elium을**입력하고 결과 목록에서 **Elium을** 선택한 다음 응용 프로그램을 추가하기 위해 **추가를** 선택합니다.

    ![갤러리 검색 상자](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Elium에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD의 사용자 및 그룹 할당을 기반으로 Elium에서 사용자 및 그룹을 생성, 업데이트 및 사용하지 않도록 설정하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> Elium [단일 사인온 자습서의](Elium-tutorial.md)지침에 따라 SAML(보안 어설션 태그 언어)을 기반으로 Elium에 대해 단일 사인온을 사용하도록 선택할 수도 있습니다. 두 기능이 서로 보완되지만 자동 사용자 프로비저닝과 는 별개로 단일 사인온을 구성할 수 있습니다.

Azure AD에서 Elium에 대한 자동 사용자 프로비저닝을 구성하려면 다음 단계를 따르십시오.

1. [Azure 포털에](https://portal.azure.com)로그인하고 **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![Azure AD 엔터프라이즈 응용 프로그램 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Elium**을 선택합니다.

    ![엔터프라이즈 응용 프로그램 블레이드의 응용 프로그램 목록](common/all-applications.png)

1. **프로비저닝** 탭을 선택합니다.

    ![엔터프라이즈 응용 프로그램 블레이드의 프로비전 탭](common/provisioning.png)

1. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 모드에 대한 자동 설정](common/provisioning-automatic.png)

1. 관리자 **자격 증명** 섹션에서 ** \<테넌트 URL 필드에 테넌트URL/scim/v2를\>** 입력합니다. **Tenant URL** **테넌트URL은** Elium 관리 콘솔에서 이전에 검색된 값입니다. 또한 **비밀** 토큰 필드에 Elium **비밀 토큰** 값을 입력합니다. 마지막으로 **테스트 연결을** 선택하여 Azure AD가 Elium에 연결할 수 있는지 확인합니다. 연결이 실패하면 Elium 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![관리자 자격 증명의 테넌트 URL 및 비밀 토큰 필드](common/provisioning-testconnection-tenanturltoken.png)

1. 알림 **전자 메일** 필드에 프로비저닝 오류 알림을 받게 될 개인 또는 그룹의 전자 메일 주소를 입력합니다. 그런 다음 **오류가 발생할 때 전자 메일 보내기 확인란을** 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **저장**을 클릭합니다.

1. **매핑** 섹션에서 **Azure Active Directory 사용자를 Elium으로 동기화합니다.**

    ![Azure AD 사용자를 Elium에 매핑하기 위한 링크 동기화](media/Elium-provisioning-tutorial/usermapping.png)

1. 속성 매핑 섹션에서 Azure AD에서 Elium으로 동기화된 사용자 **특성을 검토합니다.** **일치** 속성으로 선택된 특성은 업데이트 작업에 대한 Elium의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![Azure AD와 Elium 간의 특성 매핑](media/Elium-provisioning-tutorial/userattribute.png)

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)지침을 따릅니다.

1. Elium에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비저닝 상태가 켜짐으로 설정됨](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 드롭다운 목록 상자에서 원하는 값을 선택하여 Elium에 프로비전할 사용자 및 그룹을 정의합니다.

    ![프로비저닝 범위 목록 상자](common/provisioning-scope.png)

1. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![프로비저닝 구성을 위한 저장 버튼](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 및 그룹의 초기 동기화를 시작합니다. 이 초기 동기화 프로세스는 나중에 동기화하는 것보다 오래 걸립니다. 프로비저닝에 필요한 시간에 대한 자세한 내용은 [사용자를 프로비전하는 데 걸리는 시간을](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)참조하십시오.

현재 **상태** 섹션을 사용하여 진행 상황을 모니터링하고 프로비저닝 활동 보고서에 대한 링크를 따릅니다. 프로비저닝 활동 보고서는 Elium의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명합니다. 자세한 내용은 [사용자 프로비저닝 의 상태 확인을](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)참조하십시오. Azure AD 프로비저닝 로그를 읽으려면 [자동 사용자 계정 프로비저닝에 대한 보고를](../app-provisioning/check-status-user-account-provisioning.md)참조하십시오.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
