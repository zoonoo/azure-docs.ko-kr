---
title: Just-In-Time 액세스 승인
description: Azure 관리되는 응용 프로그램의 소비자가 관리되는 응용 프로그램에 대한 적시에 액세스하는 요청을 승인하는 방법을 설명합니다.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651021"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Azure 관리형 응용 프로그램에 대한 적시 액세스 구성 및 승인

관리되는 응용 프로그램의 소비자는 게시자가 관리되는 리소스 그룹에 영구적으로 액세스할 수 있도록 하는 것이 불편할 수 있습니다. 관리되는 리소스에 대한 액세스 권한을 부여하는 것을 보다 세월한 제어할 수 있도록 Azure Managed Application은 현재 미리 보기 중인 JIT(적시) 액세스라는 기능을 제공합니다. 이를 통해 게시자가 리소스 그룹에 액세스할 수 있는 시기와 기간(기간)을 승인할 수 있습니다. 게시자는 해당 시간 동안 필요한 업데이트를 만들 수 있지만 시간이 끝나면 게시자의 액세스가 만료됩니다.

액세스 권한을 부여하기 위한 작업 흐름은 다음과 입니다.

1. 게시자는 마켓플레이스에 관리되는 응용 프로그램을 추가하고 JIT 액세스를 사용할 수 있도록 지정합니다.

1. 배포 하는 동안 관리되는 응용 프로그램의 인스턴스에 대 한 JIT 액세스를 사용 하도록 설정 합니다.

1. 배포 후 JIT 액세스에 대한 설정을 변경할 수 있습니다.

1. 게시자가 액세스 요청을 보냅니다.

1. 요청을 승인합니다.

이 문서에서는 소비자가 JIT 액세스를 활성화하고 요청을 승인하기 위해 취하는 작업에 중점을 둡니다. JIT 액세스 권한을 사용하여 관리되는 응용 프로그램을 게시하는 방법에 대해 자세히 알아보려면 [Azure 관리형 응용 프로그램의 적시 액세스 요청을](request-just-in-time-access.md)참조하십시오.

> [!NOTE]
> 적시에 액세스를 사용하려면 [Azure Active Directory P2 라이선스가](../../active-directory/privileged-identity-management/subscription-requirements.md)있어야 합니다.

## <a name="enable-during-deployment"></a>배포 중 사용

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. JIT를 사용하도록 설정한 관리되는 응용 프로그램에 대한 마켓플레이스 항목을 찾습니다. **만들기**를 선택합니다.

1. JIT **구성** 단계에서는 새 관리되는 응용 프로그램에 대한 값을 제공하는 동시에 관리되는 응용 프로그램에 대한 JIT 액세스를 활성화하거나 비활성화할 수 있습니다. **JIT 액세스 사용에**대해 **예를** 선택합니다. 이 옵션은 마켓플레이스에서 JIT를 사용하도록 사용하도록 설정된 관리되는 응용 프로그램에 대해 기본적으로 선택됩니다.

   ![액세스 구성](./media/approve-just-in-time-access/configure-jit-access.png)

   배포 하는 동안JIT 액세스를 활성화할 수 있습니다. **아니요를**선택하면 게시자가 관리되는 리소스 그룹에 영구적으로 액세스할 수 있습니다. 나중에 JIT 액세스를 활성화할 수 없습니다.

1. 기본 승인 설정을 변경하려면 **JIT 구성 사용자 지정을**선택합니다.

   ![액세스 사용자 지정](./media/approve-just-in-time-access/customize-jit-access.png)

   기본적으로 JIT를 사용하도록 설정한 관리되는 응용 프로그램에는 다음 설정이 있습니다.

   * 승인 모드 – 자동
   * 최대 액세스 시간 - 8시간
   * 승인자 – 없음

   승인 모드가 **자동으로**설정되면 승인자는 각 요청에 대한 알림을 받지만 요청은 자동으로 승인됩니다. **수동으로**설정하면 승인자는 각 요청에 대한 알림을 받고 그 중 하나가 승인해야 합니다.

   활성화 최대 기간은 게시자가 관리되는 리소스 그룹에 대한 액세스를 요청할 수 있는 최대 시간을 지정합니다.

   승인자 목록은 JIT 액세스 요청을 승인할 수 있는 Azure Active Directory 사용자입니다. 승인자를 추가하려면 **승인자 추가를** 선택하고 사용자를 검색합니다.

   설정을 업데이트한 후 **저장을**선택합니다.

## <a name="update-after-deployment"></a>배포 후 업데이트

요청이 승인되는 방식에 대한 값을 변경할 수 있습니다. 그러나 배포 하는 동안 JIT 액세스를 사용 하지 않은 경우 나중에 사용할 수 없습니다.

배포된 관리되는 응용 프로그램의 설정을 변경하려면 다음을 수행하십시오.

1. 포털에서 관리 응용 프로그램을 선택합니다.

1. **JIT 구성을** 선택하고 필요에 따라 설정을 변경합니다.

   ![액세스 설정 변경](./media/approve-just-in-time-access/change-settings.png)

1. 완료되면 **저장**을 선택합니다.

## <a name="approve-requests"></a>요청 승인

게시자가 액세스를 요청하면 요청 에 대한 알림을 받게 됩니다. 관리되는 응용 프로그램을 통해 직접 또는 Azure AD 권한 ID 관리 서비스를 통해 관리되는 모든 응용 프로그램에서 JIT 액세스 요청을 승인할 수 있습니다. 적시에 액세스를 사용하려면 [Azure Active Directory P2 라이선스가](../../active-directory/privileged-identity-management/subscription-requirements.md)있어야 합니다.

관리되는 응용 프로그램을 통해 요청을 승인하려면 다음을 수행하십시오.

1. 관리되는 응용 프로그램에 대한 **JIT 액세스를** 선택하고 **요청 승인을**선택합니다.

   ![요청 승인](./media/approve-just-in-time-access/approve-requests.png)
 
1. 승인 요청을 선택합니다.

   ![요청 선택](./media/approve-just-in-time-access/select-request.png)

1. 양식에서 승인 이유를 제공하고 **승인을**선택합니다.

Azure AD 권한 ID 관리를 통해 요청을 승인하려면 다음을 수행하십시오.

1. **모든 서비스를** 선택하고 **Azure AD 권한 ID 관리를**검색하기 시작합니다. 사용 가능한 옵션에서 선택합니다.

   ![서비스 검색](./media/approve-just-in-time-access/search.png)

1. **요청 승인을**선택합니다.

   ![승인 요청 선택](./media/approve-just-in-time-access/select-approve-requests.png)

1. Azure 관리 응용 프로그램을 선택하고 승인할 요청을 **선택합니다.**

   ![요청 선택](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>다음 단계

JIT 액세스 권한을 사용하여 관리되는 응용 프로그램을 게시하는 방법에 대해 자세히 알아보려면 [Azure 관리형 응용 프로그램의 적시 액세스 요청을](request-just-in-time-access.md)참조하십시오.
