---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 Snowflake 구성 | Microsoft Docs'
description: 사용자 계정을 눈송이에 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539538"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Snowflake 구성

이 자습서에서는 사용자 및 그룹을 [눈송이](https://www.Snowflake.com/pricing/)로 자동으로 프로 비전 및 프로 비전 해제 하도록 azure ad를 구성 하기 위해 눈송이 및 Azure Active Directory (azure ad)에서 수행 하는 단계를 보여 줍니다. 이 서비스에서 수행 하는 작업, 작동 방법 및 질문과 대답에 대 한 중요 한 정보는 [AZURE AD에서 자동화 된 SaaS 앱 사용자 프로 비전 이란?](../app-provisioning/user-provisioning.md)을 참조 하세요. 

> [!NOTE]
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 사용 약관에 대 한 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Snowflake에서 사용자 만들기
> * 더 이상 액세스 하지 않아도 되는 경우 눈송이에서 사용자 제거
> * Azure AD와 Snowflake 간에 사용자 특성을 동기화된 상태로 유지
> * Snowflake에서 그룹 및 그룹 멤버 프로비저닝
> * 눈송이 [Single Sign-On](./snowflake-tutorial.md) 허용 (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md)
* 프로 비전을 구성할 수 있는 [권한이](../roles/permissions-reference.md) 있는 Azure AD의 사용자 계정 (응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자)
* [눈송이 테 넌 트](https://www.Snowflake.com/pricing/)
* 관리 권한이 있는 눈송이의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1 단계: 프로 비전 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 Snowflake 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>2 단계: Azure AD로 프로 비전을 지원 하도록 눈송이 구성

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 눈송이를 구성 하기 전에 눈송이에서 SCIM (도메인 간 Id 관리) 프로 비전을 위해 시스템을 사용 하도록 설정 해야 합니다.

1. 눈송이 관리 콘솔에 로그인 합니다. 강조 표시 된 워크시트에 다음 쿼리를 입력 하 고 **실행** 을 선택 합니다.

    ![쿼리 및 실행 단추가 포함 된 눈송이 관리 콘솔의 스크린샷](media/Snowflake-provisioning-tutorial/image00.png)

2.  사용자의 눈송이 테 넌 트에 대해 SCIM 액세스 토큰이 생성 됩니다. 이를 검색 하려면 다음 스크린샷에 강조 표시 된 링크를 선택 합니다.

    ![S C I M 액세스 토큰이 호출 된 눈송이 U I의 워크시트 스크린샷](media/Snowflake-provisioning-tutorial/image01.png)

3. 생성 된 토큰 값을 복사 하 고 **완료** 를 선택 합니다. 이 값은 Azure Portal의 눈송이 응용 프로그램의 **프로 비전** 탭에 있는 **비밀 토큰** 상자에 입력 됩니다.

    ![텍스트 필드에 복사 된 토큰과 Done 옵션을 모두 표시 하는 세부 정보 섹션의 스크린샷](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>3 단계: Azure AD 응용 프로그램 갤러리에서 눈송이 추가

Azure AD 애플리케이션 갤러리에서 Snowflake를 추가하여 Snowflake에 대한 프로비저닝 관리를 시작합니다. 이전에 Single Sign-On (SSO)에 대 한 눈송이를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때는 별도의 앱을 만드는 것이 좋습니다. [갤러리에서 응용 프로그램을 추가 하는 방법에 대해 자세히 알아보세요](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4 단계: 프로 비전 범위에 있는 사용자 정의 

Azure AD 프로 비전 서비스를 사용 하 여 응용 프로그램에 대 한 할당 또는 사용자 또는 그룹의 특성을 기반으로 프로 비전 되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로 비전 되는 사용자의 범위를 선택 하는 경우 [응용 프로그램에 사용자 및 그룹을 할당 하는 단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로 비전 할 사용자의 범위를 선택 하는 경우 범위 지정 [필터를 사용할](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)수 있습니다. 

다음 팁을 염두에 두어야 합니다.

* 눈송이에 사용자 및 그룹을 할당 하는 경우 기본 액세스 이외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [응용 프로그램 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md) 하 여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정 된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>5 단계: 눈송이에 자동 사용자 프로 비전 구성 

이 섹션에서는 Azure AD 프로 비전 서비스를 구성 하 여 눈송이에서 사용자 및 그룹을 만들고, 업데이트 하 고, 사용 하지 않도록 설정 하는 단계를 안내 합니다. Azure AD에서 사용자 및 그룹 할당에 대 한 구성을 기반으로 할 수 있습니다.

Azure AD에서 Snowflake에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 창을 보여 주는 스크린샷](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **눈송이** 를 선택 합니다.

    ![응용 프로그램 목록을 보여 주는 스크린샷](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 out 인 프로 비전 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 이전에 **테 넌 트 Url** 및 **암호 토큰** 상자에서 검색 한 scim 2.0 기준 url 및 인증 토큰을 입력 합니다. 

   **연결 테스트** 를 선택 하 여 Azure AD가 눈송이에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 눈송이 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 합니다.

    ![연결 테스트 단추와 함께 테 넌 트 U R L 및 비밀 토큰에 대 한 상자를 보여 주는 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력합니다. 그런 다음 **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

    ![알림 전자 메일에 대 한 상자를 표시 하는 스크린샷](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 눈송이와 동기화를** 선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Snowflake로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Snowflake의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |활성|부울|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

10. **매핑** 섹션에서 **Azure Active Directory 그룹을 눈송이에 동기화를** 선택 합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 Snowflake로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Snowflake의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    |attribute|Type|
    |---|---|
    |displayName|String|
    |members|참조|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조하세요.

13. 눈송이에 대해 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

     ![프로 비전 상태를 전환 하는 것을 보여 주는 스크린샷](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 눈송이에 프로 비전 할 사용자 및 그룹을 정의 합니다. 

    이 옵션을 사용할 수 없는 경우 **관리자 자격 증명** 에서 필수 필드를 구성 하 고, **저장** 을 선택 하 고, 페이지를 새로 고칩니다. 

     ![프로 비전 범위에 대 한 선택 항목을 보여 주는 스크린샷](common/provisioning-scope.png)

15. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

     ![프로 비전 구성을 저장 하는 단추의 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **범위** 에 정의된 모든 사용자 및 그룹의 첫 번째 동기화를 시작합니다. 초기 동기화를 수행하는 경우 후속 동기화보다 더 오랜 시간이 걸립니다. Azure AD 프로 비전 서비스가 실행 되는 동안에는 40 분 마다 이후 동기화가 발생 합니다.

## <a name="step-6-monitor-your-deployment"></a>6 단계: 배포 모니터링
프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

- [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
- [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
- 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. [격리 상태에 대해 자세히 알아보세요](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>커넥터 제한 사항

눈송이 생성 SCIM 토큰은 6 개월 후에 만료 됩니다. 프로 비전 동기화가 계속 작동할 수 있도록 이러한 토큰은 만료 되기 전에 새로 고쳐야 합니다. 

## <a name="troubleshooting-tips"></a>문제 해결 팁

Azure AD 프로 비전 서비스는 현재 특정 [IP 범위](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)에서 작동 합니다. 필요한 경우 다른 IP 범위를 제한 하 고 이러한 특정 IP 범위를 응용 프로그램의 허용 목록에 추가할 수 있습니다. 이러한 기술은 Azure AD 프로 비전 서비스에서 응용 프로그램으로의 트래픽 흐름을 허용 합니다.

## <a name="change-log"></a>로그 변경

* 07/21/2020: 활성 특성을 통해 모든 사용자에 대해 일시 삭제를 사용 하도록 설정 했습니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
