---
title: 그룹 구성원 및 소유자 검색 및 필터링 (미리 보기)-Azure Active Directory | Microsoft Docs
description: Azure Portal에서 그룹 멤버 및 소유자를 검색 하 고 필터링 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92674ed9d39ea9e84d477d19aadbaeda6da6f32c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728268"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Azure Active Directory에서 그룹 및 구성원 검색 (미리 보기)

이 문서에서는 그룹의 멤버 및 소유자를 검색 하는 방법과 Azure Active Directory (Azure AD) 포털에서 그룹 개선 미리 보기의 일부로 검색 필터를 사용 하는 방법을 설명 합니다. 그룹 환경에서는 구성원 및 소유자를 비롯 하 여 쉽고 빠르게 그룹을 관리 하는 데 도움이 되는 다양 한 기능이 향상 되었습니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 미리 보기의 변경 내용은 다음과 같습니다.

- 그룹 이름의 부분 문자열 검색과 같은 새 그룹 검색 기능
- 멤버 및 소유자 목록의 새 필터링 및 정렬 옵션
- 멤버 및 소유자 목록의 새 검색 기능
- 그룹 수가 클 경우 더 정확한 그룹 수

## <a name="enabling-and-managing-the-preview"></a>미리 보기 설정 및 관리

미리 보기에 쉽게 조인할 수 있습니다.

  1. [AZURE AD 포털](https://portal.azure.com)에 로그인 하 고 **그룹**을 선택 합니다.
  2. 그룹-모든 그룹 페이지에서 페이지 맨 위에 있는 배너를 선택 하 여 미리 보기에 참여 합니다.

**모든 그룹** 페이지에서 **미리 보기 정보** 를 선택 하 여 최신 기능과 향상 된 기능을 확인할 수도 있습니다. 미리 보기에 가입한 후에는 향상 된 기능을 포함 하 고 미리 보기의 일부인 모든 그룹 페이지에서 미리 보기 태그를 볼 수 있습니다. 모든 그룹 페이지가이 미리 보기의 일부로 업데이트 되지 않았습니다.

문제가 발생 하는 경우에는 **모든 그룹** 페이지의 맨 위에 있는 배너를 선택 하 여 레거시 환경을 다시 전환할 수 있습니다. 경험을 개선할 수 있도록 피드백에 감사 합니다.

## <a name="group-search-and-sorting"></a>그룹 검색 및 정렬

그룹 목록 검색이 향상 되어 검색 문자열을 입력할 수 있는 경우 검색에서 `startswith` 그룹 이름 목록에 대 한 및 하위 문자열 검색을 자동으로 수행 합니다. 부분 문자열 검색은 단어 단위로만 수행 되며 특수 문자를 포함 하지 않습니다. 부분 문자열 검색은 대/소문자를 구분 합니다.

![모든 그룹 페이지의 새 부분 문자열 검색](./media/groups-members-owners-search/groups-search-preview.png)

예를 들어 "정책" 검색은 이제 "MDM 정책-서 부" 및 "정책 그룹"을 모두 반환 합니다. "New_policy" 라는 그룹이 반환 되지 않습니다.

- 그룹 구성원 목록 에서도 동일한 검색을 수행할 수 있습니다.
- 이제 이름 열 머리글의 오른쪽에 있는 화살표를 사용 하 여 그룹 목록을 이름별로 정렬 하 여 목록을 오름차순 이나 내림차순으로 정렬할 수 있습니다.

## <a name="group-member-search-and-filtering"></a>그룹 구성원 검색 및 필터링

### <a name="search-group-member-and-owner-lists"></a>그룹 구성원 및 소유자 목록 검색

이제 특정 그룹의 구성원을 이름으로 검색 하 고 그룹 소유자 목록 에서도 동일한 검색을 수행할 수 있습니다. 새 환경에서 검색 상자에 문자열을 입력 하면 startswith 검색이 자동으로 수행 됩니다. 예를 들어 "Scott"을 검색 하면 Scott Wilkinson이 반환 됩니다.

![그룹 멤버 및 소유자 목록에서 새 부분 문자열 검색](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>멤버 및 소유자 목록 필터링

이제 검색 외에도 사용자 유형별 멤버 및 소유자 목록을 필터링 할 수 있습니다. 목록의 사용자 유형 열에서 찾을 수 있는 정보입니다. 따라서 구성원 및 게스트를 기준으로 목록을 필터링 하 여 그룹에 게스트가 있는지 확인할 수 있습니다.

### <a name="view-and-manage-membership"></a>멤버 자격 보기 및 관리

이제 특정 그룹의 직접 구성원을 볼 수 있을 뿐 아니라 멤버 페이지 내에서 그룹의 모든 멤버 목록을 볼 수 있습니다. 멤버 목록에는 모든 전이적 멤버를 비롯 한 그룹의 모든 고유 멤버가 포함 됩니다.

직접 멤버 목록 및 모든 멤버 목록을 개별적으로 검색 하 고 필터링 할 수도 있습니다. 모든 멤버 목록을 필터링 해도 직접 멤버 목록에 적용 되는 필터에는 영향을 주지 않습니다.

## <a name="improved-group-member-counts"></a>그룹 구성원 수가 향상 됨

그룹 **개요** 페이지를 개선 하 여 모든 규모의 그룹에 대 한 그룹 구성원 수를 제공 했습니다. 멤버 수가 1000 개를 초과 하는 그룹에 대해서도 볼 수 있습니다. 이제 **개요** 페이지에서 그룹에 대 한 총 직접 구성원 수와 총 멤버 수 (전이적 멤버를 포함 하는 그룹의 모든 고유 구성원)를 볼 수 있습니다.

![그룹 구성원 수의 정확도 높이기](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure AD에서 그룹 작업에 대 한 추가 정보를 제공 합니다.

- [그룹 및 멤버 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [그룹 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [그룹의 사용자에 대한 동적 규칙 관리](groups-create-rule.md)
- [그룹 설정 편집](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
- [그룹을 사용하여 SaaS 앱에 대한 액세스 관리](groups-saasapps.md)
- [PowerShell 명령을 사용하여 그룹 관리](groups-settings-v2-cmdlets.md)
- [Azure Active Directory에 Azure 구독 추가](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
