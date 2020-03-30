---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝을 위한 Velpic 구성 | 마이크로 소프트 문서'
description: Azure Active Directory를 구성하여 사용자 계정을 Velpic에 자동으로 프로비전 및 프로비저닝 해제하도록 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064124"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Velpic 구성

이 자습서의 목적은 Azure AD에서 Velpic으로 사용자 계정을 자동으로 프로비전 및 프로비저닝 해제하기 위해 Velpic 및 Azure AD에서 수행해야 하는 단계를 보여 드리는 것입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure Active Directory 테넌트
* [엔터프라이즈 플랜](https://www.velpic.com/pricing.html) 또는 더 나은 사용 이 가능한 벨픽 테넌트
* 관리자 권한이 있는 Velpic의 사용자 계정

## <a name="assigning-users-to-velpic"></a>벨픽에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 "할당된" 사용자 및 그룹만 동기화됩니다. 

프로비저닝 서비스를 구성하고 활성화하기 전에 Azure AD의 사용자 및/또는 그룹이 Velpic 앱에 액세스해야 하는 사용자를 나타내는 지 결정해야 합니다. 일단 결정, 당신은 여기에 지침에 따라 Velpic 응용 프로그램에 이러한 사용자를 할당 할 수 있습니다 :

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>사용자를 Velpic에 할당하기 위한 중요한 팁

* 프로비저닝 구성을 테스트하기 위해 단일 Azure AD 사용자를 Velpic에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Velpic에 할당할 때 할당 대화 상자에서 **사용자** 역할 또는 다른 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할은 프로비저닝에 작동하지 않으며 이러한 사용자는 건너뜁니다.

## <a name="configuring-user-provisioning-to-velpic"></a>Velpic에 사용자 프로비전 구성

이 섹션에서는 Azure AD를 Velpic의 사용자 계정 프로비저닝 API에 연결하고 프로비저닝 서비스를 구성하여 Azure AD의 사용자 및 그룹 할당을 기반으로 Velpic에서 할당된 사용자 계정을 생성, 업데이트 및 비활성화하도록 안내합니다.

> [!TIP]
> [Azure 포털에서](https://portal.azure.com)제공된 지침에 따라 Velpic에 대해 SAML 기반 단일 사인온을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Azure AD에서 Velpic에 자동 사용자 계정 프로비전을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

2. 단일 사인온을 위해 Velpic을 이미 구성한 경우 검색 필드를 사용하여 Velpic 인스턴스를 검색합니다. 그렇지 않으면 응용 프로그램 갤러리에서 **추가를** 선택하고 **Velpic을** 검색합니다. 검색 결과에서 Velpic을 선택하고 응용 프로그램 목록에 추가합니다.

3. Velpic 인스턴스를 선택한 다음 **프로비저닝 탭을 선택합니다.**

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![벨픽 프로비저닝](./media/velpic-provisioning-tutorial/Velpic1.png)

5. 관리자 자격 증명 섹션에서 Velpic의 **비밀 토큰에&테넌트 URL을** **입력합니다.** (당신은 당신의 Velpic 계정에서 이러한 값을 찾을 수 있습니다:**통합** > **플러그인** > **SCIM** **관리)** > 

    ![권한 부여 값](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Azure 포털에서 **테스트 연결을** 클릭하여 Azure AD가 Velpic 앱에 연결할 수 있도록 합니다. 연결이 실패하면 Velpic 계정에 관리자 권한이 있는지 확인하고 5단계를 다시 시도하십시오.

7. 프로비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 **알림 전자 메일** 필드에 입력하고 아래 확인란을 선택합니다.

8. **저장**을 클릭합니다.

9. 매핑 섹션에서 **Azure Active Directory 사용자를 Velpic로 동기화합니다.**

10. 특성 매핑 섹션에서 Azure AD에서 Velpic으로 동기화될 사용자 **특성을 검토합니다.** **일치** 속성으로 선택한 특성은 업데이트 작업에 대한 Velpic의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

11. Velpic에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

12. **저장**을 클릭합니다.

그러면 사용자 및 그룹 섹션에서 Velpic에 할당된 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 수행하는 데 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 클릭하여 프로비전 서비스에서 수행한 모든 작업을 설명하는 프로비전 작업 보고서를 확인할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).