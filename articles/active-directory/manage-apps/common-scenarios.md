---
title: Azure Active Directory에 대 한 일반적인 응용 프로그램 관리 시나리오 Microsoft Docs
description: Azure AD를 사용 하 여 응용 프로그램 관리 중앙 집중화
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
ms.openlocfilehash: 1874a2f2cf96aaa905616bddcc6cb83c60c1d279
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115611"
---
# <a name="centralize-application-management-with-azure-ad"></a>Azure AD를 사용 하 여 응용 프로그램 관리 중앙 집중화

암호, 전 세계 직원의 어려움 및 어려움 이 때문에 더 많은 회사에서 클라우드를 위한 Microsoft의 Id 및 액세스 관리 솔루션과 기타 모든 리소스를 Azure Active Directory 합니다. 각 항목에 대 한 암호를 입력 하지 않고 응용 프로그램에서 응용 프로그램으로 이동 합니다. 신속 하 고 안전 하 게 열 수 있을 만큼 빠르게 Outlook, Workday, ADP로 이동 합니다. 그런 다음 파트너와 공동 작업을 할 필요 없이 조직 외부의 다른 사용자도 공동 작업을 합니다. 무엇 보다도, Azure AD는 사용자가 누구 인지 확인 하기 위해 multi-factor authentication과 같은 앱을 보호 하 고, 지속적인 적응 기계 학습 및 보안 인텔리전스를 사용 하 여 필요한 앱에 대 한 안전한 액세스를 제공 하는 등의 작업을 수행 하 여 위험을 관리 하는 데 도움이 됩니다. 사용자에 게는 유용 하지만 사용자에 게는 유용 하지 않습니다. Just-in-time 액세스 검토와 완전 한 규모의 거 버 넌 스 제품군을 사용 하 여 Azure AD는 준수 상태를 유지 하 고 정책을 적용 하는 데 도움이 됩니다. 그러면 사용자 계정을 자동으로 프로 비전 하 여 액세스 관리를 간편 하 게 수행할 수 있습니다. 고객이 Azure Active Directory의 응용 프로그램 관리 기능을 사용 하는 일반적인 시나리오 중 일부를 확인 하세요.

**일반적인 시나리오**


> [!div class="checklist"]
> * 모든 응용 프로그램에 대 한 SSO
> * 프로 비전 및 프로 비전 해제 자동화 
> * 응용 프로그램 보안
> * 응용 프로그램에 대 한 액세스 제어
> * 하이브리드 보안 액세스

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>시나리오 1: 모든 응용 프로그램에 대 한 SSO 설정

더 이상 암호를 관리 하지 않습니다. 회사 자격 증명을 사용 하 여 필요한 모든 리소스에 안전 하 게 액세스 합니다. 

|기능  | Description | 권장 |
|---------|---------|---------|
|SSO|신뢰할 수 있는 업계 표준을 사용 하는 표준 기반 페더레이션된 SSO.|응용 프로그램에서 지원할 때 SSO를 사용 하도록 설정 하려면 항상 [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) 를 사용 해야 합니다.|
|액세스 패널|사용자에게 모든 애플리케이션을 검색 및 액세스할 수 있는 간단한 허브를 제공합니다. 앱 및 그룹에 대 한 액세스를 요청 하거나 다른 사용자를 대신 하 여 리소스에 대 한 액세스를 관리 하는 등의 셀프 서비스 기능을 사용 하 여 생산성을 높일 수 있습니다.| SSO 용 Azure AD와 앱을 통합 한 후에 조직에 [액세스 패널](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) 을 배포 합니다.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>시나리오 2: 프로 비전 및 프로 비전 해제 자동화 


대부분의 응용 프로그램에서는 필요한 리소스에 액세스 하기 전에 사용자를 응용 프로그램에 프로 비전 해야 합니다. CSV 파일이 나 복잡 한 스크립트를 사용 하면 비용이 많이 들고 관리 하기가 어려울 수 있습니다. 또한 고객은 다른 사람이 더 이상 액세스할 수 없는 경우에도 계정이 제거 되었는지 확인 해야 합니다. 아래 도구를 활용 하 여 프로 비전 및 프로 비전 해제를 자동화할 수 있습니다. 


|기능  |Description|권장 |
|---------|---------|---------|
|SCIM 프로 비전|[Scim](https://aka.ms/SCIMOverview) 은 사용자 프로 비전을 자동화 하는 업계 모범 사례입니다. 모든 SCIM 규격 응용 프로그램은 Azure AD와 통합 될 수 있습니다. CSV 파일, 사용자 지정 스크립트 또는 온-프레미스 솔루션을 유지 하지 않고도 사용자 계정을 자동으로 만들고, 업데이트 하 고, 삭제 합니다.|Azure AD 앱 갤러리에서 [미리 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 된 앱의 증가 된 목록 확인|
|Microsoft Graph|Azure AD에서 필요한 데이터를 사용 하 여 응용 프로그램을 보강 하는 데 필요한 데이터의 대략적인 성과 수준을 활용 합니다.|Microsoft [graph](https://developer.microsoft.com/graph/) 를 활용 하 여 microsoft 에코 시스템을 통해 데이터를 가져옵니다. |


## <a name="scenario-3-secure-your-applications"></a>시나리오 3: 응용 프로그램 보안
Id는 보안을 위한 linchpin입니다. Id가 손상 되 면 너무 늦게 되기 전에 domino 효과를 중지 하는 것은 매우 어렵습니다. 조직에서 손상이 발생 한 것을 확인 하기 전에 100 일이 지난 평균에 통과 합니다. Azure AD에서 제공 하는 도구를 사용 하 여 응용 프로그램의 보안 상태를 향상 시킵니다. 

|기능  |Description| 권장 |
|---------|---------| ---------|
|Azure MFA|Azure Multi-factor Authentication(MFA)은 Microsoft의 2단계 인증 솔루션입니다. 관리자가 승인한 인증 방법을 사용 하 여 Azure MFA는 간단한 로그인 프로세스에 대 한 수요를 충족 하면서 데이터와 응용 프로그램에 대 한 액세스를 보호 합니다.| 사용자에 대해 [MFA를 사용 하도록 설정](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) 합니다.  |
|조건부 액세스|조건부 액세스를 사용 하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사용자에 대 한 자동화 된 액세스 제어 결정을 구현할 수 있습니다.| 고객이 사용 하는 [보안 기본값](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 및 [공통 정책을](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) 검토 합니다. | 
|ID 보호|Identity Protection은 사용자 보호를 위해 Microsoft가 Azure AD를 사용하는 조직, Microsoft 계정의 소비자 공간 및 Xbox를 이용한 게임 등에서 사용자 위치로부터 습득한 학습을 사용합니다. Microsoft는 하루 6조 5000억 개의 신호를 분석하여 고객을 위협으로부터 보호합니다.|서비스에서 제공 하는 [기본 id 보호 정책을](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) 사용 하도록 설정 합니다. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>시나리오 4: 응용 프로그램에 대 한 액세스 제어
Id 거 버 넌 스를 통해 조직에 참여 하는 경우와 같이 조직에서 생산성을 유지 하는 데 필요한 응용 프로그램에 얼마나 신속 하 게 액세스할 수 있나요? 보안 - 해당하는 사람의 고용 상태의 변경 내용과 같이 시간이 지남에 따라 해당하는 액세스를 어떻게 변경해야 하나요? 

|기능  |Description|권장 |
|---------|---------| ---------|
|느릅나무|Azure AD 자격 관리는 조직 내부 및 외부의 사용자가 응용 프로그램에 대 한 액세스를 보다 효율적으로 관리할 수 있도록 지원 합니다.| 관리자가 아닌 사용자가 [액세스 패키지](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)를 사용 하 여 응용 프로그램 액세스를 관리할 수 있도록 허용 합니다.|
|액세스 검토|앱에 대 한 사용자의 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.| 가장 중요 한 응용 프로그램에 대 한 [액세스를 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) 합니다. |
|Log Analytics|응용 프로그램에 액세스 하는 사용자에 대 한 보고서를 생성 하 고 사용자가 선택한 SIEM 도구에 저장 하 여 데이터 원본과 시간에 따라 데이터를 상호 연결 합니다.| [Log analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) 를 사용 하도록 설정 하 고 응용 프로그램과 관련 된 중요 이벤트에 대 한 경고를 설정 합니다. |


## <a name="scenario-5-hybrid-secure-access"></a>시나리오 5: 하이브리드 보안 액세스
Id는 클라우드 및 온-프레미스 응용 프로그램에서 모든 항목을 연결할 수 있는 경우에만 컨트롤 평면이 될 수 있습니다. Azure AD 및 해당 파트너에서 제공 하는 도구를 활용 하 여 레거시 인증 기반 응용 프로그램에 대 한 액세스를 보호 합니다.

|기능  |Description|권장 |
|---------|---------|---------|
|애플리케이션 프록시|요즈음 직원은 어디서나 언제든지 어느 디바이스에서나 생산성을 높이기를 원합니다. 클라우드 및 회사 앱 온-프레미스에서 SaaS 앱에 액세스 해야 합니다. Azure AD 응용 프로그램 프록시는 비용이 많이 들고 복잡 한 Vpn (가상 사설망) 또는 완충 영역 (Dmz) 없이 이러한 강력한 액세스를 가능 하 게 합니다.|온-프레미스 앱에 대 한 [원격 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 를 설정 합니다. |
|F5, Akamai, Zscaler|기존 네트워킹 및 배달 컨트롤러를 사용하여 비즈니스 프로세스에 여전히 중요 하지만 이전에는 Azure AD로 보호할 수 없었는 레거시 애플리케이션을 쉽게 보호할 수 있습니다. 이러한 애플리케이션을 보호하는 데 필요한 모든 조건은 이미 충족되었을 것입니다.| Akamai, Citrix, F5 또는 Zscaler를 사용 하 고 있나요? [미리 작성 된 솔루션](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)을 확인 하세요. | 

## <a name="related-articles"></a>관련된 문서

- [애플리케이션 관리](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [애플리케이션 프로비저닝](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [하이브리드 보안 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Id 거 버 넌 스](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft ID 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Id 보안](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
