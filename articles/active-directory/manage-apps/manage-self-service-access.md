---
title: 셀프 서비스 애플리케이션 할당을 구성하는 방법 | Microsoft Docs
description: 셀프 서비스 애플리케이션 액세스를 활성화하여 사용자가 자신의 애플리케이션을 찾을 수 있도록 함
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcccc64e0c766164a06932e9b65a4459816f9deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409119"
---
# <a name="how-to-configure-self-service-application-assignment"></a>셀프 서비스 애플리케이션 할당을 구성하는 방법

사용자가 내 앱 액세스 패널에서 응용 프로그램을 자체 검색할 수 있으려면 사용자가 자체 검색하고 액세스를 요청할 수 있도록 하려는 모든 응용 프로그램에 셀프 **서비스 응용 프로그램** 액세스를 사용하도록 설정해야 합니다. 이 기능은 [Azure AD 갤러리,](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) [Azure AD 응용 프로그램 프록시에서](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 추가되거나 [사용자 또는 관리자 동의를](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)통해 추가된 응용 프로그램에 사용할 수 있습니다. 

이 기능은 IT 그룹이 시간과 비용을 절감하는 유용한 방법이며, Azure Active Directory를 사용하는 최신 애플리케이션 배포의 일부로 사용하는 것이 좋습니다.

이 기능을 사용하면 다음을 수행할 수 있습니다.

-   사용자가 IT 그룹을 방해하지 않고 [내 앱 액세스 패널에서](https://myapps.microsoft.com/) 응용 프로그램을 자체 검색할 수 있도록 합니다.

-   액세스를 요청한 사용자가 누군지 알고, 액세스를 제거하고, 할당된 역할을 관리할 수 있도록 미리 구성된 그룹에 해당 사용자를 추가합니다.

-   필요에 따라 비즈니스 승인자가 애플리케이션 액세스 요청을 승인할 수 있도록 합니다. 그러면 IT 그룹에서 이러한 작업을 수행할 필요가 없습니다.

-   필요에 따라 이 애플리케이션에 대한 액세스를 승인할 수 있는 최대 10명의 개별 사용자를 구성합니다.

-   필요에 따라 해당 사용자가 애플리케이션에 로그인하는 데 사용할 수 있는 암호를 비즈니스 승인자가 해당 [애플리케이션 액세스 패널](https://myapps.microsoft.com/)에서 바로 설정할 수 있도록 합니다.

-   필요에 따라 자동으로 셀프 서비스 할당 사용자를 애플리케이션 역할에 직접 할당합니다.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>셀프 서비스 애플리케이션 액세스를 활성화하여 사용자가 자신의 애플리케이션을 찾을 수 있도록 함

셀프 서비스 응용 프로그램 액세스는 사용자가 응용 프로그램을 자체 검색할 수 있도록 하고 선택적으로 비즈니스 그룹이 해당 응용 프로그램에 대한 액세스를 승인할 수 있도록 하는 좋은 방법입니다. 응용 프로그램에 대한 암호 단일 사인의 경우 비즈니스 그룹이 자체 내 앱 액세스 패널에서 해당 사용자에게 할당된 자격 증명을 관리하도록 허용할 수도 있습니다.

애플리케이션에 대한 셀프 서비스 애플리케이션 액세스를 사용하려면 다음 단계를 수행합니다.

1. [Azure 포털에](https://portal.azure.com) 글로벌 관리자로 로그인합니다.

2. **Azure 활성 디렉터리**를 선택합니다. 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램을 선택합니다.**

3. 목록에서 응용 프로그램을 선택합니다. 응용 프로그램이 표시되지 않으면 검색 상자에 이름을 입력하십시오. 또는 필터 컨트롤을 사용하여 응용 프로그램 유형, 상태 또는 표시 를 선택한 다음 **적용을**선택합니다.

4. 왼쪽 탐색 메뉴에서 **셀프 서비스**를 선택합니다.

5. 이 애플리케이션에 대한 셀프 서비스 애플리케이션 액세스를 활성화하려면 **사용자가 이 애플리케이션에 대한 액세스를 요청하도록 허용하시겠습니까?** 토글을 **예**로 전환합니다.

6. 다음으로 **사용자를 할당해야 하는 그룹다음에 그룹** **선택을**클릭합니다. 그룹을 선택한 다음 **을 선택합니다.** 사용자의 요청이 승인되면 이 그룹에 추가됩니다. 이 그룹의 구성원 자격을 볼 때 셀프 서비스 액세스를 통해 응용 프로그램에 대한 액세스 권한이 부여된 사람을 볼 수 있습니다.
  
    > [!NOTE]
    > 이 설정은 온-프레미스에서 동기화된 그룹을 지원하지 않습니다.

7. **선택 사항:** 사용자가 액세스가 허용되기 전에 비즈니스 승인을 요구하려면 **이 응용 프로그램에 대한 액세스 권한을 부여하기 전에 승인 필요를** **설정합니까?**

8. **선택 사항: 암호 Single-sign on만 사용하는 응용 프로그램의 경우 비즈니스** 승인자가 승인된 사용자를 위해 이 응용 프로그램으로 전송되는 암호를 지정할 수 있도록 하려면 **승인자가 이 응용 프로그램에 대한 사용자의 암호를 설정하도록** 설정하도록 **설정합니까?**

9. **선택 사항:** 이 응용 프로그램에 대한 액세스를 승인할 수 있는 비즈니스 승인자를 지정하려면 **이 응용 프로그램에 대한 액세스를 승인할 수 있는 사람**옆에 있는 사람 다음에 **승인자 선택을**클릭한 다음 최대 10명의 개별 비즈니스 승인자를 선택합니다. 그런 다음 **선택**을 클릭합니다.

    >[!NOTE]
    >그룹은 지원되지 않습니다. 최대 10명의 개별 비즈니스 승인자를 선택할 수 있습니다. 여러 승인자를 지정하는 경우 단일 승인자가 액세스 요청을 승인할 수 있습니다.

10. **선택 사항:** **역할을 노출하는 응용 프로그램의 경우**셀프 서비스 승인 사용자를 역할에 할당하고 다음의 To 사용자는 이 응용 프로그램에서 **할당해야 하는 역할 선택을**클릭한 다음 이러한 사용자에게 할당할 역할을 선택합니다. **Select Role** 그런 다음 **선택**을 클릭합니다.

11. 창 위쪽에서 **저장** 단추를 클릭하여 완료합니다.

셀프 서비스 응용 프로그램 구성을 완료하면 사용자는 [내 앱 액세스 패널로](https://myapps.microsoft.com/) 이동한 후 **셀프 서비스 앱 추가** 단추를 클릭하여 셀프 서비스 액세스로 사용할 수 있는 앱을 찾을 수 있습니다. 비즈니스 승인자는 내 앱 [액세스 패널에도](https://myapps.microsoft.com/)알림이 표시됩니다. 사용자가 승인이 필요한 애플리케이션에 대한 액세스를 요청한 경우 비즈니스 승인자에게 알리는 이메일을 활성화할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[셀프 서비스 그룹 관리를 위한 Azure Active Directory 설정](../users-groups-roles/groups-self-service-management.md)
