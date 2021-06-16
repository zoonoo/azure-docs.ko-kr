---
title: Azure Active Directory의 연결된 로그온 이해
description: Azure Active Directory의 연결된 로그온을 이해합니다.
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: mtillman
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 026e2cbc49bea93f326cc7c86b0b7e113bfd754c
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076261"
---
# <a name="understand-linked-sign-on"></a>연결된 로그온 이해

애플리케이션 관리에 대한 [빠른 시작 시리즈](view-applications-portal.md)에서는 애플리케이션용 IdP(ID 공급자)로 Azure AD를 사용하는 방법을 알아보았습니다. 빠른 시작 가이드에서는 SAML 기반 또는 OIDC 기반 SSO를 구성합니다. 또 다른 옵션은 **연결됨** 입니다. 이 문서에서는 연결됨 옵션에 대해 자세히 설명합니다.

**연결됨** 옵션을 사용하면 사용자가 조직의 [내 앱](https://myapps.microsoft.com/) 또는 Office 365 포털에서 앱을 선택할 때 대상 위치를 구성할 수 있습니다.

연결 옵션이 중요한 몇 가지 일반적인 경우는 다음과 같습니다.
- AD FS(Active Directory Federation Services)와 같이 현재 페더레이션을 사용하는 사용자 지정 웹 애플리케이션에 대한 링크를 추가합니다.
- 사용자의 액세스 패널에 표시하려는 특정 SharePoint 페이지 또는 다른 웹 페이지에 대한 딥 링크를 추가합니다.
- 인증을 요구하지 않는 앱에 대한 링크를 추가합니다. 
 
 **연결됨** 옵션은 Azure AD 자격 증명을 통해 로그온 기능을 제공하지 않습니다. 그러나 **엔터프라이즈 애플리케이션** 의 다른 기능 중 일부를 계속 사용할 수 있습니다. 예를 들어 감사 로그를 사용하고 사용자 지정 로고 및 앱 이름을 추가할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 정보를 간단히 확인하려면 애플리케이션 관리에 대한 [빠른 시작 시리즈](view-applications-portal.md)를 살펴보세요. Single Sign-On을 구성하는 빠른 시작에서 **연결됨** 옵션을 찾을 수도 있습니다. 

**연결됨** 옵션은 Azure AD를 통해 로그온 기능을 제공하지 않습니다. 이 옵션은 사용자가 [내 앱](https://myapps.microsoft.com/) 또는 Microsoft 365 앱 시작 관리자에서 앱을 선택할 때 전송되는 위치만 설정합니다.  로그인은 Azure AD를 통해 로그온 기능을 제공하지 않으므로 연결된 Single Sign-On으로 구성된 애플리케이션에는 조건부 액세스를 사용할 수 없습니다.

> [!IMPORTANT] 
> **엔터프라이즈 애플리케이션** 에서 애플리케이션을 탐색할 때 **Single Sign-On** 옵션이 표시되지 않는 일부 경우가 있습니다. 
>
> **앱 등록** 을 사용하여 애플리케이션을 등록하면 Single Sign-On 기능은 기본적으로 OIDC OAuth를 사용하도록 설정됩니다. 이 경우 **엔터프라이즈 애플리케이션** 에서 **Single Sign-On** 옵션이 탐색에 표시되지 않습니다. **앱 등록** 을 사용하여 사용자 지정 앱을 추가하면 매니페스트 파일의 옵션을 구성하게 됩니다. 매니페스트 파일에 대한 자세한 내용은 [Azure Active Directory 앱 매니페스트](../develop/reference-app-manifest.md)를 참조하세요. SSO 표준에 대한 자세한 내용은 [Microsoft ID 플랫폼을 사용한 인증 및 권한 부여](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)를 참조하세요. 
>
> 애플리케이션이 다른 테넌트에 호스트되거나 사용자 계정에 필요한 권한(전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자)이 없는 경우에는 탐색에서 **Single Sign-On** 이 표시되지 않을 수 있습니다. 사용 권한으로 **Single Sign-On** 을 열 수는 있지만 저장할 수 없는 경우가 발생할 수도 있습니다. Azure AD 관리자 역할에 대해 자세히 알아보려면 https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 의 설명을 참조하세요.

### <a name="configure-link"></a>링크 구성

앱에 대한 링크를 설정하려면 **Single Sign-On** 페이지에서 **연결됨** 을 선택합니다. 그런 다음, 링크를 입력하고 **저장** 을 선택합니다. 이러한 옵션을 찾을 수 있는 위치에 대한 미리 알림이 필요한가요? [빠른 시작 시리즈](view-applications-portal.md)를 확인하세요.
 
앱을 구성한 후에는 사용자 및 그룹을 할당합니다. 사용자를 할당하면 애플리케이션이 [내 앱](https://myapps.microsoft.com/) 또는 Microsoft 365 앱 시작 관리자에 표시되는 시간을 제어할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](./assign-user-or-group-access-portal.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
