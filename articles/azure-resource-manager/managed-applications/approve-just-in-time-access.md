---
title: Just-In-Time 액세스 승인
description: Azure Managed Applications의 소비자가 관리형 애플리케이션에 대한 Just-In-Time 액세스 요청을 승인하는 방법을 설명합니다.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651021"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Azure Managed Applications에 대한 Just-In-Time 액세스 구성 및 승인

관리형 애플리케이션의 소비자는 게시자에게 관리형 리소스 그룹에 대한 영구적인 액세스 권한을 제공하는 것이 불편하게 느껴질 수 있습니다. 관리형 리소스에 대한 액세스 권한 부여를 더 강력하게 제어할 수 있도록 Azure Managed Applications에서는 JIT(Just-In-Time) 액세스라고 불리는 기능을 제공합니다(현재 미리 보기). 이를 통해 게시자가 리소스 그룹에 액세스하는 시기 및 기간을 승인할 수 있습니다. 게시자는 해당 시간 동안 필요한 업데이트를 수행할 수 있지만 해당 시간이 지나면 게시자의 액세스 권한이 만료됩니다.

액세스 권한을 부여하는 워크플로는 다음과 같습니다.

1. 게시자가 마켓플레이스에 관리형 애플리케이션을 추가하고 JIT 액세스를 사용할 수 있도록 지정합니다.

1. 배포하는 동안 관리형 애플리케이션의 인스턴스에 대한 JIT 액세스를 사용하도록 설정합니다.

1. 배포 후에 JIT 액세스에 대한 설정을 변경할 수 있습니다.

1. 게시자가 액세스 권한 요청을 보냅니다.

1. 사용자가 요청을 승인합니다.

이 문서에서는 소비자가 JIT 액세스를 사용하도록 설정하고 요청을 승인하는 데 필요한 작업을 중점적으로 다룹니다. JIT 액세스를 사용하여 관리형 애플리케이션을 게시하는 방법에 대한 자세한 내용은 [Azure Managed Applications에서 Just-In-Time 액세스 권한 요청](request-just-in-time-access.md)을 참조하세요.

> [!NOTE]
> Just-In-Time 액세스를 사용하려면 [Azure Active Directory P2 라이선스](../../active-directory/privileged-identity-management/subscription-requirements.md)가 있어야 합니다.

## <a name="enable-during-deployment"></a>배포하는 동안 사용하도록 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. JIT를 사용하도록 설정된 관리형 애플리케이션의 마켓플레이스 항목을 찾습니다. **만들기** 를 선택합니다.

1. 새 관리형 애플리케이션에 대한 값을 제공하는 동안 **JIT 구성** 단계에서 관리형 애플리케이션에 대한 JIT 액세스를 사용하거나 사용하지 않도록 설정할 수 있습니다. **JIT 액세스 사용** 에 대해 **예** 를 선택합니다. 마켓플레이스에서 사용하도록 설정된 JIT로 정의된 관리형 애플리케이션의 경우 해당 옵션이 기본적으로 선택됩니다.

   ![액세스 구성](./media/approve-just-in-time-access/configure-jit-access.png)

   배포하는 동안에만 JIT 액세스를 사용하도록 설정할 수 있습니다. **아니요** 를 선택하면 게시자가 관리형 리소스 그룹에 영구적으로 액세스할 수 있습니다. JIT 액세스는 나중에 사용하도록 설정할 수 없습니다.

1. 기본 승인 설정을 변경하려면 **JIT 구성 사용자 지정** 을 선택합니다.

   ![액세스 권한 사용자 지정](./media/approve-just-in-time-access/customize-jit-access.png)

   기본적으로 JIT를 사용하도록 설정된 관리형 애플리케이션의 설정은 다음과 같습니다.

   * 승인 모드 – 자동
   * 최대 액세스 기간 – 8시간
   * 승인자 – 없음

   승인 모드가 **자동** 으로 설정되어 있으면 승인자는 각 요청에 대한 알림을 받으며 요청이 자동으로 승인됩니다. **수동** 으로 설정된 경우 승인자는 각 요청에 대한 알림을 받고 승인자 중 하나가 해당 요청을 승인해야 합니다.

   활성화 최대 기간은 게시자가 관리형 리소스 그룹에 대한 액세스 권한을 요청할 수 있는 최대 시간을 지정합니다.

   승인자 목록은 JIT 액세스 권한 요청을 승인할 수 있는 Azure Active Directory 사용자입니다. 승인자를 추가하려면 **승인자 추가** 를 선택하고 사용자를 검색합니다.

   설정을 업데이트한 후 **저장** 을 선택합니다.

## <a name="update-after-deployment"></a>배포 후 업데이트

요청이 승인되는 방법에 대한 값을 변경할 수 있습니다. 그러나 배포 중에 JIT 액세스를 사용하도록 설정하지 않은 경우에는 나중에 사용하도록 설정할 수 없습니다.

배포된 관리형 애플리케이션에 대한 설정을 변경하려면 다음을 수행합니다.

1. 포털에서 애플리케이션 관리를 선택합니다.

1. **JIT 구성** 을 선택하고 필요에 따라 설정을 변경합니다.

   ![액세스 권한 설정 변경](./media/approve-just-in-time-access/change-settings.png)

1. 완료되면 **저장** 을 선택합니다.

## <a name="approve-requests"></a>요청 승인

게시자가 액세스 권한을 요청하는 경우 요청에 대한 알림이 표시됩니다. 관리형 애플리케이션을 통해 직접 또는 Azure AD Privileged Identity Management 서비스를 통해 모든 관리형 애플리케이션에 대해 JIT 액세스 권한 요청을 승인할 수 있습니다. Just-In-Time 액세스를 사용하려면 [Azure Active Directory P2 라이선스](../../active-directory/privileged-identity-management/subscription-requirements.md)가 있어야 합니다.

관리형 애플리케이션을 통해 요청을 승인하려면 다음을 수행합니다.

1. 관리형 애플리케이션에 대한 **JIT 액세스** 를 선택하고 **요청 승인** 을 선택합니다.

   ![요청 승인](./media/approve-just-in-time-access/approve-requests.png)
 
1. 승인할 요청을 선택합니다.

   ![요청 선택](./media/approve-just-in-time-access/select-request.png)

1. 양식에 승인 이유를 입력하고 **승인** 을 선택합니다.

Azure AD Privileged Identity Management를 통해 요청을 승인하려면 다음을 수행합니다.

1. **모든 서비스** 를 선택하고 **Azure AD Privileged Identity Management** 에 대한 검색을 시작합니다. 사용 가능한 옵션에서 선택합니다.

   ![서비스 검색](./media/approve-just-in-time-access/search.png)

1. **요청 승인** 을 선택합니다.

   ![요청 승인 선택](./media/approve-just-in-time-access/select-approve-requests.png)

1. **Azure 관리형 애플리케이션** 을 선택하고 승인 요청을 선택합니다.

   ![요청 선택](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>다음 단계

JIT 액세스를 사용하여 관리형 애플리케이션을 게시하는 방법에 대한 자세한 내용은 [Azure Managed Applications에서 Just-In-Time 액세스 권한 요청](request-just-in-time-access.md)을 참조하세요.
