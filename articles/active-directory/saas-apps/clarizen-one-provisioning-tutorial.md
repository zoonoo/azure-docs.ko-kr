---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Clarizen One 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Clarizen One으로 자동으로 프로비저닝 및 프로비저닝 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 9335869797509171c71caffb0062aeccca207803
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358918"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 Clarizen One 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Clarizen One 및 Azure AD(Azure Active Directory) 모두에서 수행해야 하는 단계에 대해 설명합니다. 구성되면 Azure AD에서 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [Clarizen One](https://www.clarizen.com/)으로 자동으로 프로비저닝 및 프로비저닝 해제합니다. 이 서비스의 기능과 작동 방식 및 질문과 대답에 대한 내용은 [Azure AD를 사용하여 SaaS(Software as a Service) 애플리케이션으로 사용자 프로비저닝/프로비저닝 해제 자동화](../manage-apps/user-provisioning.md)를 참조하세요.

## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
> * Clarizen One에서 사용자 만들기
> * Clarizen One에서 더 이상 액세스할 필요가 없는 사용자 제거
> * 사용자 특성을 Azure AD와 Clarizen One 간에 동기화된 상태로 유지
> * Clarizen One에서 그룹 및 그룹 멤버 자격 프로비저닝
> * Clarizen One에 [SSO(Single Sign-On)](https://docs.microsoft.com/azure/active-directory/saas-apps/clarizen-tutorial) 사용(추천)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* 프로비저닝을 구성할 수 있는 [권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)이 있는 Azure AD의 사용자 계정. 예를 들어 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자가 있습니다.
* **통합 사용자** 및 **라이트 관리자** [권한](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)이 있는 Clarizen One의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
1. [프로비저닝 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정합니다.
1. [Azure AD와 Clarizen One 간에 매핑](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)할 데이터를 결정합니다.

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용하여 프로비저닝을 지원하도록 Clarizen One 구성

1. Clarizen One 환경 및 데이터 센터에 따라 다음 4개의 테넌트 URL 중 하나를 선택합니다.
      * 미국 프로덕션 데이터 센터: https://servicesapp2.clarizen.com/scim/v2
      * EU 프로덕션 데이터 센터: https://serviceseu1.clarizen.com/scim/v2
      * US 샌드박스 데이터 센터: https://servicesapp.clarizentb.com/scim/v2
      * EU 샌드박스 데이터 센터: https://serviceseu.clarizentb.com/scim/v2

1. [API 키](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)를 생성합니다. 이 값은 Azure Portal에 있는 Clarizen One 애플리케이션에 대한 **프로비저닝** 탭의 **비밀 토큰** 필드에 입력됩니다.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 Clarizen One 추가

Azure AD 애플리케이션 갤러리에서 Clarizen One을 추가하여 Clarizen One으로 프로비저닝 관리를 시작합니다. 이전에 SSO를 Clarizen One에 사용하도록 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 통합을 처음 테스트하는 경우 별도의 앱을 만듭니다. 갤러리에서 애플리케이션을 추가하는 방법에 대한 자세한 내용은 [Azure AD 테넌트에 애플리케이션 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)를 참조하세요.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기준으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당에 따라 앱에 프로비저닝할 사용자를 범위를 지정하도록 선택하는 경우 [Azure Active Directory에서 앱에 대한 사용자 할당 관리](../manage-apps/assign-user-or-group-access-portal.md)의 단계에 따라 사용자 및 그룹을 애플리케이션에 할당합니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝하는 사용자의 범위를 지정하도록 선택하는 경우 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비저닝](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에서 설명하는 대로 범위 지정 필터를 사용합니다.

* 사용자 및 그룹을 Clarizen One에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고, 프로비저닝 로그에서 실질적으로 권한이 없는 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)하여 더 많은 역할을 추가할 수 있습니다.
* 소규모로 시작합니다. 모든 사용자에게 롤아웃하려면 먼저 소수의 사용자 및 그룹 세트를 사용하여 테스트합니다. 프로비저닝 범위가 할당된 사용자 및 그룹으로 설정되면 앱에 하나 또는 둘의 사용자 또는 그룹을 할당하여 제어를 유지할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정되면 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다.

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>5단계. Clarizen One에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당에 따라 TestApp에서 사용자 또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Azure AD에서 Clarizen One에 대한 자동 사용자 프로비저닝 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 창을 보여 주는 스크린샷](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Clarizen One** 을 선택합니다.

    ![애플리케이션 목록에서 Clarizen One 링크를 보여 주는 스크린샷](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭을 보여 주는 스크린샷](common/provisioning.png)

1. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭의 자동 옵션을 보여 주는 스크린샷](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션 아래에서 Clarizen One **테넌트 URL** 및 **비밀 토큰** 을 입력합니다. **연결 테스트** 를 선택하여 Azure AD에서 Clarizen One에 연결할 수 있는지 확인합니다. 연결이 실패하면 Clarizen One 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![비밀 토큰 상자를 보여 주는 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일 상자를 보여 주는 스크린샷](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Clarizen One에 동기화** 를 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Clarizen One으로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Clarizen One의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경하는 경우 Clarizen One API에서 해당 특성에 따라 사용자를 필터링하도록 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |활성|부울|
   |title|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |name.honorificPrefix|String|
   |name.honorificSuffix|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].streetAddress|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |externalId|String|
   |nickName|String|
   |locale|String|
   |roles[primary eq"True".type]|String|
   |roles[primary eq"True".value]|String|
   |timezone|String|
   |userType|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|참조|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|

1. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Clarizen One에 동기화** 를 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Clarizen One으로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Clarizen One의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|참조|

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조합니다.

1. Azure AD 프로비저닝 서비스를 Clarizen One에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![켜기로 전환된 프로비저닝 상태를 보여 주는 스크린샷](common/provisioning-toggle-on.png)

1. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Clarizen One에 프로비저닝하려는 사용자 또는 그룹을 정의합니다.

    ![프로비저닝 범위를 보여 주는 스크린샷](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로비저닝 구성 저장을 보여 주는 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링

프로비저닝이 구성되면 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
1. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
1. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [격리 상태의 애플리케이션 프로비저닝](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)을 참조하세요.

## <a name="troubleshooting-tips"></a>문제 해결 팁

사용자를 Clarizen One 갤러리 앱에 할당하는 경우 **사용자** 역할만 선택합니다. 잘못된 역할은 다음과 같습니다.

* 관리자(admin)
* 이메일 보고 사용자
* 외부 사용자
* 금융 사용자
* 소셜 사용자
* 슈퍼 사용자
* 시간 및 비용 사용자

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
