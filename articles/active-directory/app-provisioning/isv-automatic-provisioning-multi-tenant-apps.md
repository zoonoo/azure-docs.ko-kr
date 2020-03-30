---
title: 다중 테넌트 응용 프로그램에 대한 자동 사용자 프로비저닝 사용 - Azure AD
description: 자동화된 프로비저닝을 사용하도록 설정하는 독립 소프트웨어 공급업체를 위한 가이드
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522396"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>다중 테넌트 응용 프로그램에 대한 자동 사용자 프로비저닝 사용

자동 사용자 프로비저닝은 서비스형 소프트웨어 응용 프로그램과 같은 대상 시스템에서 사용자 ID의 생성, 유지 관리 및 제거를 자동화하는 프로세스입니다.

## <a name="why-enable-automatic-user-provisioning"></a>자동 사용자 프로비저닝을 사용하도록 설정하는 이유는 무엇입니까?

사용자의 첫 번째 로그인 전에 사용자 레코드가 응용 프로그램에 존재해야 하는 응용 프로그램에는 사용자 프로비전이 필요합니다. 서비스 공급자로서의 이점과 고객에게 혜택이 있습니다.

### <a name="benefits-to-you-as-the-service-provider"></a>서비스 제공업체로서의 혜택

* Microsoft ID 플랫폼을 사용하여 응용 프로그램의 보안을 강화합니다.

* 응용 프로그램을 채택하려는 실제 및 인식된 고객의 노력을 줄입니다.

* SCIM(도메인 간 ID 관리) 기반 프로비저닝을 위한 System을 사용하여 자동 사용자 프로비저닝을 위해 여러 ID 공급자(IdP)와 통합하는 데 드는 비용을 절감합니다.

* 고객이 사용자 프로비저닝 문제를 해결하는 데 도움이 되는 풍부한 로그를 제공하여 지원 비용을 절감합니다.

* [Azure AD 앱 갤러리에서](https://azuremarketplace.microsoft.com/marketplace/apps)응용 프로그램의 가시성을 높입니다.

* 앱 자습서 페이지에서 우선 순위 목록을 가져옵니다.

### <a name="benefits-to-your-customers"></a>고객에게 혜택

* 역할을 변경하거나 조직을 응용 프로그램에 두고 있는 사용자를 위해 응용 프로그램에 대한 액세스를 자동으로 제거하여 보안을 강화합니다.

* 수동 프로비저닝과 관련된 인적 오류 및 반복적인 작업을 방지하여 응용 프로그램의 사용자 관리를 간소화합니다.

* 맞춤형 프로비저닝 솔루션을 호스팅하고 유지 관리하는 비용을 절감합니다.

## <a name="choose-a-provisioning-method"></a>프로비전 메서드 선택

Azure AD는 응용 프로그램에 대한 자동 사용자 프로비전을 사용하도록 설정하는 몇 가지 통합 경로를 제공합니다.

* [Azure AD 프로비저닝 서비스는](../app-provisioning/user-provisioning.md) Azure AD에서 응용 프로그램(아웃바운드 프로비저닝)과 응용 프로그램에서 Azure AD(인바운드 프로비저닝)로의 사용자 프로비저닝 및 디프로비저닝을 관리합니다. 이 서비스는 응용 프로그램에서 제공하는 SCIM(도메인 간 ID 관리) 사용자 관리 API 끝점에 연결됩니다.

* [Microsoft 그래프를](https://docs.microsoft.com/graph/)사용하는 경우 응용 프로그램은 Microsoft 그래프 API를 쿼리하여 Azure AD에서 응용 프로그램에 대한 사용자 및 그룹의 인바운드 및 아웃바운드 프로비저닝을 관리합니다.

* 응용 프로그램에서 페더레이션에 SAML을 사용하는 경우 SAML JIT(보안 어설션 태그 언어) 사용자 프로비전을 사용할 수 있습니다. SAML 토큰에서 전송된 클레임 정보를 사용하여 사용자를 프로비전합니다.

응용 프로그램에 사용할 통합 옵션을 결정하려면 상위 수준 비교 테이블을 참조한 다음 각 옵션에 대한 자세한 정보를 참조하세요.

| 자동 프로비저닝을 통해 지원 또는 향상된 기능| Azure AD 프로비저닝 서비스(SCIM 2.0)| 마이크로소프트 그래프 API (OData v4.0)| 삼엘 지트 |
|---|---|---|---|
| Azure AD의 사용자 및 그룹 관리| √| √| 사용자 전용 |
| 온-프레미스 Active Directory에서 동기화된 사용자 및 그룹 관리| √*| √*| 사용자 전용* |
| O365 데이터에 대한 액세스 프로비저닝 하는 동안 사용자 및 그룹 을 넘어 데이터에 액세스 (팀, SharePoint, 이메일, 캘린더, 문서 등)| X+| √| X |
| 비즈니스 규칙에 따라 사용자 만들기, 읽기 및 업데이트| √| √| √ |
| 비즈니스 규칙에 따라 사용자 삭제| √| √| X |
| Azure 포털의 모든 응용 프로그램에 대한 자동 사용자 프로비저닝 관리| √| X| √ |
| 여러 ID 공급자 지원| √| X| √ |
| 지원 게스트 계정(B2B)| √| √| √ |
| 비기업 계정(B2C) 지원| X| √| √ |

<sup>*</sup>– Azure AD 연결 설정은 AD에서 Azure AD로 사용자를 동기화하는 데 필요합니다.  
<sup>+</sup >– 프로비저닝을 위해 SCIM을 사용한다고 해서 다른 목적으로 응용 프로그램을 MIcrosoft Graph와 통합하는 것을 배제하지는 않습니다.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 프로비저닝 서비스(SCIM)

Azure AD 프로비저닝 서비스는 많은 ID 공급자(IdP)와 응용 프로그램(예: 슬랙, G 스위트, 드롭박스)에서 지원하는 프로비저닝을 위한 업계 표준인 [SCIM을](https://aka.ms/SCIMOverview)사용합니다. SCIM 호환 IdP가 SCIM 끝점에 연결할 수 있으므로 Azure AD 외에 IdP를 지원하려는 경우 Azure AD 프로비저닝 서비스를 사용하는 것이 좋습니다. 간단한 /User 끝점을 구축하면 자체 동기화 엔진을 유지 관리하지 않고도 프로비저닝을 활성화할 수 있습니다. 

Azure AD 프로비저닝 서비스 사용자 SCIM 방법에 대한 자세한 내용은 다음을 참조하십시오. 

* [SCIM 표준에 대해 자세히 알아보기](https://aka.ms/SCIMOverview)

* [SCIM(System for Cross-Domain Identity Management)을 사용하여 사용자 및 그룹을 Azure Active Directory에서 애플리케이션으로 자동 프로비전](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Azure AD SCIM 구현 이해](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>프로비저닝을 위한 마이크로소프트 그래프

프로비저닝에 Microsoft 그래프를 사용하면 Graph에서 사용할 수 있는 모든 풍부한 사용자 데이터에 액세스할 수 있습니다. 사용자 및 그룹의 세부 정보 외에도 사용자의 역할, 관리자 및 직접 보고서, 소유 및 등록된 장치 및 [Microsoft Graph에서](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)사용할 수 있는 수백 개의 기타 데이터 조각과 같은 추가 정보를 가져올 수도 있습니다. 

1,500만 개 이상의 조직과 Fortune 500대 기업의 90%가 Office 365, Microsoft Azure, 엔터프라이즈 모빌리티 제품군 또는 Microsoft 365와 같은 Microsoft 클라우드 서비스에 가입하는 동안 Azure AD를 사용합니다. Microsoft Graph를 사용하여 직원 온보딩(및 종료), 프로필 유지 관리 등과 같은 관리 워크플로와 앱을 통합할 수 있습니다. 

프로비저닝을 위해 Microsoft 그래프를 사용하는 방법에 대해 자세히 알아보십시오.

* [마이크로소프트 그래프 홈 페이지](https://developer.microsoft.com/graph)

* [Microsoft Graph 개요](https://docs.microsoft.com/graph/overview)

* [마이크로소프트 그래프 인증 개요](https://docs.microsoft.com/graph/auth/)

* [마이크로소프트 그래프를 시작](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>프로비저닝을 위해 SAML JIT 사용

응용 프로그램에 처음 로그인할 때만 사용자를 프로비전하고 사용자를 자동으로 프로비저닝해제할 필요가 없는 경우 SAML JIT가 옵션입니다. 응용 프로그램은 SAML JIT를 사용하려면 연합 프로토콜로 SAML 2.0을 지원해야 합니다.

SAML JIT는 SAML 토큰의 클레임 정보를 사용하여 응용 프로그램에서 사용자 정보를 만들고 업데이트합니다. 고객은 필요에 따라 Azure AD 응용 프로그램에서 이러한 필수 클레임을 구성할 수 있습니다. 고객이 이 기능을 사용할 수 있도록 응용 프로그램 측에서 JIT 프로비전을 사용하도록 설정해야 하는 경우가 있습니다. SAML JIT는 사용자를 만들고 업데이트하는 데 유용하지만 응용 프로그램의 사용자를 삭제하거나 비활성화할 수는 없습니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션에 대해 단일 사인온 사용](../manage-apps/isv-sso-content.md)

* [응용 프로그램 목록을 제출하고](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) Microsoft와 파트너관계를 맺어 Microsoft 사이트에 설명서를 작성합니다.

* [Microsoft 파트너 네트워크(무료)에 가입하여 시장 계획을 세우십시오.](https://partner.microsoft.com/en-us/explore/commercial)
