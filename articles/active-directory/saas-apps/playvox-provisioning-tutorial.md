---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Playvox 구성 Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Playvox로 자동으로 프로 비전 및 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862482"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Playvox 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 Playvox 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 설명 합니다. 구성 된 경우 azure ad는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 또는 그룹을 [Playvox](https://www.playvox.com) 에 자동으로 프로 비전 하 고 프로 비전 해제 합니다. 이 서비스의 기능 및 작동 방식에 대 한 중요 한 정보 및 질문과 대답은 [Azure Active Directory를 사용 하 여 SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전](../app-provisioning/user-provisioning.md)해제를 참조 하세요.

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Playvox에서 사용자를 만듭니다.
> * 더 이상 액세스할 필요가 없는 경우 Playvox에서 사용자를 제거 합니다.
> * Azure AD와 Playvox 간에 사용자 특성을 동기화 된 상태로 유지 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 시나리오에서는 다음과 같은 필수 구성 요소가 이미 있다고 가정 합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md)
* 프로비저닝을 구성할 수 있는 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정. 예를 들어, 계정에는 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자 역할이 있을 수 있습니다.
* 슈퍼 관리자 권한이 있는 [Playvox](https://www.playvox.com) 의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1 단계: 프로 비전 배포 계획

1. [프로 비전 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.

2. [프로 비전 범위에](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)있는 사용자를 결정 합니다.

3. [AZURE AD와 Playvox 간에 매핑할](../app-provisioning/customize-application-attributes.md)데이터를 결정 합니다.

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>2 단계: Azure AD를 사용 하 여 프로 비전을 지원 하도록 Playvox 구성

1. Playvox 관리 콘솔에 로그인 하 고 **설정 > API 키** 로 이동 합니다.

2. **API 키 만들기** 를 선택 합니다.

    ![Playvox 사용자 인터페이스에서 API 키 만들기 단추의 위치를 보여 주는 부분 스크린샷](media/playvox-provisioning-tutorial/create.png)

3. API 키에 대 한 의미 있는 이름을 입력 하 고 **저장** 을 선택 합니다. API 키가 생성 된 후 **닫기** 를 선택 합니다.

4. 만든 API 키에서 **세부 정보** 아이콘을 선택 합니다.

    ![Playvox 사용자 인터페이스에서 돋보기 인 세부 정보 아이콘의 위치를 보여 주는 부분 스크린샷.](media/playvox-provisioning-tutorial/api.png)

5. **BASE64 키** 값을 복사 하 여 저장 합니다. 나중에 Azure Portal Playvox 응용 프로그램의 **프로 비전** 탭에서 **비밀 토큰** 텍스트 상자에이 값을 입력 합니다.

    ![BASE64 키 값이 강조 표시 된 세부 정보 API 키 메시지 상자의 스크린샷](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>3 단계: Azure AD 응용 프로그램 갤러리에서 Playvox 추가

Playvox에 대 한 프로 비전 관리를 시작 하려면 응용 프로그램 갤러리에서 Azure AD 테 넌 트에 Playvox를 추가 합니다. 자세히 알아보려면 [빠른 시작: Azure Active Directory (AZURE AD) 테 넌 트에 응용 프로그램 추가](../manage-apps/add-application-portal.md)를 참조 하세요.

이전에 SSO (Playvox for Single Sign-On)를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때 별도의 앱을 만드는 것이 좋습니다.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4 단계: 프로 비전 범위에 있는 사용자 정의

Azure AD 프로 비전 서비스를 사용 하 여 응용 프로그램에 대 한 할당 또는 사용자 또는 그룹의 특성을 기반으로 프로 비전 되는 사용자의 범위를 지정 합니다. 할당에 따라 앱에 프로 비전 되는 사용자의 범위를 지정 하려면 응용 프로그램에 사용자 또는 그룹을 할당 하는 방법을 알아보려면 [Azure Active Directory에서 앱에 대 한 사용자 할당 관리](../manage-apps/assign-user-or-group-access-portal.md) 를 참조 하세요. 사용자 또는 그룹의 특성에 따라서만 프로 비전 되는 범위를 지정 하려면 범위 지정 [필터를 사용한 특성 기반 응용 프로그램 프로 비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 설명 된 대로 범위 지정 필터를 사용 합니다.

다음 사항에 주의 하세요.

* Playvox에 사용자를 할당할 때 기본 액세스 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 기본 액세스가 응용 프로그램에서 사용할 수 있는 유일한 역할인 경우에는 [응용 프로그램 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md) 하 여 다른 역할을 추가할 수 있습니다.

* 소규모로 시작합니다. 모든 사용자에 게 롤아웃 전에 작은 사용자 또는 그룹 집합으로 테스트 합니다. 프로 비전 범위가 할당 된 사용자 또는 그룹을 기반으로 하는 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여 집합의 크기를 제어할 수 있습니다. 프로 비전 범위에 모든 사용자 및 그룹이 포함 된 경우에는 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 를 지정 하 여 테스트 집합의 크기를 제한할 수 있습니다.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>5 단계: Playvox에 대 한 자동 사용자 프로 비전 구성

이 섹션에서는 azure ad의 사용자 또는 그룹 할당을 기반으로 사용자 또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

Azure AD에서 Playvox에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 응용 프로그램 및 모든 응용 프로그램 항목이 강조 표시 된 Azure Portal의 부분 스크린샷](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **Playvox** 를 검색 하 고 선택 합니다.

    ![응용 프로그램 검색 상자가 강조 표시 된 응용 프로그램 목록의 부분 스크린샷](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로 비전 메뉴 항목을 보여 주는 부분 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로 비전 모드 드롭다운 목록 상자에서 선택한 자동 옵션을 보여 주는 프로 비전 탭의 부분 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 Playvox **테 넌 트 URL** 을 다음과 같이 입력 합니다.

    `https://{tenant}.playvox.com/scim/v1`

    2 단계에서 이전에 복사한 **비밀 토큰** 을 입력 합니다. 그런 다음 **연결 테스트** 를 선택 하 여 Azure AD가 Playvox에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Playvox 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 합니다.

    ![테 넌 트 URL 및 암호 토큰 텍스트 상자를 포함 하 고 연결 테스트 링크가 강조 표시 된 관리 자격 증명 섹션을 보여 주는 부분 스크린샷.](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 전자 메일** 텍스트 상자에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 합니다. 그런 다음, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 전자 메일 텍스트 상자와 전자 메일 알림 확인란을 보여 주는 부분 스크린샷](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Playvox에 동기화를** 선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Playvox로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Playvox의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](../app-provisioning/customize-application-attributes.md)을 변경 하도록 선택 하는 경우 Playvox API에서 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |활성|부울|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조하세요.

11. Playvox에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로 비전 상태를 On으로 설정 하 여 표시 하는 설정 섹션의 부분 스크린샷](common/provisioning-toggle-on.png)

12. **설정** 에서 **범위** 에서 원하는 값을 선택 하 여 Playvox에 프로 비전 할 사용자 또는 그룹을 정의 합니다.

    ![범위 드롭다운 목록 상자를 표시 하는 설정 섹션의 부분 스크린샷](common/provisioning-scope.png)

13. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![저장 및 삭제 옵션을 보여 주는 부분 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 이후 주기 보다 오래 걸립니다. Azure AD 프로 비전 서비스가 실행 되는 경우 이후 주기는 약 40 분 마다 발생 합니다.

## <a name="step-6-monitor-your-deployment"></a>6 단계: 배포 모니터링

프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

* [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [격리 상태의 애플리케이션 프로비저닝](../app-provisioning/application-provisioning-quarantine-status.md)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).