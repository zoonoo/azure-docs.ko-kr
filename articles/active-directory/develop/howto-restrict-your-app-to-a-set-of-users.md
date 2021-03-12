---
title: Azure AD 앱을 사용자 집합으로 제한 | Microsoft
titleSuffix: Microsoft identity platform
description: Azure AD에 등록된 앱에 대한 액세스를 선택한 사용자 집합으로 제한하는 방법을 알아봅니다.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 71cfe08da42b8eec9ddbd0e4246ba1b72f895414
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199594"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>방법: azure ad 테 넌 트에서 사용자 집합으로 Azure AD 앱 제한

기본적으로 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션은 성공적으로 인증한 테넌트의 모든 사용자에게 제공됩니다.

마찬가지로, [다중 테넌트](howto-convert-app-to-be-multi-tenant.md) 앱의 경우 이 앱이 프로비전된 Azure AD 테넌트의 모든 사용자는 각 테넌트에서 성공적으로 인증되면 이 애플리케이션에 액세스할 수 있게 됩니다.

테넌트 관리자 및 개발자에게는 종종 앱을 특정 사용자 집합으로 제한해야 하는 요구 사항이 있습니다. 개발자는 azure RBAC (역할 기반 액세스 제어)와 같은 널리 사용 되는 권한 부여 패턴을 사용 하 여 동일한 작업을 수행할 수 있지만이 방법에는 개발자의 일부에 상당한 양의 작업이 필요 합니다.

테 넌 트 관리자 및 개발자는 Azure AD의 기본 제공 기능을 사용 하 여 테 넌 트에서 특정 사용자 또는 보안 그룹 집합으로 앱을 제한할 수 있습니다.

## <a name="supported-app-configurations"></a>지원되는 앱 구성

앱을 테넌트의 특정 사용자 집합 또는 보안 그룹으로 제한하는 옵션은 다음과 같은 애플리케이션 형식으로 작동합니다.

- SAML 기반 인증을 사용 하 여 페더레이션된 Single Sign-On에 대해 구성 된 응용 프로그램입니다.
- Azure AD 사전 인증을 사용 하는 응용 프로그램 프록시 응용 프로그램입니다.
- 애플리케이션은 사용자 또는 관리자가 해당 애플리케이션에 동의한 후에 OAuth 2.0/OpenID Connect 인증을 사용하는 Azure AD 애플리케이션 플랫폼에서 직접 빌드됩니다.

     > [!NOTE]
     > 이 기능은 웹앱/웹 API 및 엔터프라이즈 애플리케이션에만 사용할 수 있습니다. [네이티브](./quickstart-register-app.md)로 등록된 앱은 테넌트의 사용자 집합 또는 보안 그룹으로 제한할 수 없습니다.

## <a name="update-the-app-to-enable-user-assignment"></a>사용자 할당을 사용하도록 앱 업데이트

사용자 할당을 사용 하는 응용 프로그램을 만드는 방법에는 두 가지가 있습니다. **전역 관리자** 역할이 필요 하지만 두 번째 역할은 그렇지 않습니다.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>엔터프라이즈 응용 프로그램 (전역 관리자 역할 필요)

1. **전역 관리자** 로 <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트를 선택합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 에서 **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램** 을 선택 합니다.
1. 목록에서 사용자 또는 보안 그룹을 할당 하려는 응용 프로그램을 선택 합니다. 
    창 맨 위에 있는 필터를 사용 하 여 특정 응용 프로그램을 검색 합니다.
1. 응용 프로그램의 **개요** 페이지에 있는 **관리** 에서 **속성** 을 선택 합니다.
1. **사용자 할당이 필요합니까?** 설정을 찾아 **예** 로 설정합니다. 이 옵션을 **예** 로 설정 하면 테 넌 트의 사용자를 먼저이 응용 프로그램에 할당 해야 합니다. 그렇지 않으면이 응용 프로그램에 로그인 할 수 없습니다.
1. **저장** 을 선택합니다.

### <a name="app-registration"></a>앱 등록

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트를 선택합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록** 을 선택합니다.
1. 관리 하려는 앱을 만들거나 선택 합니다. 이 응용 프로그램의 **소유자** 여야 합니다.
1. 응용 프로그램의 **개요** 페이지에 있는 **Essentials** 섹션의 **로컬 디렉터리에서 관리 되는 응용 프로그램** 링크를 선택 합니다.
1. **관리** 에서 **속성** 을 선택합니다.
1. **사용자 할당이 필요합니까?** 설정을 찾아 **예** 로 설정합니다. 이 옵션을 **예** 로 설정 하면 테 넌 트의 사용자를 먼저이 응용 프로그램에 할당 해야 합니다. 그렇지 않으면이 응용 프로그램에 로그인 할 수 없습니다.
1. **저장** 을 선택합니다.

## <a name="assign-users-and-groups-to-the-app"></a>앱에 사용자 및 그룹 할당

사용자 할당을 사용하도록 앱을 구성하고 나면 앱에 사용자 및 그룹을 할당할 수 있습니다.

1. **관리** 에서 사용자 **및 그룹**  >  **추가 사용자/그룹** 을 선택 합니다.
1. **사용자** 선택기를 선택 합니다. 

     특정 사용자 및 그룹을 검색하고 찾는 텍스트 상자와 함께 사용자 및 보안 그룹의 목록이 표시됩니다. 이 화면에서는 여러 사용자 및 그룹을 한 번에 선택할 수 있습니다.

1. 사용자 및 그룹 선택을 완료 한 후 **선택** 을 선택 합니다.
1. 필드 응용 프로그램에 앱 역할을 정의한 경우 **역할 선택** 옵션을 사용 하 여 선택한 사용자 및 그룹을 응용 프로그램 역할 중 하나에 할당할 수 있습니다. 
1. **할당** 을 선택 하 여 앱에 대 한 사용자 및 그룹의 할당을 완료 합니다. 
1. 추가한 사용자 및 그룹이 업데이트된 **사용자 및 그룹** 목록에 보이는지 확인합니다.

## <a name="more-information"></a>자세한 정보

- [방법: 응용 프로그램에 앱 역할 추가](./howto-add-app-roles-in-azure-ad-apps.md)
- [ASP.NET Core 웹앱에 앱 역할 및 역할 클레임을 사용하는 권한 부여 추가](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [앱에서 보안 그룹 및 애플리케이션 역할 사용(비디오)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory에 그룹 클레임 및 애플리케이션 역할 포함](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 앱 매니페스트](./reference-app-manifest.md)
