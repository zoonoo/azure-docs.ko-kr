---
title: 액세스 검토에서 응용 프로그램 & 그룹에 대 한 액세스 검토-Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 구성원 또는 응용 프로그램 액세스에 대 한 액세스를 검토 하는 방법에 대해 알아봅니다.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8393a1e4719137aa09233d0a87dabdf81d8ccb59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422447"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 및 응용 프로그램에 대 한 액세스 검토

Azure ad (Azure Active Directory)는 기업에서 azure ad 액세스 검토 라는 기능을 사용 하 여 Azure AD 및 기타 Microsoft Online Services의 그룹 및 응용 프로그램에 대 한 액세스를 관리 하는 방법을 간소화 합니다.

이 문서에서는 지정 된 검토자가 그룹 구성원 또는 응용 프로그램에 대 한 액세스 권한이 있는 사용자에 대 한 액세스 검토를 수행 하는 방법을 설명 합니다.

## <a name="open-the-access-review"></a>액세스 검토 열기

액세스 검토를 수행 하는 첫 번째 단계는 액세스 검토를 찾아서 여는 것입니다.

1. Microsoft에서 액세스를 검토 하도록 요청 하는 전자 메일을 찾습니다. 그룹에 대 한 액세스를 검토 하는 예제 메일은 다음과 같습니다.

    ![그룹에 대 한 액세스를 검토 하기 위한 Microsoft의 예제 메일](./media/perform-access-review/access-review-email.png)

1. **검토 시작** 링크를 클릭 하 여 액세스 검토를 엽니다.

전자 메일이 없는 경우 다음 단계를 수행 하 여 보류 중인 액세스 검토를 찾을 수 있습니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에서 myapps 포털에 로그인 합니다.

    ![사용 권한이 있는 앱을 나열 하는 MyApps 포털](./media/perform-access-review/myapps-access-panel.png)

1. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 클릭하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

1. **액세스 검토** 타일을 클릭 하 여 보류 중인 액세스 검토의 목록을 확인 합니다.

    타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

    ![앱 및 그룹에 대 한 보류 중인 액세스 검토 목록](./media/perform-access-review/access-reviews-list.png)

1. 수행 하려는 액세스 검토에 대 한 **검토 시작** 링크를 클릭 합니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 검토 해야 하는 사용자의 이름이 표시 됩니다.

사용자가 자신의 액세스를 검토 하는 것으로 요청 하는 경우 페이지가 다르게 표시 됩니다. 자세한 내용은 [그룹 또는 응용 프로그램에 대 한 직접 액세스 검토](review-your-access.md)를 참조 하세요.

![검토 해야 하는 사용자를 나열 하는 액세스 검토 열기](./media/perform-access-review/perform-access-review.png)

액세스를 승인 하거나 거부할 수 있는 두 가지 방법이 있습니다.

- 하나 이상의 사용자에 대 한 액세스를 승인 하거나 거부할 수 있습니다.
- 가장 쉽고 빠른 방법으로 시스템 권장 사항을 적용할 수 있습니다.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대 한 액세스 승인 또는 거부

1. 사용자의 목록을 검토 하 여 계속 해 서 액세스를 승인 하거나 거부할지를 결정 합니다.

1. 단일 사용자에 대 한 액세스를 승인 하거나 거부 하려면 해당 행을 클릭 하 여 수행할 작업을 지정 하는 창을 엽니다. 여러 사용자에 대 한 액세스를 승인 하거나 거부 하려면 사용자 옆에 확인 표시를 추가 하 고 **X 사용자 검토** 단추를 클릭 하 여 수행할 작업을 지정 하는 창을 엽니다.

1. **승인** 또는 **거부**를 클릭 합니다. 확실 하지 않은 경우 **알 수 없음**을 클릭 하면 됩니다. 이렇게 하면 사용자가 액세스를 유지 관리 하지만 선택 항목이 감사 로그에 반영 됩니다.

    ![승인, 거부 및 인식 되지 않는 옵션을 포함 하는 작업 창](./media/perform-access-review/approve-deny.png)

1. 필요한 경우 **이유** 상자에 이유를 입력 합니다.

    액세스 검토 관리자는 계속 액세스 또는 그룹 멤버 자격을 승인 하는 이유를 제공 해야 할 수 있습니다.

1. 수행할 작업을 지정 했으면 **저장**을 클릭 합니다.

    응답을 변경 하려면 행을 선택 하 고 응답을 업데이트 합니다. 예를 들어 이전에 거부 된 사용자를 승인 하거나 이전에 승인 된 사용자를 거부할 수 있습니다. 액세스 검토가 종료 될 때까지 언제 든 지 응답을 변경할 수 있습니다.

    검토자가 여러 개인 경우 마지막 제출 된 응답이 기록 됩니다. 관리자가 Alice와 Bob의 두 검토자를 지정 하는 예를 살펴보겠습니다. Alice는 액세스 검토를 먼저 열고 액세스를 승인 합니다. 검토가 끝나기 전에 Bob은 액세스 검토를 열고 액세스를 거부 합니다. 마지막 거부 응답은 기록 됩니다.

    > [!NOTE]
    > 사용자의 액세스가 거부 되 면 즉시 제거 되지 않습니다. 이러한 설정은 검토가 종료 되거나 관리자가 검토를 중지할 때 제거 됩니다.

### <a name="approve-or-deny-access-based-on-recommendations"></a>권장 사항에 따라 액세스 승인 또는 거부

액세스 검토를 보다 쉽고 빠르게 수행할 수 있도록 한 번의 클릭으로 수락할 수 있는 권장 사항도 제공 합니다. 권장 사항은 사용자의 로그인 활동을 기반으로 생성 됩니다.

1. 페이지 아래쪽의 파란색 표시줄에서 **권장 구성 적용**을 클릭 합니다.

    ![권장 구성 적용 단추를 표시 하는 액세스 검토 목록 열기](./media/perform-access-review/accept-recommendations.png)

    권장 작업에 대 한 요약이 표시 됩니다.

    ![권장 작업의 요약을 표시 하는 창](./media/perform-access-review/accept-recommendations-summary.png)

1. **확인** 을 클릭 하 여 권장 사항을 적용 합니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램에 대 한 액세스 검토 완료](complete-access-review.md)
