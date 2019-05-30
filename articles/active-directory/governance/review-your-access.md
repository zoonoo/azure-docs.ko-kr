---
title: 자신에 대 한 그룹 또는 응용 프로그램에서 액세스 검토-Azure Active Directory 액세스 검토 | Microsoft Docs
description: 그룹 또는 응용 프로그램에서 Azure Active Directory 액세스 검토에 대 한 자신의 액세스를 검토 하는 방법에 알아봅니다.
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
ms.openlocfilehash: 6b38422b22885ad96c6681abe58b897155809521
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113196"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>그룹에 직접 액세스를 검토 하거나 Azure AD에서 응용 프로그램 액세스 검토

Azure Active Directory (Azure AD) 기업에서는 Azure AD에서 그룹 또는 응용 프로그램에 대 한 액세스를 관리 방법과 검토 하는 Azure AD 액세스를 호출 하는 기능을 사용 하 여 다른 Microsoft Online Services 간소화 합니다.

이 문서에서는 그룹 또는 응용 프로그램에 대 한 자신의 액세스를 검토 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure AD Premium P2

자세한 내용은 [사용자의 라이선스가 있어야 합니다.?](access-reviews-overview.md#which-users-must-have-licenses)합니다.

## <a name="open-the-access-review"></a>액세스 검토를 엽니다.

액세스 검토를 수행 하는 첫 단계를 찾아 액세스 검토를 엽니다.입니다.

1. 액세스를 검토 하도록 요청 하는 Microsoft에서 전자 메일을 찾습니다. 예제 메일 그룹에 액세스를 검토 하는 다음과 같습니다.

    ![액세스 검토 이메일](./media/review-your-access/access-review-email.png)

1. 클릭 합니다 **액세스 검토** 링크 액세스 검토를 엽니다.

전자 메일에 없는 경우 다음이 단계를 수행 하 여 보류 중인 액세스 검토를 찾을 수 있습니다.

1. MyApps portal에 로그인 [ https://myapps.microsoft.com ](https://myapps.microsoft.com)합니다.

    ![MyApps 포털](./media/review-your-access/myapps-access-panel.png)

1. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 클릭하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

1. 페이지의 오른쪽을 클릭 합니다 **액세스 검토** 보류 중인 액세스 검토의 목록을 보려면 타일을 합니다.

    타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

    ![액세스 검토 목록](./media/review-your-access/access-reviews-list.png)

1. 클릭 합니다 **검토 시작** 수행 하려는 액세스 검토에 대 한 링크입니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 사용자의 액세스를 확인할 수 있습니다.

1. 액세스를 검토 하 고 액세스 해야 하는지 여부를 결정 합니다.

    다른 사용자에 대 한 액세스를 검토 하도록 요청 인 경우 페이지 다르게 보입니다. 자세한 내용은 [그룹 또는 응용 프로그램에 대 한 액세스 검토](perform-access-review.md)합니다.

    ![액세스 검토 수행](./media/review-your-access/perform-access-review.png)

1. 클릭 **예** 액세스를 유지 하거나 클릭 **No** 액세스를 제거 하려면.

1. 클릭 하면 **예**에서 맞춤을 지정 해야 합니다 **이유** 상자입니다.

    ![액세스 검토 수행](./media/review-your-access/perform-access-review-submit.png)

1. **제출**을 클릭합니다.

    선택 항목은 전송 및 MyApps 포털에 반환 합니다.

    응답을 변경 하려는 경우 다시 액세스 검토 페이지를 열고 응답을 업데이트 합니다. 언제 든 지 액세스 검토는 종료 될 때까지 응답을 변경할 수 있습니다.

    > [!NOTE]
    > 더 이상 액세스할 필요가 있다는 표시 된 경우 사용자는 즉시 제거 되지 않습니다. 관리자가 검토를 중지 하는 경우 또는 검토 종료 될 때 제거 됩니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램의 액세스 검토를 완료 합니다.](complete-access-review.md)
