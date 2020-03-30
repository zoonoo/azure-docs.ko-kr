---
title: 액세스 검토를 통해 사용자 액세스 관리 - Azure AD
description: Azure Active Directory 액세스 검토를 사용하여 애플리케이션에 대한 그룹 또는 할당의 멤버 자격으로 사용자 액세스를 관리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c307b349144974a4d38f937feeebb98f369d047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932410"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 사용자 액세스 관리

Azure AD(Azure Active Directory)를 사용하면 사용자에게 적절한 액세스 권한이 있는지 쉽게 확인할 수 있습니다. 사용자 또는 의사 결정자에게 액세스 검토에 참여하고 사용자의 액세스를 다시 인증(또는 증명)하도록 요청할 수 있습니다. 검토자는 Azure AD의 제안 사항에 따라 지속적인 액세스에 대한 각 사용자의 요구에 입력을 제공할 수 있습니다. 액세스 검토가 완료되면 더 이상 필요하지 않은 사용자의 액세스를 변경하고 제거할 수 있습니다.

> [!NOTE]
> 게스트 사용자 액세스만 검토하고 모든 종류의 사용자 액세스는 검토하지 않으려면 [액세스 검토를 사용하여 게스트 사용자 액세스 관리](manage-guest-access-with-access-reviews.md)를 참조하세요. 전역 관리자와 같은 관리 역할에서 사용자의 멤버 자격을 검토하려면 [Azure AD Privileged Identity Management에서 액세스 검토를 시작하는 방법](../privileged-identity-management/pim-how-to-start-security-review.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD Premium P2

자세한 내용은 [라이선스 요구 사항](access-reviews-overview.md#license-requirements)을 참조하세요.

## <a name="create-and-perform-an-access-review"></a>액세스 검토 만들기 및 수행

액세스 검토 중인 검토자로 한 명 이상의 사용자를 포함할 수 있습니다.  

1. Azure AD에서 하나 이상의 멤버가 있는 그룹을 선택합니다. 또는 Azure AD에 연결되어 하나 이상의 사용자가 할당된 애플리케이션을 선택합니다. 

2. 각 사용자가 자신의 액세스를 검토하도록 할지 또는 하나 이상의 사용자가 모든 사용자의 액세스를 검토하도록 할지를 결정합니다.

3. 글로벌 관리자 또는 사용자 관리자는 [ID 거버넌스 페이지로](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)이동합니다.

4. 액세스 검토를 만듭니다. 자세한 내용은 [그룹 또는 응용 프로그램의 액세스 검토 만들기를](create-access-review.md)참조하십시오.

5. 액세스 검토를 시작할 때 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각각 Azure AD에서 액세스 패널에 대한 링크가 있는 전자 메일을 수신하며, 여기서 [그룹 또는 응용 프로그램에 대한 액세스를 검토합니다.](perform-access-review.md)

6. 검토자가 입력을 제공하지 않으면 미리 알림을 보내도록 Azure AD에 요청할 수 있습니다. Azure AD에서는 기본적으로 아직 응답하지 않은 검토자에게 종료일 중간에 미리 알림을 자동으로 보냅니다.

7. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [그룹 또는 응용 프로그램의 액세스 검토 완료를](complete-access-review.md)참조하십시오.


## <a name="next-steps"></a>다음 단계

[그룹 또는 응용 프로그램에 대한 액세스 검토 만들기](create-access-review.md)




