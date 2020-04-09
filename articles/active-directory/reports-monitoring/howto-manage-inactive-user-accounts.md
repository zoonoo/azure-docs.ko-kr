---
title: Azure AD에서 비활성 사용자 계정을 관리하는 방법 | 마이크로 소프트 문서
description: 사용되지 않는 Azure AD에서 사용자 계정을 검색하고 처리하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886044"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>방법: Azure AD에서 비활성 사용자 계정 관리

대규모 환경에서는 직원이 조직을 떠날 때 사용자 계정이 항상 삭제되는 것은 아닙니다. IT 관리자는 이러한 사용되지 않는 사용자 계정을 검색하고 처리하려고 합니다.

이 문서에서는 Azure AD에서 사용되지 않는 사용자 계정을 처리하는 방법을 설명합니다. 

## <a name="what-are-inactive-user-accounts"></a>비활성 사용자 계정이란 무엇입니까?

비활성 계정은 리소스에 액세스하기 위해 더 이상 조직의 구성원이 필요로 하지 않는 사용자 계정입니다. 비활성 계정의 한 가지 주요 식별자는 사용자 환경에 로그인하는 *데 잠시 동안* 사용되지 않았다는 것입니다. 비활성 계정은 로그인 활동에 연결되어 있으므로 마지막으로 로그인한 타임스탬프를 사용하여 해당 계정을 검색할 수 있습니다. 

이 방법의 과제는 환경의 경우 *잠시 동안* 무엇을 의미하는지 정의하는 것입니다. 예를 들어 사용자가 휴가 중이기 때문에 *잠시 동안*환경에 로그인하지 않을 수 있습니다. 비활성 사용자 계정에 대한 델타를 정의할 때 사용자 환경에 로그인하지 않는 모든 합법적인 이유를 고려해야 합니다. 많은 조직에서 비활성 사용자 계정의 델타는 90일에서 180일 사이입니다. 

마지막으로 성공한 로그인은 리소스에 대한 액세스에 대한 사용자의 지속적인 필요성에 대한 잠재적인 통찰력을 제공합니다.  그룹 구성원 또는 앱 액세스가 여전히 필요한지 또는 제거할 수 있는지 확인하는 데 도움이 될 수 있습니다. 외부 사용자 관리의 경우 외부 사용자가 테넌트 내에서 여전히 활성 상태인지 아니면 정리해야 하는지 이해할 수 있습니다. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>비활성 사용자 계정을 검색하는 방법

**Microsoft 그래프** API의 **signInActivity** 리소스 유형에 의해 노출된 **lastSignInDateTime** 속성을 평가하여 비활성 계정을 검색합니다. 이 속성을 사용하여 다음 시나리오에 대한 솔루션을 구현할 수 있습니다.

- **사용자 이름**: 이 시나리오에서는 이름으로 특정 사용자를 검색하여 lastSignInDate를 평가할 수 있습니다.`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **날짜별 사용자**: 이 시나리오에서는 지정된 날짜 이전에 lastSignInDateTime이 있는 사용자 목록을 요청합니다.`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>알아야 하는 작업

이 섹션은 lastSignInDateTime 속성에 대해 알아야 할 내용을 나열합니다.

### <a name="how-can-i-access-this-property"></a>이 속성에 액세스하려면 어떻게 해야 합니까?

**lastSignInDateTime** 속성은 [Microsoft 그래프 REST API의](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph) [signInActivity 리소스 유형에](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) 의해 노출됩니다.   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>마지막SignInDateTime 속성은 Get-AzureAdUser cmdlet을 통해 사용할 수 있습니까?

아니요.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>속성에 액세스하려면 어떤 Azure AD 버전이 필요합니까?

Azure AD의 모든 버전에서 이 속성에 액세스할 수 있습니다.

### <a name="what-permission-do-i-need-to-read-the-property"></a>숙소를 읽으려면 어떤 권한이 필요합니까?

이 속성을 읽으려면 다음 권한을 부여해야 합니다. 

- 감사로그.읽기.모두
- 조직.읽기.모두  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD는 언제 속성을 업데이트합니까?

성공적인 로그인으로 인해 기본 데이터 저장소가 업데이트됩니다. 일반적으로 성공적인 로그인은 10분 이내에 관련 로그인 보고서에 표시됩니다.
 

### <a name="what-does-a-blank-property-value-mean"></a>빈 속성 값은 무엇을 의미합니까?

lastSignInDateTime 타임 스탬프를 생성하려면 성공적인 로그인이 필요합니다. lastSignInDateTime 속성은 새 기능이므로 다음과 같은 경우 lastSignInDateTime 속성의 값이 비어 있을 수 있습니다.

- 이 기능이 릴리스되기 전에 마지막으로 성공한 로그인(2019년 12월 1일).
- 영향을 받는 사용자 계정은 성공적인 로그인에 사용되지 않았습니다.

## <a name="next-steps"></a>다음 단계

* [인증서와 함께 Azure Active Directory Reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md)
* [감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [로그인 활동 보고서 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
