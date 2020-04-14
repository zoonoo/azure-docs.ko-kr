---
title: Azure Active Directory에 대한 일반적인 응용 프로그램 관리 시나리오 | 마이크로 소프트 문서
description: Azure AD를 통해 응용 프로그램 관리 중앙 집중화
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21caec50bf9c20ec60194976b1bfbf16e4815914
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261043"
---
# <a name="centralize-application-management-with-azure-ad"></a>Azure AD를 통해 응용 프로그램 관리 중앙 집중화

암호, IT 악몽과 전 세계 직원에 대한 고통 모두. 이 때문에 점점 더 많은 기업들이 Azure Active Directory, 클라우드 및 기타 모든 리소스에 대한 Microsoft의 ID 및 액세스 관리 솔루션으로 전환하고 있습니다. 각 암호를 입력하지 않고도 응용 프로그램에서 응용 프로그램으로 이동합니다. Outlook에서 Workday로 빠르게 ADP로 이동하여 빠르고 안전하게 열 수 있습니다. 그런 다음 IT를 호출하지 않고도 파트너 및 조직 외부의 다른 사용자와 공동 작업을 수행할 수 있습니다. 또한 Azure AD는 다단계 인증과 같은 기능을 사용하여 사용자가 누구인지 확인하는 데 사용하는 앱을 보호하고, 지속적으로 적응형 기계 학습 및 보안 인텔리전스를 사용하여 의심스러운 로그인을 감지하여 필요한 앱에 안전하게 액세스할 수 있도록 하여 위험을 관리하는 데 도움이 됩니다. 사용자뿐만 아니라 IT에도 적합합니다. 적시에 액세스 검토와 전체 규모의 거버넌스 제품군을 통해 Azure AD를 사용하면 규정을 준수하고 정책을 적용할 수 있습니다. 또한 사용자 계정 프로비저닝을 자동화하여 액세스 관리를 쉽게 수행할 수도 있습니다. 고객이 Azure Active Directory의 응용 프로그램 관리 기능을 사용하는 몇 가지 일반적인 시나리오를 확인하십시오.

**일반적인 시나리오**


> [!div class="checklist"]
> * 모든 애플리케이션을 위한 SSO
> * 프로비저닝 및 프로비저닝 해제 자동화 
> * 애플리케이션 보안
> * 애플리케이션에 대한 액세스 관리
> * 하이브리드 보안 액세스

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>시나리오 1: 모든 애플리케이션에 대해 SSO 설정

더 이상 암호를 관리할 수 없습니다. 회사 자격 증명을 통해 필요한 모든 리소스에 안전하게 액세스할 수 있습니다. 

|기능  | Description | 권장 |
|---------|---------|---------|
|SSO|신뢰할 수 있는 산업 표준을 사용하는 표준 기반 페더레이션 SSO.|항상 [SAML /OIDC를](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) 사용하여 응용 프로그램이 지원하는 경우 SSO를 사용하도록 설정합니다.|
|액세스 패널|사용자에게 모든 애플리케이션을 검색 및 액세스할 수 있는 간단한 허브를 제공합니다. 앱 및 그룹에 대한 액세스 요청 또는 다른 사용자 대신 리소스에 대한 액세스 관리와 같은 셀프 서비스 기능을 통해 생산성을 높일 수 있습니다.| SSO용 Azure AD와 앱을 통합한 후 조직에 [액세스 패널을](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) 배포합니다.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>시나리오 2: 프로비저닝 및 프로비저닝 해제 자동화 


대부분의 응용 프로그램은 필요한 리소스에 액세스하기 전에 사용자를 응용 프로그램에 프로비전해야 합니다. CSV 파일이나 복잡한 스크립트를 사용하면 비용이 많이 들고 관리하기가 어려울 수 있습니다. 또한 고객은 다른 사람이 더 이상 액세스할 수 없는 경우 계정이 제거되도록 해야 합니다. 아래 도구를 활용하여 프로비저닝 및 프로비저닝을 자동화합니다. 


|기능  |Description|권장 |
|---------|---------|---------|
|SCIM 프로비저닝|[SCIM은](https://aka.ms/SICMOverview) 사용자 프로비저닝 자동화를 위한 업계 모범 사례입니다. 모든 SCIM 호환 응용 프로그램은 Azure AD와 통합될 수 있습니다. CSV 파일, 사용자 지정 스크립트 또는 온프레미 솔루션을 유지 관리할 필요 없이 사용자 계정을 자동으로 생성, 업데이트 및 삭제할 수 있습니다.|Azure AD 앱 갤러리에서 [증가하는 사전 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 앱 목록을 확인하세요.|
|Microsoft Graph|Azure AD가 필요한 데이터로 응용 프로그램을 보강하는 데 필요한 데이터의 숨결과 깊이를 활용합니다.|Microsoft [그래프를](https://developer.microsoft.com/graph/) 활용하여 Microsoft 생태계 전반에서 데이터를 가져옵니다. |


## <a name="scenario-3-secure-your-applications"></a>시나리오 3: 애플리케이션 보안
ID는 보안을 위한 핵심입니다. ID가 손상되면 너무 늦기 전에 도미노 효과를 중지하는 것은 매우 어렵습니다. 평균적으로 100일이 넘게 지나면 조직이 타협점을 찾았다는 사실을 알게 됩니다. Azure AD에서 제공하는 도구를 사용하여 응용 프로그램의 보안 상태를 개선합니다. 

|기능  |Description| 권장 |
|---------|---------| ---------|
|Azure MFA|Azure Multi-factor Authentication(MFA)은 Microsoft의 2단계 인증 솔루션입니다. Azure MFA는 관리자가 승인한 인증 방법을 사용하여 간단한 로그인 프로세스에 대한 요구를 충족하면서 데이터 및 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다.| 사용자에 대한 [MFA를 사용하도록 설정합니다.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)  |
|조건부 액세스|조건부 액세스를 사용하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사람에 대한 자동 액세스 제어 결정을 구현할 수 있습니다.| 고객이 사용하는 [보안 기본 값](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 및 일반적인 [정책을](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) 검토합니다. | 
|ID 보호|Identity Protection은 사용자 보호를 위해 Microsoft가 Azure AD를 사용하는 조직, Microsoft 계정의 소비자 공간 및 Xbox를 이용한 게임 등에서 사용자 위치로부터 습득한 학습을 사용합니다. Microsoft는 하루 6조 5000억 개의 신호를 분석하여 고객을 위협으로부터 보호합니다.|서비스에서 제공하는 [기본 ID 보호 정책을](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) 활성화합니다. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>시나리오 4: 애플리케이션에 대한 액세스 관리
ID 거버넌스를 통해 조직은 생산성 간의 균형을 맞출 수 있습니다 - 사용자가 조직에 가입할 때와 같이 필요한 응용 프로그램에 얼마나 빨리 액세스할 수 있습니까? 보안 - 해당하는 사람의 고용 상태의 변경 내용과 같이 시간이 지남에 따라 해당하는 액세스를 어떻게 변경해야 하나요? 

|기능  |Description|권장 |
|---------|---------| ---------|
|느릅나무|Azure AD 권한 관리기능을 사용하면 조직 내부와 외부의 사용자가 응용 프로그램에 대한 액세스를 보다 효율적으로 관리할 수 있습니다.| 관리자가 아닌 사용자가 액세스 패키지를 사용하여 응용 프로그램에 액세스할 수 [있도록 허용합니다.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)|
|액세스 검토|앱에 대한 사용자의 액세스 는 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.| 가장 중요한 응용 프로그램에 [대한 액세스를 검토합니다.](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) |
|Log Analytics|데이터 원본과 시간이 지남에 따라 데이터를 상호 연관시키기 위해 SIEM 도구에 어떤 응용 프로그램에 액세스하고 있는지에 대한 보고서를 생성하고 SIEM 도구에 저장합니다.| [로그 분석을](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) 활성화하고 응용 프로그램과 관련된 중요한 이벤트에 대한 경고를 설정합니다. |


## <a name="scenario-5-hybrid-secure-access"></a>시나리오 5: 하이브리드 보안 액세스
ID는 클라우드 및 온-프레미스 응용 프로그램에서 모든 것을 연결할 수 있는 경우에만 제어 평면이 될 수 있습니다. Azure AD 및 파트너가 제공하는 도구를 활용하여 레거시 인증 기반 응용 프로그램에 대한 액세스를 보호합니다.

|기능  |Description|권장 |
|---------|---------|---------|
|애플리케이션 프록시|요즈음 직원은 어디서나 언제든지 어느 디바이스에서나 생산성을 높이기를 원합니다. 클라우드의 SaaS 앱과 온-프레미스의 회사 앱에 액세스해야 합니다. Azure AD 응용 프로그램 프록시를 사용하면 비용이 많이 들고 복잡한 가상 사설망(VPN) 또는 비무장 영역(DMZ)없이 이 강력한 액세스를 수행할 수 있습니다.|온-프레임 앱에 대한 [원격 액세스를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 설정합니다. |
|F5, 아카마이, Zscaler|기존 네트워킹 및 배달 컨트롤러를 사용하여 비즈니스 프로세스에 여전히 중요 하지만 이전에는 Azure AD로 보호할 수 없었는 레거시 애플리케이션을 쉽게 보호할 수 있습니다. 이러한 애플리케이션을 보호하는 데 필요한 모든 조건은 이미 충족되었을 것입니다.| Akamai, 시트릭스, F5 또는 Zscaler를 사용하시나요? [미리 빌드된 솔루션을](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)확인하십시오. | 

## <a name="related-articles"></a>관련 문서

- [애플리케이션 관리](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [애플리케이션 프로비저닝](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [하이브리드 보안 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [ID 거버넌스](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft ID 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [ID 보안](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
