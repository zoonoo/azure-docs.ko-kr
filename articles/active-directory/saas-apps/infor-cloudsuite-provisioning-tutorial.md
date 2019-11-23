---
title: 'Tutorial: Configure Infor CloudSuite for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a1a274d9a20a5c7e487536e2850f253c3230c0cc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408717"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configure Infor CloudSuite for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Infor CloudSuite and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Infor CloudSuite.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>전제 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [A Infor CloudSuite tenant](https://www.infor.com/products/infor-os)
* A user account in Infor CloudSuite with Admin permissions.

## <a name="assigning-users-to-infor-cloudsuite"></a>Assigning users to Infor CloudSuite

Azure Active Directory uses a concept called *assignments* to determine which users should receive access to selected apps. In the context of automatic user provisioning, only the users and/or groups that have been assigned to an application in Azure AD are synchronized.

Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Infor CloudSuite. Once decided, you can assign these users and/or groups to Infor CloudSuite by following the instructions here:
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Important tips for assigning users to Infor CloudSuite

* It is recommended that a single Azure AD user is assigned to Infor CloudSuite to test the automatic user provisioning configuration. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* When assigning a user to Infor CloudSuite, you must select any valid application-specific role (if available) in the assignment dialog. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Set up Infor CloudSuite for provisioning

1. Sign in to your [Infor CloudSuite Admin Console](https://www.infor.com/customer-center). Click on the user icon and then navigate to **user management**.

    ![Infor CloudSuite Admin Console](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Click on the menu icon on the left top corner of the screen. Click on **Manage**.

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigate to **SCIM Accounts**.

    ![Infor CloudSuite SCIM Account](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Add an admin user by clicking on the plus icon. Provide a **SCIM Password** and type the same password under **Confirm Password**. Click on the folder icon to save the password. You will then see an **User Identifier** generated for the admin user.

    ![Infor CloudSuite Admin user](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite password](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. To generate the bearer token, copy the **User Identifier** and **SCIM Password**. Paste them in notepad++ separated by a colon. Encode the string value by navigating to **Plugins > MIME Tools > Basic64 Encode**. 

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copy the bearer token. This value will be entered in the Secret Token field in the Provisioning tab of your Infor CloudSuite application in the Azure portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Add Infor CloudSuite from the gallery

Before configuring Infor CloudSuite for automatic user provisioning with Azure AD, you need to add Infor CloudSuite from the Azure AD application gallery to your list of managed SaaS applications.

**To add Infor CloudSuite from the Azure AD application gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)** , in the left navigation panel, select **Azure Active Directory**.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. To add a new application, select the **New application** button at the top of the pane.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. In the search box, enter **Infor CloudSuite**, select **Infor CloudSuite** in the results panel, and then click the **Add** button to add the application.

    ![결과 목록의 Infor CloudSuite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configuring automatic user provisioning to Infor CloudSuite 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Infor CloudSuite based on user and/or group assignments in Azure AD.

> [!TIP]
> You may also choose to enable SAML-based single sign-on for Infor CloudSuite , following the instructions provided in the [Infor CloudSuite Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). Single Sign-On은 자동 사용자 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

> [!NOTE]
> To learn more about Infor CloudSuite's SCIM endpoint, refer [this](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>To configure automatic user provisioning for Infor CloudSuite in Azure AD:

1. [Azure portal](https://portal.azure.com)에 로그인합니다. Select **Enterprise Applications**, then select **All applications**.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Infor CloudSuite**를 선택합니다.

    ![애플리케이션 목록의 Infor CloudSuite 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Provisioning tab](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Provisioning tab](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in **Tenant URL**. Input the bearer token value retrieved earlier in **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to Infor CloudSuite. If the connection fails, ensure your Infor CloudSuite account has Admin permissions and try again.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. 페이지 맨 아래에 있는 **저장**을 참조하세요.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Infor CloudSuite**.

    ![Infor CloudSuite User Mappings](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Review the user attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Infor CloudSuite for update operations. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Infor CloudSuite User Attributes](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Infor CloudSuite**.

    ![Infor CloudSuite Group Mappings](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Review the group attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Infor CloudSuite for update operations. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Infor CloudSuite Group Attributes](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. To enable the Azure AD provisioning service for Infor CloudSuite, change the **Provisioning Status** to **On** in the **Settings** section.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to Infor CloudSuite by choosing the desired values in **Scope** in the **Settings** section.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. You can use the **Synchronization Details** section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Infor CloudSuite.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).