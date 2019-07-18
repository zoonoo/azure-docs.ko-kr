---
title: '자습서: Azure Active Directory로 자동 사용자 프로 비전에 대 한 Smartsheet 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 Smartsheet에 사용자 계정을 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670945"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>자습서: Smartsheet 자동 사용자 프로 비전 구성

이 자습서의 목적은 자동으로 프로 비전 하 고 사용자 및/또는 그룹 Smartsheet에 프로 비전 해제 하도록 Azure AD를 구성 하려면 Smartsheet 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 주기 위해입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 일반적인 Microsoft Azure 사용 약관 미리 보기 기능에 대 한 자세한 내용은 참조 하세요. [사용 특약 조건의 Microsoft Azure 미리 보기에 대 한](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다.

## <a name="prerequisites"></a>전제 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Smartsheet 테 넌 트](https://www.smartsheet.com/pricing)
* 시스템 관리자 권한이 있는 Smartsheet Enterprise 또는 Enterprise 프리미어 계획에서 사용자 계정입니다.

## <a name="assign-users-to-smartsheet"></a>Smartsheet에 사용자 할당

이라는 개념을 사용 하 여 azure Active Directory *할당* 어떤 사용자가 선택한 앱에 대 한 액세스를 받아야 하는지를 판단 합니다. 자동 사용자 프로 비전의 컨텍스트에서의 사용자 및/또는 Azure AD에서 응용 프로그램에 할당 된 그룹 동기화 됩니다.

를 구성 하 고 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 Smartsheet에 대 한 액세스를 해야 하는 사용자 및/또는 Azure AD의 그룹 결정 해야 합니다. 결정 했으면 할당할 수 있습니다 이러한 사용자 및/또는 그룹 Smartsheet에 여기의 지침에 따라.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Smartsheet에 사용자를 할당 하기 위한 주요 팁

* 것이 좋습니다 단일 Smartsheet에 자동 사용자 프로 비전 구성을 테스트 하려면 Azure AD 사용자를 할당 합니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* Smartsheet에 사용자를 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)를 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

* Smartsheet와 Azure AD 간에 사용자 역할 할당에는 패리티를 보장 하려면 전체 Smartsheet 사용자 목록에 채워진 같은 역할 할당을 활용 하는 것이 좋습니다. 이동이 사용자 목록 Smartsheet에서 검색할 **계정 관리자 > 사용자 관리 > 기타 작업 > 사용자 목록 다운로드 (csv)** 합니다.

* Smartsheet는 앱의 특정 기능에 액세스 하려면 사용자가 여러 역할에 필요 합니다. Smartsheet의 권한과 사용자 형식에 대 한 자세한 내용은로 이동 [사용자 형식 사용 권한과](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)합니다.

*  사용자가 여러 역할을 Smartsheet의 할당 된 경우 있습니다 **해야** 사용자 영구적으로 Smartsheet 개체에 액세스 하지 못했습니다 시나리오를 방지 하려면 Azure AD에서 이러한 역할 할당은 복제 되었는지 확인 합니다. Smartsheet에서 역할의 각 고유 **해야** Azure AD의 다른 그룹에 할당할 수 있습니다. 사용자 **해야** 각 원하는 역할에 해당 그룹에 추가 합니다. 

## <a name="set-up-smartsheet-for-provisioning"></a>Smartsheet 프로 비전을 위한 설정

Smartsheet 자동 사용자를 Azure AD를 사용 하 여 프로 비전을 구성 하기 전에 Smartsheet에서 SCIM 프로 비전을 사용 하도록 설정 해야 합니다.

1. 로 로그인을 **SysAdmin** 에 **[Smartsheet 포털](https://app.smartsheet.com/b/home)** 이동한 **계정 관리자**합니다.

    ![Smartsheet 계정 관리자](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 로 이동 **보안 제어 > 사용자 자동 프로 비전 > 편집**합니다.

    ![Smartsheet 보안 제어](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 추가 하 고 Smartsheet에 Azure AD에서 프로 비전 하려는 사용자의 전자 메일 도메인을 확인 합니다. 선택할 **해제** 프로 비전 하는 모든 작업만 Azure AD에서 온 것임을 확인 하는 데 또한 Smartsheet 사용자 목록은 Azure AD 할당을 사용 하 여 동기화를 확인 합니다.

    ![Smartsheet 사용자 프로 비전](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 유효성 검사 완료 되 면 도메인을 활성화 해야 합니다. 

    ![Smartsheet 도메인 활성화](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 생성 된 **비밀 토큰** 로 이동 하 여 Azure AD를 사용 하 여 자동 사용자 프로비저닝을 구성 하는 데 필요한 **앱 및 통합**합니다.

    ![Smartsheet 설치](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 선택할 **API 액세스**합니다. 클릭 **새 액세스 토큰 생성**합니다.

    ![Smartsheet 설치](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. API 액세스 토큰의 이름을 정의 합니다. **확인**을 클릭합니다.

    ![Smartsheet 설치](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. API 액세스 토큰을 복사 하 고 볼 수 있습니다만 게 사용 되므로 저장 합니다. 필요 합니다 **비밀 토큰** Azure AD의 필드입니다.

    ![Smartsheet 토큰](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>갤러리에서 Smartsheet를 추가 합니다.

Smartsheet 자동 사용자를 Azure AD를 사용 하 여 프로 비전을 구성 하려면 Azure AD 응용 프로그램 갤러리에서 Smartsheet를 관리 되는 SaaS 응용 프로그램의 목록에 추가 해야 합니다.

1. 에  **[Azure portal](https://portal.azure.com)** , 왼쪽된 탐색 패널에서 선택 **Azure Active Directory**합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 선택 합니다 **새 응용 프로그램** 창의 맨 위에 있는 단추입니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 입력 **Smartsheet**를 선택 **Smartsheet** 결과 패널에서 합니다. 

    ![결과 목록의 Smartsheet](common/search-new-app.png)

5. 선택 된 **Smartsheet에 대 한 등록** Smartsheet의 로그인 페이지로 리디렉션하는 단추입니다. 

    ![Smartsheet OIDC 추가](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet OpenIDConnect 앱 이므로 Microsoft 회사 계정을 사용 하 여 Smartsheet에 로그인을 선택 합니다.

    ![Smartsheet OIDC 로그인](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 인증에 성공 하면 동의 페이지에 대 한 동의 확인 프롬프트를 수락 합니다. 응용 프로그램이 자동으로 추가 됩니다 테 넌 트 및 Smartsheet 계정으로 이동 합니다.

    ![Smartsheet OIDc 동의](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Smartsheet에 자동 사용자 프로비저닝 구성 

이 섹션에서는 생성, 업데이트 및 사용자를 사용 하지 않도록 설정 하려면 Azure AD 프로 비전 서비스를 구성 하는 단계 및/또는 안내 그룹 Smartsheet의 Azure AD의 사용자 및/또는 그룹 할당에 기반 합니다.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Azure AD에서 Smartsheet에 대 한 자동 사용자 프로 비전을 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 선택 **엔터프라이즈 응용 프로그램**을 선택한 후 **모든 응용 프로그램**합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 선택 **Smartsheet**합니다.

    ![응용 프로그램 목록에서 Smartsheet 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

5. 아래는 **관리자 자격 증명** 섹션에서 입력 `https://scim.smartsheet.com/v2/` 에서 **테 넌 트 URL**합니다. 검색 및에서 Smartsheet에서 이전에 저장 하는 값 입력 **비밀 토큰**합니다. 클릭 **연결 테스트** Azure를 확인 하려면 AD Smartsheet에 연결할 수 있습니다. 연결에 실패 하면 Smartsheet 계정의 SysAdmin 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 전자 메일](common/provisioning-notification-email.png)

7. **Save**을 클릭합니다.

8. 아래는 **매핑을** 섹션에서 **Azure Active Directory 사용자를 동기화 Smartsheet**합니다.

    ![Smartsheet 사용자 매핑](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Smartsheet에서 Azure AD에서 동기화 된 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. 으로 선택한 특성 **일치** 속성 업데이트 작업을 위해 Smartsheet의 사용자 계정을 일치 시키는 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Smartsheet 사용자 특성](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Smartsheet에 대 한 서비스를 프로 비전 하는 Azure AD를 사용 하도록 설정 하려면 변경 합니다 **프로 비전 상태** 에 **에** 에 **설정** 섹션.

    ![프로 비전 상태에서 전환](common/provisioning-toggle-on.png)

12. 원하는 값을 선택 하 여 Smartsheet에 프로 비전 할 사용자 및/또는 하려는 그룹을 정의할 **범위** 에 **설정** 섹션입니다.

    ![프로 비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로 비전 구성을 저장 합니다.](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. 사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 링크를 프로 비전 활동 보고서를 프로 비전 서비스 Smartsheet에서 Azure AD에서 수행한 모든 작업을 설명 하는 섹션입니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Smartsheet는 일시 삭제를 지원 하지 않습니다. 사용자의 경우 **활성** 특성이 False로 설정 된, Smartsheet 사용자를 영구적으로 삭제 합니다.

## <a name="additional-resources"></a>추가 자료

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
