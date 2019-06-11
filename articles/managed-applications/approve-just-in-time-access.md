---
title: 구성 및 Azure Managed Applications에 대 한 just in time 액세스 승인
description: Azure Managed Applications의 소비자는 관리 되는 응용 프로그램에 대 한-just-in-time 액세스에 대 한 요청을 승인 하는 방법을 설명 합니다.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481716"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>구성 및 Azure Managed Applications에 대 한 just in time 액세스 승인

관리 되는 응용 프로그램의 소비자로 못할 게시자 제공 편리 하 게 관리 되는 리소스 그룹에 대 한 영구 액세스 합니다. 관리 되는 리소스에 대 한 액세스 권한을 부여 하는 보다 강력한 제어가 부여 하려면 Azure Managed Applications는 현재 미리 보기 상태인-just-in-time (JIT) 액세스 라는 기능을 제공 합니다. 시기를 승인할 수 있습니다 하 고 기간 게시자에 대 한 리소스 그룹에 액세스할 수 있습니다. 게시자는이 시간 동안 필요한 업데이트를 수행할 수 있지만 게시자의 액세스 만료 시간을 통해 경우.

액세스 권한 부여의 작업 흐름은 다음과 같습니다.

1. 게시자는 marketplace에 관리 되는 응용 프로그램을 추가 하 고 JIT 액세스를 사용할 수 있는지를 지정 합니다.

1. 배포 하는 동안 관리 되는 응용 프로그램의 인스턴스에 대 한 JIT 액세스할을 수 있습니다.

1. 배포 후 JIT 액세스에 대 한 설정을 변경할 수 있습니다.

1. 게시자에 대 한 액세스 요청을 보냅니다.

1. 요청을 승인 합니다.

이 문서에서는 JIT 액세스를 사용 하도록 설정 하 고 요청을 승인 하려면 소비자가 수행 하는 작업에 중점을 둡니다. JIT 액세스를 사용 하 여 관리 되는 응용 프로그램을 게시 하는 방법에 대 한 자세한 참조 [Azure Managed Applications의 just in time 액세스 요청](request-just-in-time-access.md)합니다.

> [!NOTE]
> Just in time 액세스를 사용 하려면 해야는 [Azure Active Directory P2 라이선스](../active-directory/privileged-identity-management/subscription-requirements.md)합니다.

## <a name="enable-during-deployment"></a>배포 하는 동안 사용 하도록 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. JIT 활성화를 사용 하 여 관리 되는 응용 프로그램을 marketplace 항목을 찾습니다. **만들기**를 선택합니다.

1. 새 관리 되는 응용 프로그램에 대 한 값을 제공 하는 동안 합니다 **JIT 구성** 단계에서는 관리 되는 응용 프로그램에 대 한 JIT 액세스를 사용 하지 않도록 설정 하거나 설정할 수 있습니다. 선택 **Yes** 에 대 한 **JIT 액세스를 사용 하도록 설정**합니다. 이 옵션은 marketplace에서 사용 하도록 설정 하는 JIT를 사용 하 여 정의 하는 관리 되는 응용 프로그램에 대해 기본적으로 선택 됩니다.

   ![액세스 구성](./media/approve-just-in-time-access/configure-jit-access.png)

   만 JIT 액세스를 배포 하는 동안 사용할 수 있습니다. 선택 하는 경우 **No**, 게시자는 관리 되는 리소스 그룹에 영구 액세스를 가져옵니다. JIT 액세스는 나중에 사용할 수 없습니다.

1. 변경 하려면 기본 승인 설정을 선택 합니다 **JIT 구성 사용자 지정**합니다.

   ![액세스 사용자 지정](./media/approve-just-in-time-access/customize-jit-access.png)

   기본적으로 사용 하도록 설정 하는 JIT 사용 하 여 관리 되는 응용 프로그램을 다음 설정은 다음과 같습니다.

   * 자동 승인 모드
   * 최대 액세스 기간 – 8 시간
   * 승인자 – 없음

   승인 모드로 설정 되 면 **자동**, 승인자 각 요청에 대 한 알림을 수신 하지만 요청은 자동으로 승인 합니다. 로 설정 하면 **수동**승인자 각 요청에 대 한 알림을 수신 하 고 그 중 하나는를 승인 해야 합니다.

   최대 활성화 기간 최대 기간 게시자 요청할 수 있습니다 관리 되는 리소스 그룹에 대 한 액세스를 지정 합니다.

   승인자 목록은 JIT 액세스 요청을 승인할 수 있는 Azure Active Directory 사용자입니다. 승인자를 추가 하려면 선택 **승인자 추가** 및 사용자 검색 합니다.

   설정을 업데이트 한 후 선택 **저장할**합니다.

## <a name="update-after-deployment"></a>배포 후 업데이트

요청은 승인 하는 방법에 대 한 값을 변경할 수 있습니다. 그러나 JIT 액세스를 배포 하는 동안 활성화 하지 않은 경우 사용할 수 없습니다 것 이상.

배포 된 관리 되는 응용 프로그램에 대 한 설정을 변경 하려면:

1. 포털에서 관리 응용 프로그램을 선택 합니다.

1. 선택 **JIT 구성** 필요에 따라 설정을 변경 합니다.

   ![액세스 설정 변경](./media/approve-just-in-time-access/change-settings.png)

1. 완료되면 **저장**을 선택합니다.

## <a name="approve-requests"></a>요청 승인

게시자에 액세스를 요청 하면 요청의 알림을 받을 있습니다. 관리 되는 응용 프로그램을 통해 직접 또는 Azure AD Privileged Identity Management 서비스를 통해 관리 되는 모든 응용 프로그램에서 JIT 액세스 요청을 승인할 수 있습니다. Just in time 액세스를 사용 하려면 해야는 [Azure Active Directory P2 라이선스](../active-directory/privileged-identity-management/subscription-requirements.md)합니다.

관리 되는 응용 프로그램을 통해 요청을 승인 합니다.

1. 선택 **JIT 액세스** 선택한 관리 되는 응용 프로그램에 대 한 **승인 요청**합니다.

   ![요청 승인](./media/approve-just-in-time-access/approve-requests.png)
 
1. 승인할 요청을 선택 합니다.

   ![요청 선택](./media/approve-just-in-time-access/select-request.png)

1. 폼에서 선택한 승인에 대 한 이유를 제공 **승인**합니다.

Azure AD Privileged Identity Management 통해 요청을 승인 합니다.

1. 선택 **모든 서비스** 에 대 한 검색을 시작 하 고 **Azure AD Privileged Identity Management**합니다. 사용 가능한 옵션 중에서 선택 합니다.

   ![서비스에 대 한 검색](./media/approve-just-in-time-access/search.png)

1. 선택 **요청을 승인**합니다.

   ![승인 요청 선택](./media/approve-just-in-time-access/select-approve-requests.png)

1. 선택 **응용 프로그램을 관리 되는 Azure**, 승인 요청을 선택 합니다.

   ![요청 선택](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>다음 단계

JIT 액세스를 사용 하 여 관리 되는 응용 프로그램을 게시 하는 방법에 대 한 자세한 참조 [Azure Managed Applications의 just in time 액세스 요청](request-just-in-time-access.md)합니다.
