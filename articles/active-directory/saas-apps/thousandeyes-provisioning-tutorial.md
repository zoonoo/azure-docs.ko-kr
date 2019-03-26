---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 ThousandEyes 구성 | Microsoft Docs'
description: 사용자 계정을 ThousandEyes로 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f008e981abb11a4927ec045c33342bbac9a05bd8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436805"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 ThousandEyes 구성


이 자습서의 목적은 사용자 계정을 Azure AD에서 ThousandEyes로 자동으로 프로비전 및 프로비전 해제하도록 ThousandEyes 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure Active Directory 테넌트
*   활성 [ThousandEyes 계정](https://www.thousandeyes.com/pricing)
*   다음 3 개의 사용 권한을 포함 하는 역할 할당 된 ThousandEyes 사용자 계정:
    * 모든 사용자 보기
    * 사용자 편집
    * API 액세스 권한

> [!NOTE]
> Azure AD 프로 비전 통합에 의존 합니다 [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK_ThousandEyes-support-for-SCIM)합니다. 

## <a name="assigning-users-to-thousandeyes"></a>ThousandEyes에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다. 

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 ThousandEyes 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지침에 따라 이러한 사용자를 ThousandEyes 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>ThousandEyes에 사용자를 할당하기 위한 주요 팁

*   단일 Azure AD 사용자를 ThousandEyes에 할당하여 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   ThousandEyes에 사용자를 할당할 때 선택 해야 하는 **사용자** 역할이 나 다른 유효한 응용 프로그램별 역할 (사용 가능한 경우) 할당 대화 상자에서. **기본 액세스** 역할은 프로비전에 적합하지 않으므로 이러한 사용자는 건너뜁니다.

## <a name="configure-auto-provisioned-user-roles-in-thousandeyes"></a>ThousandEyes에 자동으로 프로 비전 사용자 역할을 구성 합니다.

자동 프로 비전 하려는 각 계정 그룹에 대 한 사용자 수에 새 사용자 계정을 만들 때 적용할 역할 집합을 구성할 수 있습니다. 기본적으로 할당 된 사용자 자동 프로 비전 합니다 _일반 사용자_ 달리 구성 하지 않는 한 모든 계정에 대 한 역할을 그룹화 합니다.

1. 사용자 자동 프로 비전에 대 한 역할의 새 집합을 지정 하려면 로그에 ThousandEyes SCIM 설정 섹션으로 이동 하 고 **> 오른쪽 위 모서리에서 사용자 아이콘 > 계정 설정 > 조직 > 보안 및 인증 합니다.** 

   ![SCIM API 설정으로 이동](https://monosnap.com/file/kqY8Il7eysGFAiCLCQWFizzM27PiBG)

2. 각 계정 그룹에 대 한 항목을 추가, 그런 다음 일련의 역할 할당 *저장할* 변경 합니다.

   ![SCIM API를 통해 만든 사용자에 대 한 기본 역할 및 계정 그룹 설정](https://monosnap.com/file/16siam6U8xDQH1RTnaxnmIxvsZuNZG)


## <a name="configuring-user-provisioning-to-thousandeyes"></a>ThousandEyes에 사용자 프로비전 구성 

이 섹션에서는 사용자의 Azure AD를 ThousandEyes의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 ThousandEyes에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!TIP]
> 따라 ThousandEyes에 대 한 SAML 기반 Single Sign-on (SSO)을 사용 하도록 선택할 수도 있습니다는 [기술 자료를 Azure에 제공 된 지침](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-tutorial) SSO를 완료 합니다. SSO는 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Azure AD에서 ThousandEyes에 자동 사용자 계정 프로비전 구성


1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

2. 이미 ThousandEyes에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 ThousandEyes의 인스턴스를 검색합니다. 그러지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **ThousandEyes**를 검색합니다. 검색 결과에서 ThousandEyes를 선택하고 애플리케이션 목록에 추가합니다.

3. ThousandEyes의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![ThousandEyes 프로비전](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. 아래는 **관리자 자격 증명** 섹션을 입력 합니다 **OAuth 전달자 토큰** ThousandEyes' 계정에서 생성 (ThousandEyes 계정 토큰을 생성 하는 또는 및 찾을 수  **프로 파일링** 섹션).

    ![ThousandEyes 프로비전](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 ThousandEyes 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 ThousandEyes 계정에 관리자 권한이 있는지 확인하고 5단계를 다시 시도합니다.

7. 프로비전 오류 알림을 받을 개인 또는 그룹의 메일 주소를 **알림 메일** 필드에 입력하고 “오류가 발생할 경우 메일 알림 보내기” 확인란을 선택합니다.

8. **저장**을 클릭합니다. 

9. [매핑] 섹션에서 **Synchronize Azure Active Directory Users to ThousandEyes**(Azure Active Directory 사용자를 ThousandEyes에 동기화)를 선택합니다.

10. **특성 매핑** 섹션에서 Azure AD에서 ThousandEyes로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 ThousandEyes의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

11. ThousandEyes에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

12. **저장**을 클릭합니다. 

[사용자 및 그룹] 섹션에서 ThousandEyes에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 프로비전 서비스에서 수행하는 모든 작업을 설명하는 프로비전 활동 로그에 연결된 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.


## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비전 활동에 대한 로그를 검토하고 보고서를 받아 보는 방법을 살펴봅니다](../manage-apps/check-status-user-account-provisioning.md).
