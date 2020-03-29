---
title: 다중 테넌트 애플리케이션용 SSO 사용
description: Azure 활성 디렉터리와의 통합에 대한 독립 소프트웨어 공급업체에 대한 지침
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795174"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>다중 테넌트 응용 프로그램에 대해 단일 사인온 사용  

구매 또는 구독을 통해 다른 회사에서 사용할 수 있도록 응용 프로그램을 제공하는 경우 자체 Azure 테넌션 내의 고객이 응용 프로그램을 사용할 수 있도록 합니다. 이를 다중 테넌트 응용 프로그램 만들기라고 합니다. 이 개념에 대한 개요는 Azure 및 [테넌트의 다중 테넌트 응용 프로그램](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) 및 Azure Active [Directory의](../develop/single-and-multi-tenant-apps.md)참조입니다.

## <a name="what-is-single-sign-on"></a>단일 사인온이란?

SSO(단일 사인온)는 사용자가 Azure Active Directory 및 기타 ID를 사용하여 응용 프로그램에 로그온할 때 보안과 편의성을 추가합니다. 응용 프로그램이 SSO를 사용하도록 설정하면 사용자는 해당 응용 프로그램에 액세스하기 위해 별도의 자격 증명을 입력할 필요가 없습니다. 단일 사인온에 대한 전체 설명. [Azure Active Directory의 응용 프로그램에 대한 단일 사인온을 참조하십시오.](what-is-single-sign-on.md)

## <a name="why-enable-single-sign-on-in-your-application"></a>응용 프로그램에서 단일 사인온을 사용하도록 설정하는 이유는 무엇입니까?

다중 테넌트 응용 프로그램에서 SSO를 사용하도록 설정하는 데는 많은 이점이 있습니다. 응용 프로그램에 대해 SSO를 사용하도록 설정하는 경우:

* 응용 프로그램은 Azure Active Directory를 사용하는 수백만 개의 조직에서 앱을 검색할 수 있는 Azure Marketplace에 나열될 수 있습니다.
  * 고객이 Azure AD를 사용하여 응용 프로그램을 신속하게 구성할 수 있도록 합니다.

* 응용 프로그램은 Office 365 앱 갤러리, Office 365 앱 실행기 및 microsoft 검색 에서 Office.com

* 응용 프로그램은 Microsoft 그래프 REST API를 사용하여 Microsoft 그래프에서 사용할 수 있는 사용자 생산성을 높이는 데이터에 액세스할 수 있습니다.

* 고객이 더 쉽게 지원할 수 있도록 지원 비용을 절감할 수 있습니다.
  * 상호 고객을 위해 Azure AD 팀과 공동 으로 제공된 응용 프로그램별 설명서는 채택을 용이하게 합니다.
  * 원클릭 SSO를 사용하도록 설정한 경우 고객의 IT 관리자는 조직에서 사용할 응용 프로그램을 구성하는 방법을 배울 필요가 없습니다.

* 고객에게 직원 및 게스트 ID의 인증 및 권한 부여를 완전히 관리할 수 있는 기능을 제공합니다.

  * 모든 계정 관리 및 규정 준수 책임을 해당 ID의 고객 소유자에게 맡습니다.

  * 특정 ID 공급자, 그룹 또는 사용자가 비즈니스 요구 사항을 충족하도록 SSO를 사용 하거나 사용하지 않도록 설정할 수 있는 기능을 제공합니다.

* 시장성과 채택성을 높입니다. 많은 대규모 조직에서는 직원들이 모든 응용 프로그램에서 원활한 SSO 환경을 제공하도록 요구하고 있습니다. SSO를 쉽게 만드는 것이 중요합니다.

* 최종 사용자 마찰을 줄여 최종 사용자 사용량을 늘리고 수익을 늘릴 수 있습니다.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>게시된 응용 프로그램에서 Single Sign-On을 활성화하는 방법

1. [다중 테넌트 응용 프로그램에 적합한 페더레이션 프로토콜을 선택합니다.](isv-choose-multi-tenant-federation.md)
1. 응용 프로그램에서 SSO 구현
   - [인증 패턴에 대한 지침](../develop/v2-app-types.md) 보기
   - OIDC 및 OAuth 프로토콜에 대한 [Azure 활성 디렉터리 코드 샘플](../develop/sample-v2-code.md) 참조
1. [Azure 테넌트를 만들고](isv-tenant-multi-tenant-app.md) 응용 프로그램을 테스트합니다.
1. [사이트에 SSO 설명서를 만들고 게시합니다.](isv-create-sso-documentation.md)
1. [응용 프로그램 목록을 제출하고](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) Microsoft와 파트너관계를 맺어 Microsoft 사이트에 설명서를 작성합니다.
1. [Microsoft 파트너 네트워크(무료)에 가입하여 시장 계획을 세우십시오.](https://partner.microsoft.com/en-us/explore/commercial#gtm)
