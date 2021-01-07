---
title: Azure Active Directory의 사용자 지정 역할에 대한 앱 권한 | Microsoft Docs
description: Azure Portal, PowerShell 또는 Graph API에서 사용자 지정 Azure AD 역할에 대한 엔터프라이즈 앱 권한을 미리 봅니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39d9cf9ed87c8a8f45cb2a6239292562035d31e3
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379641"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory의 사용자 지정 역할에 대한 엔터프라이즈 애플리케이션 권한

이 문서에는 Azure AD(Azure Active Directory)의 사용자 지정 역할 정의에 대해 현재 사용 가능한 엔터프라이즈 애플리케이션 권한이 포함되어 있습니다. 이 문서에서는 몇 가지 일반적인 시나리오에 대한 권한 목록과 엔터프라이즈 앱 권한의 전체 목록을 확인할 수 있습니다. 애플리케이션 프록시 권한은 현재 이 릴리스에서 롤아웃되지 않습니다.

## <a name="required-license-plan"></a>필요한 라이선스 계획

이 기능을 사용하려면 Azure AD 조직에 대한 Azure AD Premium P1 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [Free, Basic 및 Premium 버전의 일반적으로 사용할 수 있는 기능 비교](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

## <a name="enterprise-application-permissions"></a>엔터프라이즈 애플리케이션 권한

이러한 권한을 사용하는 방법에 대한 자세한 내용은 [엔터프라이즈 앱을 관리하는 사용자 지정 역할 할당](custom-enterprise-apps.md)을 참조하세요.

### <a name="assigning-users-or-groups-to-an-application"></a>애플리케이션에 사용자 또는 그룹 할당

SAML 기반 Single Sign-On 애플리케이션에 액세스할 수 있는 사용자 및 그룹의 할당을 위임합니다. 필요한 사용 권한

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>갤러리 애플리케이션 만들기

특히 ServiceNow, F5, Salesforce와 같은 Azure AD Gallery 애플리케이션의 만들기를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applicationTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>기본 SAML URL 구성

SAML 기반 Single Sign-On 애플리케이션에 대한 기본 SAML 구성의 업데이트 및 읽기를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

### <a name="rolling-over-or-creating-signing-certs"></a>서명 인증서 롤오버 또는 만들기

SAML 기반 Single Sign-On 애플리케이션에 대한 서명 인증서 관리를 위임합니다. 필요한 권한은 다음과 같습니다.

microsoft.directory/applications/credentials/update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>만료되는 로그인 인증서 알림 이메일 주소 업데이트

SAML 기반 Single Sign-On 애플리케이션에 대해 만료되는 로그인 인증서 알림 이메일 주소 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>SAML 토큰 서명 및 로그인 알고리즘 관리

SAML 기반 Single Sign-On 애플리케이션에 대한 SAML 토큰 서명 및 로그인 알고리즘의 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

### <a name="manage-user-attributes-and-claims"></a>사용자 특성 및 클레임 관리

SAML 기반 Single Sign-On 애플리케이션에 대한 사용자 특성 및 클레임의 만들기, 삭제 및 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>앱 프로비저닝 권한

UI를 통해 작업, 스키마 또는 자격 증명 관리와 같은 쓰기 작업을 수행하려면 프로비저닝 페이지를 볼 수 있는 읽기 권한도 필요합니다.

현재 범위를 모든 사용자/그룹 또는 할당된 사용자/그룹으로 설정하려면 synchronizationJob 및 synchronizationCredentials 권한이 모두 필요합니다.

### <a name="turn-on-or-restart-provisioning-jobs"></a>프로비저닝 작업 설정 또는 다시 시작

프로비저닝 작업을 설정, 해제 및 다시 시작하는 기능을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

### <a name="configure-the-provisioning-schema"></a>프로비저닝 스키마 구성  

특성 매핑에 대한 업데이트를 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>애플리케이션 개체와 연결된 프로비저닝 설정 읽기

개체와 연결된 프로비저닝 설정을 읽는 기능을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/applications/synchronization/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>서비스 주체와 연결된 프로비저닝 설정 읽기

서비스 주체와 연결된 프로비저닝 설정을 읽는 기능을 위임합니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronization/standard/read

### <a name="authorize-application-access-for-provisioning"></a>프로비저닝을 위한 애플리케이션 액세스 권한 부여  

프로비저닝을 위해 애플리케이션 액세스 권한을 부여하는 기능을 위임합니다. 예제 입력 Oauth 전달자 토큰입니다. 필요한 권한은 다음과 같습니다.

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="full-list-of-permissions"></a>전체 권한 목록

사용 권한 | 설명
---------- | -----------
microsoft.directory/applicationPolicies/allProperties/read | 애플리케이션 정책의 모든 속성을 읽습니다.
microsoft.directory/applicationPolicies/allProperties/update | 애플리케이션 정책의 모든 속성을 업데이트합니다.
microsoft.directory/applicationPolicies/basic/update | 애플리케이션 정책의 표준 속성을 업데이트합니다.
microsoft.directory/applicationPolicies/create | 애플리케이션 정책을 만듭니다.
microsoft.directory/applicationPolicies/createAsOwner | 애플리케이션 정책을 만듭니다. 작성자가 첫 번째 소유자로 추가됩니다.
microsoft.directory/applicationPolicies/delete | 애플리케이션 정책을 삭제합니다.
microsoft.directory/applicationPolicies/owners/read | 애플리케이션 정책의 소유자를 읽습니다.
microsoft.directory/applicationPolicies/owners/update | 애플리케이션 정책의 소유자 속성을 업데이트합니다.
microsoft.directory/applicationPolicies/policyAppliedTo/read | 개체 목록에 적용된 애플리케이션 정책을 읽습니다.
microsoft.directory/applicationPolicies/standard/read | 애플리케이션 정책의 표준 속성을 읽습니다.
microsoft.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory에서 servicePrincipals를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다.
microsoft.directory/servicePrincipals/allProperties/read | servicePrincipals의 모든 속성을 읽습니다.
microsoft.directory/servicePrincipals/allProperties/update | servicePrincipals의 모든 속성을 업데이트합니다.
microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 서비스 주체 역할 할당을 읽습니다.
microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당을 업데이트합니다.
microsoft.directory/servicePrincipals/appRoleAssignments/read | 서비스 주체에 할당된 역할 할당을 읽습니다.
microsoft.directory/servicePrincipals/audience/update | 서비스 주체의 대상 그룹 속성을 업데이트합니다.
microsoft.directory/servicePrincipals/authentication/update | 서비스 주체의 인증 속성을 업데이트합니다.
microsoft.directory/servicePrincipals/basic/update | 서비스 주체의 기본 속성을 업데이트합니다.
microsoft.directory/servicePrincipals/create | 서비스 주체를 만듭니다.
microsoft.directory/servicePrincipals/createAsOwner | 서비스 주체를 만듭니다. 작성자가 첫 번째 소유자로 추가됩니다.
microsoft.directory/servicePrincipals/credentials/update | 서비스 주체의 자격 증명 속성을 업데이트합니다.
microsoft.directory/servicePrincipals/delete | 서비스 주체를 삭제합니다.
microsoft.directory/servicePrincipals/disable | 서비스 주체를 사용하지 않도록 설정합니다.
microsoft.directory/servicePrincipals/enable | 서비스 주체를 사용하도록 설정합니다.
microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명을 읽습니다.
microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명을 관리합니다.
microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 서비스 주체의 위임된 권한 부여를 읽습니다.
microsoft.directory/servicePrincipals/owners/read | 서비스 주체의 소유자를 읽습니다.
microsoft.directory/servicePrincipals/owners/update | 서비스 주체의 소유자를 업데이트합니다.
microsoft.directory/servicePrincipals/permissions/update |  
microsoft.directory/servicePrincipals/policies/read | 서비스 주체의 정책을 읽습니다.
microsoft.directory/servicePrincipals/policies/update | 서비스 주체의 정책을 업데이트합니다.
microsoft.directory/servicePrincipals/standard/read | 서비스 주체의 표준 속성을 읽습니다.
microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정을 읽습니다.
microsoft.directory/servicePrincipals/tag/update | 서비스 주체의 태그 속성을 업데이트합니다.
microsoft.directory/applicationTemplates/instantiate | 애플리케이션 템플릿에서 갤러리 애플리케이션을 인스턴스화합니다.
microsoft.directory/auditLogs/allProperties/read | 감사 로그를 읽습니다.
microsoft.directory/signInReports/allProperties/read | 로그인 보고서를 읽습니다.
microsoft.directory/applications/synchronization/standard/read | 애플리케이션 개체와 연결된 프로비저닝 설정을 읽습니다.
microsoft.directory/servicePrincipals/synchronizationJobs/manage | 서비스 주체 리소스에 대한 작업 동기화의 모든 측면을 관리합니다.
microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정을 읽습니다.
microsoft.directory/servicePrincipals/synchronizationSchema/manage | 서비스 주체 리소스에 대한 스키마 동기화의 모든 측면을 관리합니다.
microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal, Azure AD PowerShell 및 Graph API를 사용하여 사용자 지정 역할 만들기](custom-create.md)
- [사용자 지정 역할의 할당 보기](custom-view-assignments.md)
