---
title: Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Docs
description: Azure AD를 사용하여 여러 타사 SaaS 애플리케이션에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454536"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Azure Active Directory를 사용하여 응용 프로그램에 대한 사용자 프로비전 및 프로비저닝 해제 자동화

Azure Active Directory(Azure AD)에서 **앱 프로비저닝이라는** 용어는 사용자가 액세스해야 하는 클라우드(SaaS) 응용 프로그램에서 사용자 ID 및 역할을 자동으로 만드는 것을 의미합니다.[SaaS](https://azure.microsoft.com/overview/what-is-saas/) 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다. 일반적인 시나리오에는 Azure AD 사용자를 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) 등과 같은 애플리케이션에 프로비저닝하는 것이 포함됩니다.

![프로비저닝 개요 다이어그램](./media/user-provisioning/provisioning-overview.png)

이 기능을 사용하면 다음을 수행할 수 있습니다.

- **프로비저닝 자동화**: 팀이나 조직에 가입할 때 새로운 사람들이 적합한 시스템에 새 계정을 자동으로 만듭니다.
- **프로비저닝 해제 자동화:** 사람들이 팀이나 조직을 떠날 때 올바른 시스템에서 계정을 자동으로 비활성화합니다.
- **시스템 간에 데이터 동기화:** 디렉터리 또는 인적 자원 시스템의 변경 사항에 따라 앱 및 시스템의 ID가 최신 상태로 유지되도록 합니다.
- **프로비저닝 그룹:** 그룹을 지원하는 응용 프로그램에 프로비전합니다.
- **액세스 관리:** 응용 프로그램에 프로비전된 사람을 모니터링하고 감사합니다.
- **갈색 필드 시나리오에서 원활하게 배포:** 시스템 간에 기존 ID를 일치시키고 사용자가 대상 시스템에 이미 있는 경우에도 쉽게 통합할 수 있습니다.
- **풍부한 사용자 지정 사용:** 원본 시스템에서 대상 시스템으로 사용자 데이터를 흐르는 사용자를 정의하는 사용자 지정 가능한 특성 매핑을 활용합니다.
- **중요한 이벤트에 대한 경고 받기:** 프로비저닝 서비스는 중요한 이벤트에 대한 경고를 제공하며 비즈니스 요구에 맞게 사용자 지정 경고를 정의할 수 있는 Log Analytics 통합을 허용합니다.

## <a name="benefits-of-automatic-provisioning"></a>자동 프로비저닝의 이점

최신 조직에서 사용되는 응용 프로그램의 수가 계속 증가함에 따라 IT 관리자는 대규모 액세스 관리를 담당하게 됩니다. SAML(보안 어설션 태그 언어) 또는 열기 ID 연결(OIDC)과 같은 표준을 사용하면 관리자가 SSO(단일 사인온)를 신속하게 설정할 수 있지만 액세스 권한은 사용자가 앱에 프로비전되어야 합니다. 많은 관리자에게 프로비저닝은 매주 모든 사용자 계정을 수동으로 만들거나 CSV 파일을 업로드하는 것을 의미하지만 이러한 프로세스는 시간이 많이 걸리고 비용이 많이 들며 오류가 발생하기 쉽습니다. JIT(SAML 적시)와 같은 솔루션이 프로비저닝을 자동화하기 위해 채택되었지만 기업은 조직을 떠나거나 역할 변경에 따라 특정 앱에 더 이상 액세스하지 않아도 되는 경우 사용자를 프로비저닝 해제할 수 있는 솔루션이 필요합니다.

자동 프로비저닝을 사용하는 몇 가지 일반적인 동기는 다음과 같습니다.

- 프로비저닝 프로세스의 효율성과 정확성을 극대화합니다.
- 맞춤형 프로비저닝 솔루션 및 스크립트 호스팅 및 유지 관리와 관련된 비용 절감
- 조직을 떠날 때 주요 SaaS 앱에서 사용자의 ID를 즉시 제거하여 조직을 보호합니다.
- 많은 수의 사용자를 특정 SaaS 응용 프로그램 이나 시스템으로 쉽게 가져올 수 있습니다.
- 프로비저닝된 사용자및 앱에 로그인할 수 있는 사람을 결정하는 단일 정책 집합을 갖는 것입니다.

Azure AD 사용자 프로비저닝은 이러한 문제를 해결하는 데 도움이 될 수 있습니다. 고객이 Azure AD 사용자 프로비전을 사용하는 방법에 대해 자세히 알아보려면 [ASOS 사례 연구를](https://aka.ms/asoscasestudy)읽을 수 있습니다. 아래 비디오에서는 Azure AD의 사용자 프로비저닝에 대한 개요를 제공합니다.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?

Azure AD는 많은 인기 있는 SaaS 앱 및 인적 자원 시스템에 대한 사전 통합 지원 및 [SCIM 2.0 표준의](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)특정 부분을 구현하는 앱에 대한 일반 지원을 제공합니다.

* **사전 통합 된 응용 프로그램 (갤러리 SaaS 애플 리 케이 션)**. Azure AD가 사용자 프로비저닝을 위한 응용 프로그램 자습서 목록에서 사전 통합된 프로비저닝 커넥터를 지원하는 모든 응용 프로그램을 찾을 수 [있습니다.](../saas-apps/tutorial-list.md) 갤러리에 나열된 사전 통합 된 응용 프로그램은 일반적으로 프로비저닝에 SCIM 2.0 기반 사용자 관리 API를 사용합니다. 

   ![세일즈포스 로고](./media/user-provisioning/gallery-app-logos.png)

   프로비저닝을 위한 새 응용 프로그램을 요청하려면 [응용 프로그램을 앱 갤러리와 통합하도록 요청할](../develop/howto-app-gallery-listing.md)수 있습니다. 사용자 프로비저닝 요청의 경우 응용 프로그램에 SCIM 준수 끝점이 있어야 합니다. 우리는 신속하게 우리의 플랫폼에 응용 프로그램을 온보보드 할 수 있도록 응용 프로그램 공급 업체가 SCIM 표준을 따르도록 요청하십시오.

* **SCIM 2.0을 지원하는 응용 프로그램**. SCIM 2.0 기반 사용자 관리 API를 구현하는 응용 프로그램을 일반적으로 연결하는 방법에 대한 자세한 내용은 [SCIM 엔드포인트 빌드 및 사용자 프로비저닝 구성을](use-scim-to-provision-users-and-groups.md)참조하십시오.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>도메인 간 ID 관리(SCIM)를 위한 시스템은 무엇입니까?

프로비저닝 및 프로비저닝을 자동화하기 위해 앱은 독점 사용자 및 그룹 API를 노출합니다. 그러나 두 개 이상의 앱에서 사용자를 관리하려고 시도한 사람은 모든 앱에서 사용자 만들기 또는 업데이트, 그룹에 사용자 추가 또는 사용자 프로비저닝 해제와 같은 동일한 간단한 작업을 수행하려고 시도한다는 것을 알 수 있습니다. 그러나 이러한 모든 간단한 작업은 서로 다른 끝점 경로, 사용자 정보를 지정하는 다른 메서드 및 정보의 각 요소를 나타내는 다른 스키마를 사용하여 약간 다르게 구현됩니다.

이러한 문제를 해결하기 위해 SCIM 사양은 사용자가 앱으로, 앱 밖으로 이동 및 이동할 수 있도록 도와주는 일반적인 사용자 스키마를 제공합니다. SCIM은 프로비저닝을 위한 사실상의 표준이 되고 있으며, SAML 또는 OpenID Connect와 같은 페더레이션 표준과 함께 사용될 경우 관리자에게 액세스 관리를 위한 엔드 투 엔드 표준 기반 솔루션을 제공합니다.

사용자 및 그룹의 프로비저닝 및 응용 프로그램에 대한 프로비저닝 및 디프로비저닝을 자동화하기 위한 SCIM 끝점 개발에 대한 자세한 지침은 [SCIM 끝점 빌드 및 사용자 프로비저닝 구성을](use-scim-to-provision-users-and-groups.md)참조하십시오. 갤러리에 미리 통합된 응용 프로그램(슬랙, Azure Databricks, Snowflake 등)의 경우 개발자 설명서를 건너뛰고 [여기에](../saas-apps/tutorial-list.md)제공된 자습서를 사용할 수 있습니다.

## <a name="manual-vs-automatic-provisioning"></a>수동 및 자동 프로비저닝

Azure AD 갤러리의 응용 프로그램은 다음 두 가지 프로비저닝 모드 중 하나를 지원합니다.

* **수동** 프로비저닝은 앱에 대한 자동 Azure AD 프로비전 커넥터가 아직 없음을 의미합니다. 예를 들어 사용자를 앱의 관리 포털에 직접 추가하거나 사용자 계정 세부 정보가 있는 스프레드시트를 업로드하는 등 사용자 계정을 수동으로 만들어야 합니다. 앱에서 제공하는 설명서를 참조하거나 앱 개발자에게 문의하여 사용 가능한 메커니즘을 확인합니다.

* **자동**이란 Azure AD 프로비전 커넥터가 이 애플리케이션에 대해 개발되었음을 의미합니다. 응용 프로그램에 대한 프로비저닝 설정과 관련된 설정 자습서를 따라야 합니다. 앱 자습서는 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다.

Azure AD 갤러리에서 자동 프로비저닝을 지원하는 응용 프로그램은 **프로비저닝** 아이콘에 의해 지정됩니다. 이러한 아이콘을 보려면 새 갤러리 미리 보기 환경으로 전환합니다(응용 **프로그램 추가 페이지**상단의 배너에서 새 앱 **갤러리를 사용해 보려면 여기를**클릭하십시오).

![응용 프로그램 갤러리의 프로비전 아이콘](./media/user-provisioning/browse-gallery.png)

응용 프로그램에서 지원하는 프로비저닝 모드는 **엔터프라이즈 앱에**응용 프로그램을 추가한 후 **프로비저닝** 탭에도 표시됩니다.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>애플리케이션에 자동 프로비전을 설정하려면 어떻게 합니까?

갤러리에 나열된 사전 통합 된 응용 프로그램의 경우 자동 프로비저닝을 설정하기 위한 단계별 지침을 사용할 수 있습니다. 통합 [갤러리 앱에 대한 자습서 목록을](../saas-apps/tutorial-list.md)참조하십시오. 다음 비디오에서는 SalesForce에 대한 자동 사용자 프로비저닝을 설정하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCIM 2.0을 지원하는 다른 응용 프로그램의 경우 [SCIM 끝점 빌드 및 사용자 프로비저닝 구성](use-scim-to-provision-users-and-groups.md)문서의 단계를 따릅니다.


## <a name="related-articles"></a>관련 문서

- [SaaS 앱을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
- [사용자 프로비저닝을 위한 특성 매핑 사용자 지정](customize-application-attributes.md)
- [특성 매핑에 대한 표현식 작성](../app-provisioning/functions-for-customizing-application-data.md)
- [사용자 프로비저닝을 위한 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM 엔드포인트 구축 및 사용자 프로비저닝 구성](use-scim-to-provision-users-and-groups.md)
- [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
