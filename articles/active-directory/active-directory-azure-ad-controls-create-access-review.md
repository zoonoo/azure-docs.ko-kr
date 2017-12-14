---
title: "Azure AD에서 응용 프로그램에 대한 액세스 권한이 있는 그룹 구성원 또는 사용자의 액세스 검토 만들기 | Microsoft Docs"
description: "그룹 구성원이나 응용 프로그램에 대한 액세스 권한이 있는 사용자의 액세스 검토를 만드는 방법을 알아봅니다."
services: active-directory
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b2f8985f12e17ac69543cfb3a33725f796eedde8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Azure AD에서 그룹 구성원 또는 응용 프로그램 액세스에 대한 액세스 검토 만들기

사용자에게 더 이상 필요하지 않은 권한이 있는 경우 "부실" 역할 할당이 됩니다. 상태 액세스 할당과 관련된 위험을 줄이기 위해 관리자는 Azure AD(Azure Active Directory)를 사용하여 응용 프로그램에 할당된 그룹 구성원 또는 사용자에 대한 액세스 검토를 만들 수 있습니다. 이러한 시나리오에 대한 자세한 내용은 [사용자 액세스 관리](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) 및 [게스트 액세스 관리](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)를 참조하세요. 

## <a name="create-an-access-review"></a>액세스 검토 만들기

1. 전역 관리자인 경우 [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동하고 **프로그램**을 선택합니다.

2. 만들려는 액세스 검토 컨트롤을 포함하는 프로그램을 선택합니다. **기본 프로그램**은 항상 존재하거나 다른 프로그램을 만들 수 있습니다. 예를 들어 준수 이니셔티브 또는 비즈니스 목표 각각에 대한 프로그램을 하나씩 선택할 수 있습니다.

3. 프로그램 내에서 **컨트롤**을 선택한 후 **추가**를 선택하여 컨트롤을 추가합니다.

4. 각 액세스 검토에 이름을 지정합니다. 필요한 경우 각 검토에 설명을 지정합니다. 이름이 검토자에게 표시됩니다.

5. 시작 날짜와 종료 날짜를 설정합니다. 기본적으로 액세스 검토는 만든 날에 시작되고 1개월 후에 종료됩니다. 시작 날짜와 종료 날짜를 변경하여 액세스 검토를 나중에 시작하고 원하는 기간(일 수) 동안 지속할 수 있습니다.

6. 액세스 검토는 응용 프로그램에 할당된 그룹 구성원이거나 사용자일 수 있습니다. 구성원이거나 응용 프로그램에 대한 액세스 권한이 있는 모든 사용자를 검토하는 대신, 액세스 검토 범위를 자세히 지정하여 구성원이거나 앱에 할당된 게스트 사용자로만 제한하여 검토할 수 있습니다.

7. 범위에 있는 모든 사용자를 검토하려면 한 명 이상의 사용자를 선택합니다. 또는 구성원이 자신의 액세스 권한을 검토하도록 할 수도 있습니다. 리소스가 그룹인 경우 그룹 소유자에게 검토하도록 요청할 수 있습니다. 또한 검토자가 액세스를 승인할 때 이유를 제공하도록 요구할 수도 있습니다.

8. 마지막으로 **시작**을 선택합니다.


## <a name="manage-the-access-review"></a>액세스 검토 관리

검토가 시작되면 Azure AD에서 기본적으로 검토자에게 전자 메일을 보냅니다. Azure AD에서 전자 메일을 보내지 않도록 선택한 경우 검토자에게 완료할 때까지 대기 중인 액세스 검토가 있음을 알려야 합니다. [액세스 검토](active-directory-azure-ad-controls-perform-access-review.md) 방법에 대한 지침을 보여 줄 수 있습니다. 게스트가 자신의 액세스를 검토하는 경우 [직접 액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)에 대한 지침을 게스트에게 보여줍니다.

검토자 중 일부가 게스트이면 해당 게스트가 이미 초대를 수락한 경우에만 전자 메일을 통해 알려줍니다.


**액세스 검토** 섹션에서 검토자가 Azure AD 대시보드에서 검토를 수행하는 진행 상황을 추적할 수 있습니다. 액세스 권한은 [검토가 완료](active-directory-azure-ad-controls-complete-access-review.md)될 때까지 디렉터리에서 변경되지 않습니다.

## <a name="next-steps"></a>다음 단계

액세스 검토가 시작되면 Azure AD에서 검토자에게 액세스 검토를 요청하는 전자 메일을 자동으로 보냅니다. 사용자가 전자 메일을 받지 못한 경우 [액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)의 지침을 보낼 수 있습니다. 

액세스 검토 기간이 만료되었거나 관리자가 액세스 검토를 중지한 후에는 [액세스 검토 수행](active-directory-azure-ad-controls-complete-access-review.md)의 단계에 따라 결과를 확인하고 적용합니다.


