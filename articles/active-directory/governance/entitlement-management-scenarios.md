---
title: 권한 관리의 일반적인 시나리오 - Azure AD
description: Azure Active Directory 권한 관리에서 일반적인 시나리오에 대해 수행해야 하는 상위 수준 단계를 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190554"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리의 일반적인 시나리오

조직에 대한 권한 관리를 구성할 수 있는 방법에는 여러 가지가 있습니다. 그러나 막 시작하는 경우 관리자, 카탈로그 소유자, 액세스 패키지 관리자, 승인자 및 요청자에 대한 일반적인 시나리오를 이해하는 것이 좋습니다.

## <a name="delegate"></a>대리자(delegate)

### <a name="administrator-delegate-management-of-resources"></a>관리자: 리소스 관리 위임

1. [비디오 보기: IT에서 부서 관리자로 의 위임](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [사용자를 카탈로그 작성자 역할에 위임](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>카탈로그 작성자: 리소스 위임 관리

- [새 카탈로그 만들기](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>카탈로그 소유자: 리소스 의 위임 관리

1. [카탈로그에 공동 소유자 추가](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>카탈로그 소유자: 액세스 패키지의 대리자 관리

1. [비디오 보기: 카탈로그 소유자로부터 패키지 관리자에 액세스하기 위한 위임](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [패키지 관리자 역할에 액세스하도록 사용자를 위임합니다.](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>조직의 사용자에 대한 액세스 관리

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>패키지 관리자 액세스: 조직의 직원이 리소스에 대한 액세스를 요청할 수 있도록 허용

1. [새 액세스 패키지 만들기](entitlement-management-access-package-create.md#start-new-access-package)
1. [패키지에 액세스하기 위해 그룹, 팀, 응용 프로그램 또는 SharePoint 사이트 추가](entitlement-management-access-package-create.md#resource-roles)
1. [디렉터리에서 사용자가 액세스를 요청할 수 있도록 요청 정책을 추가합니다.](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [만료 설정 지정](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>요청자: 리소스에 대한 액세스 요청

1. [내 액세스 포털에 로그인](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 액세스 패키지 찾기
1. [액세스 요청](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>승인자: 리소스에 대한 요청 승인

1. [내 액세스 포털에서 열기 요청](entitlement-management-request-approve.md#open-request)
1. [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>요청자: 액세스 권한이 있는 리소스 보기

1. [내 액세스 포털에 로그인](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 활성 액세스 패키지 보기

## <a name="govern-access-for-users-outside-your-organization"></a>조직 외부 의 사용자에 대한 액세스 관리

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>관리자: 외부 파트너 조직과 공동 작업

1. [외부 사용자에 대한 액세스 작동 방식 읽기](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [외부 사용자에 대한 설정 검토](entitlement-management-external-users.md#settings-for-external-users)
1. [외부 조직에 연결 추가](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>패키지 관리자 액세스: 외부 파트너 조직과 공동 작업

1. [새 액세스 패키지 만들기](entitlement-management-access-package-create.md#start-new-access-package)
1. [패키지에 액세스하기 위해 그룹, 팀, 응용 프로그램 또는 SharePoint 사이트 추가](entitlement-management-access-package-resources.md#add-resource-roles)
1. [디렉터리에 없는 사용자가 액세스를 요청할 수 있도록 요청 정책을 추가합니다.](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [만료 설정 지정](entitlement-management-access-package-create.md#lifecycle)
1. [링크 복사하여 액세스 패키지 요청](entitlement-management-access-package-settings.md)
1. 외부 파트너 연락처 파트너에게 링크를 보내 사용자와 공유

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>요청자: 외부 사용자로 리소스에 대한 액세스 요청

1. 연락처에서 받은 액세스 패키지 링크 찾기
1. [내 액세스 포털에 로그인](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [액세스 요청](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>승인자: 리소스에 대한 요청 승인

1. [내 액세스 포털에서 열기 요청](entitlement-management-request-approve.md#open-request)
1. [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>요청자: 이미 액세스할 수 있는 리소스 보기

1. [내 액세스 포털에 로그인](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 활성 액세스 패키지 보기

## <a name="day-to-day-management"></a>일상적인 관리

### <a name="access-package-manager-update-the-resources-for-a-project"></a>패키지 관리자 액세스: 프로젝트의 리소스 업데이트

1. [비디오 보기: 일상적인 관리: 상황이 변경되었습니다.](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 액세스 패키지 열기
1. [그룹, 팀, 응용 프로그램 또는 SharePoint 사이트 추가 또는 제거](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>패키지 관리자 액세스: 프로젝트의 기간 업데이트

1. [비디오 보기: 일상적인 관리: 상황이 변경되었습니다.](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 액세스 패키지 열기
1. [수명 주기 설정 열기](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [만료 설정 업데이트](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>액세스 패키지 관리자: 프로젝트에 대한 액세스가 승인되는 방법 업데이트

1. [비디오 보기: 일상적인 관리: 상황이 변경되었습니다.](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [기존 요청 및 승인 설정 열기](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [승인 설정 업데이트](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>패키지 관리자 액세스: 프로젝트에 대한 사용자 업데이트

1. [비디오 보기: 일상적인 관리: 상황이 변경되었습니다.](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [더 이상 액세스가 필요하지 않은 사용자 제거](entitlement-management-access-package-assignments.md)
1. [기존 요청 및 승인 설정 열기](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [액세스가 필요한 사용자 추가](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>액세스 패키지 관리자: 액세스 패키지에 특정 사용자를 직접 할당

1. [사용자가 다른 수명 주기 설정이 필요한 경우 액세스 패키지에 새 정책을 추가합니다.](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [액세스 패키지에 특정 사용자를 직접 할당](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>과제 및 보고서

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>관리자: 액세스 패키지에 대한 할당이 있는 사용자 보기

1. 액세스 패키지 열기
1. [과제 보기](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [보고서 및 로그 보관](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>관리자: 사용자에게 할당된 리소스 보기

1. [사용자에 대한 액세스 패키지 보기](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [사용자에 대한 리소스 할당 보기](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>프로그래밍 방식 관리

Microsoft Graph를 사용하여 액세스 패키지, 카탈로그, 정책, 요청 및 할당을 관리할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램이 있는 적절한 역할의 사용자는 [권한 관리 API를](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [위임 및 역할](entitlement-management-delegate.md)
- [프로세스 및 이메일 알림 요청](entitlement-management-process.md)
