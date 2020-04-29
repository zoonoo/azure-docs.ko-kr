---
title: Azure AD에서 비활성 사용자 계정을 관리 하는 방법 | Microsoft Docs
description: Azure AD에서 더 이상 사용 되지 않는 사용자 계정을 검색 하 고 처리 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886044"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>방법: Azure AD에서 비활성 사용자 계정 관리

규모가 많은 환경에서는 직원이 조직을 떠날 때 사용자 계정이 항상 삭제 되는 것은 아닙니다. IT 관리자는 보안 위험을 나타내므로 이러한 사용 되지 않는 사용자 계정을 감지 하 고 처리 하려고 합니다.

이 문서에서는 Azure AD에서 사용 되지 않는 사용자 계정을 처리 하는 방법을 설명 합니다. 

## <a name="what-are-inactive-user-accounts"></a>비활성 사용자 계정 이란?

비활성 계정은 리소스에 대 한 액세스 권한을 얻기 위해 조직의 구성원이 더 이상 필요 하지 않은 사용자 계정입니다. 비활성 계정에 대 한 키 식별자 하나는 환경에 로그인 하는 *동안* 에는 사용 되지 않은 것입니다. 비활성 계정은 로그인 활동에 연결 되므로 마지막 로그인의 타임 스탬프를 사용 하 여이를 검색할 수 있습니다. 

이 방법의 문제는 환경의 *경우에는 무엇을* 정의 하는 것입니다. 예를 들어 사용자는 휴가에 있기 때문 *에 환경에*로그인 하지 못할 수 있습니다. 비활성 사용자 계정에 대 한 델타를 정의할 때 사용자 환경에 로그인 하지 않는 모든 합법적인 이유를 고려해 야 합니다. 많은 조직에서 비활성 사용자 계정에 대 한 델타는 90 ~ 180 일 사이입니다. 

마지막으로 성공한 로그인은 리소스에 대 한 액세스를 지속적으로 사용자에 게 제공 하는 잠재적 정보를 제공 합니다.  그룹 멤버 자격 또는 앱 액세스가 여전히 필요한 지 또는 제거할 수 있는지를 확인 하는 데 도움이 될 수 있습니다. 외부 사용자 관리의 경우 외부 사용자가 테 넌 트 내에서 활성 상태 인지 아니면 정리 되어야 하는지 파악할 수 있습니다. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>비활성 사용자 계정을 검색 하는 방법

**Microsoft Graph** API의 **signinactivity** 리소스 유형에 의해 노출 되는 **lastSignInDateTime** 속성을 평가 하 여 비활성 계정을 검색 합니다. 이 속성을 사용 하 여 다음 시나리오에 대 한 솔루션을 구현할 수 있습니다.

- **이름별 사용자**:이 시나리오에서는 lastSignInDate를 평가할 수 있도록 이름으로 특정 사용자를 검색 합니다.`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **날짜별 사용자**:이 시나리오에서는 지정 된 날짜 이전에 lastSignInDateTime를 사용 하 여 사용자 목록을 요청 합니다.`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>기억해야 하는 사항

이 섹션에서는 lastSignInDateTime 속성에 대해 알아야 할 사항을 설명 합니다.

### <a name="how-can-i-access-this-property"></a>이 속성에 액세스 하려면 어떻게 해야 하나요?

**LastSignInDateTime** 속성은 [Microsoft Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)의 [signinactivity 리소스 종류](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) 에 의해 노출 됩니다.   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Get-azureaduser cmdlet을 통해 lastSignInDateTime 속성을 사용할 수 있나요?

아니요.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>속성에 액세스 하는 데 필요한 Azure AD 버전은 무엇 인가요?

모든 버전의 Azure AD에서이 속성에 액세스할 수 있습니다.

### <a name="what-permission-do-i-need-to-read-the-property"></a>속성을 읽는 데 필요한 권한은 무엇 인가요?

이 속성을 읽으려면 다음 권한을 부여 해야 합니다. 

- AuditLogs
- 조직. 모두 읽기  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD가 속성을 언제 업데이트 하나요?

성공적으로 수행 된 각 대화형 로그인은 기본 데이터 저장소를 업데이트 합니다. 일반적으로 성공적인 로그인은 10 분 이내에 관련 로그인 보고서에 표시 됩니다.
 

### <a name="what-does-a-blank-property-value-mean"></a>빈 속성 값은 무엇을 의미 하나요?

LastSignInDateTime 타임 스탬프를 생성 하려면 성공적인 로그인이 필요 합니다. LastSignInDateTime 속성은 새로운 기능 이므로 다음과 같은 경우 lastSignInDateTime 속성의 값을 비워 둘 수 있습니다.

- 이 기능을 출시 하기 전에 사용자의 마지막으로 성공한 로그인이 발생 한 것입니다 (12 월 1 일, 2019).
- 영향을 받는 사용자 계정이 성공적인 로그인에 사용 되지 않았습니다.

## <a name="next-steps"></a>다음 단계

* [인증서와 함께 Azure Active Directory Reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md)
* [감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [로그인 활동 보고서 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
