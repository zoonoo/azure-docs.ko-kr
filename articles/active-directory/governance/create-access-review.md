---
title: 그룹 또는 응용 프로그램-Azure Active Directory의 액세스 검토 만들기 | Microsoft Docs
description: Azure Active Directory 액세스 검토에서 그룹 구성원 또는 응용 프로그램 액세스의 액세스 검토를 만드는 방법에 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef72f1649c3f3e0af7fba53b2e8dbcee49d4b59
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734573"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>그룹의 액세스 검토를 만들거나 Azure AD에서 응용 프로그램 액세스 검토

직원 및 게스트용 그룹과 애플리케이션에 대한 액세스는 시간이 지나면 변합니다. 관리자는 Azure AD(Azure Active Directory)를 사용하여 그룹 구성원 또는 애플리케이션 액세스에 대한 액세스 검토를 만들면 상태 액세스 할당과 관련된 위험을 줄일 수 있습니다. 일상적으로 액세스를 검토해야 하는 경우 되풀이 액세스 검토를 만들 수도 있습니다. 이러한 시나리오에 대한 자세한 내용은 [사용자 액세스 관리](manage-user-access-with-access-reviews.md) 및 [게스트 액세스 관리](manage-guest-access-with-access-reviews.md)를 참조하세요.

이 문서에는 그룹 구성원 또는 응용 프로그램 액세스에 대 한 하나 이상의 액세스 검토를 만드는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure AD Premium P2
- 전역 관리자 또는 사용자 관리자

자세한 내용은 [사용자의 라이선스가 있어야 합니다.?](access-reviews-overview.md#which-users-must-have-licenses)합니다.

## <a name="create-one-or-more-access-reviews"></a>하나 이상의 액세스 검토 만들기

1. 포털을 열고 Azure에 로그인 합니다 [Identity 거 버 넌 스 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)합니다.

1. 왼쪽된 메뉴에서 클릭 **액세스 검토**합니다.

1. **새 액세스 검토**를 클릭하여 새 액세스 검토를 만듭니다.

    ![액세스 검토 - 컨트롤](./media/create-access-review/access-reviews.png)

1. 액세스 검토의 이름을 지정합니다. 필요한 경우 검토에 설명을 지정합니다. 이름 및 설명이 검토자에게 표시됩니다.

    ![액세스 검토 만들기 - 이름 및 설명 검토](./media/create-access-review/name-description.png)

1. **시작 날짜**를 설정합니다. 기본적으로 액세스 검토는 한 번 발생하고, 만들어진 시간에 시작되고, 1개월 후에 종료됩니다. 시작 날짜와 종료 날짜를 변경하여 액세스 검토를 나중에 시작하고 원하는 기간(일 수) 동안 지속할 수 있습니다.

    ![액세스 검토 만들기 - 시작 및 종료 날짜](./media/create-access-review/start-end-dates.png)

1. 되풀이 되는 액세스 검토를 변경 합니다 **빈도** 에서 설정 **한 번** 하 **매주**, **월간**,  **분기별** 나 **매년**합니다. 사용 합니다 **기간** 슬라이더 또는 텍스트 상자를 검토자가 입력에 대 한 되풀이의 각 검토 될 기간 (일) 열을 정의할 수 있습니다. 예를 들어 검토가 겹치는 상황을 방지하기 위해 월별 검토에 대해 설정할 수 있는 최대 기간은 27일입니다.

1. **종료** 설정을 사용하여 되풀이 액세스 검토 시리즈를 종료하는 방법을 지정합니다. 이 시리즈는 세 가지 방법으로 종료할 수 있습니다. 무기한으로, 특정 날짜까지, 또는 정의된 되풀이 횟수가 완료된 이후에 검토를 시작하도록 연속적으로 실행됩니다. 있습니다, 다른 사용자 관리자 또는 다른 전역 관리자가 후에 중지할 수 계열 생성 날짜를 변경 하 여 **설정을**해당 날짜에 종료 되도록 합니다.

1. 에 **사용자** 섹션, 액세스 검토 적용 되는 사용자 지정 합니다. 액세스 검토는 애플리케이션에 할당된 그룹 구성원이거나 사용자일 수 있습니다. 구성원이거나 애플리케이션에 대한 액세스 권한이 있는 모든 사용자를 검토하는 대신, 액세스 검토 범위를 자세히 지정하여 구성원이거나 앱에 할당된 게스트 사용자로만 제한하여 검토할 수 있습니다.

    ![액세스 검토 만들기 - 사용자](./media/create-access-review/users.png)

1. 에 **그룹** 섹션, 하나 이상의 그룹의 구성원 자격을 검토 하려는 선택 합니다.

    > [!NOTE]
    > 둘 이상의 그룹을 선택 하는 여러 액세스 검토를 만듭니다. 예를 들어 5 개의 그룹을 선택 하면 5 별도 액세스 검토를 만들어 됩니다.
    
    ![액세스 검토를 만드는-그룹 선택](./media/create-access-review/select-group.png)

1. 에 **응용 프로그램** 섹션 (선택한 경우 **응용 프로그램에 할당 된** 8 단계에서), 응용 프로그램에 대 한 액세스를 검토 하려는 선택 합니다.

    > [!NOTE]
    > 둘 이상의 응용 프로그램을 선택 하면 여러 액세스 검토 생성 됩니다. 예를 들어 5 응용 프로그램 선택 5 별도 액세스 검토를 만들어 됩니다.
    
    ![응용 프로그램 선택-액세스 검토 만들기](./media/create-access-review/select-application.png)

1. **검토자** 섹션에서 범위 내 모든 사용자를 검토할 한 명 이상의 사용자를 선택합니다. 또는 구성원이 자신의 액세스 권한을 검토하도록 할 수도 있습니다. 리소스가 그룹인 경우 그룹 소유자에게 검토하도록 요청할 수 있습니다. 또한 검토자가 액세스를 승인할 때 이유를 제공하도록 요구할 수도 있습니다.

    ![액세스 검토 만들기 - 검토자](./media/create-access-review/reviewers.png)

1. **프로그램** 섹션에서 사용하려는 프로그램을 선택합니다. **기본 프로그램**은 항상 존재합니다.

    ![액세스 검토 만들기 - 프로그램](./media/create-access-review/programs.png)

    액세스 검토를 프로그램으로 구성하여 다양한 용도로 추적하고 수집하는 방법을 간소화할 수 있습니다. 각 액세스 검토를 프로그램에 연결할 수 있습니다. 그런 다음, 감사자에게 제공할 보고서를 준비하면 특정 이니셔티브 범위의 액세스 검토에 초점을 맞출 수 있습니다. 프로그램 및 액세스 검토 결과 전역 관리자, 사용자 관리자, 보안 관리자 또는 보안 읽기 권한자 역할의 사용자에 게 표시 됩니다.

    Go 액세스가 검토 페이지에 프로그램의 목록을 보려면 **프로그램**합니다. 전역 관리자 또는 사용자 관리자 역할의 경우 추가 프로그램을 만들 수 있습니다. 예를 들어 준수 이니셔티브 또는 비즈니스 목표 각각에 대한 프로그램을 하나씩 선택할 수 있습니다. 프로그램이 더 이상 필요하지 않고 연결된 컨트롤이 없는 경우 해당 프로그램을 삭제할 수 있습니다.

### <a name="upon-completion-settings"></a>완료 시 설정

1. 검토가 완료된 후 수행할 작업을 지정하려면 **완료 시 설정** 섹션을 확장합니다.

    ![완료 시 설정](./media/create-access-review/upon-completion-settings.png)

1. 거부된 사용자에 대한 액세스를 자동으로 제거하려면 **결과를 리소스에 자동 적용**을 **사용**으로 설정합니다. 검토가 완료될 때 결과를 수동으로 적용하려면 스위치를 **사용 안 함**으로 설정합니다.

1. **검토자가 응답하지 않는 경우** 목록을 사용하여 검토 기간 내에 검토자가 검토하지 않은 사용자를 어떻게 할 것인지 지정합니다. 이 설정은 검토자가 수동으로 검토한 사용자에게 영향을 주지 않습니다. 최종 검토자의 결정이 [거부]이면 사용자의 액세스 권한이 제거됩니다.

    - **변경 없음** - 사용자의 액세스 권한을 그대로 유지
    - **액세스 권한 제거** - 사용자의 액세스 권한을 제거
    - **액세스 권한 승인** - 사용자의 액세스 권한을 승인
    - **권장 작업 수행** - 사용자의 지속적인 액세스를 거부할 것인지 아니면 승인할 것인지에 대한 시스템의 권장 사항을 수용

### <a name="advanced-settings"></a>고급 설정

1. 추가 설정을 지정하려면 **고급 설정** 섹션을 확장합니다.

    ![고급 설정](./media/create-access-review/advanced-settings.png)

1. **권장 사항 표시**를 **사용**으로 설정하면 사용자의 액세스 정보에 따라 검토자에게 시스템 권장 사항이 표시됩니다.

1. **승인 시 이유 필요**를 **사용**으로 설정하면 검토자가 승인 이유를 입력해야 합니다.

1. **메일 알림**을 **사용**으로 설정하면 Azure AD는 액세스 검토가 시작될 때 검토자에게 이메일 알림을 보내고 검토가 완료될 때 관리자에게 이메일 알림을 보냅니다.

1. **미리 알림**을 **사용**으로 설정하면 Azure AD는 검토를 완료하지 않은 검토자에게 진행 중인 액세스 검토에 대한 미리 알림을 보냅니다.

    Azure AD에서는 기본적으로 아직 응답하지 않은 검토자에게 종료일 중간에 미리 알림을 자동으로 보냅니다.

## <a name="start-the-access-review"></a>액세스 검토 시작

액세스 검토에 대한 설정을 지정했으면 **시작**을 클릭합니다. 액세스 검토는 해당 상태 표시기를 사용 하 여 목록에 표시 됩니다.

![액세스 검토 목록](./media/create-access-review/access-reviews-list.png)

검토가 시작되면 Azure AD에서 기본적으로 검토자에게 전자 메일을 보냅니다. Azure AD에서 전자 메일을 보내지 않도록 선택한 경우 검토자에게 완료할 때까지 대기 중인 액세스 검토가 있음을 알려야 합니다. 방법에 대 한 지침을 표시할 수 있습니다 [그룹 또는 응용 프로그램에 대 한 액세스 검토](perform-access-review.md)합니다. 게스트가 자신의 액세스 검토를 표시 하는 방법에 대 한 지침 [그룹 또는 응용 프로그램에 직접 액세스를 검토](review-your-access.md)합니다.

게스트 검토자로 할당 하 고는 초대를 수락 하지 않은 경우는 받지 전자 메일 액세스 검토에서 때문 invite 검토 하기 전에 먼저 동의 해야 합니다.

## <a name="create-reviews-via-apis"></a>API를 통해 검토 만들기

API를 사용하여 액세스 검토를 만들 수도 있습니다. Azure Portal에서 그룹 및 애플리케이션 사용자의 액세스 검토를 관리하기 위해 하는 일은 Microsoft Graph API를 사용하여 할 수도 있습니다. 자세한 내용은 참조는 [API 참조의 Azure AD 액세스 검토](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta)합니다. 코드 샘플을 보려면 [Microsoft Graph를 통해 Azure AD 액세스를 검색 하는 예제 검토](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)합니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램에 대 한 액세스를 검토 합니다.](perform-access-review.md)
- [그룹 또는 응용 프로그램에 직접 액세스를 검토](review-your-access.md)
- [그룹 또는 응용 프로그램의 액세스 검토를 완료 합니다.](complete-access-review.md)
