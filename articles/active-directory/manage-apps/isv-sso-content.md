---
title: 다중 테넌트 애플리케이션용 SSO 사용
description: Azure active Directory와의 통합에 대 한 독립 소프트웨어 공급 업체 지침
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "67795174"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>다중 테 넌 트 응용 프로그램에 Single Sign-on 사용  

구매 또는 구독을 통해 다른 회사에서 사용할 수 있도록 응용 프로그램을 제공 하는 경우 자신의 Azure 테 넌 트 내에서 고객이 응용 프로그램을 사용할 수 있도록 합니다. 다중 테 넌 트 응용 프로그램을 만드는 것으로 알려져 있습니다. 이 개념에 대 한 개요는 [Azure의 다중 테 넌 트 응용 프로그램](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) 및 [Azure Active Directory의 테 넌 트](../develop/single-and-multi-tenant-apps.md)를 참조 하세요.

## <a name="what-is-single-sign-on"></a>Single Sign-on 이란?

SSO (Single sign-on)는 사용자가 Azure Active Directory 및 기타 id를 사용 하 여 응용 프로그램에 로그온 하는 경우 보안 및 편리 함을 추가 합니다. 응용 프로그램이 SSO를 사용 하도록 설정 된 경우 사용자는 별도의 자격 증명을 입력 하 여 해당 응용 프로그램에 액세스할 필요가 없습니다. Single sign-on에 대 한 전체 설명을 보려면 [Azure Active Directory에서 응용 프로그램에 대 한 Single sign-on을 참조](what-is-single-sign-on.md)하세요.

## <a name="why-enable-single-sign-on-in-your-application"></a>응용 프로그램에서 Single Sign-on을 사용 하도록 설정 하는 이유

다중 테 넌 트 응용 프로그램에서 SSO를 사용 하도록 설정 하는 경우 많은 이점이 있습니다. 응용 프로그램에 SSO를 사용 하도록 설정 하는 경우:

* 응용 프로그램은 Azure Marketplace에 나열 될 수 있습니다. 여기서 앱은 Azure Active Directory를 사용 하 여 수백만 개의 조직에서 검색할 수 있습니다.
  * 고객이 Azure AD를 사용 하 여 응용 프로그램을 신속 하 게 구성할 수 있습니다.

* 응용 프로그램은 Office 365 앱 갤러리, Office 365 앱 시작 관리자 및 Office.com의 Microsoft Search 내에서 검색할 수 있습니다.

* 응용 프로그램은 Microsoft Graph REST API를 사용 하 여 Microsoft Graph에서 사용할 수 있는 사용자 생산성을 유도 하는 데이터에 액세스할 수 있습니다.

* 고객에 게 더 쉽게 지원 하 여 지원 비용을 절감할 수 있습니다.
  * 상호 고객을 위해 Azure AD 팀과 공동으로 작성 된 응용 프로그램별 설명서는 채택을 용이 하 게 합니다.
  * 한 번 클릭 SSO가 사용 하도록 설정 된 경우 고객의 IT 관리자는 조직에서 사용 하기 위해 응용 프로그램을 구성 하는 방법을 배울 필요가 없습니다.

* 고객에 게 직원 및 게스트 id의 인증 및 권한 부여를 완전히 관리할 수 있는 기능을 제공 합니다.

  * 이러한 id의 고객 소유자에 게 모든 계정 관리 및 규정 준수 책임을 배치 합니다.

  * 특정 id 공급자, 그룹 또는 사용자가 비즈니스 요구에 맞게 SSO를 사용 하거나 사용 하지 않도록 설정 하는 기능을 제공 합니다.

* Marketability 및 adoptability를 늘립니다. 많은 조직에서 직원 들이 모든 응용 프로그램에서 원활 하 게 SSO 환경을 필요로 하는 것을 요구 합니다 (또는 따르는). SSO를 용이 하 게 하는 것이 중요 합니다.

* 최종 사용자의 원활한 감소를 줄이고 최종 사용자의 사용량을 높이고 수익을 늘릴 수 있습니다.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>게시 된 응용 프로그램에서 Single Sign-on을 사용 하도록 설정 하는 방법

1. [다중 테 넌 트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜을 선택](isv-choose-multi-tenant-federation.md)합니다.
1. 응용 프로그램에서 SSO 구현
   - [인증 패턴에 대 한 지침을](../develop/v2-app-types.md) 참조 하세요.
   - OIDC 및 OAuth 프로토콜에 대 한 [Azure Active Directory 코드 샘플](../develop/sample-v2-code.md) 을 참조 하세요.
1. [Azure 테 넌 트 만들기](isv-tenant-multi-tenant-app.md) 및 응용 프로그램 테스트
1. [사이트에서 SSO 설명서를 만들고 게시](isv-create-sso-documentation.md)합니다.
1. Microsoft의 사이트에 대 한 설명서를 만들려면 [응용 프로그램 목록과](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) 파트너를 Microsoft에 제출 하세요.
1. [Microsoft 파트너 네트워크 (무료)에 참여 하 고 시장 출시 계획을 만듭니다](https://partner.microsoft.com/en-us/explore/commercial#gtm).
