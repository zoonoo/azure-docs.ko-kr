---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝에 대 한 4me 구성 | 마이크로 소프트 문서'
description: 사용자 계정을 4me로 자동으로 프로비전 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법에 대해 알아봅니다.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 423ba8c7aea9659a4c91f68a01392954c2ba6db2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059169"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 4me 구성

이 자습서의 목적은 4me 및 Azure Active Directory(Azure AD)에서 수행할 단계를 보여 주어 사용자 및/또는 그룹을 4me로 자동으로 프로비전 및 프로비저닝 해제하도록 Azure AD를 구성하는 것입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [4me 테넌트](https://www.4me.com/trial/)
* 관리자 권한이 있는 4me의 사용자 계정입니다.

## <a name="add-4me-from-the-gallery"></a>갤러리에서 4me 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위해 4me를 구성하기 전에 Azure AD 응용 프로그램 갤러리에서 관리되는 SaaS 응용 프로그램 목록에 4me를 추가해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 4me를 추가하려면 다음 단계를 수행합니다.**

1. Azure **[포털에서](https://portal.azure.com)** 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가하려면 창 상단의 **새 응용 프로그램** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **4me를**입력하고 결과 패널에서 **4me를** 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 4me](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>사용자를 4me로 할당

Azure Active Directory는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 활성화하기 전에 Azure AD의 사용자 및/또는 그룹이 4me에 액세스해야 하는지 결정해야 합니다. 일단 결정, 당신은 여기에 지침에 따라 4me에 이러한 사용자 및 / 또는 그룹을 할당 할 수 있습니다 :

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>사용자를 4me로 할당하기 위한 중요한 팁

* 자동 사용자 프로비저닝 구성을 테스트하기 위해 단일 Azure AD 사용자에게 4me로 할당되는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 4me에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>자동 사용자 프로비전을 4me로 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당을 기반으로 4me에서 사용자 및/또는 그룹을 생성, 업데이트 및 비활성화하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> 또한 [4me 단일 사인온 자습서에서](4me-tutorial.md)제공된 지침에 따라 SAML 기반 단일 사인온을 4me에 사용하도록 선택할 수도 있습니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Azure AD에서 4me에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **4me**를 선택합니다.

    ![애플리케이션 목록의 4me 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 4me 계정의 **테넌트 URL** 및 **비밀 토큰을** 검색하려면 6단계에서 설명한 대로 연습에 따릅니다.

6. 4me 관리 콘솔에 로그인합니다. **설정으로**이동합니다.

    ![4me 설정](media/4me-provisioning-tutorial/4me01.png)

    검색 표시줄에 **앱을** 입력합니다.

    ![4me 앱](media/4me-provisioning-tutorial/4me02.png)

    **SCIM** 드롭다운을 열어 비밀 토큰과 SCIM 끝점을 검색합니다.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. 5단계에 표시된 필드를 채우면 **테스트 연결을** 클릭하여 Azure AD가 4me에 연결할 수 있도록 합니다. 연결이 실패하면 4me 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

8. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

9. **저장**을 클릭합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자 동기화를 4me로**선택합니다.

    ![4me 사용자 매핑](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. **속성 매핑** 섹션에서 Azure AD에서 4me로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업에 대해 4me의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![4me 사용자 매핑](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. 매핑 섹션에서 **Azure Active Directory 그룹 동기화를 4me로**선택합니다. **Mappings**

    ![4me 사용자 매핑](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. **속성 매핑** 섹션에서 Azure AD에서 4me로 동기화된 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업에 대해 4me의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![4me 그룹 매핑](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

15. Azure AD 프로비전 서비스를 4me로 활성화하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

16. **설정** 섹션의 **Scope에서** 원하는 값을 선택하여 4me로 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

17. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. 동기화 세부 **정보** 섹션을 사용하여 진행 상황을 모니터링하고 4me의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* 4me에는 테스트 및 프로덕션 환경에 대해 서로 다른 SCIM 끝점 URL이 있습니다. 전자는 **.qa로** 끝나고 후자는 **.com으로 끝납니다.**
* 4me 생성 된 비밀 토큰은 세대로부터 한 달의 만료 날짜가 있습니다.
* **4me는 DELETE** 작업을 지원하지 않습니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).