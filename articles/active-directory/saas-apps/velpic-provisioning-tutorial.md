---
title: '자습서: Azure Active Directory로 자동 사용자 프로 비전에 대 한 Velpic 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 Velpic에 사용자 계정을 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270917"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Velpic 구성

이 자습서에서는 자동으로 프로 비전 하 고 Velpic에 Azure AD에서 사용자 계정을 프로 비전 해제를 Velpic 및 Azure AD에서 수행 해야 하는 단계를 설명 하는 것입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure Active Directory 테넌트
* 사용 하 여 Velpic 테 넌 트를 [Enterprise 계획](https://www.velpic.com/pricing.html) 이상을 사용 하도록 설정
* Velpic에 관리자 권한이 있는 사용자 계정

## <a name="assigning-users-to-velpic"></a>Velpic에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 "할당된" 사용자 및 그룹만 동기화됩니다. 

를 구성 하 고 프로 비전 서비스를 사용 하도록 설정 하기 전에 Velpic 앱에 액세스 해야 하는 사용자를 대표 하는 사용자 및/또는 Azure AD의 그룹을 결정 해야 합니다. 결정 했으면 다음 지침에 따라 이러한 사용자에이 게 Velpic 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Velpic에 사용자를 할당 하기 위한 주요 팁

* 것이 좋습니다는 단일 Azure AD 사용자 프로 비전 구성을 테스트 하려면 Velpic에 할당할 수 있습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Velpic에 할당할 때 선택 해야 하는 **사용자** 역할 또는 다른 유효한 응용 프로그램별 역할 (사용 가능한 경우) 할당 대화 상자에서. 이때 합니다 **기본 액세스** 역할 프로 비전에 작동 하지 않습니다 하 고 이러한 사용자를 건너뜁니다.

## <a name="configuring-user-provisioning-to-velpic"></a>Velpic에 사용자 프로 비전 구성

이 섹션에서는 Azure AD에 Velpic의 사용자 계정 프로 비전 API에 연결 하는 방법을 안내 하 고 할당 된 사용자와 Azure AD의 그룹 할당을 기반으로 Velpic에 사용자 계정을 만들고, 업데이트 하 고 비활성화 하도록 프로 비전 서비스를 구성 합니다.

> [!TIP]
> 제공한 지침에 따라 Velpic SAML 기반 Single Sign-on 사용 하기로 합니다 [Azure portal](https://portal.azure.com)합니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>구성 하려면 Azure AD에 Velpic에 자동 사용자 계정 프로비저닝:

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

2. Single sign on Velpic를 이미 구성한 경우 검색 필드를 사용 하 여 Velpic의 인스턴스를 검색 합니다. 선택이 고, 그렇지 **추가** 검색 **Velpic** 응용 프로그램 갤러리에 있습니다. 검색 결과에서 Velpic를 선택 하 고 응용 프로그램의 목록에 추가 합니다.

3. Velpic에 인스턴스를 선택한 다음 선택 합니다 **프로 비전** 탭 합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Velpic 프로 비전](./media/velpic-provisioning-tutorial/Velpic1.png)

5. 아래는 **관리자 자격 증명** 섹션에서 입력 합니다 **테 넌 트 URL 및 토큰 암호** Velpic의. ( Velpic 계정에서 이러한 값을 찾을 수 있습니다. **관리할** > **Integration** > **플러그 인** > **SCIM**)

    ![권한 부여 값](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Azure 포털에서 클릭 **연결 테스트** 를 azure AD에 Velpic 앱에 연결할 수 있습니다. 연결이 실패 Velpic 계정에 관리자 권한이 있는지 확인 하 고 5 단계를 다시 시도 하세요.

7. 프로비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 **알림 전자 메일** 필드에 입력하고 아래 확인란을 선택합니다.

8. **저장**을 클릭합니다.

9. 매핑 섹션에서 선택 **동기화 할 Azure Active Directory 사용자를 Velpic**합니다.

10. 에 **특성 매핑** 섹션에서 Velpic에 Azure AD에서 동기화 할 사용자 특성을 검토 합니다. 특성으로 선택한 **일치** Velpic에 업데이트 작업에 대 한 사용자 계정을 일치 시키는 속성이 사용 됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

11. Velpic에 대 한 서비스를 프로 비전 하는 Azure AD를 사용 하도록 설정 하려면 변경 합니다 **프로 비전 상태** 에 **에** 에 **설정** 섹션

12. **저장**을 클릭합니다.

모든 사용자 및/또는 사용자 및 그룹 섹션에서 Velpic에 할당 된 그룹의 초기 동기화가 시작 됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 수행하는 데 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 클릭하여 프로비전 서비스에서 수행한 모든 작업을 설명하는 프로비전 작업 보고서를 확인할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).