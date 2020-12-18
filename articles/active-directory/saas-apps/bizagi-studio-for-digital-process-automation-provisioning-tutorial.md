---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 디지털 프로세스 자동화에 대 한 Bizagi Studio 구성 Microsoft Docs'
description: 디지털 프로세스 자동화를 위해 Azure AD에서 Bizagi Studio로 사용자 계정을 자동으로 프로 비전 하 고 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 4eaac716d06b102a07872059af28da4986889caa
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673442"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 디지털 프로세스 자동화에 대해 Bizagi Studio 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 Bizagi Studio for Digital Process Automation and Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 이렇게 구성 된 경우 azure ad는 Azure AD 프로 비전 서비스를 사용 하 여 자동으로 사용자 및 그룹을 [Bizagi Studio](https://www.bizagi.com/) 로 프로 비전 하 고 프로 비전 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * 디지털 프로세스 자동화를 위해 Bizagi Studio에서 사용자 만들기
> * 더 이상 액세스 하지 않아도 되는 디지털 프로세스 자동화를 위해 Bizagi Studio에서 사용자 제거
> * 디지털 프로세스 자동화를 위해 Azure AD와 Bizagi Studio 간에 사용자 특성을 동기화 상태로 유지
> * Bizagi Studio에서 디지털 프로세스 자동화에 대 한 [Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명한 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로비저닝을 구성할 수 있는 [권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)이 있는 Azure AD의 사용자 계정. 예를 들면 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자가 있습니다. 
* Bizagi Studio for Digital Process Automation version 11.2.4.2 X 이상.

## <a name="plan-your-provisioning-deployment"></a>프로비저닝 배포 계획
계획을 위해 다음 단계를 수행 합니다.

1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로 비전 범위에](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)있는 사용자를 결정 합니다.
3. [디지털 프로세스 자동화를 위해 AZURE AD와 Bizagi Studio 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정 합니다. 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Azure AD를 사용 하 여 프로 비전을 지원 하도록 구성
Azure AD를 사용 하 여 프로 비전을 지원 하도록 Bizagi Studio for Digital Process 자동화를 구성 하려면 다음 단계를 수행 합니다.

1. **관리자 권한이** 있는 사용자로 작업 포털에 로그인 합니다.

2. **관리**  >  **보안**  >  **OAuth 2 응용 프로그램** 으로 이동 합니다.

   ![OAuth 2 응용 프로그램이 강조 표시 된 Bizagi의 스크린샷](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. **추가** 를 선택합니다.
4. **권한 유형** 에서 **전달자 토큰** 을 선택 합니다. **허용 되는 범위** 에서 **API** 및 **사용자 동기화** 를 선택 합니다. 그런 다음 **저장** 을 선택합니다.

   ![권한 부여 유형 및 허용 범위가 강조 표시 된 응용 프로그램 등록의 스크린샷](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. **클라이언트 암호** 를 복사 하 고 저장 합니다. Azure Portal에서 Bizagi Studio for Digital Process Automation 응용 프로그램의 경우 **프로 비전** 탭에서 클라이언트 암호 값이 **비밀 토큰** 필드에 입력 됩니다.

   ![클라이언트 암호 highlighed를 사용 하는 Oauth의 스크린샷](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 응용 프로그램 추가

디지털 프로세스 자동화에 대 한 Bizagi Studio 프로 비전 관리를 시작 하려면 Azure AD 응용 프로그램 갤러리에서 앱을 추가 합니다. Single Sign-On에 대 한 디지털 프로세스 자동화를 위해 Bizagi Studio를 이전에 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트 하는 경우 별도의 앱을 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure Active Directory (AZURE AD) 테 넌 트에 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)를 참조 하세요. 

## <a name="define-who-is-in-scope-for-provisioning"></a>프로 비전 범위에 있는 사용자 정의 

Azure AD 프로 비전 서비스를 사용 하면 사용자 및 그룹의 특성 또는 둘 다를 기반으로 응용 프로그램에 대 한 할당을 기반으로 프로 비전 된 사용자의 범위를 지정할 수 있습니다. 할당에 따라 범위를 지정 하는 경우 응용 프로그램에 사용자 및 그룹을 할당 하 [는 Graph API를 사용 하는 앱에 대해 사용자 및 그룹 할당 또는](../manage-apps/assign-user-or-group-access-portal.md) 할당 해제의 단계를 참조 하세요. 사용자 또는 그룹의 특성만을 기준으로 범위를 지정 하는 경우 범위 지정 필터를 사용할 수 있습니다. 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)을 참조 하세요. 

범위 지정에 대해 다음 사항에 유의 하세요.

* 디지털 프로세스 자동화를 위해 Bizagi Studio에 사용자 및 그룹을 할당 하는 경우 **기본 액세스** 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로 비전에서 제외 되 고, 프로 비전 로그에 표시 되는 것은 사실상 자격이 없는 것으로 표시 됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)하여 더 많은 역할을 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정 된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 


## <a name="configure-automatic-user-provisioning"></a>자동 사용자 프로비저닝 구성 

이 섹션에서는 사용자 및 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다. Azure AD의 사용자 및 그룹 할당을 기반으로 테스트 앱에서이 작업을 수행 하는 중입니다.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Azure AD에서 디지털 프로세스 자동화에 대 한 Bizagi Studio 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 으로 차례로 선택합니다.

    ![엔터프라이즈 응용 프로그램 및 모든 응용 프로그램이 강조 표시 된 Azure Portal의 스크린샷](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **Bizagi Studio For Digital Process Automation** 을 선택 합니다.

3. **프로비전** 탭을 선택합니다.

    ![프로 비전이 강조 표시 된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 강조 표시 된 Screenshotof 프로 비전 모드 컨트롤입니다.](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 디지털 프로세스 자동화를 위해 Bizagi Studio 용 테 넌 트 URL 및 비밀 토큰을 입력 합니다. 

      * **테 넌 트 URL:** 다음 구조를 사용 하 여 Bizagi SCIM 끝점을 입력  `<Your_Bizagi_Project>/scim/v2/` 합니다.
         예: `https://my-company.bizagi.com/scim/v2/`.

      * **비밀 토큰:** 이 값은이 문서의 앞부분에 설명 된 단계에서 검색 됩니다.

      Azure AD가 디지털 프로세스 자동화를 위해 Bizagi Studio에 연결할 수 있는지 확인 하려면 **연결 테스트** 를 선택 합니다. 연결에 실패 하면 Bizagi Studio for Digital Process Automation 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

   ![테스트 연결이 강조 표시 된 관리자 자격 증명의 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 전자 메일** 에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 합니다. 오류가 발생 하는 **경우 전자 메일 알림을 보내는** 옵션을 선택 합니다.

    ![알림 전자 메일 옵션의 스크린샷](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **디지털 프로세스 자동화를 위해 Azure Active Directory 사용자를 Bizagi Studio에 동기화** 를 선택 합니다.

9. **특성 매핑** 섹션에서 디지털 프로세스 자동화를 위해 Azure AD에서 Bizagi Studio로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 디지털 프로세스 자동화에 대 한 Bizagi Studio의 사용자 계정을 일치 시키는 데 사용 됩니다. [일치 하는 대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경 하는 경우 Bizagi Studio For Digital PROCESS Automation API에서 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |활성|부울|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|

   **Bizagi에 대 한 특성 목록 편집 > 고급 옵션 표시** 로 이동 하 여 사용자 지정 확장 특성을 추가할 수 있습니다. 사용자 지정 확장 특성에는 urn: **ietf: params: scim: 스키마: extension: bizagi: 2.0: UserProperties:** 접두사가와 야 합니다. 예를 들어 사용자 지정 확장 특성이 **IdentificationNumber** 인 경우 특성은 **urn: ietf: params: scim: 스키마: extension: bizagi: 2.0: UserProperties: IdentificationNumber** 로 추가 해야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.
   
    ![특성 목록을 편집 합니다.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   사용자 지정 특성을 추가 하는 방법에 대 한 자세한 내용은 [응용 프로그램 특성 사용자 지정](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)에서 찾을 수 있습니다.

> [!NOTE]
> 기본 형식 속성만 지원 됩니다 (예: 문자열, 정수, 부울, 날짜/시간 등). 패라메트릭 테이블 또는 여러 형식에 연결 된 속성은 아직 지원 되지 않습니다.

10. 범위 지정 필터를 구성 하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)를 참조 하세요.

11. Bizagi Studio for Digital Process Automation에 대해 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로 비전 상태 전환 스크린샷](common/provisioning-toggle-on.png)

12. 디지털 프로세스 자동화를 위해 Bizagi Studio에 프로 비전 할 사용자 및 그룹을 정의 합니다. **설정** 섹션의 **범위** 에서 원하는 값을 선택 합니다.

    ![범위 옵션의 스크린샷](common/provisioning-scope.png)

13. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![컨트롤 저장의 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="monitor-your-deployment"></a>배포 모니터링
프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

- [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
- [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) 에서 프로 비전 주기의 상태와 완료 방법의 상태를 확인 합니다.
- 프로 비전 구성이 비정상 상태 이면 응용 프로그램이 격리 됩니다. 자세한 내용은 [격리 상태에서 응용 프로그램 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)을 참조 하세요.  

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
