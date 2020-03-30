---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝을 위한 저장소 게이트 구성 | 마이크로 소프트 문서'
description: Storegate에 사용자 계정을 자동으로 프로비전 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064260"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 스토어게이트 구성

이 자습서의 목적은 Storegate 및 Azure Active Directory(Azure AD)에서 수행할 단계를 보여 주어 사용자 및/또는 그룹을 Storegate에 자동으로 프로비전 및 프로비전 해제하도록 Azure AD를 구성합니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [스토어게이트 테넌트](https://www.storegate.com)
* 관리자 권한이 있는 스토어게이트의 사용자 계정입니다.

## <a name="assign-users-to-storegate"></a>스토어게이트에 사용자 할당

Azure Active Directory는 할당이라는 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 활성화하기 전에 Azure AD의 스토어게이트에 액세스해야 하는 사용자 및/또는 그룹을 결정해야 합니다. 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Storegate에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>사용자를 Storegate에 할당하기 위한 중요한 팁

* 단일 Azure AD 사용자가 Storegate에 할당되어 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* Storegate에 사용자를 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-storegate-for-provisioning"></a>프로비저닝을 위한 스토어게이트 설정

Azure AD를 사용하여 자동 사용자 프로비전을 위해 Storegate를 구성하기 전에 Storegate에서 일부 프로비저닝 정보를 검색해야 합니다.

1. [Storegate 관리 콘솔에](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) 로그인하고 오른쪽 상단 모서리에 있는 사용자 아이콘을 클릭하여 설정으로 이동하여 **계정 설정을**선택합니다.

    ![스토어게이트, SCIM 추가](media/storegate-provisioning-tutorial/admin.png)

2. 설정 내에서 **팀 > 설정으로** 이동하여 **단일 사인온** 섹션에서 토글 스위치가 켜져 있는지 확인합니다.

    ![스토어게이트 설정](media/storegate-provisioning-tutorial/team.png)

    ![스토어게이트 토글 버튼](media/storegate-provisioning-tutorial/sso.png)

3. **테넌트 URL** 및 **토큰**을 복사합니다. 이러한 값은 Azure Portal에서 Storegate 응용 프로그램의 프로비저닝 탭에서 **테넌트 URL** 및 **비밀 토큰** 필드에 각각 입력됩니다. 

    ![스토어게이트 토큰 만들기](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>갤러리에서 스토어게이트 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위해 Storegate를 구성하려면 Azure AD 응용 프로그램 갤러리에서 관리되는 SaaS 응용 프로그램 목록에 Storegate를 추가해야 합니다.

1. Azure **[포털에서](https://portal.azure.com)** 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가하려면 창 상단의 **새 응용 프로그램** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Storegate를**입력하고 결과 패널에서 **Storegate를** 선택합니다. 

    ![결과 목록에 있는 스토어게이트](common/search-new-app.png)

5. 스토어게이트 로그인 페이지로 리디렉션되는 **스토어게이트** 등록 버튼을 선택합니다. 

    ![스토어게이트 OIDC 추가](media/storegate-provisioning-tutorial/signup.png)

6.  [Storegate 관리 콘솔에](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) 로그인하고 오른쪽 상단 모서리에 있는 사용자 아이콘을 클릭하여 설정으로 이동하여 **계정 설정을**선택합니다.

    ![스토어게이트 로그인](media/storegate-provisioning-tutorial/admin.png)

7. 설정 에서 **팀 > 설정으로** 이동 하 고 단일 사인온 섹션에서 전환 스위치를 클릭, 이 동의 흐름을 시작 합니다. **활성화를 클릭합니다.**

    ![스토어게이트 팀](media/storegate-provisioning-tutorial/team.png)

    ![스토어게이트 스소](media/storegate-provisioning-tutorial/sso.png)

    ![스토어게이트 활성화](media/storegate-provisioning-tutorial/activate.png)

8. Storegate는 OpenIDConnect 앱이기 때문에 Microsoft 사용자 계정을 사용하여 스토어게이트에 로그인하도록 선택합니다.

    ![스토어게이트 OIDC 로그인](media/storegate-provisioning-tutorial/login.png)

9. 인증에 성공하면 동의 페이지에 대한 동의 프롬프트에 동의합니다. 그러면 응용 프로그램이 테넌트에 자동으로 추가되고 Storegate 계정으로 리디렉션됩니다.

    ![스토어게이트 OIDc 동의](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Storegate에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당을 기반으로 Storegate에서 사용자 및/또는 그룹을 생성, 업데이트 및 비활성화하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!NOTE]
> Storegate의 SCIM 끝점에 대한 자세한 내용은 [이](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)을 참조하십시오.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Azure AD에서 스토어게이트에 대한 자동 사용자 프로비저닝을 구성하려면

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **스토어게이트**를 선택합니다.

    ![응용 프로그램 목록의 스토어게이트 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 관리자 **자격 증명** 섹션에서 `https://dialpad.com/scim` **테넌트 URL에**입력합니다. **비밀 토큰의**Storegate에서 이전에 검색하고 저장한 값을 입력합니다. Azure AD가 스토어게이트에 연결할 수 있는지 확인하려면 **테스트 연결을** 클릭합니다. 연결이 실패하면 Storegate 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 클릭합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자 동기화를 선택하여 스토어게이트.**

    ![스토어게이트 사용자 매핑](media/storegate-provisioning-tutorial/usermappings.png)

9. **속성 매핑** 섹션에서 Azure AD에서 Storegate로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업에 대한 Storegate의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![스토어게이트 사용자 속성](media/storegate-provisioning-tutorial/userattributes.png)

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Storegate에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **Scope에서** 원하는 값을 선택하여 Storegate에 프로비전할 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. 동기화 세부 **정보** 섹션을 사용하여 진행 상황을 모니터링하고 Storegate에서 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
