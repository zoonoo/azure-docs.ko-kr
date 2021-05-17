---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 BrowserStack Single Sign-on 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 BrowserStack Single Sign-On으로 자동 프로비저닝 및 프로비저닝 해제 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 39999abc-e4a2-4058-81e0-bf88182f8864
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2021
ms.author: Zhchia
ms.openlocfilehash: 200338ac50bceae6c3bf9730ee92a16c04a84b69
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108141454"
---
# <a name="tutorial-configure-browserstack-single-sign-on-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 BrowserStack Single Sign-On 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 BrowserStack Single Sign-On 및 Azure Active Directory(Azure AD)에서 수행해야 하는 단계를 설명합니다. 구성된 경우 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자를 [BrowserStack Single Sign-On](https://www.browserstack.com)에 자동으로 프로비저닝 및 프로비저닝 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * BrowserStack Single Sign-on에서 사용자 만들기
> * BrowserStack Single Sign-on에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 BrowserStack Single Sign-on 간 사용자 특성을 동기화된 상태로 유지
> * BrowserStack Single Sign-on에 대한 [Single sign-on](./browserstack-single-sign-on-tutorial.md)(맞춤)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* BrowserStack에 **Owner** 권한이 있는 사용자 계정
* BrowserStack을 사용하는 [엔터프라이즈 플랜](https://www.browserstack.com/pricing) 
* BrowserStack과 [Single sign-on](https://www.browserstack.com/docs/enterprise/single-sign-on/azure-ad) 통합(필수)

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 BrowserStack Single Sign-On 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-browserstack-single-sign-on-to-support-provisioning-with-azure-ad"></a>2단계. BrowserStack Single Sign-On를 구성하여 Azure AD로 프로비저닝 지원

1. **Owner** 권한이 있는 사용자로 [BrowserStack](https://www.browserstack.com/users/sign_in)에 로그인합니다.

2. **계정** -> **설정 및 권한** 으로 이동합니다. **보안** 탭을 선택합니다.

3. **자동 사용자 프로비저닝** 에서 **구성** 을 클릭합니다.

    ![설정](media/browserstack-single-sign-on-provisioning-tutorial/configure.png)

4. Azure AD를 통해 제어하려는 사용자 특성을 선택하고 **확인** 을 클릭합니다.

    ![사용자](media/browserstack-single-sign-on-provisioning-tutorial/attributes.png)

5. **테넌트 URL** 및 **비밀 토큰** 을 복사합니다. 이러한 값은 Azure Portal의 BrowserStack Single Sign-On 애플리케이션 프로비저닝 탭에서 테넌트 URL 및 비밀 토큰 필드에 입력됩니다. **Done** 을 클릭합니다.

    ![권한 부여](media/browserstack-single-sign-on-provisioning-tutorial/credential.png)

6. 프로비저닝 구성이 BrowserStack에 저장되었습니다. **Azure AD에서 프로비저닝 설치** 가 완료되면 BrowserStack에서 사용자 프로비저닝을 **사용하도록 설정** 하여 BrowserStack [계정](https://www.browserstack.com/accounts/manage-users)에서 새 사용자를 초대하지 못하도록 차단합니다. 

    ![계정](media/browserstack-single-sign-on-provisioning-tutorial/enable.png)
    
## <a name="step-3-add-browserstack-single-sign-on-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 BrowserStack Single Sign-On 추가

Azure AD 애플리케이션 갤러리에서 BrowserStack Single Sign-On을 추가하여 BrowserStack Single Sign-on에 대한 프로비저닝 관리를 시작합니다. 이전에 SSO에 대해 BrowserStack Single Sign-On을 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자를 할당할 수 있습니다. 사용자의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하는 경우 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자를 BrowserStack Single Sign-On에 할당하는 경우 **Default Access** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 집합으로 테스트합니다. 할당된 사용자로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자를 할당하여 범위를 제어할 수 있습니다. 모든 사용자로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-browserstack-single-sign-on"></a>5단계. BrowserStack Single Sign-On에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 할당에 따라 앱에서 사용자를 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-browserstack-single-sign-on-in-azure-ad"></a>Azure AD의 BrowserStack Single Sign-On에 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **BrowserStack Single Sign-On** 을 선택합니다.

    ![애플리케이션 목록의 BrowserStack Single Sign-On 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭 자동](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에 BrowserStack Single Sign-On 테넌트 URL 및 비밀 토큰을 입력합니다. **연결 테스트** 를 클릭하여 Azure AD가 BrowserStack Single Sign-On에 연결할 수 있는지 확인합니다. 연결이 실패하면 BrowserStack Single Sign-on 계정에 관리자 권한이 있는지 확인한 후 다시 시도합니다.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인의 이메일 주소를 입력하고, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 BrowserStack Single Sign-on에 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 BrowserStack Single Sign-on으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 BrowserStack Single Sign-On의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하는 경우 BrowserStack Single Sign-On API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|--|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_role|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_team|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_product|String|


10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. BrowserStack Single Sign-On에 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택하여 BrowserStack Single Sign-On에 프로비저닝하려는 사용자를 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **범위** 에 정의된 모든 사용자의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

- [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
- [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
- 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.  

## <a name="connector-limitations"></a>커넥터 제한 사항

* BrowserStack Single Sign-on은 그룹 프로비저닝을 지원하지 않습니다.
* BrowserStack Single Sign-on에는 **emails[type eq “work”].value** 와 **userName** 이 동일한 원본 값을 포함해야 합니다.

## <a name="troubleshooting-tips"></a>문제 해결 팁

* 문제 해결 팁은 [여기](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#troubleshooting)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [BrowserStack Single Sign-on에서 특성 매핑 구성](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad)
* [BrowserStack에서 자동 사용자 프로비저닝 설정 및 사용](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#setup-and-enable-auto-user-provisioning)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).