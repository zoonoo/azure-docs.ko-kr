---
title: '자습서: ThousandEyes에 대한 사용자 프로비저닝 - Azure AD'
description: 사용자 계정을 ThousandEyes로 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 885ee993748a0a571f396cc0dc28f2c0c1a4a0c3
ms.sourcegitcommit: 00aa5afaa9fac91f1059cfed3d8dbc954caaabe2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2020
ms.locfileid: "97792520"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 ThousandEyes 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 ThousandEyes로 자동으로 프로비전 및 프로비전 해제하도록 ThousandEyes 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure Active Directory 테넌트
* [표준 계획](https://www.thousandeyes.com/pricing) 이상을 사용하도록 설정한 ThousandEyes 테넌트 
* 관리자 권한이 있는 ThousandEyes의 사용자 계정 

> [!NOTE]
> Azure AD 프로비전 통합에서는 [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)를 사용하며, 이 API는 ThousandEyes 팀이 표준 계획 이상에서 사용할 수 있습니다.

## <a name="assigning-users-to-thousandeyes"></a>ThousandEyes에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다. 

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 ThousandEyes 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지침에 따라 이러한 사용자를 ThousandEyes 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>ThousandEyes에 사용자를 할당하기 위한 주요 팁

* 단일 Azure AD 사용자를 ThousandEyes에 할당하여 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* ThousandEyes에 사용자를 할당할 때 [할당] 대화 상자에서 **사용자** 역할이나 다른 유효한 애플리케이션 관련 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할은 프로비전에 적합하지 않으므로 이러한 사용자는 건너뜁니다.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>ThousandEyes에 사용자 프로비전 구성 

이 섹션에서는 사용자의 Azure AD를 ThousandEyes의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 ThousandEyes에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!TIP]
> [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 ThousandEyes에 대해 SAML 기반 Single Sign-On을 사용하도록 설정할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Azure AD에서 ThousandEyes에 자동 사용자 계정 프로비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 이미 ThousandEyes에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 ThousandEyes의 인스턴스를 검색합니다. 그러지 않은 경우 **추가** 를 선택하고 애플리케이션 갤러리에서 **ThousandEyes** 를 검색합니다. 검색 결과에서 ThousandEyes를 선택하고 애플리케이션 목록에 추가합니다.

    ![애플리케이션 목록의 ThousandEyes 링크](common/all-applications.png)
    
3. ThousandEyes의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

![스크린샷은 프로비저닝 모드로 자동 선택된 ThousandEyes의 프로비저닝 탭을 보여줍니다.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. **관리자 자격 증명** 섹션 아래에 ThousandEyes 계정에서 생성한 **OAuth 전달자 토큰** 을 입력합니다(이 토큰은 ThousandEyes 계정의 **Profile**(프로필) 섹션 아래에서 찾거나 생성할 수 있음).

    ![스크린샷은 현재 계정 그룹에 대한 계정 설정 링크를 찾을 수 있는 위치를 보여줍니다.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Azure Portal에서 **연결 테스트** 를 클릭하여 Azure AD가 ThousandEyes 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 ThousandEyes 계정에 관리자 권한이 있는지 확인하고 5단계를 다시 시도합니다.

7. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

8. **저장** 을 클릭합니다.

9. [매핑] 섹션에서 **Synchronize Azure Active Directory Users to ThousandEyes**(Azure Active Directory 사용자를 ThousandEyes에 동기화)를 선택합니다.

10. **특성 매핑** 섹션의 Azure AD에서 ThousandEyes로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Parsable의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경하는 경우 Parsable API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

     |attribute|Type|필터링에 지원됨|
     |---|---|---|
     |externalId|String|&check;|
     |userName|String|&check;|
     |활성|부울|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |name.formatted|String|


11. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

12. ThousandEyes에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

13. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 ThousandEyes에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

14. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)를 참조하세요.  

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
