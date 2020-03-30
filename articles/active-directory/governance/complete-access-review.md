---
title: 응용 프로그램 & 그룹에 대한 액세스 검토 완료 - Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 구성원 또는 응용 프로그램 액세스에 대한 액세스 검토를 완료하는 방법에 대해 알아봅니다.
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932540"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 및 응용 프로그램에 대한 액세스 검토 완료

관리자는 [그룹 또는 응용 프로그램에 대한 액세스 검토를 만들고](create-access-review.md) 검토자는 액세스 검토를 [수행합니다.](perform-access-review.md) 이 문서에서는 액세스 검토 결과를 보고 결과를 적용하는 방법을 설명합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD Premium P2
- 글로벌 관리자, 사용자 관리자, 보안 관리자 또는 보안 리더

자세한 내용은 [라이선스 요구 사항](access-reviews-overview.md#license-requirements)을 참조하세요.

## <a name="view-an-access-review"></a>액세스 검토 보기

검토자가 검토를 완료할 때 진행 상황을 추적할 수 있습니다.

1. Azure 포털에 로그인하고 [ID 거버넌스 페이지를](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)엽니다.

1. 왼쪽 메뉴에서 리뷰 액세스 를 **클릭합니다.**

1. 목록에서 액세스 검토를 클릭합니다.

    일련의 액세스 검토를 보려면 액세스 검토로 이동하면 예약된 리뷰에서 예정된 문제를 확인할 수 있습니다.

    **개요** 페이지에서 진행 상황을 확인할 수 있습니다. 액세스 권한은 검토가 완료될 때까지 디렉터리에서 변경되지 않습니다.

    ![검토 진행 상황 액세스](./media/complete-access-review/overview-progress.png)

1. 예약된 종료 날짜에 도달하기 전에 액세스 검토를 중지하려면 **중지** 단추를 클릭합니다.

    검토를 중지하면 검토자는 더 이상 응답을 제공할 수 없습니다. 중단한 검토는 다시 시작할 수 없습니다.

1. 액세스 검토에 더 이상 관심이 없는 경우 **삭제** 단추를 클릭하여 삭제할 수 있습니다.

## <a name="apply-the-changes"></a>변경 내용 적용

**리소스에 자동 적용 결과가** 활성화되었고 **완료 설정에서**선택한 항목에 따라 자동 적용이 검토 종료일 이후 또는 검토를 수동으로 중지하면 자동 적용이 실행됩니다.

**리소스에 결과를 자동으로 적용해도** 검토가 활성화되지 않은 경우 **적용을** 클릭하여 변경 내용을 수동으로 적용합니다. 검토에서 사용자의 액세스가 거부된 경우 **적용을**클릭하면 Azure AD가 해당 구성원 또는 응용 프로그램 할당을 제거합니다.

![액세스 검토 변경 적용](./media/complete-access-review/apply-changes.png)

검토 상태는 **완료됨에서** **적용** 과 같은 중간 상태까지 변경되고 마지막으로 **결과가 적용된**상태로 변경됩니다. 거부된 사용자(있는 경우)가 몇 분 내에 그룹 구성원 자격 또는 애플리케이션 할당에서 제거되는 것을 볼 수 있어야 합니다.

구성된 자동 적용 검토를 선택하거나 **적용**을 선택하더라도 온-프레미스 디렉터리 또는 동적 그룹에서 시작된 그룹에는 영향을 주지 않습니다. 온-프레미스에서 시작된 그룹을 변경하려면 결과를 다운로드하고 이러한 변경 내용을 해당 디렉터리의 그룹 표시에 적용합니다.

## <a name="retrieve-the-results"></a>결과 검색

일회성 액세스 검토에 대한 결과를 보려면 **결과** 페이지를 클릭합니다. 사용자의 액세스만 보려면 검색 상자에서 액세스가 검토된 사용자의 표시 이름 또는 사용자 주체 이름을 입력합니다.

![액세스 검토를 위해 결과 검색](./media/complete-access-review/retrieve-results.png)

되풀이되는 활성 액세스 검토의 진행 률을 보려면 **결과** 페이지를 클릭합니다.

되풀이되는 액세스 검토의 완료된 인스턴스 의 결과를 보려면 **검토 기록을**클릭한 다음 인스턴스의 시작 및 종료 날짜에 따라 완료된 액세스 검토 인스턴스 목록에서 특정 인스턴스를 선택합니다. 이 인스턴스의 결과는 **결과** 페이지에서 얻을 수 있습니다.

액세스 검토의 모든 결과를 검색하려면 **다운로드** 단추를 클릭합니다. 결과 CSV 파일은 Excel 또는 UTF-8로 인코딩된 CSV 파일을 열 수 있는 다른 프로그램에서 볼 수 있습니다.

## <a name="remove-users-from-an-access-review"></a>액세스 검토에서 사용자 제거

 기본적으로 삭제된 사용자는 30일 동안 Azure AD에서 삭제된 채로 남아 있지만 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다.  30일 후 해당 사용자가 영구적으로 삭제됩니다.  또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다.  사용자가 영구적으로 삭제됐으므로 추후 해당 사용자에 대한 데이터가 활성 액세스 검토에서 제거됩니다.  삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 액세스 검토를 사용하여 사용자 액세스 관리](manage-user-access-with-access-reviews.md)
- [Azure AD 액세스 검토를 사용하여 게스트 액세스 관리](manage-guest-access-with-access-reviews.md)
- [그룹 또는 응용 프로그램에 대한 액세스 검토 만들기](create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md)
