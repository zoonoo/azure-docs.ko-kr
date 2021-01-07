---
title: Just-In-Time 액세스 승인
description: Azure Managed Applications의 소비자가 관리 되는 응용 프로그램에 대 한 just-in-time 액세스 요청을 승인 하는 방법을 설명 합니다.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651021"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Azure Managed Applications에 대 한 just-in-time 액세스 구성 및 승인

관리 되는 응용 프로그램의 소비자는 관리 되는 리소스 그룹에 대 한 영구 액세스 권한을 게시자에 게 제공 하지 못할 수 있습니다. 관리 되는 리소스에 대 한 액세스 권한을 보다 잘 제어할 수 있도록 Azure Managed Applications는 현재 미리 보기로 제공 되는 JIT (just-in-time) 액세스 라는 기능을 제공 합니다. 이를 통해 게시자가 리소스 그룹에 액세스 하는 시간 및 시기를 승인할 수 있습니다. 게시자는 해당 시간 동안 필요한 업데이트를 수행할 수 있지만, 해당 시간이 지나면 게시자의 액세스가 만료 됩니다.

액세스 권한을 부여 하는 작업 흐름은 다음과 같습니다.

1. 게시자가 marketplace에 관리 되는 응용 프로그램을 추가 하 고 JIT 액세스를 사용할 수 있도록 지정 합니다.

1. 배포 하는 동안 관리 되는 응용 프로그램의 인스턴스에 대 한 JIT 액세스를 사용 하도록 설정 합니다.

1. 배포 후에 JIT 액세스에 대 한 설정을 변경할 수 있습니다.

1. 게시자가 액세스 요청을 보냅니다.

1. 요청을 승인 합니다.

이 문서에서는 소비자가 JIT 액세스를 설정 하 고 요청을 승인 하는 데 사용 하는 작업에 중점을 둔 JIT 액세스를 사용 하 여 관리 되는 응용 프로그램을 게시 하는 방법에 대 한 자세한 내용은 [Azure Managed Applications에서 just-in-time 액세스 요청](request-just-in-time-access.md)을 참조 하세요.

> [!NOTE]
> Just-in-time 액세스를 사용 하려면 [Azure Active Directory P2 라이선스가](../../active-directory/privileged-identity-management/subscription-requirements.md)있어야 합니다.

## <a name="enable-during-deployment"></a>배포 하는 동안 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. JIT를 사용 하도록 설정 된 관리 되는 응용 프로그램의 마켓플레이스 항목을 찾습니다. **만들기**를 선택합니다.

1. 새 관리 되는 응용 프로그램에 대 한 값을 제공 하는 동안 **Jit 구성** 단계에서 관리 되는 응용 프로그램에 대 한 jit 액세스를 활성화 하거나 비활성화할 수 있습니다. **JIT 액세스 사용**에 대해 **예** 를 선택 합니다. Marketplace에서 사용 하도록 설정 된 JIT로 정의 된 관리 되는 응용 프로그램의 경우이 옵션은 기본적으로 선택 됩니다.

   ![액세스 구성](./media/approve-just-in-time-access/configure-jit-access.png)

   배포 하는 동안에만 JIT 액세스를 사용할 수 있습니다. **아니요**를 선택 하면 게시자가 관리 되는 리소스 그룹에 영구적으로 액세스할 수 있습니다. JIT 액세스는 나중에 사용 하도록 설정할 수 없습니다.

1. 기본 승인 설정을 변경 하려면 **JIT 구성 사용자 지정**을 선택 합니다.

   ![액세스 사용자 지정](./media/approve-just-in-time-access/customize-jit-access.png)

   기본적으로 JIT를 사용 하도록 설정 된 관리 되는 응용 프로그램의 설정은 다음과 같습니다.

   * 승인 모드 – 자동
   * 최대 액세스 기간 – 8 시간
   * 승인자 – 없음

   승인 모드가 **자동**으로 설정 되어 있으면 승인자는 각 요청에 대 한 알림을 받으며 요청이 자동으로 승인 됩니다. **수동으로**설정 하면 승인자는 각 요청에 대 한 알림을 수신 하 고 그 중 하나에서 승인 해야 합니다.

   활성화 최대 기간은 게시자가 관리 되는 리소스 그룹에 대 한 액세스를 요청할 수 있는 최대 시간을 지정 합니다.

   승인자 목록은 JIT 액세스 요청을 승인할 수 있는 사용자 Azure Active Directory입니다. 승인자를 추가 하려면 **승인자 추가** 를 선택 하 고 사용자를 검색 합니다.

   설정을 업데이트 한 후 **저장**을 선택 합니다.

## <a name="update-after-deployment"></a>배포 후 업데이트

요청이 승인 되는 방법에 대 한 값을 변경할 수 있습니다. 그러나 배포 중에 JIT 액세스를 사용 하도록 설정 하지 않은 경우에는 나중에 사용 하도록 설정할 수 없습니다.

배포 된 관리 되는 응용 프로그램에 대 한 설정을 변경 하려면:

1. 포털에서 응용 프로그램 관리를 선택 합니다.

1. **JIT 구성** 을 선택 하 고 필요에 따라 설정을 변경 합니다.

   ![액세스 설정 변경](./media/approve-just-in-time-access/change-settings.png)

1. 완료되면 **저장**을 선택합니다.

## <a name="approve-requests"></a>요청 승인

게시자가 액세스를 요청 하는 경우 요청에 대 한 알림이 표시 됩니다. 관리 되는 응용 프로그램을 통해 직접 또는 Azure AD Privileged Identity Management 서비스를 통해 모든 관리 되는 응용 프로그램에서 JIT 액세스 요청을 승인할 수 있습니다. Just-in-time 액세스를 사용 하려면 [Azure Active Directory P2 라이선스가](../../active-directory/privileged-identity-management/subscription-requirements.md)있어야 합니다.

관리 되는 응용 프로그램을 통해 요청을 승인 하려면:

1. 관리 되는 응용 프로그램에 대 한 **JIT 액세스** 를 선택 하 고 **요청 승인**을 선택 합니다.

   ![요청 승인](./media/approve-just-in-time-access/approve-requests.png)
 
1. 승인할 요청을 선택 합니다.

   ![요청 선택](./media/approve-just-in-time-access/select-request.png)

1. 양식에서 승인 이유를 제공 하 고 **승인**을 선택 합니다.

Azure AD Privileged Identity Management를 통해 요청을 승인 하려면:

1. **모든 서비스** 를 선택 하 고 **Azure AD Privileged Identity Management**검색을 시작 합니다. 사용 가능한 옵션에서 선택 합니다.

   ![서비스 검색](./media/approve-just-in-time-access/search.png)

1. **요청 승인**을 선택 합니다.

   ![요청 승인 선택](./media/approve-just-in-time-access/select-approve-requests.png)

1. **Azure 관리 되는 응용 프로그램**을 선택 하 고 승인 요청을 선택 합니다.

   ![요청 선택](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>다음 단계

JIT 액세스를 사용 하 여 관리 되는 응용 프로그램을 게시 하는 방법에 대 한 자세한 내용은 [Azure Managed Applications에서 just-in-time 액세스 요청](request-just-in-time-access.md)을 참조 하세요.
