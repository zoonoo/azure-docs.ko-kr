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
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---

# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Azure AD에서 응용 프로그램에 대한 액세스 권한이 있는 그룹 구성원 또는 사용자의 액세스 검토 수행

관리자는 [액세스 검토를 만들어](active-directory-azure-ad-controls-create-access-review.md) 응용 프로그램에 할당된 그룹 구성원이나 사용자의 검토를 요청할 수 있습니다. Azure AD는 검토자에게 액세스를 검토하도록 요청하는 전자 메일을 자동으로 보냅니다. 사용자가 전자 메일을 받지 못한 경우 [액세스를 검토하는 방법](active-directory-azure-ad-controls-perform-access-review.md)의 지침을 보낼 수 있습니다.  액세스 검토 기간이 만료되었거나 관리자가 액세스 검토를 중지한 후에는 이 문서의 단계에 따라 결과를 확인하고 적용합니다.

## <a name="viewing-an-access-review-in-the-azure-portal"></a>Azure Portal에서 액세스 검토 보기

1. [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동하고, **프로그램** 탭으로 변경하고, 액세스 검토 컨트롤이 포함된 프로그램을 선택합니다.
2. 관리를 클릭하고 액세스 검토 컨트롤을 선택합니다.  프로그램에 많은 컨트롤이 있는 경우 특정 유형의 컨트롤만 필터링하거나, 상태별로 정렬하거나, 액세스 검토 컨트롤의 이름 또는 해당 컨트롤을 만든 소유자의 표시 이름을 기준으로 검색할 수 있습니다. 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>아직 완료되지 않은 검토 중지

검토가 예약된 종료일에 아직 도달하지 않은 경우 관리자는 검토를 조기에 완료하기 위해 **중지** 단추를 클릭하여 검토를 중지할 수 있습니다.  만일 이 때까지 검토하지 않은 사용자가 있다면, 검토를 중지한 후에는 그들을 검토할 수 없습니다. 검토를 중단한 후에는 다시 시작할 수 없습니다.

## <a name="applying-the-changes"></a>변경 내용 적용 

종료 날짜에 도달했거나 관리자가 수동으로 중지하여 액세스 검토가 완료되면 **적용** 단추를 통해 그룹 또는 응용 프로그램을 업데이트하여 검토 결과를 구현합니다. 검토에서 사용자의 액세스가 거부된 경우 관리자가 이 단추를 클릭하면 Azure AD에서 구성원 자격 또는 응용 프로그램 할당을 제거합니다. 

[적용] 단추를 클릭하는 경우 온-프레미스 디렉터리 또는 동적 그룹에서 시작된 그룹에는 영향을 주지 않습니다.  온-프레미스에서 시작된 그룹을 변경하려면 결과를 다운로드하고 이러한 변경 내용을 해당 디렉터리의 그룹 표시에 적용합니다.

## <a name="downloading-the-results-of-the-review"></a>검토 결과 다운로드

검토 결과를 검색하려면 [승인] 탭으로 변경하고 **다운로드** 단추를 클릭합니다.  결과적인 CSV 파일은 Excel 또는 CSV 파일을 열 수 있는 다른 프로그램에서 볼 수 있습니다.

## <a name="optional-deleting-a-review"></a>선택 사항: 검토 삭제
더 이상 검토가 필요 없는 경우 검토를 삭제합니다. **삭제** 단추는 Azure AD에서 검토를 제거합니다.

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

