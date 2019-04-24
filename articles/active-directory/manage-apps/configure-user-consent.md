---
title: 애플리케이션에 대한 사용자 동의 구성 - Azure Active Directory | Microsoft Docs
description: 사용자가 애플리케이션 권한에 동의하는 방법을 관리하는 방법에 대해 알아봅니다. 관리자 동의를 허용하여 사용자 환경을 간소화할 수 있습니다. 이러한 방법은 Azure AD(Azure Active Directory) 테넌트의 모든 최종 사용자에게 적용됩니다.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35f8b440fe748f91c9e01003fe83a3a5343c8df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291588"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>최종 사용자가 Azure Active Directory에서 애플리케이션에 동의하는 방법을 구성합니다.
사용자가 애플리케이션 권한에 동의하는 방법을 구성하는 방법에 대해 알아봅니다. 관리자 동의를 허용하여 사용자 환경을 간소화할 수 있습니다. 이 문서에서는 사용자 동의를 구성하는 다양한 방법을 제공합니다. 이러한 방법은 Azure AD(Azure Active Directory) 테넌트의 모든 최종 사용자에게 적용됩니다. 

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

관리자 동의를 허용하려면 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 로그인해야 합니다.

애플리케이션에 대한 액세스를 제한하려면 사용자 할당이 필요하며, 애플리케이션에 사용자 또는 그룹을 할당해야 합니다.  자세한 내용은 [사용자 및 그룹을 할당하는 메서드](methods-for-assigning-users-and-groups.md)를 참조하세요.

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 대한 관리자 동의 허용

엔터프라이즈 앱에 대한 관리자 동의를 허용하려면

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 메뉴의 맨 위에 있는 **모든 서비스**를 클릭합니다. **Azure Active Directory 확장**이 열립니다.
3. 필터 검색 상자에 **“Azure Active Directory”** 를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4. 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.
5. **관리자 동의 허용**을 클릭합니다. 애플리케이션을 관리하려면 로그인하라는 메시지가 표시됩니다.
6. 애플리케이션에 대한 관리자 동의를 허용할 수 있는 권한이 있는 계정으로 로그인합니다. 
7. 애플리케이션 사용 권한에 동의합니다.

이 옵션은 애플리케이션이 다음과 같은 경우에만 적용됩니다. 

- 테넌트에 등록되어 있습니다. 또는
- 다른 Azure AD 테넌트에서 등록되고 하나 이상의 최종 사용자가 동의했습니다. 최종 사용자가 애플리케이션에 동의하면 Azure AD는 Azure Portal의 **엔터프라이즈 앱** 아래에 애플리케이션을 표시합니다.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Azure Portal에서 앱을 등록하는 경우 관리자 동의 허용

앱을 등록할 때 관리자 동의를 허용하려면 

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **앱 등록** 블레이드로 이동합니다.
3. 동의할 애플리케이션을 선택합니다.
4. **필수 권한**을 선택합니다.
5. 블레이드 맨 위에 있는 **권한 부여**를 클릭합니다.


## <a name="grant-admin-consent-through-a-url-request"></a>URL 요청을 통해 관리자 동의 허용

URL 요청을 통해 관리자 동의를 허용하려면 다음을 수행합니다.

1. 앱 구성을 사용하여 *login.microsoftonline.com*에 대한 요청을 생성하고 `&prompt=admin_consent`에 추가합니다. 
2. 관리자 자격 증명으로 로그인한 후 앱에서 모든 사용자에 대한 동의를 부여했습니다.


## <a name="force-user-consent-through-a-url-request"></a>URL 요청을 통해 강제로 사용자 동의

최종 사용자가 인증을 받을 때마다 애플리케이션에 동의하도록 하려면 인증 요청 URL에 `&prompt=consent`를 추가합니다.

## <a name="next-steps"></a>다음 단계

[동의 및 AzureAD와 앱 통합](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[AzureAD v2.0 수렴형 앱에 대한 동의 및 권한 부여](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
