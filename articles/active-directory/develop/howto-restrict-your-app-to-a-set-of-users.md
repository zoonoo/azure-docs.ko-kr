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
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8bdc7e6e3795719128a8ecfb1e8bc97c1a9a08c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759034"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>방법: azure ad 테 넌 트에서 사용자 집합으로 Azure AD 앱 제한

기본적으로 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션은 성공적으로 인증한 테넌트의 모든 사용자에게 제공됩니다.

마찬가지로, [다중 테넌트](howto-convert-app-to-be-multi-tenant.md) 앱의 경우 이 앱이 프로비전된 Azure AD 테넌트의 모든 사용자는 각 테넌트에서 성공적으로 인증되면 이 애플리케이션에 액세스할 수 있게 됩니다.

테넌트 관리자 및 개발자에게는 종종 앱을 특정 사용자 집합으로 제한해야 하는 요구 사항이 있습니다. 개발자는 RBAC(역할 기반 액세스 제어)처럼 인기 있는 권한 부여 패턴을 사용하여 동일하게 수행할 수 있지만, 이 접근 방식은 개발자가 상당한 양의 작업을 수행해야 합니다.

테 넌 트 관리자 및 개발자는 Azure AD의 기본 제공 기능을 사용 하 여 테 넌 트에서 특정 사용자 또는 보안 그룹 집합으로 앱을 제한할 수 있습니다.

## <a name="supported-app-configurations"></a>지원되는 앱 구성

앱을 테넌트의 특정 사용자 집합 또는 보안 그룹으로 제한하는 옵션은 다음과 같은 애플리케이션 형식으로 작동합니다.

- SAML 기반 인증을 사용하여 페더레이션된 Single Sign-On에 대해 구성된 애플리케이션
- Azure AD 사전 인증을 사용하는 애플리케이션 프록시 애플리케이션
- 애플리케이션은 사용자 또는 관리자가 해당 애플리케이션에 동의한 후에 OAuth 2.0/OpenID Connect 인증을 사용하는 Azure AD 애플리케이션 플랫폼에서 직접 빌드됩니다.

     > [!NOTE]
     > 이 기능은 웹앱/웹 API 및 엔터프라이즈 애플리케이션에만 사용할 수 있습니다. [네이티브](quickstart-v1-integrate-apps-with-azure-ad.md)로 등록된 앱은 테넌트의 사용자 집합 또는 보안 그룹으로 제한할 수 없습니다.

## <a name="update-the-app-to-enable-user-assignment"></a>사용자 할당을 사용하도록 앱 업데이트

사용자 할당을 사용 하는 응용 프로그램을 만드는 방법에는 두 가지가 있습니다. **전역 관리자** 역할이 필요 하지만 두 번째 역할은 그렇지 않습니다.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>엔터프라이즈 응용 프로그램 (전역 관리자 역할 필요)

1. [**Azure Portal**](https://portal.azure.com/) 로 이동 하 여 **전역 관리자 권한**으로 로그인 합니다.
1. 위쪽 바에서 로그인 계정을 선택합니다. 
1. **디렉터리**에서 앱을 등록할 Azure AD 테넌트를 선택합니다.
1. 왼쪽 탐색 메뉴에서 **Azure Active Directory**를 선택합니다. 탐색 창에서 Azure Active Directory을 사용할 수 없는 경우 다음 단계를 수행 합니다.

    1. 왼쪽 주 탐색 메뉴의 맨 위에 있는 **모든 서비스**를 선택합니다.
    1. 필터 검색 상자에 **Azure Active Directory** 를 입력 한 다음 결과에서 **Azure Active Directory** 항목을 선택 합니다.

1. **Azure Active Directory** 창에 있는 **Azure Active Directory** 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 선택합니다.
1. 모든 **응용 프로그램을 선택 하** 여 모든 응용 프로그램의 목록을 봅니다.

     여기에 원하는 애플리케이션이 보이지 않으면 **모든 애플리케이션** 목록의 맨 위에서 다양한 필터를 사용하여 목록을 제한하거나 목록을 아래로 스크롤하여 애플리케이션을 찾습니다.

1. 목록에서 사용자 또는 보안 그룹을 할당하려는 애플리케이션을 선택합니다.
1. 응용 프로그램의 **개요** 페이지에 있는 응용 프로그램의 왼쪽 탐색 메뉴에서 **속성** 을 선택 합니다.
1. **사용자 할당이 필요합니까?** 설정을 찾아 **예**로 설정합니다. 이 옵션을 **예**로 설정 하면 테 넌 트의 사용자를 먼저이 응용 프로그램에 할당 해야 합니다. 그렇지 않으면이 응용 프로그램에 로그인 할 수 없습니다.
1. **저장**을 선택하여 이 구성 변경을 저장합니다.

### <a name="app-registration"></a>앱 등록

1. [**Azure Portal**](https://portal.azure.com/)로 이동 합니다.
1. 위쪽 바에서 로그인 계정을 선택합니다. 
1. **디렉터리**에서 앱을 등록할 Azure AD 테넌트를 선택합니다.
1. 왼쪽 탐색 메뉴에서 **Azure Active Directory**를 선택합니다.
1. **Azure Active Directory** 창의 왼쪽 탐색 메뉴 **Azure Active Directory** 에서 **앱 등록** 을 선택 합니다.
1. 관리 하려는 앱을 만들거나 선택 합니다. 이 앱 등록의 **소유자** 여야 합니다.
1. 응용 프로그램의 **개요** 페이지에서 페이지 위쪽의 essentials 아래에 있는 **로컬 디렉터리의 관리 되는 응용 프로그램** 링크를 따릅니다. 그러면 앱 등록의 _관리 되는 엔터프라이즈 응용 프로그램_ 으로 이동 합니다.
1. 왼쪽의 탐색 블레이드에서 **속성**을 선택 합니다.
1. **사용자 할당이 필요합니까?** 설정을 찾아 **예**로 설정합니다. 이 옵션을 **예**로 설정 하면 테 넌 트의 사용자를 먼저이 응용 프로그램에 할당 해야 합니다. 그렇지 않으면이 응용 프로그램에 로그인 할 수 없습니다.
1. **저장**을 선택하여 이 구성 변경을 저장합니다.

## <a name="assign-users-and-groups-to-the-app"></a>앱에 사용자 및 그룹 할당

사용자 할당을 사용하도록 앱을 구성하고 나면 앱에 사용자 및 그룹을 할당할 수 있습니다.

1. 애플리케이션의 왼쪽 탐색 메뉴에서 **사용자 및 그룹** 창을 선택합니다.
1. **사용자 및 그룹** 목록의 맨 위에서 **사용자 추가** 단추를 선택하여 **할당 추가** 창을 엽니다.
1. **할당 추가** 창에서 **사용자** 선택기를 선택합니다. 

     특정 사용자 및 그룹을 검색하고 찾는 텍스트 상자와 함께 사용자 및 보안 그룹의 목록이 표시됩니다. 이 화면에서는 여러 사용자 및 그룹을 한 번에 선택할 수 있습니다.

1. 사용자 및 그룹을 모두 선택하고 나면 맨 아래 **선택** 단추를 눌러서 다음 부분으로 이동합니다.
1. 필드 응용 프로그램에 앱 역할을 정의한 경우 **역할 선택** 옵션을 사용 하 여 선택한 사용자 및 그룹을 응용 프로그램 역할 중 하나에 할당할 수 있습니다. 
1. 맨 아래 **할당** 단추를 눌러서 앱에 사용자 및 그룹의 할당을 완료합니다. 
1. 추가한 사용자 및 그룹이 업데이트된 **사용자 및 그룹** 목록에 보이는지 확인합니다.

## <a name="more-information"></a>추가 정보

- [방법: 응용 프로그램에 앱 역할 추가](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [ASP.NET Core 웹 앱에 대 한 역할 클레임 & 앱 역할을 사용 하 여 권한 부여 추가](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [앱에서 보안 그룹 및 애플리케이션 역할 사용(비디오)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory에 그룹 클레임 및 애플리케이션 역할 포함](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 앱 매니페스트](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)