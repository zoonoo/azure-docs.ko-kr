---
title: 액세스 검토에서 응용 프로그램 & 그룹에 대한 액세스 검토 - Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 구성원 또는 응용 프로그램 액세스의 액세스를 검토하는 방법에 대해 알아봅니다.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128462"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 및 응용 프로그램에 대한 액세스 검토

Azure Active Directory(Azure AD)는 Azure AD 및 기타 Microsoft 온라인 서비스에서 Azure AD 액세스 검토라는 기능을 사용하여 기업에서 그룹 및 응용 프로그램에 대한 액세스를 관리하는 방법을 간소화합니다.

이 문서에서는 지정된 검토자가 그룹의 구성원 또는 응용 프로그램에 액세스할 수 있는 사용자에 대한 액세스 검토를 수행하는 방법을 설명합니다.

## <a name="open-the-access-review"></a>액세스 검토 열기

액세스 검토를 수행하는 첫 번째 단계는 액세스 검토를 찾아 여는 것입니다.

1. 액세스 검토를 요청하는 Microsoft의 전자 메일을 찾습니다. 다음은 그룹에 대한 액세스를 검토하는 예제 전자 메일입니다.

    ![그룹에 대한 액세스를 검토하기 위해 Microsoft의 전자 메일 예제](./media/perform-access-review/access-review-email.png)

1. 검토 **시작** 링크를 클릭하여 액세스 검토를 엽니다.

이메일이 없는 경우 다음 단계에 따라 보류 중인 액세스 리뷰를 찾을 수 있습니다.

1. 에서 MyApps 포털에 [https://myapps.microsoft.com](https://myapps.microsoft.com)로그인합니다.

    ![권한이 있는 MyApps 포털 목록 앱](./media/perform-access-review/myapps-access-panel.png)

1. 페이지의 오른쪽 위 모서리에 있는 사용자 기호를 클릭하면 이름과 기본 조직이 표시됩니다. 둘 이상의 조직이 나열되는 경우 액세스 검토를 요청한 조직을 선택합니다.

1. 액세스 **검토** 타일을 클릭하여 보류 중인 액세스 검토 목록을 확인합니다.

    타일이 표시되지 않은 경우 해당 조직에 대해 수행할 액세스 검토가 없으므로 이 시점에서는 어떤 작업도 필요하지 않습니다.

    ![앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/perform-access-review/access-reviews-list.png)

1. 수행하려는 액세스 **검토의** 검토 시작 링크를 클릭합니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 검토해야 하는 사용자의 이름이 표시됩니다.

사용자 고유의 액세스를 검토하도록 요청하는 경우 페이지가 다르게 표시됩니다. 자세한 내용은 [그룹 또는 응용 프로그램에 대한 액세스 검토를](review-your-access.md)참조하십시오.

![검토해야 하는 사용자를 나열하는 액세스 검토 열기](./media/perform-access-review/perform-access-review.png)

액세스를 승인하거나 거부할 수 있는 방법에는 두 가지가 있습니다.

- 하나 이상의 사용자에 대한 액세스를 승인하거나 거부할 수 있습니다.
- 가장 쉽고 빠른 시스템 권장 사항을 받아들일 수 있습니다.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대한 액세스 승인 또는 거부

1. 사용자 목록을 검토하여 지속적인 액세스를 승인할지 또는 거부할지 결정합니다.

1. 단일 사용자에 대한 액세스를 승인하거나 거부하려면 행을 클릭하여 창을 열어 취할 작업을 지정합니다. 여러 사용자에 대한 액세스를 승인하거나 거부하려면 사용자 옆에 확인 표시를 추가한 다음 **X 사용자 검토** 단추를 클릭하여 창을 열어 취할 작업을 지정합니다.

1. **승인** 또는 **거부를**클릭합니다. 확실하지 않은 경우 알 수 **없는**을 클릭할 수 있습니다. 이렇게 하면 사용자가 액세스를 유지하지만 선택 영역은 감사 로그에 반영됩니다.

    ![승인, 거부 및 모르는 옵션이 포함된 작업 창](./media/perform-access-review/approve-deny.png)

1. 필요한 경우 **이유** 상자에 이유를 입력합니다.

    액세스 검토 관리자는 지속적인 액세스 또는 그룹 구성원 자격을 승인하는 이유를 제공해야 할 수 있습니다.

1. 수행할 작업을 지정한 후 **저장을**클릭합니다.

    응답을 변경하려면 행을 선택하고 응답을 업데이트합니다. 예를 들어 이전에 거부된 사용자를 승인하거나 이전에 승인된 사용자를 거부할 수 있습니다. 액세스 검토가 종료될 때까지 언제든지 응답을 변경할 수 있습니다.

    검토자가 여러 개인 경우 마지막으로 제출된 응답이 기록됩니다. 관리자가 Alice와 Bob라는 두 명의 검토자를 지정하는 예를 생각해 보십시오. Alice는 액세스 검토를 먼저 열고 액세스를 승인합니다. 검토가 끝나기 전에 Bob은 액세스 검토를 열고 액세스를 거부합니다. 마지막 거부 응답은 기록된 것입니다.

    > [!NOTE]
    > 사용자가 액세스가 거부되면 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 중지하면 제거됩니다.

### <a name="approve-or-deny-access-based-on-recommendations"></a>권장 사항에 따라 액세스 승인 또는 거부

액세스 리뷰를 보다 쉽고 빠르게 할 수 있도록 한 번의 클릭으로 받아들일 수 있는 권장 사항도 제공합니다. 권장 사항은 사용자의 로그인 활동에 따라 생성됩니다.

1. 페이지 하단의 파란색 막대에서 권장 **사항 수락을**클릭합니다.

    ![권장 사항 수락 단추를 표시하는 액세스 검토 목록 열기](./media/perform-access-review/accept-recommendations.png)

    권장 작업의 요약이 표시됩니다.

    ![권장 작업의 요약을 표시하는 창](./media/perform-access-review/accept-recommendations-summary.png)

1. 권장 사항을 수락하려면 **확인을** 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램에 대한 액세스 검토 완료](complete-access-review.md)
