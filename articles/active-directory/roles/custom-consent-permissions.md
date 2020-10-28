---
title: Azure Active Directory의 사용자 지정 역할에 대한 앱 동의 권한 | Microsoft Docs
description: Azure Portal, PowerShell 또는 Graph API에서 사용자 지정 Azure AD 역할에 대한 앱 동의 권한을 미리 봅니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 10/06/2020
ms.author: curtand
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: 372ae994c62767032f1bfb069093f66d738c23b3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378962"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory의 사용자 지정 역할에 대한 앱 동의 권한

이 문서에는 Azure AD(Azure Active Directory)의 사용자 지정 역할 정의에 대해 현재 사용할 수 있는 앱 동의 권한이 포함되어 있습니다. 이 문서에서는 앱 동의 및 권한과 관련된 몇 가지 일반적인 시나리오에 필요한 권한을 확인할 수 있습니다.

## <a name="required-license-plan"></a>필요한 라이선스 계획

이 기능을 사용하려면 Azure AD 조직에 대한 Azure AD Premium P1 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [Free, Basic 및 Premium 버전의 일반적으로 사용할 수 있는 기능 비교](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

## <a name="app-consent-permissions"></a>앱 동의 권한

이 문서에 나열된 권한을 사용하여 앱 동의 정책 및 동의를 앱에 부여할 수 있는 권한을 관리합니다.

> [!NOTE]
> Azure AD 관리 포털에서는 아직 이 문서에 나열된 권한을 사용자 지정 디렉터리 역할 정의에 추가하는 것을 지원하지 않습니다. 이 문서에 나열된 권한이 있는 [사용자 지정 디렉터리 역할을 만들려면 Azure AD PowerShell을 사용해야 합니다](custom-create.md#create-a-role-using-powershell).

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>자신을 대신하여 앱에 위임된 권한 부여(사용자 동의)

앱 동의 정책에 따라 사용자가 자신을 대신하여 애플리케이션에 대한 동의(사용자 동의)를 허용합니다.

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

여기서 `{id}`를 이 권한이 활성화되기 위해 충족해야 하는 조건을 설정하는 [앱 동의 정책](../manage-apps/manage-app-consent-policies.md)의 ID로 바꿉니다.

예를 들어 ID가 `microsoft-user-default-low`인 기본 제공 앱 동의 정책에 따라 사용자가 자신을 대신하여 동의를 부여하도록 허용하려면 `...managePermissionGrantsForSelf.microsoft-user-default-low` 권한을 사용합니다.

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>모두를 대신하여 앱에 권한 부여(관리자 동의)

위임된 권한 및 애플리케이션 권한(앱 역할) 모두에 대해 테넌트 전체 관리자 동의를 앱에 위임하려면 다음과 같습니다.

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

여기서 `{id}`를 이 권한을 사용하기 위해 충족해야 하는 조건을 설정하는 [앱 동의 정책](../manage-apps/manage-app-consent-policies.md)의 ID로 바꿉니다.

예를 들어 ID가 `low-risk-any-app`인 사용자 지정 [앱 동의 정책](../manage-apps/manage-app-consent-policies.md)에 따라 역할 담당자가 테넌트 전체 관리자 동의를 앱에 부여하도록 허용하려면 `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` 권한을 사용합니다.

### <a name="managing-app-consent-policies"></a>앱 동의 정책 관리

[앱 동의 정책](../manage-apps/manage-app-consent-policies.md)의 만들기, 업데이트 및 삭제를 위임합니다.

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>전체 권한 목록

사용 권한 | 설명
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | `{id}` 앱 동의 정책에 따라 자신을 대신하여 앱에 동의할 수 있는 권한(사용자 동의)을 부여합니다.
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | `{id}` 애플리케이션 동의 정책에 따라 모두를 대신하여 앱에 동의할 수 있는 권한(테넌트 전체 관리자 동의)을 부여합니다.
microsoft.directory/permissionGrantPolicies/standard/read | 앱 동의 정책을 읽을 수 있는 권한을 부여합니다.
microsoft.directory/permissionGrantPolicies/basic/update | 기존 앱 동의 정책에 대한 기본 속성을 업데이트할 수 있는 권한을 부여합니다.
microsoft.directory/permissionGrantPolicies/create | 앱 동의 정책을 만들 수 있는 권한을 부여합니다.
microsoft.directory/permissionGrantPolicies/delete | 앱 동의 정책을 삭제할 수 있는 권한을 부여합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal, Azure AD PowerShell 및 Graph API를 사용하여 사용자 지정 역할 만들기](custom-create.md)
- [사용자 지정 역할의 할당 보기](../roles/view-assignments.md)
