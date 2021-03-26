---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Olfeo SAAS 구성 Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Olfeo SAAS로 자동으로 프로 비전 및 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549323"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Olfeo SAAS 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 Olfeo SAAS 및 Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [Olfeo SAAS](https://www.olfeo.com) 에 자동으로 프로 비전 하 고 프로 비전 해제 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Olfeo SAAS에서 사용자 만들기
> * 더 이상 액세스할 필요가 없는 경우 Olfeo SAAS의 사용자를 제거 합니다.
> * Azure AD와 Olfeo SAAS 간에 사용자 특성을 동기화 상태로 유지
> * Olfeo SAAS에서 그룹 및 그룹 멤버 자격 프로 비전
> * Olfeo SAAS에 대 [한 Single sign-on](olfeo-saas-tutorial.md) (권장)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* [Olfeo SAAS 테 넌 트](https://www.olfeo.com/).
* 관리자 권한이 있는 Olfeo SAAS의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
1. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
1. [AZURE AD와 Olfeo SAAS 간에 매핑할](../app-provisioning/customize-application-attributes.md)데이터를 결정 합니다.

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD로 프로 비전을 지원 하도록 Olfeo SAAS 구성

1. Olfeo SAAS 관리 콘솔에 로그인 합니다. 
1. **구성 > Annuaires** 로 이동 합니다.
1. 새 디렉터리를 만들고 이름을로 지정한 다음
1. **Azure** 공급자를 선택한 다음 **Cr er** 를 클릭 하 여 새 디렉터리를 저장 합니다. 
1. **동기화** 탭으로 이동 하 여 **테 넌 트 URL** 및 **Jeton 암호** 를 확인 합니다. 이러한 값은 Azure Portal에서 Olfeo SAAS 응용 프로그램의 프로 비전 탭에 있는 **테 넌 트 URL** 및 **비밀 토큰** 필드에 복사 하 여 붙여 넣습니다.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 Olfeo SAAS 추가

Azure AD 응용 프로그램 갤러리에서 Olfeo SAAS를 추가 하 여 Olfeo SAAS에 대 한 프로 비전 관리를 시작 합니다. 이전에 SSO 용 Olfeo SAAS를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-gallery-app.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* Olfeo SAAS에 사용자 및 그룹을 할당할 때 **기본 액세스** 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 더 많은 역할을 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>5단계. Olfeo SAAS에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및 그룹 할당을 기반으로 Olfeo SAAS 앱에서 사용자 및 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Azure AD에서 Olfeo SAAS에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Olfeo SAAS** 를 선택합니다.

    ![응용 프로그램 목록의 Olfeo SAAS 링크](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

1.  **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭 자동](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에서 Olfeo SAAS **테 넌 트 URL** 및 **비밀 토큰** 정보를 입력 합니다. **연결 테스트** 를 선택 하 여 Azure AD가 Olfeo SAAS에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Olfeo SAAS 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 전자 메일** 필드에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 섹션에서 **사용자 Azure Active Directory OLFEO SAAS와 동기화를** 선택 합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Olfeo SAAS로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Olfeo SAAS의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](../app-provisioning/customize-application-attributes.md)을 변경 하는 경우 Olfeo SAAS API가 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |displayName|String|
   |활성|부울|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|  

1. **매핑** 섹션에서 **OLFEO SAAS에 Azure Active Directory 그룹 동기화를** 선택 합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Olfeo SAAS로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Olfeo SAAS의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|필터링에 지원됨|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |members|참조|

1. 범위 지정 필터를 구성 하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공 하는 지침을 참조 하세요.

1. Olfeo SAAS에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 olfeo SAAS에 프로 비전 할 사용자 또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로 비전 서비스가 실행 되는 동안 40 분 마다 발생 하는 다음 주기 보다 시간이 더 오래 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링

프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

* [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md) 를 사용 하 여 성공적으로 프로 비전 된 사용자 또는 실패 한 사용자를 확인 합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) 에서 프로 비전 주기의 상태와 완료에 대 한 종료 시간을 확인 합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대해 자세히 알아보려면 [격리의 응용 프로그램 프로 비전 상태](../app-provisioning/application-provisioning-quarantine-status.md)를 참조 하세요.

## <a name="more-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).