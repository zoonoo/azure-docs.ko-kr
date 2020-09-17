---
title: Id 관리를 위해 Azure Active Directory 테 넌 트를 사용 하 여 앱 보기
description: Id 관리를 위해 Azure Active Directory 테 넌 트를 사용 하 여 모든 응용 프로그램을 보는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 5229b123986149903d562bc0b3d6902c0136f647
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707886"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Id 관리를 위해 Azure AD 테 넌 트를 사용 하 여 앱 보기
[응용 프로그램 관리에 대 한 빠른 시작 시리즈](view-applications-portal.md) 는 기본 사항을 안내 합니다. 여기서는 id 관리를 위해 Azure AD 테 넌 트를 사용 하 여 모든 앱을 보는 방법을 알아봅니다. 이 문서는 다이브 앱의 유형을 약간 자세히 설명 합니다.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>내 모든 애플리케이션 목록에 특정 애플리케이션이 나타나는 이유는 무엇입니까?
**모든 애플리케이션**으로 필터링한 경우 **모든 애플리케이션** **목록**은 테넌트의 모든 서비스 주체 개체를 보여 줍니다. 서비스 주체 개체는 다양한 방법으로 이 목록에 나타날 수 있습니다.
- 다음을 포함하여 애플리케이션 갤러리에서 모든 애플리케이션을 추가하는 경우:
   - **AZURE ad-엔터프라이즈 응용 프로그램** -azure ad 포털에서 **엔터프라이즈 응용 프로그램** 옵션을 사용 하 여 테 넌 트에 추가 된 앱입니다. 일반적으로 SAML 표준을 사용 하 여 통합 된 앱입니다.
   - **AZURE ad 앱 등록** – azure ad 포털의 **앱 등록** 옵션을 사용 하 여 테 넌 트에 추가 된 앱입니다. 일반적으로 Open ID Connect 및 OAuth 표준을 사용 하 여 사용자 지정 개발 된 앱입니다.
   - **애플리케이션 프록시 애플리케이션** – 외부로 보안 Single Sign-On을 제공하려는 온-프레미스 환경에서 실행 중인 애플리케이션입니다.
- 에 등록 하거나에 로그인 할 때 Azure Active Directory와 통합 된 타사 응용 프로그램입니다. 한 가지 예로 [Smartsheet](https://app.smartsheet.com/b/home) 또는 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx)이 있습니다.
- Microsoft 365와 같은 Microsoft 앱입니다.
- [애플리케이션 레지스트리](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)를 사용하여 사용자 지정 개발된 애플리케이션을 만들어 새 애플리케이션 등록을 추가하는 경우
- [V2.0 애플리케이션 레지스트리 포털](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)을 사용하여 사용자 지정 개발된 애플리케이션을 만들어 새 애플리케이션 등록을 추가하는 경우
- 응용 프로그램을 추가 하는 경우 Visual Studio의 [ASP.NET 인증 메서드](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) 를 사용 하 여 개발 하거나 [연결된 서비스](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- [Azure AD PowerShell 모듈](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)을 사용하여 서비스 주체 개체를 만드는 경우
- 테넌트의 데이터를 사용하도록 관리자 권한으로 [애플리케이션에 동의](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)하는 경우
- 테넌트의 데이터를 사용하도록 [사용자가 애플리케이션에 동의](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)하는 경우
- 테넌트에 데이터를 저장하는 특정 서비스를 활성화하는 경우. 한 가지 예는 암호 재설정 정책을 안전하게 저장하도록 서비스 주체로 모델링되는 암호 재설정입니다.

응용 프로그램을 디렉터리에 추가 하는 방법 및 이유에 대 한 자세한 내용은 [AZURE AD에 응용 프로그램을 추가 하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 애플리케이션 관리](what-is-application-management.md)
