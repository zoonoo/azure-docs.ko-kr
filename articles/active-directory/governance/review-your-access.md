---
title: 액세스 검토에서 앱 & 그룹에 대 한 액세스 검토-Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 또는 응용 프로그램에 대 한 자신의 액세스를 검토 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422409"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 또는 응용 프로그램에 대 한 액세스를 검토 합니다.

Azure Active Directory (Azure AD)는 기업이 azure ad 액세스 검토 라는 기능을 사용 하 여 Azure AD 및 기타 Microsoft Online Services의 그룹 또는 응용 프로그램에 대 한 액세스를 관리 하는 방법을 간소화 합니다.

이 문서에서는 그룹 또는 응용 프로그램에 대 한 자신의 액세스를 검토 하는 방법을 설명 합니다.

## <a name="open-the-access-review"></a>액세스 검토 열기

액세스 검토를 수행 하는 첫 번째 단계는 액세스 검토를 찾아서 여는 것입니다.

1. Microsoft에서 액세스를 검토 하도록 요청 하는 전자 메일을 찾습니다. 그룹에 대 한 액세스를 검토 하는 예제 메일은 다음과 같습니다.

    ![그룹에 대 한 액세스를 검토 하는 Microsoft의 예제 메일](./media/review-your-access/access-review-email.png)

1. **액세스 검토 링크를** 클릭 하 여 액세스 검토를 엽니다.

전자 메일이 없는 경우 다음 단계를 수행 하 여 보류 중인 액세스 검토를 찾을 수 있습니다.

1. 에서 [https://myapps.microsoft.com](https://myapps.microsoft.com)myapps 포털에 로그인 합니다.

    ![사용 권한이 있는 앱을 나열 하는 MyApps 포털](./media/review-your-access/myapps-access-panel.png)

1. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 클릭하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

1. 페이지의 오른쪽에서 **액세스 검토** 타일을 클릭 하 여 보류 중인 액세스 검토의 목록을 확인 합니다.

    타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

    ![앱 및 그룹에 대 한 보류 중인 액세스 검토 목록](./media/review-your-access/access-reviews-list.png)

1. 수행 하려는 액세스 검토에 대 한 **검토 시작** 링크를 클릭 합니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 액세스를 볼 수 있습니다.

1. 액세스를 검토 하 고 여전히 액세스 권한이 필요한 지 여부를 결정 합니다.

    다른 사용자에 대 한 액세스를 검토 하도록 요청 하는 경우 페이지가 다르게 표시 됩니다. 자세한 내용은 [그룹 또는 응용 프로그램에 대 한 액세스 검토](perform-access-review.md)를 참조 하세요.

    ![그룹에 대 한 액세스가 필요한 지 여부를 묻는 액세스 검토를 엽니다.](./media/review-your-access/perform-access-review.png)

1. 액세스를 유지 하려면 **예** 를 클릭 하 고, 액세스를 제거 하려면 **아니요** 를 클릭 합니다.

1. **예**를 클릭 하면 **이유** 상자에 근거를 지정 해야 할 수 있습니다.

    ![그룹에 대 한 액세스가 필요한 지 여부를 묻는 액세스 검토 완료](./media/review-your-access/perform-access-review-submit.png)

1. **제출**을 클릭합니다.

    선택 항목이 전송 되 고 MyApps 포털로 돌아갑니다.

    응답을 변경 하려면 액세스 검토 페이지를 다시 열고 응답을 업데이트 합니다. 액세스 검토가 종료 될 때까지 언제 든 지 응답을 변경할 수 있습니다.

    > [!NOTE]
    > 더 이상 액세스할 필요가 없는 것으로 표시 되 면 즉시 제거 되지 않습니다. 검토가 종료 되거나 관리자가 검토를 중지 하면 제거 됩니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램에 대 한 액세스 검토 완료](complete-access-review.md)
