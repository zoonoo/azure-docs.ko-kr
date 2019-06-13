---
title: 그룹 또는 응용 프로그램-Azure Active Directory의 액세스 검토 완료 | Microsoft Docs
description: 그룹 구성원 또는 Azure Active Directory 액세스 검토에 대 한 응용 프로그램 액세스의 액세스 검토를 완료 하는 방법을 알아봅니다.
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
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec3909ffbb624284f999360140b7454098643062
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473358"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>그룹의 액세스 검토를 완료 하거나 Azure AD에서 응용 프로그램 액세스 검토

관리자로 서 있습니다 [그룹 또는 응용 프로그램의 액세스 검토를 만드는](create-access-review.md) 및 검토자 [액세스 검토를 수행할](perform-access-review.md)합니다. 이 문서에는 액세스 검토의 결과 확인 하 고 결과 적용 하는 방법을 설명 합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>필수 조건

- Azure AD Premium P2
- 전역 관리자, 사용자 관리자, 보안 관리자 또는 보안 읽기 권한자

자세한 내용은 [사용자의 라이선스가 있어야 합니다.?](access-reviews-overview.md#which-users-must-have-licenses)합니다.

## <a name="view-an-access-review"></a>액세스 검토 보기

검토자가 검토를 완료 진행률을 추적할 수 있습니다.

1. 포털을 열고 Azure에 로그인 합니다 [Identity 거 버 넌 스 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)합니다.

1. 왼쪽된 메뉴에서 클릭 **액세스 검토**합니다.

1. 목록에서 액세스 검토를 클릭 합니다.

    일련의 액세스를 보려는 검토, 액세스 검토로 이동 하 고 예약 된 검토의 예정 된 항목을 확인할 수 있습니다.

    에 **개요** 페이지에서 진행률을 볼 수 있습니다. 액세스 권한이 없습니다. 검토를 완료 될 때까지 디렉터리에서 변경 됩니다.

    ![액세스 검토 진행률](./media/complete-access-review/overview-progress.png)

1. 액세스 검토를 중지 하려는 경우 예약된 된 종료 날짜에 도달 하기 전에,를 클릭 합니다 **중지** 단추입니다.

    검토를 중지 하면, 검토자가 더 이상 응답할 수 없게 됩니다. 중단한 검토는 다시 시작할 수 없습니다.

1. 더 이상 액세스 검토에 관심이 있다면 클릭 하 여 삭제할 수 없습니다 합니다 **삭제** 단추입니다.

## <a name="apply-the-changes"></a>변경 내용 적용

경우 **자동 리소스에 결과 적용** 가 사용 되 고에서 선택한 항목 기반 **설정 완료 시**, 자동-가 적용 검토를 종료 날짜 또는 검토를 수동으로 중지 후에 실행 합니다.

경우 **자동 리소스에 결과 적용** 되지 않은 검토를 사용 하도록 설정, 클릭 **적용** 변경 내용을 수동으로 적용할 합니다. 클릭할 때 사용자의 액세스 검토에서 거부 된 경우 **적용**, 해당 멤버 자격 또는 응용 프로그램 할당을 제거 하는 Azure AD입니다.

![변경 내용에 대 한 액세스 검토 적용](./media/complete-access-review/apply-changes.png)

검토 상태가 변경 됩니다 **완료 됨** 와 같은 중간 상태 **적용** 마지막 상태로 **적용 하는 결과**합니다. 거부된 사용자(있는 경우)가 몇 분 내에 그룹 구성원 자격 또는 애플리케이션 할당에서 제거되는 것을 볼 수 있어야 합니다.

구성된 자동 적용 검토를 선택하거나 **적용**을 선택하더라도 온-프레미스 디렉터리 또는 동적 그룹에서 시작된 그룹에는 영향을 주지 않습니다. 온-프레미스에서 시작된 그룹을 변경하려면 결과를 다운로드하고 이러한 변경 내용을 해당 디렉터리의 그룹 표시에 적용합니다.

## <a name="retrieve-the-results"></a>결과 검색

일회성 액세스 검토에 대 한 결과 보려면 클릭 합니다 **결과** 페이지입니다. 검색 상자에서 방금 사용자의 액세스를 보려는 표시 이름 또는 액세스를 검토 하는 사용자의 사용자 계정 이름 입력 합니다.

![액세스 검토에 대 한 결과 검색 합니다.](./media/complete-access-review/retrieve-results.png)

반복 되는 활성 액세스 검토의 진행률을 보려면 클릭 합니다 **결과** 페이지입니다.

반복 되는 액세스 검토를 완료 된 인스턴스의 결과 보려면 클릭 **기록을 검토**를 선택 하 고, 완료 된 액세스 검토 인스턴스 목록에서 특정 인스턴스 인스턴스의 시작 및 종료 날짜 기준입니다. 이 인스턴스의 결과 얻을 수 있습니다 합니다 **결과** 페이지입니다.

액세스 검토의 모든 결과 검색 하려면 클릭 합니다 **다운로드** 단추입니다. 결과 CSV 파일은 Excel 또는 UTF-8로 인코딩된 CSV 파일을 열 수 있는 다른 프로그램에서 볼 수 있습니다.

## <a name="remove-users-from-an-access-review"></a>액세스 검토에서 사용자 제거

 기본적으로 삭제된 사용자는 30일 동안 Azure AD에서 삭제된 채로 남아 있지만 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다.  30일 후 해당 사용자가 영구적으로 삭제됩니다.  또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다.  사용자가 영구적으로 삭제됐으므로 추후 해당 사용자에 대한 데이터가 활성 액세스 검토에서 제거됩니다.  삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 액세스 검토를 사용하여 사용자 액세스 관리](manage-user-access-with-access-reviews.md)
- [Azure AD 액세스 검토를 사용하여 게스트 액세스 관리](manage-guest-access-with-access-reviews.md)
- [그룹 또는 응용 프로그램의 액세스 검토 만들기](create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md)
