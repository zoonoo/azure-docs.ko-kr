---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 위한 Cornerstone OnDemand 구성 | Microsoft Docs'
description: 사용자 계정을 코너스톤 온디맨드에 자동으로 프로비전 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058445"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Cornerstone OnDemand 구성

이 자습서에서는 코너스톤 온디맨드 및 Azure Active Directory(Azure AD)에서 수행하는 단계를 보여 주며, Azure AD를 구성하여 사용자 또는 그룹을 코너스톤 온디맨드에 자동으로 프로비전및 프로비저닝 해제하도록 구성합니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스의 작동 방식, 작동 방식 및 자주 묻는 질문에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS(서비스형 소프트웨어) 응용 프로그램에 대한 사용자 프로비저닝 및 프로비저닝 을 자동화합니다.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오는 다음과 같은 것을 가정합니다.

* Azure AD 테넌트.
* 코너스톤 온디맨드 테넌트.
* 관리자 권한이 있는 코너스톤 온디맨드의 사용자 계정입니다.

> [!NOTE]
> Azure AD 프로비저닝 통합은 [코너스톤 온디맨드 웹 서비스에](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)의존합니다. 이 서비스는 코너스톤 온디맨드 팀에서 사용할 수 있습니다.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Azure 마켓플레이스에서 초석 온디맨드 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위한 코너스톤 온디맨드를 구성하기 전에 마켓플레이스에서 관리되는 SaaS 응용 프로그램 목록에 코너스톤 온디맨드를 추가합니다.

마켓플레이스에서 코너스톤 온디맨드를 추가하려면 다음 단계를 따르세요.

1. Azure [포털에서](https://portal.azure.com)왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active 디렉터리 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **코너스톤 온디맨드를** 입력하고 결과 패널에서 **코너스톤 온디맨드를** 선택합니다. 응용 프로그램을 추가하려면 **에 추가를**선택합니다.

    ![결과 목록의 Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>코너스톤 온디맨드에 사용자 할당

Azure Active Directory는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD의 어떤 사용자 또는 그룹이 코너스톤 온디맨드에 액세스해야 하는지 결정합니다. 이러한 사용자 또는 그룹을 코너스톤 온디맨드에 할당하려면 [엔터프라이즈 앱에 사용자 또는 그룹 할당의](../manage-apps/assign-user-or-group-access-portal.md)지침을 따릅니다.

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Cornerstone OnDemand에 사용자를 할당하기 위한 주요 팁

* 단일 Azure AD 사용자를 코너스톤 온디맨드에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 나중에 추가 사용자 또는 그룹을 할당할 수 있습니다.

* 사용자를 코너스톤 온디맨드에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>코너스톤 온디맨드에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다. Azure AD의 사용자 또는 그룹 할당을 기반으로 코너스톤 온디맨드에서 사용자 또는 그룹을 생성, 업데이트 및 사용하지 않도록 설정하는 데 사용합니다.

Azure AD에서 코너스톤 온디맨드에 대한 자동 사용자 프로비저닝을 구성하려면 다음 단계를 따르십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램** > **선택 모든 응용 프로그램** > **초석 온디맨드.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Cornerstone OnDemand**를 선택합니다.

    ![응용 프로그램 목록의 초석 온디맨드 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![Cornerstone OnDemand 프로비전](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![코너스톤 온디맨드 프로비저닝 모드](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 관리자 **자격 증명** 섹션에서 코너스톤 OnDemand 계정의 관리자 사용자 이름, 관리자 암호 및 도메인을 입력합니다.

    * 관리자 **사용자 이름** 상자에서 코너스톤 OnDemand 테넌트의 관리자 계정의 도메인 또는 사용자 이름을 입력합니다. 예를 들어 contoso\admin입니다.

    * 관리자 **암호** 상자에 관리자 사용자 이름에 해당하는 암호를 입력합니다.

    * **도메인** 상자에서 코너스톤 온디맨드 테넌트의 웹 서비스 URL을 입력합니다. 예를 들어 서비스는 `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`에 있으며 Contoso의 경우 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`도메인은 입니다. 웹 서비스 URL을 검색하는 방법에 대한 자세한 내용은 [이 PDF를](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)참조하십시오.

6. 5단계에 표시된 상자를 입력한 후 **테스트 연결을** 선택하여 Azure AD가 코너스톤 온디맨드에 연결할 수 있는지 확인합니다. 연결이 실패하면 코너스톤 OnDemand 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![초석 온디맨드 테스트 연결](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 알림 **전자 메일** 상자에 개인 또는 그룹의 전자 메일 주소를 입력하여 프로비저닝 오류 알림을 받습니다. 오류가 **발생할 때 전자 메일 보내기 확인란을** 선택합니다.

    ![초석 온디맨드 알림 이메일](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. **저장**을 선택합니다.

9. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Cornerstone OnDemand에 동기화**를 선택합니다.

    ![코너스톤 온디맨드 동기화](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 속성 매핑 섹션에서 Azure AD에서 코너스톤 온디맨드로 동기화된 사용자 **특성을 검토합니다.** **일치** 속성으로 선택된 특성은 업데이트 작업을 위해 Cornerstone OnDemand의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장을**선택합니다.

    ![초석 온디맨드 특성 매핑](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)지침을 따릅니다.

12. 설정 섹션에서 코너스톤 OnDemand에 대한 Azure AD 프로비저닝 서비스를 활성화하려면 **프로비저닝 상태를** **을 로**변경합니다. **Settings**

    ![코너스톤 온디맨드 프로비저닝 상태](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 코너스톤 온디맨드에 프로비전할 사용자 또는 그룹을 정의합니다. **설정** 섹션에서 **범위에서**원하는 값을 선택합니다.

    ![코너스톤 온디맨드 스코프](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![코너스톤 온디맨드 저장](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 나중에 동기화하는 것보다 수행하는 데 시간이 오래 걸립니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생합니다. 

동기화 세부 **정보** 섹션을 사용하여 진행 상황을 모니터링하고 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다. 이 보고서는 코너스톤 온디맨드에서 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

Cornerstone OnDemand **위치** 특성은 Cornerstone OnDemand 포털에서 역할에 해당하는 값을 예상합니다. 유효한 **위치** 값 목록을 가져오려면 코너스톤 OnDemand 포털에서 **사용자 레코드 > 조직 구조 > 위치 편집으로** 이동합니다.

![초석 온디맨드 프로비저닝 편집 사용자 레코드](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![코너스톤 온디맨드 프로비저닝 포지션](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![코너스톤 온디맨드 프로비저닝 포지션 목록](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
