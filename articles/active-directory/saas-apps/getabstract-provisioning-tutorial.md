---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 getAbstract 구성 Microsoft Docs'
description: Azure Active Directory에서 getAbstract로 사용자 계정을 자동으로 프로 비전 하 고 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616131"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 getAbstract 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 getAbstract 및 Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure ad는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 자동으로 프로 비전 하 고 [Getabstract](https://www.getabstract.com) 에 프로 비전 합니다. 이 서비스에서 수행 하는 작업, 작동 방법 및 질문과 대답에 대 한 자세한 내용은 [AZURE AD를 사용 하 여 SaaS (software as a service) 응용 프로그램에 사용자 프로비저닝 자동화 및 프로 비전](../app-provisioning/user-provisioning.md)해제를 참조 하세요.

## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
> * GetAbstract에서 사용자를 만듭니다.
> * 더 이상 액세스 하지 않아도 되는 getAbstract의 사용자를 제거 합니다.
> * Azure AD와 getAbstract 간에 사용자 특성을 동기화 된 상태로 유지 합니다.
> * GetAbstract에서 그룹 및 그룹 멤버 자격을 프로 비전 합니다.
> * GetAbstract에 [Single Sign-On (SSO)](getabstract-tutorial.md) 를 사용 하도록 설정 합니다 (권장).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md)
* 프로비저닝을 구성할 수 있는 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정. 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자의 예가 있습니다.
* GetAbstract 테 넌 트 (getAbstract 회사 라이선스).
* Azure AD 테 넌 트 및 getAbstract 테 넌 트에서 SSO를 사용 하도록 설정 했습니다.
* GetAbstract를 사용 하도록 설정 하는 SCIM (도메인 간 Id 관리)에 대 한 승인 및 시스템입니다. 로 전자 메일을 보냅니다 b2b.itsupport@getabstract.com .

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
1. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
1. [AZURE AD와 getAbstract 간에 매핑할](../app-provisioning/customize-application-attributes.md)데이터를 결정 합니다.

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용 하 여 프로 비전을 지원 하도록 getAbstract 구성

1. GetAbstract에 로그인 합니다.
1. 오른쪽 위 모퉁이에 있는 설정 아이콘을 선택 하 고 **내 중앙 관리자** 옵션을 선택 합니다.

    ![GetAbstract 내 중앙 관리를 보여 주는 스크린샷](media/getabstract-provisioning-tutorial/my-account.png)

1. **Scim 관리자** 옵션을 찾아 선택 합니다.

    ![GetAbstract SCIM 관리자를 보여 주는 스크린샷](media/getabstract-provisioning-tutorial/scim-admin.png)

1. **이동** 을 선택합니다.

    ![GetAbstract SCIM 클라이언트 Id를 보여 주는 스크린샷](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. **Generate new token**(새 토큰 생성)을 탭합니다.

    ![GetAbstract SCIM 토큰 1을 보여 주는 스크린샷](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. 잘 모르겠으면 **새 토큰 생성** 을 선택 합니다. 그렇지 않으면 **취소** 를 선택합니다.

    ![GetAbstract SCIM 토큰 2를 보여 주는 스크린샷](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. 클립보드에 복사 아이콘을 선택 하거나 전체 토큰을 선택 하 고 복사 합니다. 또한 테 넌 트/기본 URL은입니다 `https://www.getabstract.com/api/scim/v2` . 이러한 값은 Azure Portal에서 getAbstract 응용 프로그램의 **프로 비전** 탭에 있는 **비밀 토큰** 및 **테 넌 트 URL** 상자에 입력 됩니다.

    ![GetAbstract SCIM 토큰 3을 보여 주는 스크린샷](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 응용 프로그램 갤러리에서 getAbstract 추가

GetAbstract로 프로 비전 관리를 시작 하려면 Azure AD 응용 프로그램 갤러리에서 getAbstract를 추가 합니다. 이전에 SSO에 대 한 getAbstract를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 처음에 통합을 테스트 하는 경우 별도의 앱을 만드는 것이 좋습니다. 갤러리에서 응용 프로그램을 추가 하는 방법에 대해 자세히 알아보려면 [이 빠른](../manage-apps/add-application-portal.md)시작을 참조 하세요.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의

Azure AD 프로 비전 서비스를 사용 하 여 응용 프로그램에 대 한 할당 또는 사용자 또는 그룹의 특성을 기반으로 프로 비전 할 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로 비전 할 사용자의 범위를 선택 하는 경우 범위 지정 필터를 [사용 하 여 앱 프로 비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 설명 된 대로 범위 지정 필터를 사용할 수 있습니다.

* GetAbstract에 사용자 및 그룹을 할당 하는 경우 **기본 액세스** 이외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고, 프로비저닝 로그에서 실질적으로 권한이 없는 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 더 많은 역할을 추가할 수 있습니다.

* 소규모로 시작합니다. 모든 사용자에게 롤아웃하려면 먼저 소수의 사용자 및 그룹 세트를 사용하여 테스트합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여이 옵션을 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정되면 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다.

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>5단계. GetAbstract에 자동 사용자 프로 비전 구성

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당에 따라 TestApp에서 사용자 또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Azure AD에서 getAbstract에 대 한 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 창을 보여 주는 스크린샷](common/enterprise-applications.png)

1. 응용 프로그램 목록에서 **Getabstract** 를 선택 합니다.

    ![응용 프로그램 목록의 getAbstract 링크를 보여 주는 스크린샷](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭을 보여 주는 스크린샷](common/provisioning.png)

1. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![[프로비저닝 모드]가 [자동]으로 설정된 것을 보여주는 스크린샷](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에서 Getabstract **테 넌 트 URL** 및 **암호 토큰** 정보를 입력 합니다. **연결 테스트** 를 선택 하 여 Azure AD가 getabstract에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 getAbstract 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 합니다.

    ![테 넌 트 URL 및 암호 토큰 상자를 보여 주는 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일 상자를 보여 주는 스크린샷](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 섹션에서 **사용자 Azure Active Directory Getabstract에 동기화를** 선택 합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 getabstract로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 getabstract의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](../app-provisioning/customize-application-attributes.md)을 변경 하는 경우 GETABSTRACT API가 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |활성|부울|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |preferredLanguage|String|

1. **매핑** 섹션에서 **Azure Active Directory 그룹을 Getabstract에 동기화를** 선택 합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 getabstract로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 getabstract의 그룹을 일치 시키는 데 사용 됩니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

    |attribute|Type|필터링에 지원됨|
    |---|---|---|
    |displayName|String|&check;|
    |externalId|String|
    |members|참조|

1. 범위 지정 필터를 구성 하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공 하는 지침을 참조 하세요.

1. GetAbstract에 대해 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![켜기로 전환된 프로비저닝 상태를 보여 주는 스크린샷](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 getabstract에 프로 비전 하려는 사용자 또는 그룹을 정의 합니다.

    ![프로 비전 범위를 보여 주는 스크린샷](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![저장 단추를 표시 하는 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. Azure AD 프로 비전 서비스가 실행 되는 동안에는 40 분 마다 발생 하는 이후 주기 보다 수행 하는 데 더 많은 시간이 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링

프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

* [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md) 를 사용 하 여 성공적으로 프로 비전 된 사용자 또는 실패 한 사용자를 확인 합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대해 자세히 알아보려면 [격리의 응용 프로그램 프로 비전 상태](../app-provisioning/application-provisioning-quarantine-status.md)를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
