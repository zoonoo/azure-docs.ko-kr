---
title: Azure AD 액세스 검토란? | Microsoft Docs
description: Azure Active Directory 액세스 검토를 사용하면 조직의 거버넌스, 위험 관리 및 규정 준수 이니셔티브를 충족할 수 있도록 그룹 멤버 자격 및 애플리케이션 액세스를 제어할 수 있습니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/19/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 338ae760aae90131a7353f990e1014aa714faa0f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177906"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD 액세스 검토란?

Azure AD(Azure Active Directory) 액세스 검토를 사용하면 조직에서 그룹 멤버 자격을 효율적으로 관리하고 엔터프라이즈 애플리케이션에 액세스하고 권한 있는 역할을 할당할 수 있습니다. 사용자의 액세스는 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

다음은 액세스 검토의 빠른 개요를 제공하는 비디오입니다.

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>액세스 검토가 중요한 이유는 무엇인가요?

Azure AD를 사용하면 조직 내에서 내부적으로, 또한 파트너와 같은 외부 조직의 사용자와 공동 작업을 수행할 수 있습니다. 사용자는 그룹에 가입하고, 게스트를 초대하고, 클라우드 앱에 연결하고, 해당 회사 또는 개인 디바이스에서 원격으로 작업할 수 있습니다. 셀프 서비스 기능을 활용할 수 있는 편의성으로 인해 더 나은 액세스 관리 기능이 필요하게 되었습니다.

- 새로운 직원이 참여했을 때 생산성을 높일 수 있는 적절한 액세스 권한을 어떻게 보장할 수 있나요?
- 사람들이 팀을 이동하거나 회사를 떠날 때, 특히 게스트가 포함된 경우 해당 사용자의 이전 액세스 권한이 제거되었는지 어떻게 보장할 수 있나요?
- 과도한 액세스 권한은 액세스에 대한 제어가 부족함을 나타내므로 결과 및 손상에 대한 감사로 이어질 수 있습니다.
- 해당 리소스에 대한 액세스 권한이 있는 사용자를 정기적으로 검토하도록 리소스 소유자와 사전에 협의해야 합니다.

## <a name="when-to-use-access-reviews"></a>언제 액세스 검토를 사용하나요?

- **권한 있는 역할에 사용자가 너무 많은 경우:** 관리 액세스 권한이 있는 사용자 수, 글로벌 관리자의 수, 관리 작업을 수행하도록 할당된 후 제거되지 않은 초대된 게스트 또는 파트너가 있는지 여부를 확인하는 것이 좋습니다. [Azure AD PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md) 환경에서 글로벌 관리자와 같은 [Azure AD 디렉터리 역할](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) 또는 사용자 액세스 관리자와 같은 [Azure 리소스 역할](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)의 역할 할당 사용자를 다시 인증할 수 있습니다.
- **자동화를 사용할 수 없는 경우:** 보안 그룹 또는 Office 365 그룹에서 동적 멤버 자격에 대한 규칙을 만들 수 있습니다. 하지만, HR 데이터가 Azure AD에 없거나 사용자가 그룹을 떠난 뒤에도 대체자를 교육시키기 위해 액세스 권한이 필요한 경우 어찌하나요? 해당 그룹에서 검토를 만들어서 계속 액세스 권한이 필요한 사용자가 계속 액세스할 수 있도록 합니다.
- **새로운 목적을 위해 그룹이 사용되는 경우:** Azure AD에 동기화될 그룹이 있거나 판매 팀 그룹의 모든 사용자에 대해 애플리케이션 Salesforce를 사용하도록 설정하려는 경우 그룹이 다른 위험 콘텐츠에 사용되기 전에 그룹 소유자에게 다른 그룹 멤버 자격을 검토하도록 요청하는 것이 유용합니다.
- **비즈니스 중요 데이터 액세스:** 특정 리소스의 경우 IT 외부 사용자에게 정기적으로 로그오프하도록 요청하고 감사 목적을 위해 액세스가 필요한 이유에 대한 근거를 제공해야 할 수 있습니다.
- **정책의 예외 목록을 유지 관리하려면:** 이상적인 환경에서 모든 사용자는 조직의 리소스에 대한 액세스를 보호하는 액세스 정책을 따릅니다. 그러나 경우에 따라 예외가 있는 비즈니스 사례가 있습니다. IT 관리자로서, 이 작업을 관리하고, 정책 예외 감시를 방지하며, 이러한 예외를 정기적으로 검토했음에 대한 증명을 감사자에게 제공할 수 있습니다.
- **그룹 소유자에게 해당 그룹에 게스트가 여전히 필요한지 확인하도록 요청합니다:** 초대된 게스트가 아닌 일부 온-프레미스 IAM을 사용하여 직원 액세스를 자동화할 수 있습니다. 그룹이 게스트에게 비즈니스상 중요한 콘텐츠에 대한 액세스 권한을 부여한 경우 게스트에게 액세스에 대한 합법적인 비즈니스 요구가 여전히 있는지를 확인할 책임은 그룹 소유자에게 있습니다.
- **검토를 주기적으로 반복되도록 함:** 주간, 월별, 분기별 또는 연간과 같이 설정된 빈도에 따라 사용자의 액세스 검토 반복을 설정할 수 있으며, 검토자는 각 검토 시작 시 알림을 받게 됩니다. 검토자는 친숙한 인터페이스를 사용하거나 스마트 권장 사항을 활용하여 액세스를 승인하거나 거부할 수 있습니다.

## <a name="where-do-you-create-reviews"></a>검토는 어디에서 만드나요?

검토할 항목에 따라 Azure AD 액세스 검토, Azure AD 엔터프라이즈 앱(미리 보기) 또는 Azure AD PIM에서 액세스 검토를 만들게 됩니다.

| 사용자의 액세스 권한 | 검토자는 다음을 수행할 수 있음 | 검토 생성 위치 | 검토자 경험 |
| --- | --- | --- | --- |
| 보안 그룹 멤버</br>사무실 그룹 멤버 | 지정된 검토자</br>그룹 소유자</br>자체 검토 | Azure AD 액세스 검토</br>Azure AD 그룹 | 액세스 패널 |
| 연결된 앱에 할당됨 | 지정된 검토자</br>자체 검토 | Azure AD 액세스 검토</br>Azure AD 엔터프라이즈 앱(미리 보기) | 액세스 패널 |
| Azure AD 디렉터리 역할 | 지정된 검토자</br>자체 검토 | Azure AD PIM | Azure portal |
| Azure 리소스 역할 | 지정된 검토자</br>자체 검토 | Azure AD PIM | Azure portal |

## <a name="prerequisites"></a>필수 조건

액세스 검토를 사용하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스

자세한 내용은 [방법: Azure Active Directory Premium에 가입](../fundamentals/active-directory-get-started-premium.md) 또는 [Enterprise Mobility + Security E5 평가판](https://aka.ms/emse5trial)을 참조하세요.

## <a name="get-started-with-access-reviews"></a>액세스 검토로 시작

액세스 검토를 만들고 수행하는 데 관해 자세히 알아보려면 다음 짧은 데모를 시청하세요.

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

조직에서 액세스 검토를 배포할 준비가 경우 비디오의 단계에 따라 등록하고, 관리자를 교육하고, 첫 번째 액세스 검토를 만드세요!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>액세스 검토 사용하도록 설정

액세스 검토를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 액세스 검토를 사용할 [Azure Portal](https://portal.azure.com)에 글로벌 관리자 또는 사용자 계정 관리자로 로그인합니다.

1. **모든 서비스**를 클릭하고 액세스 검토 서비스를 찾습니다.

    ![모든 서비스 - 액세스 검토](./media/access-reviews-overview/all-services-access-reviews.png)

1. **액세스 검토**를 클릭합니다.

    ![액세스 검토 등록](./media/access-reviews-overview/onboard-button.png)

1. 탐색 목록에서 **등록**을 클릭하여 **액세스 검토 등록** 페이지를 엽니다.

    ![액세스 검토 등록](./media/access-reviews-overview/onboard-access-reviews.png)

1. **만들기**를 클릭하여 현재 디렉터리에서 액세스 검토를 사용하도록 설정합니다. 다음 번에 액세스 검토를 시작할 때 해당 옵션을 사용할 수 있습니다.

    ![액세스 검토를 사용하도록 설정](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>다음 단계

- [그룹의 멤버 또는 애플리케이션에 액세스에 대한 액세스 검토 만들기](create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Azure AD 액세스 검토를 사용하여 액세스 검토 수행](perform-access-review.md)
- [Azure AD에서 애플리케이션에 대한 액세스 권한이 있는 그룹 멤버 또는 사용자의 액세스 검토 완료](complete-access-review.md)
