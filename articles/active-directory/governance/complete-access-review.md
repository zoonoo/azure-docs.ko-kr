---
title: 그룹 & 응용 프로그램에 대 한 액세스 검토 완료-Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 구성원 또는 응용 프로그램 액세스에 대 한 액세스 검토를 완료 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251ca1dc2b288d3448cdcda0f28335a92c54b431
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078871"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 및 응용 프로그램에 대 한 액세스 검토 완료

관리자는 [그룹 또는 응용 프로그램](create-access-review.md) 에 대 한 액세스 검토를 만들고 검토자 [는 액세스 검토를 수행](perform-access-review.md)합니다. 이 문서에서는 액세스 검토 결과를 확인 하 고 결과를 적용 하는 방법을 설명 합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD Premium P2
- 전역 관리자, 사용자 관리자, 보안 관리자 또는 보안 읽기 권한자

자세한 내용은 [라이선스 요구 사항](access-reviews-overview.md#license-requirements)을 참조하세요.

## <a name="view-an-access-review"></a>액세스 검토 보기

검토자가 검토를 완료할 때 진행률을 추적할 수 있습니다.

1. Azure Portal에 로그인 하 고 [Id 관리 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)를 엽니다.

1. 왼쪽 메뉴에서 **액세스 검토**를 클릭 합니다.

1. 목록에서 액세스 검토를 클릭 합니다.

    일련의 액세스 검토를 보려면 액세스 검토로 이동 하면 예정 된 검토에서 향후 발생 하는 항목을 찾을 수 있습니다.

    **개요** 페이지에서 진행률을 볼 수 있습니다. 액세스 권한은 검토가 완료될 때까지 디렉터리에서 변경되지 않습니다.

    ![액세스 검토 진행률](./media/complete-access-review/overview-progress.png)

1. 예약 된 종료 날짜에 도달 하기 전에 액세스 검토를 중지 하려면 **중지** 단추를 클릭 합니다.

    검토를 중지할 때 검토자는 더 이상 응답을 제공할 수 없습니다. 중단한 검토는 다시 시작할 수 없습니다.

1. 액세스 검토에 더 이상 관심이 없는 경우 **삭제** 단추를 클릭 하 여 삭제할 수 있습니다.

## <a name="apply-the-changes"></a>변경 내용 적용

**리소스에 결과 자동 적용** 이 사용 하도록 설정 되 고 **완료 설정 시**의 선택에 따라 자동 적용이 검토의 종료 날짜 또는 수동으로 검토를 중지 한 후에 실행 됩니다.

검토를 위해 **리소스에 결과 자동 적용** 이 사용 하도록 설정 되지 않은 경우 **적용** 을 클릭 하 여 변경 내용을 수동으로 적용 합니다. 검토에서 사용자의 액세스가 거부 된 경우 **적용**을 클릭 하면 Azure AD에서 구성원 자격 또는 응용 프로그램 할당을 제거 합니다.

![액세스 검토 변경 내용 적용](./media/complete-access-review/apply-changes.png)

검토 상태는 **완료 됨** 에서 **적용** 하는 등의 중간 상태와 마지막으로 적용 된 상태 **결과**에 따라 변경 됩니다. 거부된 사용자(있는 경우)가 몇 분 내에 그룹 구성원 자격 또는 애플리케이션 할당에서 제거되는 것을 볼 수 있어야 합니다.

구성된 자동 적용 검토를 선택하거나 **적용**을 선택하더라도 온-프레미스 디렉터리 또는 동적 그룹에서 시작된 그룹에는 영향을 주지 않습니다. 온-프레미스에서 시작된 그룹을 변경하려면 결과를 다운로드하고 이러한 변경 내용을 해당 디렉터리의 그룹 표시에 적용합니다.

## <a name="retrieve-the-results"></a>결과 검색

일회성 액세스 검토에 대 한 결과를 보려면 **결과** 페이지를 클릭 합니다. 사용자의 액세스만 보려면 검색 상자에 액세스를 검토 한 사용자의 표시 이름 또는 사용자 계정 이름을 입력 합니다.

![액세스 검토에 대 한 결과 검색](./media/complete-access-review/retrieve-results.png)

되풀이 되는 활성 액세스 검토의 진행률을 보려면 **결과** 페이지를 클릭 합니다.

반복 되는 액세스 검토의 완료 된 인스턴스 결과를 보려면 **기록 검토**를 클릭 한 다음 인스턴스의 시작 및 종료 날짜를 기준으로 완료 된 액세스 검토 인스턴스 목록에서 특정 인스턴스를 선택 합니다. 이 인스턴스의 결과는 **결과** 페이지에서 얻을 수 있습니다.

액세스 검토의 모든 결과를 검색 하려면 **다운로드** 단추를 클릭 합니다. 결과 CSV 파일은 Excel 또는 UTF-8로 인코딩된 CSV 파일을 열 수 있는 다른 프로그램에서 볼 수 있습니다.

## <a name="remove-users-from-an-access-review"></a>액세스 검토에서 사용자 제거

 기본적으로 삭제된 사용자는 30일 동안 Azure AD에서 삭제된 채로 남아 있지만 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다.  30일 후 해당 사용자가 영구적으로 삭제됩니다.  또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다.  사용자가 영구적으로 삭제됐으므로 추후 해당 사용자에 대한 데이터가 활성 액세스 검토에서 제거됩니다.  삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 액세스 검토를 사용하여 사용자 액세스 관리](manage-user-access-with-access-reviews.md)
- [Azure AD 액세스 검토를 사용하여 게스트 액세스 관리](manage-guest-access-with-access-reviews.md)
- [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md)
