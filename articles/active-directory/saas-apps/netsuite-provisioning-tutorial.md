---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 NetSuite OneWorld 구성 Microsoft Docs'
description: Azure Active Directory와 NetSuite OneWorld 간에 Single Sign-On를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 8406ee5647d02cc917a0fdb1daf2355611bb781d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792211"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 NetSuite 구성

이 자습서는 사용자 계정을 Azure AD에서 NetSuite로 자동으로 프로 비전 및 프로 비전 해제 하도록 NetSuite OneWorld 및 Azure AD에서 수행 해야 하는 단계를 보여 주기 위한 것입니다.

> [!NOTE]
> 이 통합은 현재 기본 인증 (사용자 이름 및 암호)을 사용 하 여 인증 합니다. NetSuite는이 요구 사항에 대 한 예외가 있는 경우를 제외 하 고 고객이이 통합을 사용할 수 없도록 하는 multi-factor authentication 요구 사항을 구현 했습니다. NetSuite를 사용 하 여이 통합을 최신 인증 방법으로 업데이트 하는 것은 예외 없이 고객이 다시 사용 하도록 설정 하는 것입니다. 이 문서를 사용할 수 있게 되 면이 문서를 에타로 업데이트 합니다.

권장 조치:이 통합에 대 한 인증 동작에 대 한 업데이트를 릴리스할 때까지 기다리거나, multi-factor authentication 요구 사항에 대 한 예외를 NetSuite 지원에 문의 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure Active Directory 테넌트.
*   NetSuite OneWorld 구독. 자동 사용자 프로비저닝은 현재 NetSuite OneWorld에서만 지원됩니다.
*   관리자 권한이 있는 Netsuite의 사용자 계정
*   Azure AD와 통합 하려면 2FA 예외가 필요 합니다. 이 예외를 요청 하려면 NetSuite의 지원 팀에 문의 하세요.

## <a name="assigning-users-to-netsuite-oneworld"></a>NetSuite OneWorld에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 "할당된" 사용자 및 그룹만 동기화됩니다.

프로 비전 서비스를 구성 하 고 사용 하도록 설정 하기 전에 NetSuite 앱에 액세스 해야 하는 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정 해야 합니다. 일단 결정 되 면 다음 지침에 따라 이러한 사용자를 NetSuite 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>NetSuite OneWorld에 사용자를 할당 하기 위한 주요 팁

*   프로 비전 구성을 테스트 하기 위해 단일 Azure AD 사용자를 NetSuite에 할당 하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   NetSuite에 사용자를 할당할 때 유효한 사용자 역할을 선택 해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-user-provisioning"></a>사용자 프로비저닝 사용

이 섹션에서는 Azure AD를 NetSuite의 사용자 계정 프로 비전 API에 연결 하 고, Azure AD의 사용자 및 그룹 할당을 기반으로 NetSuite에서 할당 된 사용자 계정을 만들고, 업데이트 하 고 비활성화 하도록 프로 비전 서비스를 구성 하는 과정을 안내 합니다.

> [!TIP] 
> [Azure Portal](https://portal.azure.com)에 제공 된 지침에 따라 NetSuite에 대해 SAML 기반 Single Sign-On을 사용 하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-user-account-provisioning"></a>사용자 계정 프로비전을 구성하려면

이 섹션은 NetSuite에 Active Directory 사용자 계정을 프로 비전 할 수 있도록 설정 하는 방법을 간략하게 설명 하기 위한 것입니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory > Enterprise Apps > 모든 응용 프로그램** 섹션으로 이동 합니다.

1. Single Sign-On에 대해 NetSuite를 이미 구성한 경우 검색 필드를 사용 하 여 NetSuite의 인스턴스를 검색 합니다. 그렇지 않은 경우 **추가** 를 선택 하 고 응용 프로그램 갤러리에서 **NetSuite** 를 검색 합니다. 검색 결과에서 NetSuite를 선택 하 고 응용 프로그램 목록에 추가 합니다.

1. NetSuite의 인스턴스를 선택한 다음 **프로 비전** 탭을 선택 합니다.

1. **프로비전 모드** 를 **자동** 으로 설정합니다. 

    ![NetSuite 프로 비전 페이지가 표시 되 고 프로 비전 모드가 자동으로 설정 되 고 다른 값을 설정할 수 있습니다.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. **관리자 자격 증명** 섹션에서 다음 구성 설정을 제공합니다.
   
    a. **관리 사용자 이름** 텍스트 상자에 NetSuite.com이 할당 된 **시스템 관리자** 프로필을 가진 NetSuite 계정 이름을 입력 합니다.
   
    b. **관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.
      
1. Azure Portal에서 **연결 테스트** 를 클릭 하 여 Azure AD가 NetSuite 앱에 연결할 수 있는지 확인 합니다.

1. 프로비전 오류 알림을 받을 개인 또는 그룹의 메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

1. **저장** 을 클릭합니다.

1. 매핑 섹션 아래에서 **NetSuite에 사용자 Azure Active Directory 동기화를 선택 합니다.**

1. **특성 매핑** 섹션에서 Azure AD에서 NetSuite로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 NetSuite의 사용자 계정을 일치 시키는 데 사용 됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

1. NetSuite에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 설정 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

1. **저장** 을 클릭합니다.

사용자 및 그룹 섹션에서 NetSuite에 할당 된 모든 사용자 및/또는 그룹의 초기 동기화가 시작 됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 링크를 따라 NetSuite 앱의 프로 비전 서비스에서 수행 하는 모든 작업을 설명 하는 활동 로그를 프로 비전 할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Single Sign-on 구성](netsuite-tutorial.md)
