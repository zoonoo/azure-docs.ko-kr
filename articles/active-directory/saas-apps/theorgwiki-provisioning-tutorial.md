---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝에 대 한 TheOrgWiki 구성 | 마이크로 소프트 문서'
description: TheOrgWiki에 사용자 계정을 자동으로 프로비전 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063149"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>튜토리얼: 자동 사용자 프로비저닝을 위한 TheOrgWiki 구성

이 자습서의 목적은 TheOrgWiki 및 Azure Active Directory(Azure AD)에서 수행할 단계를 보여 주어 사용자 및/또는 그룹을 TheOrgWiki에 자동으로 프로비전 및 프로비저닝 해제하도록 Azure AD를 구성하는 것입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트.
* [조직위키 테넌트](https://www.theorgwiki.com/welcome/).
* 관리자 권한이 있는 TheOrgWiki의 사용자 계정입니다.

## <a name="assign-users-to-theorgwiki"></a>TheOrgWiki에 사용자 할당

Azure Active Directory는 할당이라는 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 활성화하기 전에 Azure AD의 사용자 및/또는 그룹이 TheOrgWiki에 액세스해야 하는지 결정해야 합니다. 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 TheOrgWiki에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>TheOrgWiki에 사용자를 할당하기 위한 중요한 팁

* 단일 Azure AD 사용자가 자동 사용자 프로비저닝 구성을 테스트하기 위해 TheOrgWiki에 할당되는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* TheOrgWiki에 사용자를 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-theorgwiki-for-provisioning"></a>프로비저닝을 위한 더오그위키 설정

Azure AD를 사용하여 자동 사용자 프로비전을 위해 TheOrgWiki를 구성하기 전에 TheOrgWiki에서 SCIM 프로비저닝을 사용하도록 설정해야 합니다.

1. [TheOrgWiki 관리 콘솔에](https://www.theorgwiki.com/login/)로그인합니다. 관리 **콘솔을 클릭합니다.**

    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. 관리 콘솔에서 **설정 탭을 클릭합니다.** 

    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. 서비스 **계정으로**이동합니다.

    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. **+서비스 계정을**클릭합니다. **서비스 계정 유형에서** **토큰 기반**을 선택합니다. **저장**을 클릭합니다.

    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  활성 **토큰을 복사합니다.** 이 값은 Azure 포털에서 TheOrgWiki 응용 프로그램의 프로비저닝 탭의 비밀 토큰 필드에 입력됩니다.
     
    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>갤러리에서 더위키 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 위해 TheOrgWiki를 구성하려면 Azure AD 응용 프로그램 갤러리에서 관리되는 SaaS 응용 프로그램 목록에 TheOrgWiki를 추가해야 합니다.

1. Azure **[포털에서](https://portal.azure.com)** 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가하려면 창 상단의 **새 응용 프로그램** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **TheOrgWiki를**입력하고 결과 패널에서 **TheOrgWiki를** 선택합니다. 

    ![결과 목록의 더오르그위키](common/search-new-app.png)

5. TheOrgWiki 로그인 페이지로 리디렉션되는 **TheOrgWiki** 에 대한 가입 버튼을 선택합니다. 

    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  오른쪽 상단 모서리에서 **로그인을**선택합니다.

    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. TheOrgWiki는 OpenIDConnect 앱이기 때문에 Microsoft 작업 계정을 사용하여 조직위키에 로그인하도록 선택합니다.

    ![TheOrgWiki 추가 SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. 인증에 성공하면 응용 프로그램이 테넌트에 자동으로 추가되고 TheOrgWiki 계정으로 리디렉션됩니다.

    ![조직위키, SCIM 추가](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>TheOrgWiki에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당을 기반으로 TheOrgWiki에서 사용자 및/또는 그룹을 생성, 업데이트 및 비활성화하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Azure AD에서 TheOrgWiki에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **TheOrgWiki를**선택합니다.

    ![응용 프로그램 목록의 조직위키 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 관리자 **자격 증명** 섹션에서 `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **테넌트 URL에**입력합니다. 

    예: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **하위 도메인 값은** TheOrgWiki의 초기 등록 프로세스 중에만 설정할 수 있습니다.
 
6. TheOrgWiki에서 이전에 검색한 **비밀 토큰** 필드에 토큰 값을 입력합니다. Azure AD가 TheOrgWiki에 연결할 수 있는지 확인하려면 **테스트 연결을** 클릭합니다. 연결이 실패하면 TheOrgWiki 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

7. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

8. **저장**을 클릭합니다.

9. **매핑** 섹션에서 **Azure Active Directory 사용자를 TheOrgWiki로 동기화합니다.**

    ![더오그위키 사용자 매핑](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. **속성 매핑** 섹션에서 Azure AD에서 TheOrgWiki로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 TheOrgWiki의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![TheOrgWiki 사용자 속성](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

12. TheOrgWiki에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

13. **설정** 섹션의 **범위에서** 원하는 값을 선택하여 OrgWiki에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

14. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 후속 동기화보다 수행하는 데 시간이 오래 걸립니다. 사용자 및/또는 그룹이 프로비전하는 데 걸리는 시간(자세한 [내용은) 을](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)참조하세요.

**현재 상태** 섹션을 사용하여 진행 상황을 모니터링하고 TheOrgWiki의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다. 자세한 내용은 [사용자 프로비저닝 의 상태 확인을](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)참조하십시오. Azure AD 프로비저닝 로그를 읽으려면 [자동 사용자 계정 프로비저닝에 대한 보고를](../app-provisioning/check-status-user-account-provisioning.md)참조하십시오.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [로그를 검토하고 프로비저닝 활동에 대한 보고서를 받는 방법에 대해 알아봅니다.](../app-provisioning/check-status-user-account-provisioning.md)