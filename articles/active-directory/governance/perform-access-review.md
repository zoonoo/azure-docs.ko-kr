---
title: 그룹 또는 액세스 검토-Azure Active Directory의에서 응용 프로그램에 대 한 액세스 검토 | Microsoft Docs
description: 그룹 구성원의 액세스 또는 Azure Active Directory 액세스 검토에 대 한 응용 프로그램 액세스를 검토 하는 방법에 알아봅니다.
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
ms.openlocfilehash: b6f73d3bf5e502a758dd46561059c15a2970d9b6
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471821"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>그룹에 대 한 액세스를 검토 하거나 Azure AD에서 응용 프로그램 액세스 검토

Azure Active Directory (Azure AD) 기업에서는 Azure AD에서 그룹 및 응용 프로그램에 대 한 액세스를 관리 방법과 검토 하는 Azure AD 액세스를 호출 하는 기능을 사용 하 여 다른 Microsoft Online Services 간소화 합니다.

이 문서에서는 지정 된 검토자 그룹 또는 응용 프로그램에 대 한 액세스를 사용 하 여 사용자의 멤버에 대 한 액세스 검토를 수행 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure AD Premium P2

자세한 내용은 [사용자의 라이선스가 있어야 합니다.?](access-reviews-overview.md#which-users-must-have-licenses)합니다.

## <a name="open-the-access-review"></a>액세스 검토를 엽니다.

액세스 검토를 수행 하는 첫 단계를 찾아 액세스 검토를 엽니다.입니다.

1. 액세스를 검토 하도록 요청 하는 Microsoft에서 전자 메일을 찾습니다. 예제 메일 그룹에 대 한 액세스를 검토 하는 다음과 같습니다.

    ![예제 메일 그룹에 대 한 액세스를 검토 하기 위해 Microsoft에서](./media/perform-access-review/access-review-email.png)

1. 클릭 합니다 **검토 시작** 링크 액세스 검토를 엽니다.

전자 메일에 없는 경우 다음이 단계를 수행 하 여 보류 중인 액세스 검토를 찾을 수 있습니다.

1. MyApps portal에 로그인 [ https://myapps.microsoft.com ](https://myapps.microsoft.com)합니다.

    ![MyApps 포털 앱을 수 있는 권한이 나열](./media/perform-access-review/myapps-access-panel.png)

1. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 클릭하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

1. 클릭 합니다 **액세스 검토** 보류 중인 액세스 검토의 목록을 보려면 타일을 합니다.

    타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

    ![앱 및 그룹에 대 한 보류 중인 액세스 검토 목록](./media/perform-access-review/access-reviews-list.png)

1. 클릭 합니다 **검토 시작** 수행 하려는 액세스 검토에 대 한 링크입니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 검토 해야 하는 사용자의 이름이 표시 됩니다.

요청이 자신의 액세스 검토를 인 페이지 다르게 보입니다. 자세한 내용은 [그룹 또는 응용 프로그램에 직접 액세스를 검토](review-your-access.md)합니다.

![검토 해야 하는 사용자를 나열 하는 개방형 액세스 검토](./media/perform-access-review/perform-access-review.png)

두 가지는를 승인 하거나 액세스를 거부할 수 있습니다.

- 승인 하거나 하나 이상의 사용자에 대 한 액세스를 거부할 수 있습니다 또는
- 가장 빠르고 가장 쉬운 방법인 시스템 권장 사항은 적용할 수 있습니다.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대 한 액세스 승인 또는 거부

1. 가 계속된 액세스 승인 또는 거부 여부를 결정 하는 사용자의 목록을 검토 합니다.

1. 를 승인 하거나 단일 사용자에 대 한 액세스를 거부 하려면 창에서 수행할 동작을 지정 하려면 행을 클릭 합니다. 를 승인 하거나 여러 사용자에 대 한 액세스를 거부 하려면 사용자 옆의 확인 표시를 추가 하 고 클릭 합니다 **검토 X 사용자** 수행할 동작을 지정 하는 창을 여는 단추입니다.

1. 클릭 **승인할** 하거나 **거부**합니다. 확실 하지 않은 경우 클릭 수 있습니다 **알지**합니다. 그렇게 하면 사용자에 대 한 액세스를 유지 하지만 선택 감사 로그에 반영 됩니다.

    ![승인, 거부를 포함 하는 작업 창 옵션 알지](./media/perform-access-review/approve-deny.png)

1. 필요한 경우에 이유를 입력 합니다 **이유** 상자입니다.

    관리자 액세스 검토에 지속적인된 액세스 또는 그룹 멤버 자격을 승인 하는 것에 대 한 이유를 제공 해야 합니다.

1. 수행할 동작을 지정 했으면, 클릭 **저장할**합니다.

    응답을 변경 하려는 경우 행을 선택 하 고 응답을 업데이트 합니다. 예를 들어, 이전에 거부 된 사용자를 승인 하거나 이전에 승인 된 사용자를 거부할 수 있습니다. 언제 든 지 액세스 검토는 종료 될 때까지 응답을 변경할 수 있습니다.

    여러 명의 검토자가 있으면 마지막으로 제출 된 내용이 기록 됩니다. 관리자가 두 검토자 – Alice와 bob은 지정 하는 위치는 예제를 고려해 보세요. Alice가 액세스 검토를 먼저 열리고 액세스를 승인 합니다. 검토를 종료 되기 전에 Bob 액세스 검토 열리고 액세스를 거부 합니다. 마지막 응답은 기록 된 항목 거부 합니다.

    > [!NOTE]
    > 사용자 액세스를 거부 되 면 즉시 제거 되지 않습니다. 관리자가 검토를 중지 하는 경우 또는 검토 종료 될 때 제거 됩니다.

### <a name="approve-or-deny-access-based-on-recommendations"></a>권장 사항에 따라 액세스 승인 또는 거부

쉽고 빠르게 액세스 검토를 확인, 제공 받아들일 수 있는 권장 사항을 한 번의 클릭을 사용 하 여. 권장 사항은 사용자의 로그인 활동을 기반으로 생성 됩니다.

1. 페이지의 맨 아래에 파란색 막대를 클릭 **권장 사항을 따르는**합니다.

    ![Accept 권장 사항 단추를 보여 주는 나열 하는 개방형 액세스 검토](./media/perform-access-review/accept-recommendations.png)

    권장 되는 작업의 요약이 표시 됩니다.

    ![권장 되는 작업의 요약을 표시 하는 창](./media/perform-access-review/accept-recommendations-summary.png)

1. 클릭 **확인** 권장 사항을 수락 하도록 합니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램의 액세스 검토를 완료 합니다.](complete-access-review.md)
