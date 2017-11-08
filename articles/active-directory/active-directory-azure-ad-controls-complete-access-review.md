---
title: "Azure AD에서 응용 프로그램에 대한 액세스 권한이 있는 그룹 구성원 또는 사용자의 액세스 검토 수행 | Microsoft Docs"
description: "Azure Active Directory에서 응용 프로그램에 대한 액세스 권한이 있는 그룹 구성원 또는 사용자의 액세스 검토를 수행하는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b301ff06c01d51c02f7d7393801b35cd8965403c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Azure AD에서 응용 프로그램에 대한 액세스 권한이 있는 그룹 구성원 또는 사용자의 액세스 검토 수행

관리자는 Azure AD(Azure Active Directory)를 사용하여 응용 프로그램에 할당된 그룹 구성원 또는 사용자에 대한 [액세스 검토를 만들 수](active-directory-azure-ad-controls-create-access-review.md) 있습니다. Azure AD는 검토자에게 액세스를 검토하도록 요청하는 전자 메일을 자동으로 보냅니다. 사용자가 전자 메일을 받지 못한 경우 [액세스 검토](active-directory-azure-ad-controls-perform-access-review.md)의 지침을 보낼 수 있습니다. 액세스 검토 기간이 만료되었거나 관리자가 액세스 검토를 중지한 후에는 이 문서의 단계에 따라 결과를 확인하고 적용합니다.

## <a name="view-an-access-review-in-the-azure-portal"></a>Azure Portal에서 액세스 검토 보기

1. [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동하고, **프로그램**을 선택하고 액세스 검토 컨트롤이 포함된 프로그램을 선택합니다.

2. **관리**를 선택하고 액세스 검토 컨트롤을 선택합니다. 프로그램에 많은 컨트롤이 있는 경우 특정 유형의 컨트롤에 대해 필터링하고 해당 상태별로 정렬할 수 있습니다. 또한 액세스 검토 컨트롤의 이름이나 만든 소유자의 표시 이름으로 검색할 수도 있습니다. 

## <a name="stop-a-review-that-hasnt-finished"></a>완료하지 않은 검토 중지

검토가 예약된 종료 날짜에 도달하지 않은 경우 관리자는 **중지**를 선택하여 검토를 조기에 종료할 수 있습니다. 검토를 중지한 후에는 사용자가 더 이상 검토될 수 없습니다. 중단한 검토는 다시 시작할 수 없습니다.

## <a name="apply-the-changes"></a>변경 내용 적용 

종료 날짜에 도달했거나 관리자가 수동으로 중지했기 때문에 액세스 검토가 완료된 경우 **적용**을 선택합니다. 검토의 결과는 그룹 또는 응용 프로그램을 업데이트하면 구현됩니다. 검토에서 사용자의 액세스가 거부된 경우 관리자가 이 옵션을 선택하면 Azure AD에서 구성원 자격 또는 응용 프로그램 할당을 제거합니다. 

**적용**을 선택해도 온-프레미스 디렉터리 또는 동적 그룹에서 시작된 그룹에는 영향을 주지 않습니다. 온-프레미스에서 시작된 그룹을 변경하려면 결과를 다운로드하고 이러한 변경 내용을 해당 디렉터리의 그룹 표시에 적용합니다.

## <a name="download-the-results-of-the-review"></a>검토 결과 다운로드

검토 결과를 검색하려면 **승인**을 선택한 후 **다운로드**를 선택합니다. 결과적인 CSV 파일은 Excel 또는 CSV 파일을 열 수 있는 다른 프로그램에서 볼 수 있습니다.

## <a name="optional-delete-a-review"></a>선택 사항: 검토 삭제
검토에 더 이상 관심이 없으면 삭제할 수 있습니다. **삭제**를 선택하여 Azure AD에서 검토를 제거합니다.

> [!IMPORTANT]
> 삭제가 수행되기 전에 경고를 표시하지 않으므로 반드시 해당 검토를 삭제하려고 하는지 확인해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계

- [Azure AD 액세스 검토를 사용하여 사용자 액세스 관리](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Azure AD 액세스 검토를 사용하여 게스트 액세스 관리](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Azure AD 액세스 검토용 프로그램 및 컨트롤 관리](active-directory-azure-ad-controls-manage-programs-controls.md)
- [그룹의 멤버 또는 응용 프로그램에 액세스에 대한 액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](active-directory-privileged-identity-management-how-to-start-security-review.md)
