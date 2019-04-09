---
title: '자습서: Azure Active Directory로 자동 사용자 프로 비전에 대 한 MerchLogix 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 MerchLogix에 사용자 계정을 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fecc5232b26c98c4027174454cf29b81b0ee41
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263602"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 MerchLogix 구성

이 자습서의 목적은 자동으로 프로 비전 하 고 사용자 및/또는 그룹 MerchLogix 프로 비전 해제 하도록 Azure AD를 구성 하려면 MerchLogix와 Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* A MerchLogix tenant
* SCIM 끝점 URL 및 사용자 프로 비전 하는 데 필요한 보안 토큰을 제공할 수 있는 MerchLogix에서 기술 담당자

## <a name="adding-merchlogix-from-the-gallery"></a>갤러리에서 MerchLogix 추가

MerchLogix 자동 사용자를 Azure AD를 사용 하 여 프로 비전을 구성 하기 전에 Azure AD 응용 프로그램 갤러리에서 MerchLogix를 관리 되는 SaaS 응용 프로그램의 목록에 추가 해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 MerchLogix를 추가 하려면 다음 단계를 수행 합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 섹션][2]

3. MerchLogix를 추가 하려면 클릭 합니다 **새 응용 프로그램** 대화 상자의 위쪽 단추입니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 입력 **MerchLogix**합니다.

5. 결과 패널에서 선택 **MerchLogix**를 클릭 하 고는 **추가** MerchLogix SaaS 응용 프로그램 목록에 추가 하려면 단추입니다.

    ![MerchLogix 프로 비전][4]

## <a name="assigning-users-to-merchlogix"></a>MerchLogix에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 "할당된" 사용자 및/또는 그룹만 동기화됩니다. 

를 구성 하 고 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 MerchLogix에 대 한 액세스를 해야 하는 사용자 및/또는 Azure AD의 그룹 결정 해야 합니다. 결정 했으면 할당할 수 있습니다 이러한 사용자 및/또는 그룹을 MerchLogix 여기의 지침에 따라.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>MerchLogix에 사용자를 할당 하기 위한 주요 팁

* 것이 좋습니다 단일 MerchLogix에 초기에 자동 사용자 프로 비전 구성을 테스트 하려면 Azure AD 사용자를 할당 합니다. 테스트가 성공적으로 완료되면 추가 사용자 및/또는 그룹이 나중에 할당될 수 있습니다.

* 사용자를 MerchLogix에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)를 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>MerchLogix에 자동 사용자 프로비저닝 구성 

이 섹션에서는 생성, 업데이트 및 사용자를 사용 하지 않도록 설정 하려면 Azure AD 프로 비전 서비스를 구성 하는 단계 및/또는 안내 그룹 MerchLogix에서 Azure AD의 사용자 및/또는 그룹 할당에 기반 합니다.

> [!TIP]
> 제공한 지침에 따라 SAML 기반 single sign-on 사용 MerchLogix에 대 한 수도 있습니다, 합니다 [MerchLogix single sign-on 자습서](merchlogix-tutorial.md)합니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Azure AD에서 MerchLogix에 대 한 자동 사용자 프로 비전을 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션**으로 차례로 이동합니다.

2. SaaS 응용 프로그램 목록에서 MerchLogix를 선택 합니다.

3. **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![MerchLogix 프로 비전](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. 아래는 **관리자 자격 증명** 섹션:

    * 에 **테 넌 트 URL** 필드 MerchLogix의 기술 담당자가 제공 하는 SCIM 끝점 URL을 입력 합니다.

    * 에 **비밀 토큰** 필드 MerchLogix의 기술 담당자에서 제공 하는 보안 토큰을 입력 합니다.

6. 5 단계에서에서 표시 된 필드를 채우면 클릭 **연결 테스트** Azure를 확인 하려면 AD MerchLogix에 연결할 수 있습니다. 연결에 실패 하면 MerchLogix 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

7. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

8. **저장**을 클릭합니다.

9. 아래는 **매핑을** 섹션에서 **동기화 할 Azure Active Directory 사용자를 MerchLogix**합니다.

10. Merchlogix에서 Azure AD에서 동기화 되는 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. 으로 선택한 특성 **일치** 속성은 MerchLogix의 업데이트 작업에 대 한 사용자 계정을 일치 하는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

11. 아래는 **매핑을** 섹션에서 **동기화 할 Azure Active Directory 그룹을 MerchLogix**합니다.

12. MerchLogix에서 Azure AD에서 동기화 된 그룹 특성을 검토 합니다 **특성 매핑** 섹션입니다. 으로 선택한 특성 **일치** 속성 업데이트 작업을 위해 MerchLogix의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

13. 프로 비전 서비스 MerchLogix Azure AD를 사용 하도록 설정 하려면 변경 합니다 **프로 비전 상태** 를 **에** 에 **설정** 섹션.

14. 프로비전할 준비가 되면 **저장**을 클릭합니다.

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. 사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 링크를 프로 비전 활동 보고서를 프로 비전 서비스 MerchLogix에서 Azure AD에서 수행한 모든 작업을 설명 하는 섹션입니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대 한 사용자 계정 프로 비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [로그를 검토 하 고 프로 비전 활동에 대 한 보고서를 확인 하는 방법에 알아봅니다](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
