---
title: '자습서: Azure Active Directory를 사용한 자동 사용자 프로비저닝을 위한 Cisco Spark 구성 | Microsoft Docs'
description: 사용자 계정을 Cisco Spark에 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c45a19c8f6bbce2ffdfff0758eb3e57203b7cf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211209"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Cisco Spark 구성


이 자습서에서는 사용자를 Cisco Spark로 자동으로 프로비전 및 프로비전 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Cisco Spark 및 Azure AD에서 수행하는 단계를 보여 줍니다.


> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

*   Azure AD 테넌트
*   Cisco Spark 테넌트
*   관리자 권한이 있는 Cisco Spark의 사용자 계정


> [!NOTE]
> Azure AD 프로비전 통합에는 [Cisco Spark Webservice](https://developer.webex.com/getting-started.html)가 필요하며, 이는 Cisco Spark 팀에서 사용할 수 있습니다.

## <a name="adding-cisco-spark-from-the-gallery"></a>갤러리에서 Cisco Spark 추가
Azure AD를 사용하여 사용자를 자동으로 프로비전하도록 Cisco Spark를 구성하기 전에, Cisco Spark를 Azure AD 애플리케이션 갤러리에서 관리되는 SaaS 애플리케이션 목록으로 추가해야 합니다.

**Azure AD 애플리케이션 갤러리에서 Cisco Spark를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 섹션][2]

3. Cisco Spark를 추가하려면 대화 상자의 위쪽에서 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Cisco Spark**를 입력합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. 결과 패널에서 **Cisco Spark**를 선택한 다음, **추가** 단추를 클릭하여 SaaS 애플리케이션의 목록에 Cisco Spark를 추가합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Cisco Spark에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 "할당된" 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비전을 구성하고 사용하도록 설정하기 전에 Cisco Spark에 액세스해야 하는 Azure AD의 사용자를 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Cisco Spark에 할당할 수 있습니다.

*   [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Cisco Spark에 사용자를 할당하기 위한 주요 팁

*   단일 Azure AD 사용자를 Cisco Spark에 할당하여 자동 사용자 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자를 나중에 할당할 수 있습니다.

*   사용자를 Cisco Spark에 할당할 때 할당 대화 상자에서 유효한 애플리케이션별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Cisco Spark에 자동 사용자 프로비전 구성

이 섹션에서는 Azure AD의 사용자 할당에 따라 Cisco Spark에서 사용자를 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Azure AD에서 Cisco Spark에 대한 자동 사용자 프로비전을 구성하려면 다음을 수행합니다.


1. [Azure Portal](https://portal.azure.com)에 로그인하고, **Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션**으로 차례로 이동합니다.

2. SaaS 애플리케이션 목록에서 Cisco Spark를 선택합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. **프로비전** 탭을 선택합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. **관리자 자격 증명** 섹션 아래에 **테넌트 URL** 및 Cisco Spark 계정의 **비밀 토큰**을 입력합니다.

    *   **테넌트 URL** 필드에서 테넌트의 Cisco Spark SCIM API URL을 `https://api.ciscospark.com/v1/scim/[Tenant ID]/` 형식으로 채웁니다. 여기서 `[Tenant ID]`는 6단계에서 설명한 대로 영숫자 문자열입니다.

    *   6단계에서 설명한 대로 **비밀 토큰** 필드에 비밀 토큰을 채웁니다.

1. Cisco Spark 계정의 **테넌트 ID** 와 **비밀 토큰**은 관리자 계정으로 [Cisco Spark 개발자 사이트](https://developer.webex.com/)에 로그인하면 찾을 수 있습니다. 로그인한 후에
    * [시작 페이지](https://developer.webex.com/getting-started.html)로 이동합니다.
    * [인증 섹션](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Spark 인증 토큰](./media/cisco-spark-provisioning-tutorial/SecretToken.png)까지 아래로 스크롤합니다.
    * 상자에 있는 영숫자 문자열이 **비밀 토큰**입니다. 이 토큰을 클립보드에 복사합니다.
    * [Get My Own Details page](https://developer.webex.com/endpoint-people-me-get.html)(내 세부 정보 가져오기 페이지)로 이동합니다.
        * Test Mode(테스트 모드)가 ON(켜짐)인지 확인합니다.
        * Authorization(인증) 필드에 "Bearer"를 입력하고 그 뒤에 공백을 입력한 다음, 비밀 토큰을 붙여넣습니다. ![Cisco Spark 인증 토큰](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
        * 실행을 클릭합니다.
    * 오른쪽의 응답 텍스트에 **테넌트 ID**가 "orgId":로 표시됩니다.

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. 5단계에 표시된 필드를 채우면 **연결 테스트**를 클릭하여 Azure AD가 Cisco Spark에 연결할 수 있는지 확인합니다. 연결이 실패하면 Cisco Spark 계정에 관리자 권한이 있는지 확인한 후 다시 시도합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. **저장**을 클릭합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자를 Cisco Spark에 동기화**를 선택합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. **특성 매핑** 섹션에서 Azure AD에서 Cisco Spark로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Cisco Spark의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Cisco Spark에 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. **설정**의 **범위** 섹션에서 원하는 값을 선택하여 Cisco Spark에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![Cisco Spark 프로비전](./media/cisco-spark-provisioning-tutorial/Save.png)


이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고, Cisco Spark의 Azure AD 프로비전 서비스에서 수행한 모든 작업을 설명하는 프로비전 활동 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Cisco Spark는 현재 Cisco의 EFT(초기 필드 테스트) 단계에 있습니다. 자세한 내용은 [Cisco 지원 팀](https://www.webex.co.in/support/support-overview.html)에 문의하세요. 

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>다음 단계

* [프로비전 활동에 대한 로그를 검토하고 보고서를 받아 보는 방법을 살펴봅니다](../manage-apps/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
